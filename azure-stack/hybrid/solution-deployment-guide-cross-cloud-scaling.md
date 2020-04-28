---
title: 部署在 Azure 中缩放跨云并 Azure Stack 中心的应用
description: 了解如何部署可在 Azure 和 Azure Stack 集线器中缩放跨云的应用。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: a95ce4d59e5374cab351c165405895f552478f6b
ms.sourcegitcommit: e5b587216a137819444680ec619281c90f37bad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "82167069"
---
# <a name="deploy-an-app-that-scales-cross-cloud-using-azure-and-azure-stack-hub"></a>使用 Azure 和 Azure Stack Hub 部署可跨云缩放的应用

了解如何创建可提供手动触发过程的跨云解决方案，以通过流量管理器使用自动缩放功能从 Azure Stack Hub 托管的 Web 应用切换到 Azure 托管的 Web 应用。 此过程确保云实用工具在承受负载时保持灵活性和可伸缩性。

使用此模式时，租户可能尚未准备好在公有云中运行你的应用。 但是，要让企业在本地环境中保持用于处理应用需求高峰的容量，在经济上似乎不切实际。 租户可以通过其本地解决方案使用公有云的弹性。

在此解决方案中，你将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 创建多节点 Web 应用。
> - 配置和管理持续部署 (CD) 过程。
> - 将 Web 应用发布到 Azure Stack Hub。
> - 创建发布。
> - 了解如何监视和跟踪部署。

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack 中心为本地环境带来了云计算的灵活性和创新，实现了唯一的混合云，使你能够在任何位置构建和部署混合应用。  
> 
> [混合应用设计注意事项](overview-app-design-considerations.md)一文介绍了用于设计、部署和操作混合应用的软件质量（放置、可伸缩性、可用性、复原能力、可管理性和安全性）的支柱。 这些设计注意事项有助于优化混合应用设计，从而最大限度地减少生产环境中的难题。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果需要，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure Stack 中心集成系统或部署 Azure Stack 开发工具包（ASDK）。
  - 有关安装 Azure Stack 集线器的说明，请参阅[安装 ASDK](../asdk/asdk-install.md)。
  - 有关 ASDK 部署后自动化脚本，请参阅：[https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1)
  - 此项安装可能需要几个小时才能完成。
- 将[应用服务](../operator/azure-stack-app-service-deploy.md) PaaS 服务部署到 Azure Stack Hub。
- 在 Azure Stack 中心环境中[创建计划/产品/服务](../operator/service-plan-offer-subscription-overview.md)。
- 在 Azure Stack Hub 环境中[创建租户订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。
- 在租户订阅中创建 Web 应用。 记下新 Web 应用的 URL，供稍后使用。
- 在租户订阅中部署 Azure Pipelines 虚拟机（VM）。
- 需要装有 .NET 3.5 的 Windows Server 2016 VM。 将在 Azure Stack Hub 上的租户订阅中构建此 VM 作为专用生成代理。
- 在 Azure Stack Hub Marketplace 中提供[了包含 SQL 2017 VM 映像的 Windows Server 2016](../operator/azure-stack-add-vm-image.md) 。 如果此映像不可用，请与 Azure Stack Hub 操作员协作，以确保将此映像添加到环境中。

## <a name="issues-and-considerations"></a>问题和注意事项

### <a name="scalability"></a>可伸缩性

跨云缩放的关键要素是能按需在公共和本地云基础结构之间提供即时缩放功能，证明服务可保持一致且可靠。

### <a name="availability"></a>可用性

确定通过本地硬件配置和软件部署来配置本地部署的应用，以实现高可用性。

### <a name="manageability"></a>可管理性

跨云解决方案确保在环境之间提供无缝的管理和熟悉的界面。 建议使用 PowerShell 进行跨平台管理。

## <a name="cross-cloud-scaling"></a>跨云缩放

### <a name="get-a-custom-domain-and-configure-dns"></a>获取自定义域并配置 DNS

更新域的 DNS 区域文件。 然后，Azure AD 将会验证自定义域名的所有权。 将 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) 用于 Azure 中的 Azure/Office 365/外部 DNS 记录，或在[其他 DNS 注册机构](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)中添加 DNS 条目。

