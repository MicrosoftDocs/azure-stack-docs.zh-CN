---
title: 通过 Azure 和 Azure Stack 创建跨云缩放应用解决方案 |Microsoft Docs
description: 了解如何在 Azure 中创建跨云缩放解决方案。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 64554d0bd2c9e9d8622b1fd48bb7d086cd0b4b76
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603123"
---
# <a name="create-cross-cloud-scaling-app-solutions-with-azure-and-azure-stack"></a>通过 Azure 和 Azure Stack 创建跨云缩放应用解决方案

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何创建跨云解决方案, 以提供手动触发的进程, 以便通过流量管理器通过自动缩放, 从 Azure Stack 的托管 web 应用切换到 Azure 托管 web 应用。 此过程可确保在负载下灵活且可缩放的云实用程序。

在此模式下, 你的租户可能尚未准备好在公有云中运行你的应用。 但是，要让企业在本地环境中保持用于处理应用需求高峰的容量，在经济上似乎不切实际。 你的租户可以使用公有云的弹性和其本地解决方案。

在此解决方案中, 你将构建一个示例环境, 用于:

> [!div class="checklist"]
> - 创建多节点 web 应用。
> - 配置和管理持续部署 (CD) 过程。
> - 将 Web 应用发布到 Azure Stack。
> - 创建发布。
> - 了解如何监视和跟踪部署。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新带入本地环境, 从而实现了唯一的混合云, 使你能够在任何位置构建和部署混合应用。  
> 
> [混合应用程序的设计注意事项](azure-stack-edge-pattern-overview.md)查看软件质量的支柱 (放置、可伸缩性、可用性、复原能力、可管理性和安全性), 以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计, 并最大程度减少生产环境中的挑战。

## <a name="prerequisites"></a>系统必备

-   Azure 订阅。 如果需要, 请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Azure Stack 集成系统或 Azure Stack 开发工具包部署。
    - 有关安装 Azure Stack 的说明, 请参阅[安装 Azure Stack 开发工具包](../asdk/asdk-install.md)。
    - 对于 ASDK 后期部署自动化脚本, 请参阅:[https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) 
    - 此安装可能需要几个小时才能完成。

-   将[应用服务](../operator/azure-stack-app-service-deploy.md) PaaS 服务部署到 Azure Stack。

-   在 Azure Stack 环境中[创建计划/产品/服务](../operator/azure-stack-plan-offer-quota-overview.md)。

-   在 Azure Stack 环境中[创建租户订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。

-   在租户订阅中创建 web 应用。 记下新的 web 应用 URL 以供以后使用。

-   在租户订阅中部署 Azure Pipelines 虚拟机 (VM)。

-   需要 .NET 3.5 的 Windows Server 2016 VM。 将在 Azure Stack 上的租户订阅中构建此 VM 作为专用生成代理。

-   Azure Stack 市场中提供了[包含 SQL 2017 VM 映像的 Windows Server 2016](../operator/azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal)。 如果此映像不可用, 请使用 Azure Stack 运算符, 以确保将其添加到环境中。

## <a name="issues-and-considerations"></a>问题和注意事项

### <a name="scalability"></a>可伸缩性

跨云缩放的关键组件是能够在公共云基础结构和本地云基础结构之间提供即时和按需缩放, 从而提供一致可靠的服务。

### <a name="availability"></a>可用性

确定通过本地硬件配置和软件部署来配置本地部署的应用，以实现高可用性。

### <a name="manageability"></a>可管理性

跨云解决方案确保在环境之间提供无缝的管理和熟悉的界面。 建议使用 PowerShell 进行跨平台管理。

## <a name="cross-cloud-scaling"></a>跨云缩放

### <a name="obtain-a-custom-domain-and-configure-dns"></a>获取自定义域并配置 DNS

更新域的 DNS 区域文件。 然后，Azure AD 将会验证自定义域名的所有权。 将 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) 用于 Azure 中的 Azure/Office 365/外部 DNS 记录，或在[其他 DNS 注册机构](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)中添加 DNS 条目。

1.  向公共注册机构注册自定义域。

2.  登录到域的域名注册机构。 可能需要由获批准的管理员进行 DNS 更新。

