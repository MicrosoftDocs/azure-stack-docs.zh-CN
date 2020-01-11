---
title: 部署使用 Azure 和 Azure Stack 中心缩放跨云的应用
description: 了解如何使用 Azure 和 Azure Stack 中心部署可缩放跨云的应用。
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 3a8edb749ba995636f8e2fe626063ff4c495bb7b
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881750"
---
# <a name="deploy-an-app-that-scales-cross-cloud-using-azure-and-azure-stack-hub"></a>部署使用 Azure 和 Azure Stack 中心缩放跨云的应用

了解如何创建跨云解决方案，以提供手动触发的进程，以便通过流量管理器使用自动缩放从 Azure Stack 中心托管 web 应用切换到 Azure 托管 web 应用。 此过程可确保在负载下灵活且可缩放的云实用程序。

在此模式下，你的租户可能尚未准备好在公有云中运行你的应用。 不过，企业不能经济地维护本地环境中所需的容量来处理应用程序的需求峰值。 你的租户可以使用公有云的弹性和其本地解决方案。

在此解决方案中，你将构建一个示例环境，用于：

> [!div class="checklist"]
> - 创建多节点 web 应用。
> - 配置和管理持续部署（CD）进程。
> - 将 web 应用发布到 Azure Stack 中心。
> - 创建发布。
> - 了解如何监视和跟踪部署。

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack 中心为本地环境带来了云计算的灵活性和革新，使你能够在任何位置构建和部署混合应用，从而实现了唯一的混合云。  
> 
> [混合应用程序的设计注意事项](overview-app-design-considerations.md)查看软件质量的支柱（放置、可伸缩性、可用性、复原能力、可管理性和安全性），以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计，并最大程度减少生产环境中的挑战。

## <a name="prerequisites"></a>必备组件

-   Azure 订阅。 如果需要，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Azure Stack 中心集成的系统或部署 Azure Stack 集线器开发工具包。
    - 有关安装 Azure Stack 集线器的说明，请参阅[安装 Azure Stack 集线器开发工具包](../asdk/asdk-install.md)。
    - 对于 ASDK 后期部署自动化脚本，请参阅： [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) 
    - 此安装可能需要几个小时才能完成。

-   将[应用服务](../operator/azure-stack-app-service-deploy.md)PaaS 服务部署到 Azure Stack 中心。

-   在 Azure Stack 中心环境中[创建计划/产品/服务](../operator/service-plan-offer-subscription-overview.md)。