1. 向公共注册机构注册自定义域。
2. 登录到域的域名注册机构。 可能需要由获批准的管理员进行 DNS 更新。
3. 通过添加 Azure AD 提供的 DNS 条目来更新域的 DNS 区域文件。 （DNS 条目不会影响电子邮件路由或 web 托管行为。）

### <a name="create-a-default-multi-node-web-app-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建默认的多节点 Web 应用

设置混合持续集成和持续部署 (CI/CD)，以将 Web 应用部署到 Azure 和 Azure Stack Hub，并自动将更改推送到这两个云中。

> [!Note]  
> 需要在 Azure Stack Hub 上创建适当的合成映像用于运行 Windows Server 和 SQL，并需要部署应用服务。 有关详细信息，请参阅在[Azure Stack Hub 上部署应用服务](../operator/azure-stack-app-service-before-you-get-started.md)的应用服务文档先决条件。

### <a name="add-code-to-azure-repos"></a>向 Azure Repos 中添加代码

Azure Repos

1. 使用在 Azure Repos 上拥有项目创建权限的帐户登录到 Azure Repos。

    混合 CI/CD 可同时应用到应用代码和基础结构代码。 使用 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)进行专用与托管的云开发。

    ![连接到 Azure Repos 上的项目](media/solution-deployment-guide-cross-cloud-scaling/image1.JPG)

