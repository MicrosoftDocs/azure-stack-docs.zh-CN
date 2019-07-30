---
title: 将应用部署到 Azure 和 Azure Stack
description: 了解如何使用混合 CI/CD 管道将应用部署到 Azure 和 Azure Stack。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/23/2019
ms.topic: conceptual
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: 5357fcf548971e0962bec41ad9238bf88290531c
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603108"
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>将应用部署到 Azure 和 Azure Stack

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

此解决方案演示如何使用 Azure Pipelines 混合持续集成和持续交付 (CI/CD) 将应用部署到 Azure 并 Azure Stack。

根据[先决条件](#prerequisites)设置 Azure Stack、Azure DevOps 和 web 应用后, 可以:

> [!div class="checklist"]
> - 注册 web 应用, 并在 Azure Active Directory 中设置 Azure Pipelines 访问权限 (Azure AD)。 
> - 创建 Azure AD 和 Active Directory 联合身份验证服务 (AD FS) 的 Azure Pipelines 终结点。 
> - 在 Azure Stack 生成服务器上安装 Azure Pipelines 生成代理。 
> - 配置自包含应用程序部署到 Azure 并 Azure Stack。
> - 创建一个生成管道, 用于根据项目的代码提交运行自动生成。
> - 创建一个发布管道, 用于自动将生成部署到 Azure AD 和 Azure Stack。 
> - 手动创建和部署发布, 并查看发布摘要和日志。 

若要了解有关 CI/CD 的详细信息, 请参阅以下文章:

* [什么是持续集成？](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [什么是持续交付？](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="azure-stack-and-hybrid-cicd"></a>Azure Stack 和混合 CI/CD

Microsoft Azure Stack 是 Azure 的扩展, 可将云计算的灵活性和革新带入本地环境。 这是唯一的混合云, 使你能够在本地和公有云环境中构建和部署混合应用。 可在 Azure Stack 上开发应用，然后将其部署到 Azure Stack、Azure 或 Azure 混合云。 

应用部署的持续性、安全性和可靠性是组织和开发团队的关键元素。 利用 Azure Pipelines 混合 CI/CD 交付模式, 你可以在本地环境和公有云之间合并生成管道, 并更改部署位置, 而无需更改应用。 使用混合方法的其他好处包括:

- 可以在本地 Azure Stack 环境和 Azure 公有云中使用一组一致的开发工具。  使用通用的工具集可以更轻松地实施 CI/CD 模式和做法。
- 在 Azure 或 Azure Stack 中部署的应用和服务可互换, 相同的代码可以在任一位置运行。 可以利用本地和公有云的特性与功能。

> [!TIP]
> ![hybrid-pillars.png](./media/azure-stack-solution-pipeline/hybrid-pillars.png)  
> [适用于 Azure Stack 的混合云设计模式](azure-stack-edge-pattern-overview.md)一文介绍了用于设计、部署和操作混合应用程序的软件质量要素。 质量标准包括位置、可伸缩性、可用性、复原能力、可管理性和安全性。 这些设计注意事项有助于优化混合应用设计, 并最大程度减少生产环境中的挑战。

## <a name="prerequisites"></a>先决条件

- Azure 和 Azure Stack 的基本知识。 若要详细了解如何部署此解决方案, 请阅读以下文章:
  
  - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
  - [Azure Stack 概述](../operator/azure-stack-overview.md)
  
- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
  
- 在 Azure 中创建的 web 应用。 使用[Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)创建 web 应用, 可将本地和公有云部署到该应用。 记下应用程序 URI 以供稍后使用。 
  
- [安装](/visualstudio/install/install-visual-studio)Visual Studio 2019。
  
- 对可创建管道的[Azure DevOps](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)组织和 DevOps[项目](/azure/devops/organizations/projects/create-project)或[工作区](/azure/devops/repos/tfvc/create-work-workspaces)的管理员访问权限。 
  
- 适用于专用生成代理虚拟机 (VM) 的 .NET 3.5 的 Windows Server 2016 映像 Azure Pipelines 在你的 Azure Stack 上构建。
  
- 根据以下说明部署并配置 Azure Stack 集成系统或 Azure Stack 开发工具包 (ASDK)。 
  
  
  Azure Stack 开发工具包 (ASDK) 是可免费下载和使用 Azure Stack 的单节点部署。 ASDK 可让你评估 Azure Stack, 并在非生产环境中使用 Azure Api 和工具。
  
  具有 Azure AD 或 Active Directory 联合身份验证服务 (AD FS) 管理员凭据的任何用户都可以部署 ASDK。 Azure OEM/硬件合作伙伴可以部署生产型 Azure Stack。 您必须是 Azure Stack 运算符才能执行以下 Azure Stack 配置任务: 
  
  - 部署 Azure App Service
  - 创建计划和套餐
  - 创建租户订阅
  - 应用 Windows Server 2016 映像
  
  > [!Note]
  > ASDK 安装大约要花费7小时, 因此请相应地进行规划。
    
  部署和配置 ASDK:
  
  1. 按照[使用安装程序部署 ASDK](../asdk/asdk-install.md)中的详细部署说明进行操作。
     
  1. 使用 [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) PowerShell 脚本自动完成 ASDK 部署后步骤。
     
  1. 将[Azure App Service](../operator/azure-stack-app-service-deploy.md) PaaS 服务部署到 Azure Stack。
     
  1. 在 Azure Stack 中创建[计划和产品/服务](../operator/azure-stack-plan-offer-quota-overview.md)。
     
  1. 在 Azure Stack 中创建产品/服务的[租户订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。 
     
  1. 在租户订阅中创建 web 应用。 记下新的 web 应用 URL 以供以后使用。
     
  1. 将租户订阅中包含 .NET 3.5 的 Windows Server 2016 VM 部署为运行 Azure Pipelines 的生成服务器。
  
  > [!Note]
  > Azure Stack 环境需要正确的映像才能运行 Windows Server 和 SQL Server。 它还必须部署应用服务。

## <a name="register-your-web-app-and-give-it-access-to-resources"></a>注册 web 应用并向其授予对资源的访问权限 

在 Azure Active Directory (Azure AD) 中, Azure Pipelines 使用*服务主体*对 Azure 资源管理器进行身份验证。 若要为 Azure Pipelines 设置资源, 服务主体必须拥有 Azure 订阅中的 "**参与者**" 角色。 

可以使用 Azure 门户配置应用的身份验证。 

1. 注册应用程序以创建服务主体。
1. 使用*基于角色的访问控制 (RBAC)* 为 "**参与者**" 角色指定服务主体名称 (SPN)。
1. 复制并保存为 Azure Pipelines 创建终结点所需的应用程序 ID 和租户 ID 值。 
1. 创建并保存应用程序密钥值。

你还可以[使用 PowerShell 脚本](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5)创建服务主体和终结点。 [创建与现有服务主体的 Azure 资源管理器服务连接一](/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal)文介绍了此过程。

 > [!Note]  
 > 如果使用 PowerShell 脚本创建 Azure Stack Azure 资源管理器终结点, 则需要传递 **-azureStackManagementURL**参数和 **-environmentName**参数。 例如：  
 > `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="register-your-app-in-azure-ad"></a>在 Azure AD 中注册你的应用 

1. 在[Azure 门户](https://portal.azure.com)中, 选择 " **Azure Active Directory**", 然后在左侧导航栏中选择 "**应用注册**"。
   
1. 选择“新注册”。
   
1. 在 "**注册应用程序**" 页上
   1. 输入 web 应用的名称。
   1. 选择受支持的帐户类型。 
   1. 在 "**重定向 URI**" 下, 为要创建的应用程序类型选择 " **web** ", 并输入 web 应用的 URI。 
   1. 选择“注册”。
      
      ![注册应用程序](./media/azure-stack-solution-pipeline/create-app.png) 

### <a name="assign-the-app-to-a-role"></a>将应用分配给角色

必须将应用程序分配到角色, 才能访问订阅中的资源。 使用 Azure RBAC 可控制用户执行其作业所需的访问级别。 有关 RBAC 的详细信息, 请参阅[管理对 Azure 订阅资源的访问权限](/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](/azure/role-based-access-control/built-in-roles)。

Azure Pipelines 必须具有**参与者**角色才能在 Azure Stack 订阅中预配资源。 

可以在订阅、资源组或资源级别设置角色作用域。 较低级别的作用域将继承权限。 例如, 将某个应用程序添加到资源组的 "**读取**者" 角色意味着该应用程序可以读取该资源组及其任何资源。

若要将应用程序分配到 "**参与者**" 角色:

1. 在 Azure 门户中, 导航到所需的范围级别。 例如，若要在订阅范围内分配角色，请依次选择“所有服务”和“订阅”。
   
   ![在订阅范围内分配角色](./media/azure-stack-solution-pipeline/select-subscription.png)
   
1. 选择要将应用程序分配到的订阅。
   
1. 在左侧导航栏中, 选择 "**访问控制 (IAM)** "。
   
1. 选择 "**添加角色分配**"。
   
1. 在 "**添加角色分配**" 对话框中, 选择 "**参与者**" 角色。 默认情况下，可用选项中不显示 Azure AD 应用程序。 若要查找应用程序，请搜索其名称并选中它。
   
   ![选择角色和应用程序](./media/azure-stack-solution-pipeline/select-role.png)
   
1. 选择“保存”完成角色分配。 该应用程序将显示在分配到该作用域的某个角色的用户列表中。

服务主体已设置完毕。 下一部分演示如何获取 Azure Pipelines 需要以编程方式登录的值。

### <a name="get-values-for-signing-in"></a>获取用于登录的值

为 Azure Pipelines 创建终结点时, 需要输入租户 ID 和应用程序 ID。 获取这些值:

1. 在 Azure 门户中，选择“Azure Active Directory”。
   
1. 在左侧导航窗格中, 选择 "**应用注册**", 并选择应用程序。
   
1. 复制并保存要用于创建终结点的**目录 (租户) id**和**应用程序 (客户端) id** 。
   
   ![复制目录 (租户 ID) 和应用程序 (客户端) ID](./media/azure-stack-solution-pipeline/copy-app-id.png)

### <a name="create-a-new-application-secret"></a>创建新的应用程序机密

为 Azure Pipelines 创建终结点时, 应用需要进行身份验证。 它可以[使用证书](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)或应用程序机密。 如果使用 AD FS 作为标识提供者部署 Azure Stack, 则必须使用证书。

按照[证书和机密](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)中的步骤创建并上传新证书。 

或者, 若要创建新的应用程序密钥, 请执行以下操作:

1. 在 Azure 门户中，选择“Azure Active Directory”。
   
1. 在左侧导航窗格中, 选择 "**应用注册**", 并选择应用程序。
   
1. 在左侧导航栏中, 选择 "**证书" & "机密**"。
   
1. 在 "**客户端密码**" 下, 选择 "**新建客户端密码**"。
   
1. 在 "**添加客户端密码**" 中, 键入说明, 选择过期时间, 然后选择 "**添加**"。
   
1. 复制新密钥的**值**。 需要提供要作为应用登录的值。 现在请务必保存此值, 因为离开此页面后, 不会再次显示此值。
   
   ![复制机密值, 因为稍后不能检索它](./media/azure-stack-solution-pipeline/copy-secret.png)

## <a name="create-endpoints"></a>创建终结点

通过创建终结点, Azure Pipelines 生成可以将 Azure AD 应用部署到 Azure Stack。 Azure Pipelines 会连接到生成代理，而后者会连接到 Azure Stack。

设置终结点创建权限后, 可以为 Azure AD 或 AD FS 创建终结点。 

- 如果你使用 Azure AD 作为标识提供者部署了 Azure Stack, 则可以使用证书或应用程序机密来为 Azure 部署创建 Azure 资源管理器服务连接。 
  
- 如果使用 Active Directory 联合身份验证服务 (AD FS) 作为 Azure Stack 的标识提供程序, 则必须使用证书而不是客户端密码来创建服务连接以进行身份验证。 

### <a name="set-endpoint-creation-permissions"></a>设置终结点创建权限

1. 在 Azure DevOps 组织和项目中, 选择 "**项目设置**"。 
   
1. 选择 "**安全**", 然后在 " **Azure DevOps 组**" 下, 选择 "**终结点管理员**"。
   
1. 在“成员”选项卡上，选择“添加”。
   
1. 在 "**添加用户和组**" 中, 从列表中选择 "用户名", 然后选择 "**保存更改**"。
   
   ![添加成员](./media/azure-stack-solution-pipeline/endpoint-permissions.png)
   
1. 在 " **Azure DevOps 组**" 列表中, 选择 "**终结点创建者**", 然后重复上述步骤, 将用户添加到**终结点创建者**组。 

### <a name="create-an-endpoint-for-azure-ad-or-ad-fs-deployments"></a>为 Azure AD 或 AD FS 部署创建终结点

按照[使用现有服务主体创建 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal)中的说明创建服务连接终结点。  

使用以下值填写表单: 

- **连接名称**:输入引用此服务连接时要使用的用户友好名称。
  
- **环境**:选择环境名称, 如**AzureCloud**或**test-azurestack**。 如果未在下拉列表中看到 Test-azurestack, 请参阅[连接到 Azure Stack](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#connect-to-azure-stack)。
  
- **环境 URL**:如果未选择**AzureCloud**, 请输入你的环境 URL, 如*https:\//management.local.azurestack.external*。
  
- **作用域级别**:选择所需的范围级别, 如 "**订阅**"。 
  
- **订阅 ID**：输入订阅 ID。
  
- **订阅名称**:输入 Azure Stack 中的用户名。
  
- **服务主体客户端 ID**:输入先前保存的**应用程序 (客户端) ID** 。 
  
- **服务主体密钥**或**证书**:选择其中一个选项。 
  
  > [!NOTE]
  > 若要创建 AD FS 终结点, 必须使用证书进行身份验证。 
  
  - 对于 "**服务主体密钥**", 输入之前保存的客户端密钥值。
  - 如果选择 "**证书**", 请输入*pem*证书文件的证书和私钥部分的内容。 
    
    > [!NOTE]
    > 若要将 *.pfx*转换为*pem*证书文件, 请运行`openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>`。
  
- **租户 ID**：输入先前保存的**目录 (租户) ID** 。
  
- **连接未验证**:选择 "**验证连接**" 以验证服务主体的连接设置。
  
  > [!NOTE]
  > 如果 Azure 资源管理器终结点未向 internet 公开, 则连接验证将失败。 这是预期的, 你可以通过使用简单的任务创建发布管道来验证你的连接。

## <a name="install-and-configure-the-build-agent"></a>安装和配置生成代理 

在 Azure Pipelines 中使用托管生成代理是生成并部署 Web 应用的便捷做法。 Azure 会自动执行代理维护和升级, 以实现持续且无中断的开发周期。

在 Azure DevOps 中, 创建一个用于 Azure Stack 的个人访问令牌 (PAT)。 然后, 使用 PAT 在 Azure Stack 生成服务器 VM 上部署和配置生成代理。 
   
### <a name="create-a-personal-access-token"></a>创建个人访问令牌

1. 登录到 Azure DevOps, 并选择右上角的 **"我的配置文件**"。 
   
1. 在配置文件页面上, 展开 Azure Stack 组织名称旁的下拉列表, 并选择 "**管理安全性**"。 
   
   ![管理安全性](media/azure-stack-solution-pipeline/managesecurity.png)
   
1. 在 "**个人访问令牌**" 页上, 选择 "**新令牌**"。
   
   ![个人访问令牌](media/azure-stack-solution-pipeline/pats.png)
   
1. 在 "新建**个人访问令牌**" 页上, 填写令牌信息, 然后选择 "**创建**"。 
   
   ![创建 PAT](media/azure-stack-solution-pipeline/createpat.png)
   
1. 复制并保存令牌。 离开网页后, 不会再次显示。
   
   ![PAT 警告](media/azure-stack-solution-pipeline/patwarning.png)
   
### <a name="install-and-configure-the-agent-on-the-build-server"></a>在生成服务器上安装并配置代理

1. 连接到在 Azure Stack 主机上部署的生成服务器 VM。
   
1. 下载生成代理映像。 
   
1. 在管理员命令提示符下, 使用 PAT 将代理部署为服务, 并运行它。
   
1. 导航到所提取生成代理的文件夹, 并运行*配置 .cmd*文件。 *App.config*用其他文件更新生成代理文件夹。
   
   ![安装和配置生成代理](media/azure-stack-solution-pipeline/buildagent.png)

现在, 你已创建终结点并在生成服务器上安装了 Azure Pipelines 生成代理, Azure Stack 连接的 Azure Pipelines 可供使用。 Azure Stack 中的生成代理会从 Azure Pipelines 获取指令，然后，此代理会传达与 Azure Stack 通信所需的终结点信息。

你可以将代理组织到*代理池中*, 而不是单独管理每个代理。 代理池定义该池中所有代理的共享边界。 代理池的作用域限定为 Azure DevOps 组织, 这意味着可以在项目之间共享代理池。 若要了解有关代理池的详细信息, 请参阅[创建代理池和队列](/azure/devops/pipelines/agents/pools-queues)。

## <a name="create-build-and-release-pipelines"></a>创建生成和发布管道 

Azure Pipelines 为发布到多个环境 (例如开发、过渡、质量保证 (QA) 和生产) 提供高度可配置和可管理的管道。 发布过程可能包括在应用程序生命周期的特定阶段需要审批。

在此部分的解决方案中, 你可以:

- 在 Visual Studio 中克隆、连接并将代码添加到 Azure DevOps 项目。
- 创建独立的 web 应用部署。
- 配置 CI/CD 生成和发布管道。

混合 CI/CD 可适用于应用代码和基础结构代码。 可以使用[azure 资源管理器混合模板](https://azure.microsoft.com/resources/templates/)将 azure web 应用代码部署到本地云和公有云。

### <a name="clone-your-project"></a>克隆项目

1. 在 Visual Studio**团队资源管理器**中, 选择 "**连接**" 图标, 并登录到 Azure DevOps 组织。 
   
1. 选择 "**管理连接** > " "**连接到项目**"。 
   
   ![从团队资源管理器连接到项目](media/azure-stack-solution-pipeline/connecttoprojectteamexp.png)

1. 在 "**连接到项目**" 对话框中, 选择 web 应用项目, 设置本地路径, 然后选择 "**克隆**" 以在本地克隆存储库。
   
   ![在连接到项目的中克隆存储库](media/azure-stack-solution-pipeline/cloneproject.png)

### <a name="create-a-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>为两个云中的应用服务创建独立的 web 应用部署

1. 在 Visual Studio**解决方案资源管理器**中, 打开*WebApplication*文件并添加`<RuntimeIdentifier>win10-x64</RuntimeIdentifier>`。 有关此步骤的详细信息, 请参阅[自包含部署](/dotnet/core/deploying/#self-contained-deployments-scd)。
   
   ![配置 Runtimeidentifiers](media/azure-stack-solution-pipeline/runtimeidentifier.png)
   
1. 保存您的工作, 并使用**团队资源管理器**将代码签入到您的项目中。

### <a name="create-a-build-pipeline-and-run-a-build"></a>创建生成管道并运行生成

1. 在 web 浏览器中, 打开 Azure DevOps 组织和项目。
   
1. 选择左侧导航**栏中的**"**管道** > ", 然后选择 "**新建管道**"。 
   
1. 在 "**选择模板**" 下, 选择 " **ASP.NET Core** " 模板, 然后选择 "**应用**"。 
   
1. 在 "配置" 页上的左窗格中, 选择 "**发布**"。
   
1. 在右窗格中的 "**参数**" 下`-r win10-x64` , 将添加到配置。 
   
   ![添加生成管道参数](media/azure-stack-solution-pipeline/buildargument.png)
   
1. 选择页面顶部的 "**保存 & 队列**"。
   
1. 在 "**运行管道**" 对话框中, 选择 "**保存并运行**"。 
   
[自包含的部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)可以发布可在 Azure 和 Azure Stack 上运行的项目。

### <a name="create-a-release-pipeline"></a>创建发布管道

创建发布管道是混合 CI/CD 配置过程中的最后一步。 使用发布管道创建发布并部署生成。

1. 在 Azure DevOps 项目中, 在左侧导航栏中选择 "**管道** > ", 然后选择 "**新建管道**"。 
   
1. 在 "**选择模板**" 页上, 选择 " **Azure App Service 部署**", 然后选择 "**应用**"。
   
   ![选择发布模板](media/azure-stack-solution-pipeline/releasetemplate.png)
   
1. 在 "**管道**" 选项卡上, 选择左窗格中的 "**添加项目**"。 在右侧窗格中, 从 "**源 (生成管道)** " 下拉菜单中选择刚创建的 web 应用生成, 并选择 "**添加**"。
   
   ![添加生成项目](media/azure-stack-solution-pipeline/addartifact.png)
   
1. 在 "**管道**" 选项卡上的 "**阶段**" 下, 选择**阶段 1**中的超链接来**查看阶段任务**。
   
   ![查看阶段任务](media/azure-stack-solution-pipeline/viewstagetasks.png)
   
1. 在 "**任务**" 选项卡上, 输入*Azure*作为**阶段名称**。 
   
1. 在 "**参数**" 下, 从 " **Azure 订阅**" 下拉列表中选择你的订阅, 并输入你的**应用服务名称**。
   
   ![选择订阅并输入应用服务名称](media/azure-stack-solution-pipeline/stage1.png)
   
1. 在左窗格中, 选择 "**在代理上运行"** 。 在右侧窗格中, 从 "**代理池**" 下拉列表中选择 "**托管 VS2017** " (如果尚未选择)。
   
   ![选择托管代理](media/azure-stack-solution-pipeline/agentjob.png)
   
1. 在左窗格中, 选择 "**部署 Azure App Service**", 然后在右侧窗格中, 浏览到 Azure web 应用生成的**包或文件夹**。
   
   ![选择包或文件夹](media/azure-stack-solution-pipeline/packageorfolder.png)
   
1. 在 "**选择文件或文件夹**" 对话框中, 选择 **"确定"** 。
   
1. 选择**新的发布管道**页右上角的 "**保存**"。
   
   ![保存更改](media/azure-stack-solution-pipeline/save-devops-icon.png)
   
1. 在 "**管道**" 选项卡上, 选择 "**添加项目**"。 选择项目, 然后从 "**源 (生成管道)** " 下拉菜单中选择 Azure Stack "生成"。 选择 **添加** 。 
   
1. 在 "**管道**" 选项卡上的 "**阶段**" 下, 选择 "**添加**"。
   
1. 在新阶段中, 选择超链接以**查看阶段任务**。 输入*Azure Stack*作为阶段名称。 
   
   ![查看新阶段](media/azure-stack-solution-pipeline/newstage.png)
   
1. 在 "**参数**" 下, 选择 Azure Stack 终结点, 然后输入 Azure Stack web 应用名称作为**应用服务名称**。
   
1. 在 "**在代理上运行**" 下, 从 "**代理池**" 下拉列表中选择 Azure Stack 生成服务器代理 "。
   
1. 对于 "**部署 Azure App Service**, 选择 Azure Stack 生成的**包或文件夹**, 然后在"**选择文件或文件夹**"对话框中选择 **" 确定 "** 。
   
1. 在 "**变量**" 选项卡上, 找到名为**VSTS_ARM_REST_IGNORE_SSL_ERRORS**的变量。 将变量值设置为 **true**，将其范围设置为 **Azure Stack**。
   
   ![配置变量](media/azure-stack-solution-pipeline/setvariable.png)
   
1. 在 "**管道**" 选项卡上, 为每个项目选择**连续部署触发器**图标, 并将其设置为 "**启用**"。  
   
   ![设置持续部署触发器](media/azure-stack-solution-pipeline/contindeploymentenabled.png)
   
1. 选择 "Azure Stack" 阶段上的 "**预先部署条件**" 图标, 并将 "触发器" 设置为 "**发布后**"。
   
   ![设置部署前条件触发器](media/azure-stack-solution-pipeline/predeployafterrelease.png)
   
1. 选择 "**新发布管道**" 页右上角的 "**保存**" 以保存发布管道。

> [!Note]
> 在从模板创建发布管道时, 某些发布任务设置可能已自动定义为[自定义变量](/azure/devops/pipelines/release/variables?view=vsts#custom-variables)。 这些设置不能在任务设置中修改, 但你可以在父阶段项中进行编辑。

## <a name="release-the-app"></a>发布应用

现在, 你已有了一个发布管道, 你可以使用它来创建发布和部署你的应用程序。 

由于在发布管道中设置了连续部署触发器, 因此修改源代码会启动新的生成并自动创建新版本。 不过, 您将手动创建并运行此新版本。

若要创建和部署发布:

1. 在新的发布管道页面上, 选择 "在右上角**创建发布**"。 
   
   ![创建发布 ](media/azure-stack-solution-pipeline/createreleaseicon.png)
   
1. 在 "**创建新发布**" 页上:
   1. 在 "**管道**" 下, 选择 " **Azure**阶段", 将其触发器从自动更改为手动。 
   1. 在 "**项目**" 下, 确保选择了正确的项目。
   1. 输入**发布说明**, 然后选择 "**创建**"。 
   
   标题表明已创建新版本。 你可以选择 "发布名称" 链接来查看发布摘要页, 其中显示了有关发布的详细信息, 例如部署状态。
   
1. 若要部署手动发布, 请选择 " **Azure** " 阶段, 选择 "**部署**", 然后在 "阶段" 对话框中选择 "**部署**"。 
   
1. 部署成功完成后, 在浏览器中打开已部署的应用。 例如，对于 Azure 应用服务网站，请打开 URL `https://<your-app-name>.azurewebsites.net`。

### <a name="monitor-and-track-releases"></a>监视和跟踪版本

可以在 "发布" 阶段选择超链接状态以查看有关部署的详细信息。 

![发布摘要页](media/azure-stack-solution-pipeline/releasesummary.png)

管理员可以轻松跟踪发布的总体进度, 并查看正在等待批准的版本。

![显示待审批的发布摘要页](media/azure-stack-solution-pipeline/releasepending.png)

你可以从所有部署中查看版本日志: 

1. 在 Azure DevOps 项目中, 选择左侧的 "**管道** > ", 然后选择一个版本。 
   
1. 在 "发布摘要" 页上, 将鼠标悬停在 "阶段" 或选择一个阶段, 然后选择 "**日志**"。 
   
   在版本日志中, 左窗格显示每个阶段的每个操作的状态。 在部署期间, 右窗格将显示来自代理的实时日志。 部署完成后, 将在右窗格中显示整个日志文件。 
   
1. 选择左窗格中的任意步骤, 查看该步骤的日志文件, 例如 "**预先部署审批**"。 
   
1. 若要查看审批, 请在右侧窗格中选择 "**查看批准**", 以查看批准或拒绝发布的人员以及其他详细信息。 
   
查看各个步骤的日志可以更轻松地跟踪和调试整个部署的各个部分。 你还可以将**所有日志下载**为 *.zip*文件。
   
![版本日志](media/azure-stack-solution-pipeline/releaselog.png)

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](/azure/architecture/patterns)。