-   在 Azure Stack 中心环境中[创建租户订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。

-   在租户订阅中创建 web 应用。 记下新的 web 应用 URL 以供以后使用。

-   在租户订阅中部署 Azure Pipelines 虚拟机（VM）。

-   需要 .NET 3.5 的 Windows Server 2016 VM。 此 VM 将在 Azure Stack 中心作为专用生成代理的租户订阅中生成。

-   在 Azure Stack Hub Marketplace 中提供[了包含 SQL 2017 VM 映像的 Windows Server 2016](../operator/azure-stack-add-vm-image.md) 。 如果此映像不可用，请与 Azure Stack 中心操作员合作，以确保将其添加到环境中。

## <a name="issues-and-considerations"></a>问题和注意事项

### <a name="scalability"></a>可伸缩性

跨云缩放的关键组件是能够在公共云基础结构和本地云基础结构之间提供即时和按需缩放，从而提供一致可靠的服务。

### <a name="availability"></a>可用性

确保通过本地硬件配置和软件部署为高可用性配置本地部署的应用。

### <a name="manageability"></a>可管理性

跨云解决方案确保了环境之间的无缝管理和熟悉的界面。 对于跨平台管理，建议使用 PowerShell。

## <a name="cross-cloud-scaling"></a>跨云缩放

### <a name="obtain-a-custom-domain-and-configure-dns"></a>获取自定义域并配置 DNS

更新域的 DNS 区域文件。 Azure AD 将验证自定义域名的所有权。 使用 Azure 中 Azure/Office 365/外部 DNS 记录的[Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) ，或在[其他 dns 注册机构](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)中添加 dns 条目。

1.  向公共注册机构注册自定义域。

2.  登录到域的域名注册机构。 批准的管理员可能需要进行 DNS 更新。

3.  通过添加 Azure AD 提供的 DNS 条目来更新域的 DNS 区域文件。 （DNS 条目不会影响邮件路由或 web 托管行为。）

### <a name="create-a-default-multi-node-web-app-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建默认的多节点 web 应用

设置混合持续集成和持续部署（CI/CD），将 web 应用部署到 Azure 并 Azure Stack 集线器，并 autopush 对这两个云的更改。

> [!Note]  
> 需要联合用于运行的适当映像的 Azure Stack 集线器（Windows Server 和 SQL）和应用服务部署是必需的。 有关详细信息，请查看应用服务文档，[然后在 Azure Stack 中心开始应用服务](../operator/azure-stack-app-service-before-you-get-started.md)。

### <a name="add-code-to-azure-repos"></a>将代码添加到 Azure Repos

Azure Repos

1. 使用对 Azure Repos 具有项目创建权限的帐户登录到 Azure Repos。

    混合 CI/CD 可适用于应用代码和基础结构代码。 使用[Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)来实现私有和托管云开发。

    ![连接到 Azure Repos 上的项目](media/solution-deployment-guide-cross-cloud-scaling/image1.JPG)

2. 通过创建并打开默认 web 应用，**克隆存储库**。

    ![在 Azure web 应用中克隆存储库](media/solution-deployment-guide-cross-cloud-scaling/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>为两个云中的应用服务创建独立的 web 应用部署

1.  编辑**WebApplication**文件。 选择 `Runtimeidentifier` 并添加 `win10-x64`。 （请参阅[自包含的部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文档。） 

    ![编辑 web 应用项目文件](media/solution-deployment-guide-cross-cloud-scaling/image3.png)

2.  使用团队资源管理器签入要 Azure Repos 的代码。

3.  确认应用程序代码已签入 Azure Repos。

## <a name="create-the-build-definition"></a>创建生成定义

1. 登录到 Azure Pipelines 以确认是否能够创建生成定义。

2. Add **-r win10-x64**代码。 此添加项是使用 .NET Core 触发独立部署所必需的。

    ![将代码添加到 web 应用](media/solution-deployment-guide-cross-cloud-scaling/image4.png)

3. 运行生成。 [自包含的部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)过程将发布在 Azure 和 Azure Stack 集线器上运行的项目。

## <a name="use-an-azure-hosted-agent"></a>使用 Azure 托管代理

在 Azure Pipelines 中使用托管生成代理是一种用于生成和部署 web 应用的便利选项。 维护和升级是通过 Microsoft Azure 自动完成的，可实现持续且无中断的开发周期。

### <a name="manage-and-configure-the-cd-process"></a>管理和配置 CD 过程

Azure Pipelines 和 Azure DevOps Services 为发布到多个环境（例如开发、过渡、QA 和生产环境）提供高度可配置和可管理的管道;包括在特定阶段要求审批。

## <a name="create-release-definition"></a>创建发布定义

1.  选择**加号**按钮，在 VSO 的 "**生成和发布**" 部分中的 "**发布**" 选项卡下添加新发布。

    ![创建发布定义](media/solution-deployment-guide-cross-cloud-scaling/image5.png)

2. 应用 Azure App Service 部署模板。

   ![应用 Azure App Service 部署模板](meDia/solution-deployment-guide-cross-cloud-scaling/image6.png)

3. 在 "**添加项目**" 下，添加 Azure Cloud build 应用的项目。

   ![向 Azure Cloud build 添加项目](media/solution-deployment-guide-cross-cloud-scaling/image7.png)

4. 在 "管道" 选项卡下，选择环境的 "**阶段"、"任务**" 链接，并设置 Azure 云环境值。

   ![设置 Azure 云环境值](media/solution-deployment-guide-cross-cloud-scaling/image8.png)

5. 设置**环境名称**，然后选择 azure 云终结点的**azure 订阅**。

      ![选择 azure 云终结点的 Azure 订阅](media/solution-deployment-guide-cross-cloud-scaling/image9.png)

6. 在 "**应用服务名称**" 下，设置所需的 Azure 应用服务名称。

      ![设置 Azure 应用服务名称](media/solution-deployment-guide-cross-cloud-scaling/image10.png)

7. 在 Azure 云托管环境的 "**代理队列**" 下输入 "Hosted VS2017"。

      ![为 Azure 云托管环境设置代理队列](media/solution-deployment-guide-cross-cloud-scaling/image11.png)

8. 在 "部署 Azure App Service" 菜单中，选择适用于环境的有效**包或文件夹**。 选择 **"确定" "** **文件夹位置**"。
  
      ![选择 Azure App Service 环境的包或文件夹](media/solution-deployment-guide-cross-cloud-scaling/image12.png)

      ![选择 Azure App Service 环境的包或文件夹](media/solution-deployment-guide-cross-cloud-scaling/image13.png)

9. 保存所有更改并返回到**发布管道**。

    ![在发布管道中保存更改](media/solution-deployment-guide-cross-cloud-scaling/image14.png)

10. 添加新项目，为 Azure Stack 中心应用选择生成。
    
    ![为 Azure Stack 中心应用添加新项目](media/solution-deployment-guide-cross-cloud-scaling/image15.png)


11. 通过应用 Azure App Service 部署，添加一个更多环境。
    
    ![将环境添加到 Azure App Service 部署](media/solution-deployment-guide-cross-cloud-scaling/image16.png)

12. 将新环境命名 Azure Stack。
    
    ![Azure App Service 部署中的名称环境](media/solution-deployment-guide-cross-cloud-scaling/image17.png)

13. 在 "**任务**" 选项卡下找到 Azure Stack 环境。
    
    ![Azure Stack 环境](media/solution-deployment-guide-cross-cloud-scaling/image18.png)

14. 选择 Azure Stack 终结点的订阅。
    
    ![选择 Azure Stack 终结点的订阅](media/solution-deployment-guide-cross-cloud-scaling/image19.png)

15. 将 Azure Stack web 应用名称设置为应用服务名称。

    ![设置 Azure Stack web 应用名称](media/solution-deployment-guide-cross-cloud-scaling/image20.png)

16. 选择 Azure Stack 代理。
    
    ![选择 Azure Stack 代理](media/solution-deployment-guide-cross-cloud-scaling/image21.png)

17. 在 "部署 Azure App Service" 部分下，选择适用于环境的有效**包或文件夹**。 选择 **"确定" "** 文件夹位置"。

    ![为 Azure App Service 部署选择文件夹](media/solution-deployment-guide-cross-cloud-scaling/image22.png)

    ![为 Azure App Service 部署选择文件夹](media/solution-deployment-guide-cross-cloud-scaling/image23.png)

18. 在 "变量" 选项卡下，添加名为 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`的变量，将其值设置为**true**，并将范围设置为 Azure Stack。
    
    ![将变量添加到 Azure 应用部署](media/solution-deployment-guide-cross-cloud-scaling/image24.png)

19. 选择两个项目中的**连续**部署触发器图标，并启用 "**继续**部署" 触发器。
    
    ![选择连续部署触发器](media/solution-deployment-guide-cross-cloud-scaling/image25.png)

20. 在 Azure Stack 环境中选择 "**预先部署**条件" 图标，并将触发器设置为 "**发布后"。**
    
    ![选择预先部署条件](media/solution-deployment-guide-cross-cloud-scaling/image26.png)

21. 保存所有更改。

> [!Note]  
> 从模板创建发布定义时，任务的某些设置可能已自动定义为[环境变量](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables)。 无法在任务设置中修改这些设置;相反，必须选择父环境项来编辑这些设置。

## <a name="publish-to-azure-stack-hub-via-visual-studio"></a>通过 Visual Studio 发布到 Azure Stack 集线器

通过创建终结点，Visual Studio Online （VSTO）生成可将 Azure 服务应用部署到 Azure Stack 中心。 Azure Pipelines 连接到用于连接到 Azure Stack 中心的生成代理。

1.  登录到 VSTO 并导航到 "应用设置" 页。

2.  在“设置”中选择“安全性”。

3.  在**VSTS 组**中，选择 "**终结点创建者**"。

4.  在“成员”选项卡上，选择“添加”。

5.  在 "**添加用户和组**" 中，输入用户名并从用户列表中选择该用户。

6.  选择“保存更改”。

7.  在 " **VSTS 组**" 列表中，选择 "**终结点管理员**"。

8.  在“成员”选项卡上，选择“添加”。

9.  在 "**添加用户和组**" 中，输入用户名并从用户列表中选择该用户。

10. 选择“保存更改”。

现在，终结点信息已存在，Azure Stack 中心连接的 Azure Pipelines 可供使用。 Azure Stack 集线器中的生成代理从 Azure Pipelines 中获取说明，然后代理会传达用于与 Azure Stack 中心通信的终结点信息。

## <a name="develop-the-application-build"></a>开发应用程序生成

> [!Note]  
> 需要联合用于运行的适当映像的 Azure Stack 集线器（Windows Server 和 SQL）和应用服务部署是必需的。 有关详细信息，请查看应用服务文档，[然后在 Azure Stack 中心开始应用服务](../operator/azure-stack-app-service-before-you-get-started.md)。

使用[Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)，如 Azure Repos 的 web 应用代码部署到这两个云。

### <a name="add-code-to-an-azure-repos-project"></a>向 Azure Repos 项目添加代码

1.  使用对 Azure Stack 中心具有项目创建权限的帐户登录到 Azure Repos。 下一个屏幕捕获显示了如何连接到 HybridCICD 项目。

2.  通过创建并打开默认 web 应用，**克隆存储库**。

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>为两个云中的应用服务创建独立的 web 应用部署

1.  编辑**WebApplication**文件：选择 `Runtimeidentifier` 然后添加 `win10-x64`。 有关详细信息，请参阅[自包含的部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文档。

2.  使用团队资源管理器将代码签入 Azure Repos。

3.  确认应用程序代码已签入 Azure Repos。

### <a name="create-the-build-definition"></a>创建生成定义

1.  使用可创建生成定义的帐户登录 Azure Pipelines。

2.  导航到项目的 "**生成 Web 应用程序**" 页。

3.  在 "**参数**" 中，add **-r win10-x64** code。 此添加项是使用 .NET Core 触发独立部署所必需的。

4.  运行生成。 [自包含的部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)过程将发布可在 Azure 和 Azure Stack 中心运行的项目。

#### <a name="use-an-azure-hosted-build-agent"></a>使用 Azure 托管生成代理

在 Azure Pipelines 中使用托管生成代理是一种用于生成和部署 web 应用的便利选项。 维护和升级是通过 Microsoft Azure 自动完成的，可实现持续且无中断的开发周期。

### <a name="configure-the-continuous-deployment-cd-process"></a>配置持续部署（CD）过程

Azure Pipelines 和 Azure DevOps Services 为发布到多个环境（例如开发、过渡、质量保证（QA）和生产）提供高度可配置和可管理的管道。 此过程可能包括在应用程序生命周期的特定阶段需要审批。

#### <a name="create-release-definition"></a>创建发布定义

在应用程序生成过程中，创建发布定义是最后一步。 此发布定义用于创建发布和部署生成。

1.  登录到 Azure Pipelines，导航到项目的**生成和发布**。

2.  在 "**发布**" 选项卡上，选择 " **[+]** "，然后选择 "**创建发布定义**"。

3.  在 "**选择模板**" 中，选择 " **Azure App Service 部署**"，然后选择 "**应用**"。

4.  在 "**添加项目**" 的 "**源（生成定义）** " 中，选择 "Azure 云生成应用"。

5.  在 "**管道**" 选项卡上，选择 " **1 阶段**， **1 个任务**" 链接来**查看环境任务**。

6.  在 "**任务**" 选项卡上，输入 Azure 作为**环境名称**，并从 " **azure 订阅**" 列表中选择 "AzureCloud"。

7.  输入**Azure 应用服务名称**，此名称在下一个屏幕捕获中 `northwindtraders`。

8.  对于 "代理" 阶段，从 "**代理队列**" 列表中选择 "**托管 VS2017** "。

9.  在 "**部署 Azure App Service**中，选择适用于环境的有效**包或文件夹**。

10. 在 "**选择文件或文件夹**" 中，选择 **"确定** **位置**"。

11. 保存所有更改并返回到**管道**。

12. 在 "**管道**" 选项卡上，选择 "**添加项目**"，并从 "**源（生成定义）** " 列表中选择**NorthwindCloud 商贸容器**。

13. 在 "**选择模板**" 中，添加另一个环境。 选择**Azure App Service 部署**，然后选择 "**应用**"。

14. 输入 `Azure Stack Hub` 作为**环境名称**。

15. 在 "**任务**" 选项卡上，找到并选择 "Azure Stack 集线器"。

16. 从 " **Azure 订阅**" 列表中，选择 "Test-azurestack" 中心的 "**商贸**Azure Stack" 容器。

17. 输入 Azure Stack 中心 web 应用名称作为**应用服务名称**。

18. 在 "**代理选择**" 下，从 "**代理队列**" 列表中选择**test-azurestack-b Douglas 杉树**。

19. 对于 "**部署 Azure App Service**"，为环境选择有效的**包或文件夹**。 在 "**选择文件或文件夹**" 中，选择 **"确定**" 作为文件夹**位置**。

20. 在 "**变量**" 选项卡上，找到名为 "`VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`" 的变量。 将变量值设置为**true**，并将其范围设置为**Azure Stack 集线器**。

21. 在 "**管道**" 选项卡上，选择 "NorthwindCloud 的**持续部署触发器**" 图标，并将 "**持续部署触发器**" 设置为 "**已启用**"。 对于**NorthwindCloud 商贸容器**项目，请执行相同的操作。

22. 对于 Azure Stack 中心环境，请选择 "**预先部署条件**" 图标将触发器设置为 "**发布后**"。

23. 保存所有更改。

> [!Note]  
> 在从模板创建发布定义时，会自动将某些发布任务设置定义为[环境变量](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables)。 无法在任务设置中修改这些设置，但可以在父环境项中修改这些设置。

## <a name="create-a-release"></a>创建发布

1.  在 "**管道**" 选项卡上，打开 "**发布**" 列表并选择 "**创建发布**"。

2.  输入发布的说明，检查是否选择了正确的项目，然后选择 "**创建**"。 几分钟后，会显示一个横幅，指示已创建新版本，并且发布名称将显示为链接。 选择该链接可以查看 "发布摘要" 页面。

3.  "发布摘要" 页将显示有关版本的详细信息。 在以下 "Release-2" 屏幕捕获中，"**环境**" 部分显示 Azure 的**部署状态**"正在进行"，Azure Stack 中心的状态为 "成功"。 当 Azure 环境的部署状态更改为 "成功" 时，将显示一个横幅，指示发布已准备好进行审批。 当某个部署挂起或失败时，将显示一个蓝色 **（i）** 信息图标。 将鼠标悬停在该图标上，可以看到一个弹出窗口，其中包含延迟或失败的原因。

4.  其他视图（如版本列表）还会显示表明审批处于挂起状态的图标。 此图标的弹出窗口会显示环境名称以及与部署相关的更多详细信息。 管理员可以轻松地查看发布的总体进度，并查看哪些发布正在等待批准。

## <a name="monitor-and-track-deployments"></a>监视和跟踪部署

1.  在**第2版**的 "摘要" 页上，选择 "**日志**"。 在部署过程中，此页显示代理的实时日志。 左窗格显示部署中每个环境的每个操作的状态。

2.  为预先部署或后期部署审批选择 "**操作**" 列中的 "人员" 图标，以查看批准（或拒绝）部署的人员以及他们提供的消息。

3.  部署完成后，将在右窗格中显示整个日志文件。 选择左窗格中的任意**步骤**，查看单个步骤的日志文件，例如 "**初始化作业**"。 查看单个日志的功能可以更轻松地跟踪和调试整个部署的各个部分。 **保存**步骤的日志文件，或以**zip 格式下载所有日志**。

4.  打开 "**摘要**" 选项卡以查看有关该版本的一般信息。 此视图显示有关生成的详细信息、部署到的环境、部署状态和有关版本的其他信息。

5.  选择环境链接（**Azure**或**Azure Stack 中心**），以查看有关现有和待定部署到特定环境的信息。 使用这些视图可以快速验证是否已将同一版本部署到这两个环境。

6.  在浏览器中打开已**部署的生产应用**。 例如，对于 Azure 应用 Services 网站，请打开 URL `https://[your-app-name\].azurewebsites.net`。

**Azure 与 Azure Stack 中心的集成提供了一个可缩放的跨云解决方案**

灵活、可靠的多云服务提供数据安全性、备份和冗余、一致、快速的可用性、可缩放的存储和分发以及与地域兼容的路由。 此手动触发的过程可确保托管 web 应用之间的可靠且高效的负载切换以及重要数据的即时可用性。

## <a name="next-steps"></a>后续步骤
- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