3.  通过添加 Azure AD 提供的 DNS 条目来更新域的 DNS 区域文件。 (DNS 条目不会影响邮件路由或 web 托管行为。)

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>在 Azure Stack 中创建默认的多节点 Web 应用

设置混合持续集成和持续部署 (CI/CD), 将 web 应用部署到 Azure, 并 Azure Stack 并自动将更改推送到这两个云。

> [!Note]  
> 需要在 Azure Stack 上创建适当的合成映像用于运行 Windows Server 和 SQL，并需要部署应用服务。 有关详细信息, 请在[Azure Stack 上的应用服务入门之前](../operator/azure-stack-app-service-before-you-get-started.md)查看应用服务文档。

### <a name="add-code-to-azure-repos"></a>向 Azure Repos 中添加代码

Azure Repos

1. 使用在 Azure Repos 上拥有项目创建权限的帐户登录到 Azure Repos。

    混合 CI/CD 可适用于应用代码和基础结构代码。 使用 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)进行专用与托管的云开发。

    ![连接到 Azure Repos 上的项目](media/azure-stack-solution-cloud-burst/image1.JPG)

2. 创建并打开默认 Web 应用以**克隆存储库**。

    ![在 Azure web 应用中克隆存储库](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>为这两个云中的应用服务创建独立的 Web 应用部署

1.  编辑 **WebApplication.csproj** 文件。 选择`Runtimeidentifier`并添加`win10-x64`。 （请参阅[独立部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文档。） 

    ![编辑 web 应用项目文件](media/azure-stack-solution-cloud-burst/image3.png)

2.  使用团队资源管理器将代码签入 Azure Repos。

3.  确认应用程序代码已签入 Azure Repos。

## <a name="create-the-build-definition"></a>创建生成定义

1. 登录到 Azure Pipelines 以确认是否能够创建生成定义。

2. 添加 **-r win10-x64** 代码。 此添加项是使用 .NET Core 触发独立部署所必需的。

    ![将代码添加到 web 应用](media/azure-stack-solution-cloud-burst/image4.png)

3. 运行生成。 [自包含的部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)过程将发布在 Azure 上运行的项目, 并 Azure Stack。

## <a name="use-an-azure-hosted-agent"></a>使用 Azure 托管代理

在 Azure Pipelines 中使用托管生成代理是一种用于生成和部署 web 应用的便利选项。 维护和升级是通过 Microsoft Azure 自动完成的, 可实现持续且无中断的开发周期。

### <a name="manage-and-configure-the-cd-process"></a>管理和配置 CD 过程

Azure Pipelines 和 Azure DevOps Server 提供高度可配置、可管理的管道，用于将内容发布到多个环境（例如开发、过渡、QA 和生产环境）；在特定的阶段要求审批。

## <a name="create-release-definition"></a>创建发布定义

1.  选择**加号**按钮, 在 VSO 的 "**生成和发布**" 部分中的 "**发布**" 选项卡下添加新发布。

    ![创建发布定义](media/azure-stack-solution-cloud-burst/image5.png)

2. 应用“Azure 应用服务部署”模板。

   ![应用 Azure App Service 部署模板](meDia/azure-stack-solution-cloud-burst/image6.png)

3. 在 "**添加项目**" 下, 添加 Azure Cloud build 应用的项目。

   ![向 Azure Cloud build 添加项目](media/azure-stack-solution-cloud-burst/image7.png)

4. 在 "管道" 选项卡下, 选择环境的 "**阶段"、"任务**" 链接, 并设置 Azure 云环境值。

   ![设置 Azure 云环境值](media/azure-stack-solution-cloud-burst/image8.png)

5. 设置**环境名称**, 然后选择 azure 云终结点的**azure 订阅**。

      ![选择 azure 云终结点的 Azure 订阅](media/azure-stack-solution-cloud-burst/image9.png)

6. 在 "**应用服务名称**" 下, 设置所需的 Azure 应用服务名称。

      ![设置 Azure 应用服务名称](media/azure-stack-solution-cloud-burst/image10.png)

7. 在 Azure 云托管环境的 "**代理队列**" 下输入 "Hosted VS2017"。

      ![为 Azure 云托管环境设置代理队列](media/azure-stack-solution-cloud-burst/image11.png)

8. 在“部署 Azure 应用服务”菜单中，为环境选择有效的**包或文件夹**。 选择**文件夹位置**旁边的“确定”。
  
      ![选择 Azure App Service 环境的包或文件夹](media/azure-stack-solution-cloud-burst/image12.png)

      ![选择 Azure App Service 环境的包或文件夹](media/azure-stack-solution-cloud-burst/image13.png)

9. 保存所有更改并返回**发布管道**。

    ![在发布管道中保存更改](media/azure-stack-solution-cloud-burst/image14.png)

10. 选择 Azure Stack 应用的生成以添加新项目。
    
    ![为 Azure Stack 应用添加新项目](media/azure-stack-solution-cloud-burst/image15.png)


11. 通过应用 Azure App Service 部署, 添加一个更多环境。
    
    ![将环境添加到 Azure App Service 部署](media/azure-stack-solution-cloud-burst/image16.png)

12. 将新环境命名为 Azure Stack。
    
    ![Azure App Service 部署中的名称环境](media/azure-stack-solution-cloud-burst/image17.png)

13. 在“任务”选项卡下找到 Azure Stack 环境。
    
    ![Azure Stack 环境](media/azure-stack-solution-cloud-burst/image18.png)

14. 选择 Azure Stack 终结点的订阅。
    
    ![选择 Azure Stack 终结点的订阅](media/azure-stack-solution-cloud-burst/image19.png)

15. 将 Azure Stack Web 应用名称设置为应用服务名称。

    ![设置 Azure Stack web 应用名称](media/azure-stack-solution-cloud-burst/image20.png)

16. 选择“Azure Stack 代理”。
    
    ![选择 Azure Stack 代理](media/azure-stack-solution-cloud-burst/image21.png)

17. 在 "部署 Azure App Service" 部分下, 选择适用于环境的有效**包或文件夹**。 选择文件夹位置旁边的“确定”。

    ![为 Azure App Service 部署选择文件夹](media/azure-stack-solution-cloud-burst/image22.png)

    ![为 Azure App Service 部署选择文件夹](media/azure-stack-solution-cloud-burst/image23.png)

18. 在“变量”选项卡下添加名为 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` 的变量，将其值设置为 **true**，将范围设置为 Azure Stack。
    
    ![将变量添加到 Azure 应用部署](media/azure-stack-solution-cloud-burst/image24.png)

19. 选择两个项目中的“持续”部署触发器图标，并启用“持续”部署触发器。
    
    ![选择连续部署触发器](media/azure-stack-solution-cloud-burst/image25.png)

20. 选择 Azure Stack 环境中的“部署前”条件图标，并将触发器设置为“发布后”。
    
    ![选择预先部署条件](media/azure-stack-solution-cloud-burst/image26.png)

21. 保存所有更改。

> [!Note]  
> 任务的某些设置可能已在从模板创建发布定义时自动定义为[环境变量](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables)。 无法在任务设置中修改这些设置;相反, 必须选择父环境项来编辑这些设置。

## <a name="publish-to-azure-stack-via-visual-studio"></a>通过 Visual Studio 发布到 Azure Stack

通过创建终结点，Visual Studio Online (VSTO) 生成可以将 Azure 服务应用部署到 Azure Stack。 Azure Pipelines 会连接到生成代理，而后者会连接到 Azure Stack。

1.  登录到 VSTO，然后导航到应用设置页。

2.  在“设置”中，选择“安全性”。

3.  在“VSTS 组”中，选择“终结点创建者”。

4.  在“成员”选项卡上，选择“添加”。

5.  在“添加用户和组”中输入用户名，然后从用户列表中选择该用户。

6.  选择“保存更改”。

7.  在“VSTS 组”列表中，选择“终结点管理员”。

8.  在“成员”选项卡上，选择“添加”。

9.  在“添加用户和组”中输入用户名，然后从用户列表中选择该用户。

10. 选择“保存更改”。

有了终结点信息以后，就可以使用 Azure Pipelines 到 Azure Stack 的连接了。 Azure Stack 中的生成代理会从 Azure Pipelines 获取指令，然后，此代理会传达与 Azure Stack 通信所需的终结点信息。

## <a name="develop-the-application-build"></a>开发应用程序生成

> [!Note]  
> 需要在 Azure Stack 上创建适当的合成映像用于运行 Windows Server 和 SQL，并需要部署应用服务。 有关详细信息, 请在[Azure Stack 上的应用服务入门之前](../operator/azure-stack-app-service-before-you-get-started.md)查看应用服务文档。

使用[Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/), 如 Azure Repos 的 web 应用代码部署到这两个云。

### <a name="add-code-to-an-azure-repos-project"></a>向 Azure Repos 项目添加代码

1.  使用在 Azure Stack 上拥有项目创建权限的帐户登录到 Azure Repos。 下一屏幕捕获显示如何连接到 HybridCICD 项目。

2.  创建并打开默认 Web 应用以**克隆存储库**。

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>为这两个云中的应用服务创建独立的 Web 应用部署

1.  编辑 **WebApplication.csproj** 文件：选择`Runtimeidentifier`并添加`win10-x64`。 有关详细信息，请参阅[独立部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文档。

2.  使用团队资源管理器将代码签入 Azure Repos。

3.  确认应用程序代码已签入 Azure Repos。

### <a name="create-the-build-definition"></a>创建生成定义

1.  使用可以创建生成定义的帐户登录到 Azure Pipelines。

2.  导航到项目的“生成 Web 应用程序”页。

3.  在“参数”中，添加 **-r win10-x64** 代码。 此添加项是使用 .NET Core 触发独立部署所必需的。

4.  运行生成。 [独立部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)过程将发布可在 Azure 和 Azure Stack 上运行的项目。

#### <a name="use-an-azure-hosted-build-agent"></a>使用 Azure 托管生成代理

在 Azure Pipelines 中使用托管生成代理是一种用于生成和部署 web 应用的便利选项。 维护和升级是通过 Microsoft Azure 自动完成的, 可实现持续且无中断的开发周期。

### <a name="configure-the-continuous-deployment-cd-process"></a>配置持续部署 (CD) 过程

Azure Pipelines 和 Azure DevOps Server 提供高度可配置、可管理的管道，用于将内容发布到多个环境（例如开发、过渡、质量保证 (QA) 和生产）。 此过程可能包括要求在应用程序生命周期的特定阶段进行审批。

#### <a name="create-release-definition"></a>创建发布定义

创建发布定义是应用程序生成过程中的最后一步。 此发布定义用于创建一个发布并部署一个生成。

1.  登录到 Azure Pipelines，然后导航到项目的“生成和发布”。

2.  在“发布”选项卡上选择“[ + ]”，然后选择“创建发布定义”。

3.  在“选择模板”上选择“Azure 应用服务部署”，然后选择“应用”。

4.  在 "**添加项目**" 的 "**源 (生成定义)** " 中, 选择 "Azure 云生成应用"。

5.  在“管道”选项卡上选择“1 阶段，1 任务”链接，以便**查看环境任务**。

6.  在 "**任务**" 选项卡上, 输入 Azure 作为**环境名称**, 并从 " **azure 订阅**" 列表中选择 "AzureCloud"。

7.  输入 **Azure 应用服务名称**，即下一屏幕截图中的 `northwindtraders`。

8.  从“代理队列”列表中选择“托管的 VS2017”作为“代理阶段”。

9.  在“部署 Azure 应用服务”中，为环境选择有效的**包或文件夹**。

10. 确认“选择文件或文件夹”中的“Location”后，选择“确定”。

11. 保存所有更改后，回到“管道”。

12. 在“管道”选项卡上选择“添加项目”，然后从“源(生成定义)”列表中选择“NorthwindCloud Traders-Vessel”。

13. 在“选择模板”中添加另一环境。 选取“Azure 应用服务部署”，然后选择“应用”。

14. 输入 `Azure Stack` 作为“环境名称”。

15. 在“任务”选项卡上，找到并选择“Azure Stack”。

16. 从“Azure 订阅”列表中选择“AzureStack Traders-Vessel EP”作为“Azure Stack 终结点”。

17. 输入 Azure Stack Web 应用名称作为**应用服务名称**。

18. 从“代理选择”下的“代理队列”列表中选取“AzureStack -b Douglas Fir”。

19. 至于“部署 Azure 应用服务”，请为环境选择有效的**包或文件夹**。 确认“选择文件或文件夹”中的“Location”文件夹后，选择“确定”。

20. 在“变量”选项卡上，找到名为 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` 的变量。 将变量值设置为 **true**，将其范围设置为 **Azure Stack**。

21. 在“管道”选项卡上，选择 NorthwindCloud Traders-Web 项目对应的“持续部署触发器”图标，然后将“持续部署触发器”设置为“启用”。 针对“NorthwindCloud Traders-Vessel”项目执行相同的操作。

22. 至于 Azure Stack 环境，请选择“部署前条件”图标，并将触发器设置为“发布后”。

23. 保存所有更改。

> [!Note]  
> 发布任务的某些设置将在从模板创建发布定义时自动定义为[环境变量](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables)。 无法在任务设置中修改这些设置，但可以在父环境项中修改。

## <a name="create-a-release"></a>创建发布

1.  在 "**管道**" 选项卡上, 打开 "**发布**" 列表并选择 "**创建发布**"。

2.  输入发布的说明, 检查是否选择了正确的项目, 然后选择 "**创建**"。 几分钟后, 会显示一个横幅, 指示已创建新版本, 并且发布名称将显示为链接。 选择该链接可以查看 "发布摘要" 页面。

3.  "发布摘要" 页将显示有关版本的详细信息。 在 "版本 2" 的以下屏幕截图中, "**环境**" 部分显示 Azure 的**部署状态**"正在进行", 并将 Azure Stack 的状态显示为 "成功"。 当 Azure 环境的部署状态变为“成功”以后，会显示一个横幅，指示可以审批发布了。 如果部署挂起或失败，则会显示一个蓝色的 **(i)** 信息图标。 将鼠标悬停在图标上方即可看到一个弹出窗口，其中包含延迟或失败的原因。

4.  其他视图（例如发布列表）也会显示一个图标，指示正在等待审批。 此图标的弹出窗口会显示环境名称以及与部署相关的更多详细信息。 管理员可以很容易地查看发布的总体进度以及哪些发布正在等待审批。

## <a name="monitor-and-track-deployments"></a>监视和跟踪部署

1.  在“Release-2”摘要页中选择“日志”。 部署期间，此页显示代理的实时日志。 左窗格显示每个环境的部署过程中每个操作的状态。

2.  为预先部署或后期部署审批选择 "**操作**" 列中的 "人员" 图标, 以查看批准 (或拒绝) 部署的人员以及他们提供的消息。

3.  部署完成后，整个日志文件会显示在右窗格中。 选择左窗格中的任何**步骤**以查看单个步骤（例如“初始化作业”）的日志文件。 有了查看单个日志的功能，就可以更轻松地跟踪和调试整体部署的部件。 **保存**某个步骤的日志文件，或者**以 Zip 格式下载所有日志**。

4.  打开“摘要”选项卡，查看有关该发布的常规信息。 此视图详细显示了该发布所部署到的生成和环境、部署状态，以及有关该发布的其他信息。

5.  选择环境链接（**Azure** 或 **Azure Stack**），查看部署到特定环境的现有部署和待定部署的相关信息。 使用这些视图快速验证同一个生成是否已部署到这两个环境。

6.  在浏览器中打开已**部署的生产应用**。 例如，对于 Azure 应用服务网站，请打开 URL `https://[your-app-name\].azurewebsites.net`。

**Azure 与 Azure Stack 的集成提供可缩放的跨云解决方案**

灵活可靠的多云服务提供数据安全性、备份和冗余、一致且快速的可用性、可缩放的存储和分发，以及异地兼容的路由。 此手动触发的过程可确保托管 web 应用之间的可靠且高效的负载切换以及重要数据的即时可用性。

## <a name="next-steps"></a>后续步骤
- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