2. 创建并打开默认 Web 应用以**克隆存储库**。

    ![在 Azure Web 应用中克隆存储库](media/solution-deployment-guide-cross-cloud-scaling/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>为这两个云中的应用服务创建独立的 Web 应用部署

1. 编辑 **WebApplication.csproj** 文件。 选择 `Runtimeidentifier` 并添加 `win10-x64`。 （请参阅[自包含的部署](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf)文档。）

    ![编辑 Web 应用项目文件](media/solution-deployment-guide-cross-cloud-scaling/image3.png)

2. 使用团队资源管理器将代码签入 Azure Repos。

3. 确认应用代码已签入到 Azure Repos。

## <a name="create-the-build-definition"></a>创建生成定义

1. 登录到 Azure Pipelines 以确认能够创建生成定义。

2. 添加 **-r win10-x64** 代码。 使用 .NET Core 触发独立部署时需要添加此代码。

    ![将代码添加到 Web 应用](media/solution-deployment-guide-cross-cloud-scaling/image4.png)

3. 运行生成。 [独立部署生成](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf)过程将发布可在 Azure 和 Azure Stack Hub 上运行的项目。

## <a name="use-an-azure-hosted-agent"></a>使用 Azure 托管代理

在 Azure Pipelines 中使用托管生成代理是生成和部署 Web 应用的便捷做法。 维护和升级是通过 Microsoft Azure 自动完成的，可实现持续且无中断的开发周期。

### <a name="manage-and-configure-the-cd-process"></a>管理和配置 CD 过程

Azure Pipelines 和 Azure DevOps Services 为发布到多个环境（例如开发、过渡、QA 和生产环境）提供高度可配置和可管理的管道;包括在特定阶段要求审批。

## <a name="create-release-definition"></a>创建发布定义

1. 在 Azure DevOps Services 的 "**生成和发布**" 部分的 "**发布**" 选项卡下，选择**加号**按钮以添加新发布。

    ![创建发布定义](media/solution-deployment-guide-cross-cloud-scaling/image5.png)

2. 应用“Azure 应用服务部署”模板。

   ![应用 Azure 应用服务部署模板](meDia/solution-deployment-guide-cross-cloud-scaling/image6.png)

3. 在“添加项目”下，为 Azure 云生成应用添加项目。 

   ![向 Azure 云生成添加项目](media/solution-deployment-guide-cross-cloud-scaling/image7.png)

4. 在“管道”选项卡下选择环境的“阶段和任务”链接，并设置 Azure 云环境值。 

   ![设置 Azure 云环境值](media/solution-deployment-guide-cross-cloud-scaling/image8.png)

5. 设置**环境名称**，并选择 Azure 云终结点的 **Azure 订阅**。

      ![选择 Azure 云终结点的 Azure 订阅](media/solution-deployment-guide-cross-cloud-scaling/image9.png)

6. 在“应用服务名称”下，设置所需的 Azure 应用服务名称。 

      ![设置 Azure 应用服务名称](media/solution-deployment-guide-cross-cloud-scaling/image10.png)

7. 在 Azure 云托管环境的“代理队列”下输入“Hosted VS2017”。 

      ![为 Azure 云托管环境设置代理队列](media/solution-deployment-guide-cross-cloud-scaling/image11.png)

8. 在“部署 Azure 应用服务”菜单中，为环境选择有效的**包或文件夹**。 选择**文件夹位置**旁边的“确定”。 
  
      ![选择 Azure 应用服务环境的包或文件夹](media/solution-deployment-guide-cross-cloud-scaling/image12.png)

      ![选择 Azure 应用服务环境的包或文件夹](media/solution-deployment-guide-cross-cloud-scaling/image13.png)

9. 保存所有更改并返回**发布管道**。

    ![在发布管道中保存更改](media/solution-deployment-guide-cross-cloud-scaling/image14.png)

10. 选择 Azure Stack Hub 应用的生成以添加新项目。

    ![为 Azure Stack Hub 应用添加新项目](media/solution-deployment-guide-cross-cloud-scaling/image15.png)

11. 通过应用 Azure 应用服务部署额外添加一个环境。

    ![将环境添加到 Azure 应用服务部署](media/solution-deployment-guide-cross-cloud-scaling/image16.png)

12. 将新环境命名为 "Azure Stack"。

    ![为 Azure 应用服务部署中的环境命名](media/solution-deployment-guide-cross-cloud-scaling/image17.png)

13. 在“任务”选项卡下找到 Azure Stack 环境。 

    ![Azure Stack 环境](media/solution-deployment-guide-cross-cloud-scaling/image18.png)

14. 选择 Azure Stack 终结点的订阅。

    ![选择 Azure Stack 终结点的订阅](media/solution-deployment-guide-cross-cloud-scaling/image19.png)

15. 将 Azure Stack Web 应用名称设置为应用服务名称。
    ![设置 Azure Stack Web 应用名称](media/solution-deployment-guide-cross-cloud-scaling/image20.png)

16. 选择“Azure Stack 代理”。

    ![选择“Azure Stack 代理”](media/solution-deployment-guide-cross-cloud-scaling/image21.png)

17. 在“部署 Azure 应用服务”部分下，为环境选择有效的**包或文件夹**。 选择文件夹位置旁边的“确定”。 

    ![选择 Azure 应用服务部署的文件夹](media/solution-deployment-guide-cross-cloud-scaling/image22.png)

    ![选择 Azure 应用服务部署的文件夹](media/solution-deployment-guide-cross-cloud-scaling/image23.png)

18. 在“变量”选项卡下添加名为 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` 的变量，将其值设置为 **true**，将范围设置为 Azure Stack。

    ![将变量添加到 Azure 应用部署](media/solution-deployment-guide-cross-cloud-scaling/image24.png)

19. 选择两个项目中的“持续”部署触发器图标，并启用“持续”部署触发器。  

    ![选择持续部署触发器](media/solution-deployment-guide-cross-cloud-scaling/image25.png)

20. 选择 Azure Stack 环境中的“部署前”条件图标，并将触发器设置为“发布后”。  

    ![选择部署前的条件](media/solution-deployment-guide-cross-cloud-scaling/image26.png)

21. 保存所有更改。

> [!Note]  
> 任务的某些设置可能已在从模板创建发布定义时自动定义为[环境变量](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables)。 无法在任务设置中修改这些设置；必须选择父环境项才能编辑这些设置。

## <a name="publish-to-azure-stack-hub-via-visual-studio"></a>通过 Visual Studio 发布到 Azure Stack Hub

通过创建终结点，Azure DevOps Services 生成可以将 Azure 服务应用部署到 Azure Stack 中心。 Azure Pipelines 会连接到生成代理，而后者会连接到 Azure Stack Hub。

1. 登录到 Azure DevOps Services 并中转到 "应用程序设置" 页。

2. 在“设置”中，选择“安全性”。  

3. 在“VSTS 组”中，选择“终结点创建者”。  

4. 在“成员”选项卡上，选择“添加”。  

5. 在“添加用户和组”中输入用户名，然后从用户列表中选择该用户。 

6. 选择“保存更改”。 

7. 在“VSTS 组”列表中，选择“终结点管理员”。  

8. 在“成员”选项卡上，选择“添加”。  

9. 在“添加用户和组”中输入用户名，然后从用户列表中选择该用户。 

10. 选择“保存更改”。 

获取终结点信息后，可以使用 Azure Pipelines 到 Azure Stack Hub 的连接。 Azure Stack 集线器中的生成代理从 Azure Pipelines 中获取说明，然后代理会传达用于与 Azure Stack 中心通信的终结点信息。

## <a name="develop-the-app-build"></a>开发应用生成

> [!Note]  
> 需要在 Azure Stack Hub 上创建适当的合成映像用于运行 Windows Server 和 SQL，并需要部署应用服务。 有关详细信息，请参阅[在 Azure Stack 集线器上部署应用服务的先决条件](../operator/azure-stack-app-service-before-you-get-started.md)。

使用 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)（例如 Azure Repos 中的 Web 应用代码）将内容部署到这两个云。

### <a name="add-code-to-an-azure-repos-project"></a>向 Azure Repos 项目添加代码

1. 使用在 Azure Stack Hub 上拥有项目创建权限的帐户登录到 Azure Repos。

2. 创建并打开默认 Web 应用以**克隆存储库**。

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>为这两个云中的应用服务创建独立的 Web 应用部署

1. 编辑**WebApplication**文件：选择`Runtimeidentifier`然后添加。 `win10-x64` 有关详细信息，请参阅[独立部署](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf)文档。

2. 使用团队资源管理器将代码签入 Azure Repos。

3. 确认应用代码已签入 Azure Repos。

### <a name="create-the-build-definition"></a>创建生成定义

1. 使用可以创建生成定义的帐户登录到 Azure Pipelines。

2. 前往项目的 "**生成 Web 应用程序**" 页。

3. 在“参数”中，**** 添加 **-r win10-x64** 代码。 在 .NET Core 中触发独立部署时需要添加此代码。

4. 运行生成。 [独立部署生成](https://docs.microsoft.com/dotnet/core/deploying/deploy-with-vs#simpleSelf)过程将发布可在 Azure 和 Azure Stack Hub 上运行的项目。

#### <a name="use-an-azure-hosted-build-agent"></a>使用 Azure 托管生成代理

在 Azure Pipelines 中使用托管生成代理是生成和部署 Web 应用的便捷做法。 维护和升级是通过 Microsoft Azure 自动完成的，可实现持续且无中断的开发周期。

### <a name="configure-the-continuous-deployment-cd-process"></a>配置持续部署 (CD) 过程

Azure Pipelines 和 Azure DevOps Services 为发布到多个环境（例如开发、过渡、质量保证（QA）和生产）提供高度可配置和可管理的管道。 此过程可能包括在应用生命周期的特定阶段需要审批。

#### <a name="create-release-definition"></a>创建发布定义

创建发布定义是应用生成过程中的最后一步。 此发布定义用于创建一个发布并部署一个生成。

1. 登录到 Azure Pipelines，然后前往项目的**生成和发布**。

2. 在“发布”选项卡上选择“[ + ]”，然后选择“创建发布定义”。************

3. 在“选择模板”上选择“Azure 应用服务部署”，然后选择“应用”。************

4. 在“添加项目”的“源(生成定义)”中，选择“Azure 云生成应用”。********

5. 在“管道”选项卡上选择“1 阶段，1 任务”链接，以便**查看环境任务**。************

6. 在“任务”选项卡上输入 Azure 作为“环境名称”，然后从“Azure 订阅”列表中选择“AzureCloud Traders-Web EP”。************

7. 输入 **Azure 应用服务名称**，即下一屏幕截图中的 `northwindtraders`。

8. 从“代理队列”列表中选择“托管的 VS2017”作为“代理阶段”********。

9. 在“部署 Azure 应用服务”中，为环境选择有效的**包或文件夹**。****

10. 确认“选择文件或文件夹”中的“Location”后，选择“确定”。************

11. 保存所有更改后，回到“管道”****。

12. 在“管道”选项卡上选择“添加项目”，然后从“源(生成定义)”列表中选择“NorthwindCloud Traders-Vessel”。****************

13. 在“选择模板”中添加另一环境。**** 选取“Azure 应用服务部署”，然后选择“应用”。********

14. 输入 `Azure Stack Hub` 作为“环境名称”。****

15. 在“任务”选项卡上，找到并选择“Azure Stack Hub”。****

16. 从“Azure 订阅”列表中选择“AzureStack Traders-Vessel EP”作为“Azure Stack Hub 终结点”********。

17. 输入 Azure Stack Hub Web 应用名称作为**应用服务名称**。

18. 从“代理选择”下的“代理队列”列表中选取“AzureStack -b Douglas Fir”************。

19. 至于“部署 Azure 应用服务”，请为环境选择有效的**包或文件夹**。**** 确认“选择文件或文件夹”中的“Location”文件夹后，选择“确定”。************

20. 在“变量”选项卡上，找到名为 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` 的变量。**** 将变量值设置为 **true**，将其范围设置为 **Azure Stack Hub**。

21. 在“管道”选项卡上，选择 NorthwindCloud Traders-Web 项目对应的“持续部署触发器”图标，然后将“持续部署触发器”设置为“启用”。**************** 针对“NorthwindCloud Traders-Vessel”项目执行相同的操作。****

22. 至于 Azure Stack Hub 环境，请选择“部署前条件”图标，并将触发器设置为“发布后”。********

23. 保存所有更改。

> [!Note]  
> 发布任务的某些设置将在从模板创建发布定义时自动定义为[环境变量](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables)。 无法在任务设置中修改这些设置，但可以在父环境项中修改这些设置。

## <a name="create-a-release"></a>创建发布

1. 在“管道”选项卡上打开“发布”列表，然后选择“创建发布”。************

2. 输入发布的说明，查看是否选择了正确的项目，然后选择“创建”。**** 片刻之后，将会出现一个横幅，指出已创建新的发布，发布名称以链接形式显示。 选择该链接，查看发布摘要页。

3. 发布摘要页显示发布详细信息。 在下面的针对“Release-2”的屏幕捕获中，“环境”部分显示 Azure 的“部署状态”为“正在进行”，Azure Stack Hub 的状态为“成功”********。 当 Azure 环境的部署状态变为“成功”以后，会显示一个横幅，指示可以审批发布了。 如果部署挂起或失败，则会显示一个蓝色的 **(i)** 信息图标。 将鼠标悬停在图标上方即可看到一个弹出窗口，其中包含延迟或失败的原因。

4. 其他视图（如版本列表）还会显示表明审批处于挂起状态的图标。 此图标的弹出窗口会显示环境名称以及与部署相关的更多详细信息。 管理员可以轻松查看发布的总体进度，并查看哪些发布正在等待批准。

## <a name="monitor-and-track-deployments"></a>监视和跟踪部署

1. 在“Release-2”摘要页中选择“日志”********。 部署期间，此页显示代理的实时日志。 左窗格显示每个环境的部署过程中每个操作的状态。

2. 选择 "**操作**" 列中的 "人员" 图标进行预先部署或后期部署审批，以查看谁批准（或拒绝）部署及其提供的消息。

3. 部署完成后，整个日志文件会显示在右窗格中。 选择左窗格中的任意**步骤**，查看日志文件中的单个步骤，如**初始化作业**。 有了查看单个日志的功能，就可以更轻松地跟踪和调试整体部署的部件。 **保存**日志文件以进行一步或将**所有日志下载为 zip**。

4. 打开“摘要”选项卡，查看有关该发布的常规信息。**** 此视图详细显示了该发布所部署到的生成和环境、部署状态，以及有关该发布的其他信息。

5. 选择环境链接（“Azure”或“Azure Stack Hub”），查看部署到特定环境的现有部署和待定部署的相关信息********。 使用这些视图可以快速检查是否已将同一版本部署到这两个环境。

6. 在浏览器中打开**已部署的生产应用**。 例如，对于 Azure 应用服务网站，请打开 URL `https://[your-app-name\].azurewebsites.net`。

### <a name="integration-of-azure-and-azure-stack-hub-provides-a-scalable-cross-cloud-solution"></a>Azure 与 Azure Stack Hub 的集成提供可缩放的跨云解决方案

灵活可靠的多云服务提供数据安全性、备份和冗余、一致且快速的可用性、可缩放的存储和分发，以及异地兼容的路由。 此手动触发过程可确保在托管的 Web 应用之间提供高效可靠的负载切换，使关键数据即时可用。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 云模式，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
