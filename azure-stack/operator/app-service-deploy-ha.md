---
title: 在高可用性配置中部署 Azure Stack Hub 应用服务
description: 了解如何使用高可用性配置在 Azure Stack Hub 中部署应用服务。
author: BryanLa
ms.topic: article
ms.date: 01/02/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/02/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: e6d8708b15d5402d16b17a722640c9bf0c2251c4
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848024"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>在高可用性配置中部署应用服务

本文介绍如何使用 Azure Stack Hub 市场项，在高可用性配置中部署 Azure Stack Hub 的应用服务。 除了可用的市场项以外，此解决方案还使用了 [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack Hub 快速入门模板。 此模板可以自动创建高可用性基础结构用于用于托管应用服务资源提供程序。 然后，将在此高可用性 VM 基础结构上安装应用服务。 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>部署高可用性应用服务基础结构 VM
[appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack Hub 快速入门模板简化了在高可用性配置中部署应用服务的过程。 应在默认提供程序订阅中部署该模板。 

使用该模板在 Azure Stack Hub 中创建自定义资源时，该模板将会创建：
- 虚拟网络和所需的子网。
- 文件服务器、SQL Server 和 Active Directory 域服务 (AD DS) 子网的网络安全组。
- VM 磁盘和群集云见证的存储帐户。
- 一个用于 SQL VM 的内部负载均衡器，其专用 IP 已绑定到 SQL AlwaysOn 侦听器。
- AD DS、文件服务器群集和 SQL 群集的三个可用性集。
- 双节点 SQL 群集。
- 双节点文件服务器群集。
- 两个域控制器。

### <a name="required-azure-stack-hub-marketplace-items"></a>所需的 Azure Stack Hub 市场项
使用此模板之前，请确保 Azure Stack Hub 实例中存在以下 [Azure Stack Hub 市场项](azure-stack-marketplace-azure-items.md)：

- Windows Server 2016 Datacenter Core 映像（用于 AD DS 和文件服务器 VM）
- Windows Server 2016 上的 SQL Server 2016 SP2 (Enterprise)
- 最新的 SQL IaaS 扩展 
- 最新的 PowerShell Desired State Configuration 扩展 

> [!TIP]
> 有关模板要求和默认值的更多详细信息，请查看 GitHub 上的[模板自述文件](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha)。 

### <a name="deploy-the-app-service-infrastructure"></a>部署应用服务基础结构
参考本部分所述的步骤，使用 **appservice-fileshare-sqlserver-ha** Azure Stack Hub 快速入门模板创建自定义部署。

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 选择“ **创建资源”\+“自定义”，然后选择“模板部署”。**   >   

   ![自定义模板部署](media/app-service-deploy-ha/1.png)


3. 在“自定义部署”边栏选项卡上，选择“编辑模板” **“快速入门模板”，然后在可用自定义模板下拉列表中选择“appservice-fileshare-sqlserver-ha”模板。**   >    单击“确定”，然后单击“保存”。  

   ![选择 appservice-fileshare-sqlserver-ha 快速入门模板](media/app-service-deploy-ha/2.png)

4. 在“自定义部署”边栏选项卡上选择“编辑参数”，然后向下滚动以查看默认模板值。   根据需要修改这些值以提供全部所需的参数信息，然后单击“确定”。 <br><br> 至少为 `ADMINPASSWORD`、`FILESHAREOWNERPASSWORD`、`FILESHAREUSERPASSWORD`、`SQLSERVERSERVICEACCOUNTPASSWORD` 和 `SQLLOGINPASSWORD` 参数提供复杂的密码。
    
   ![编辑自定义部署参数](media/app-service-deploy-ha/3.png)

5. 在“自定义部署”边栏选项卡上，确保选择“默认提供程序订阅”作为要使用的订阅，然后为自定义部署创建新的资源组或选择现有的资源组。  <br><br> 接下来，选择资源组的位置（对于 ASDK 安装，请选择“本地”），然后单击“创建”。   在模板部署开始之前，系统会验证自定义部署设置。

    ![创建自定义部署](media/app-service-deploy-ha/4.png)

6. 在管理员门户中选择“资源组”，然后选择针对自定义部署创建的资源组名称（在本示例中为 **app-service-ha**）。  查看部署状态，确保所有部署已成功完成。

   > [!NOTE]
   > 模板部署大约需要一小时才能完成。

   [![](media/app-service-deploy-ha/5-sm.png "Review template deployment status")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>记下模板输出
模板部署成功完成后，请记下模板部署输出。 在运行应用服务安装程序时需要此信息。

请务必记下以下每个输出值：
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

遵循以下步骤找到模板输出值：

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 在管理员门户中选择“资源组”，然后选择针对自定义部署创建的资源组名称（在本示例中为 **app-service-ha**）。  

3. 单击“部署”，然后选择“Microsoft.Template”。  

    ![Microsoft. 模板部署](media/app-service-deploy-ha/6.png)

4. 选择“Microsoft.Template”部署后，选择“输出”并记下模板参数输出。******** 部署应用服务时需要此信息。

    ![参数输出](media/app-service-deploy-ha/7.png)

## <a name="deploy-app-service-in-a-highly-available-configuration"></a>在高可用性配置中部署应用服务
按照本部分中的步骤，根据[appservice-sqlserver](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack Hub 快速入门模板，在高度可用的配置中部署 Azure Stack 集线器的应用服务。 

安装应用服务资源提供程序后，可以将其包括在套餐和计划中。 然后，用户可以通过订阅获取服务并开始创建应用。

> [!IMPORTANT]
> 在运行资源提供程序安装程序之前，请确保已阅读每个应用服务版本随附的发行说明，以了解新功能、修复程序，以及可能影响部署的任何已知问题。

### <a name="prerequisites"></a>先决条件
在运行应用服务安装程序之前，需要执行几个步骤，如在[Azure Stack 集线器上开始应用服务之前](azure-stack-app-service-before-you-get-started.md)所述：

> [!TIP]
> 不一定要执行[应用服务准备工作](azure-stack-app-service-before-you-get-started.md)一文所述的所有步骤，因为模板部署会为你配置基础结构 VM。

- [下载应用服务安装程序与帮助器脚本](azure-stack-app-service-before-you-get-started.md#installer-and-helper-scripts)。
- [从 Azure Stack 中心市场下载项目](azure-stack-app-service-before-you-get-started.md#download-items-from-the-azure-marketplace)。
- [为部署类型生成所需的证书](azure-stack-app-service-before-you-get-started.md)。
- 基于为 Azure Stack 集线器选择的标识提供者创建 ID 应用程序。 可为 [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app) 或 [Active Directory 联合身份验证服务](azure-stack-app-service-before-you-get-started.md#create-an-adfs-app)创建 ID 应用程序，并记下应用程序 ID。
<!-- Connected --->
::: zone pivot="state-connected"
- 确保已将 Windows Server 2016 Datacenter 映像添加到 Azure Stack 中心市场。 应用服务安装必须使用该映像。
::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->
- 已**激活 Microsoft.Net 3.5.1 SP1 的 Windows Server 2016 Datacenter FULL VM 映像**。  Azure Stack 中心的 Azure App Service 要求在用于部署的映像上激活 Microsoft .NET 3.5.1 SP1。 Marketplace 联合的 Windows Server 2016 映像未启用此功能，并且在断开连接的环境中，无法通过 DISM 访问 Microsoft 更新来下载要安装的程序包。 因此，你必须创建并使用 Windows Server 2016 映像，并将此功能预启用为断开连接的部署。

   有关创建自定义映像并将其添加到 Marketplace 的详细信息，请参阅[将自定义 VM 映像添加到 Azure Stack 集线器](azure-stack-add-vm-image.md)。 将映像添加到 Marketplace 时，请确保指定以下属性：

   - 发布服务器 = MicrosoftWindowsServer
   - 提议 = WindowsServer
   - SKU = 2016-Datacenter
   - 版本 = 指定 "最新" 版本

::: zone-end

<!-- For All --> 

### <a name="steps-for-app-service-deployment"></a>应用服务部署步骤
安装应用服务资源提供程序至少需要一小时。 所需时长取决于部署的角色实例数。 部署期间，安装程序运行以下任务：

- 在默认提供程序订阅中注册所需的资源提供程序
- 授予参与者对应用服务标识应用程序的访问权限
- 创建资源组和虚拟网络（如有必要）
- 为应用服务安装项目、使用情况服务和资源混合创建存储帐户和容器
- 下载应用服务项目并将其上传到应用服务存储帐户
- 部署应用服务
- 注册使用情况服务
- 为应用服务创建 DNS 条目
- 注册应用服务管理员和租户资源提供程序
- 注册库项-Web、API、Function App、应用服务计划、WordPress、DNN、Orchard 和 Django 应用程序

<!-- Connected --->
::: zone pivot="state-connected"
若要部署应用服务资源提供程序，请执行以下步骤：

1. 从可访问 Azure Stack 中心管理员 Azure 资源管理终结点的计算机上以管理员身份运行之前下载的应用服务安装程序（**appservice**）。

2. 选择“部署应用服务或升级到最新版本”。****

    ![部署或升级应用服务](media/app-service-deploy-ha/01.png)

3. 接受 Microsoft 许可条款，然后单击“下一步”。****

    ![有关应用服务的 Microsoft 许可条款](media/app-service-deploy-ha/02.png)

4. 接受非 Microsoft 许可条款，然后单击“下一步”。****

    ![有关应用服务的非 Microsoft 许可条款](media/app-service-deploy-ha/03.png)

5. 为 Azure Stack 中心环境提供应用服务云终结点配置。

    ![有关应用服务的应用服务云终结点配置](media/app-service-deploy-ha/04.png)

6. **连接**到要用于安装的 Azure Stack 中心订阅，并选择位置。 

    ![在应用服务上连接到 Azure Stack 中心订阅](media/app-service-deploy-ha/05.png)

7. 选择“使用现有 VNet 和子网”，并选择用于部署高可用性模板的资源组的“资源组名称”。********<br><br>接下来，选择在模板部署过程中创建的虚拟网络，然后从下拉列表选项中选择相应的角色子网。 

    ![在应用服务上进行的 VNet 选择](media/app-service-deploy-ha/06.png)

8. 提供前面记录的文件共享路径和文件共享所有者参数的模板输出信息。 完成后单击“下一步”****。

    ![应用服务上的文件共享输出信息](media/app-service-deploy-ha/07.png)

9. 由于用于安装应用服务的计算机与用于托管应用服务文件共享的文件服务器不在同一 VNet 上，因此无法解析名称。 **此错误是预期行为**。<br><br>验证为文件共享 UNC 路径和帐户信息输入的信息是否正确， 然后在警报对话框中按“是”以继续安装应用服务。****

    ![应用服务上的预期错误对话框](media/app-service-deploy-ha/08.png)

10. 提供标识应用程序 ID 以及标识证书的路径和密码，然后单击“下一步”：****
    - 标识应用程序证书（格式为 **sso.appservice.local.azurestack.external.pfx**）
    - Azure 资源管理器根证书 (**AzureStackCertificationAuthority.cer**)

    ![应用服务上的 ID 应用程序证书和根证书](media/app-service-deploy-ha/008.png)

11. 接下来，提供以下证书的剩余所需信息，然后单击“下一步”：****
    - 默认 Azure Stack 中心 SSL 证书（采用**appservice. test-azurestack**格式）。
    - API SSL 证书（格式为 **api.appservice.local.azurestack.external.pfx**）
    - 发布者证书（格式为 **ftp.appservice.local.azurestack.external.pfx**） 

    ![应用服务上的其他配置证书](media/app-service-deploy-ha/09.png)

12. 使用高可用性模板部署输出中的 SQL Server 连接信息提供 SQL Server 连接信息：

    ![应用服务上的 SQL Server 连接信息](media/app-service-deploy-ha/10.png)

13. 由于用于安装应用服务的计算机与用于托管应用服务数据库的 SQL Server 不在同一 VNet 上，因此无法解析名称。  **这是预期的行为**。<br><br>验证为 SQL Server 名称和帐户信息输入的信息是否正确，然后按“是”以继续安装应用服务。**** 单击“下一步”  。

    ![应用服务上的 SQL Server 连接信息](media/app-service-deploy-ha/11.png)

14. 接受默认角色配置值或更改为建议的值，然后单击“下一步”。****<br><br>对于高可用性配置，我们建议对应用服务基础结构角色实例的默认值做出如下所示的更改：

    |角色|默认|高可用性建议|
    |-----|-----|-----|
    |控制器角色|2|2|
    |管理角色|1|3|
    |“发布者”角色|1|3|
    |前端角色|1|3|
    |共享辅助角色|1|2|
    |     |     |     |

    ![应用服务上的基础结构角色实例值](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > 将默认值更改为本教程中建议的值会提高安装应用服务所要满足的硬件要求。 总共需要18个核心和 32256 MB 的 RAM 来支持推荐的13个 Vm，而不是6个 Vm 的默认9个核心和 16128 MB RAM。

15. 选择用于安装应用服务基础结构 VM 的平台映像，然后单击“下一步”：****

    ![应用服务上的平台映像选择](media/app-service-deploy-ha/13.png)

16. 提供要使用的应用服务基础结构角色凭据信息，然后单击“下一步”：****

    ![应用服务上的基础结构角色凭据](media/app-service-deploy-ha/14.png)

17. 查看用于部署应用服务的信息，然后单击“下一步”开始部署。****

    ![在应用服务上查看安装摘要](media/app-service-deploy-ha/15.png)

18. 查看应用服务部署进度。 此部署可能需要一小时以上，具体取决于部署配置和硬件。 安装程序成功完成后，选择“退出”。****

    ![应用服务的设置完成](media/app-service-deploy-ha/16.png)

::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->

## <a name="create-an-offline-installation-package"></a>创建脱机安装包

若要在脱机环境中部署 Azure App Service，请先在连接到 internet 的计算机上创建脱机安装包。

1. 在连接到 Internet 的计算机上运行 AppService.exe 安装程序。 

1. 选择 "**高级** > **创建脱机安装包**"。 此步骤需要几分钟才能完成。

    ![在 Azure App Service 安装程序中创建脱机包]超过

1. Azure App Service 安装程序将创建一个脱机安装包并显示其路径。 可以选择“打开文件夹”，在文件资源管理器中打开该文件夹。****

    ![Azure App Service 安装程序中成功生成脱机安装包](media/azure-stack-app-service-deploy-offline/image02.png)

1. 将安装程序（AppService）和脱机安装包复制到已连接到 Azure Stack 集线器的计算机。

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>在 Azure Stack 集线器上完成 Azure App Service 的脱机安装

1. 在可访问 Azure Stack 中心管理员 Azure 资源管理终结点的计算机上以管理员身份运行 appservice。

1. 选择 "**高级** > " "**完全脱机安装**"。

    ![Azure App Service 安装程序中的完全脱机安装][32]

1. 浏览到前面创建的脱机安装包的位置，选择选择“下一步”。****

    ![指定脱机安装包路径 im Azure App Service 安装程序](media/azure-stack-app-service-deploy-offline/image04.png)

1. 接受 Microsoft 许可条款，然后单击“下一步”。****

    ![有关应用服务的 Microsoft 许可条款](media/app-service-deploy-ha/02.png)

1. 接受非 Microsoft 许可条款，然后单击“下一步”。****

    ![有关应用服务的非 Microsoft 许可条款](media/app-service-deploy-ha/03.png)

1. 为 Azure Stack 中心环境提供应用服务云终结点配置。

    ![有关应用服务的应用服务云终结点配置](media/app-service-deploy-ha/04.png)

1. **连接**到要用于安装的 Azure Stack 中心订阅，并选择位置。 

    ![在应用服务上连接到 Azure Stack 中心订阅](media/app-service-deploy-ha/05.png)

1. 选择“使用现有 VNet 和子网”，并选择用于部署高可用性模板的资源组的“资源组名称”。********  接下来，选择在模板部署过程中创建的虚拟网络，然后从下拉列表选项中选择相应的角色子网。 

    ![在应用服务上进行的 VNet 选择](media/app-service-deploy-ha/06.png)

1. 提供前面记录的文件共享路径和文件共享所有者参数的模板输出信息。 完成后单击“下一步”****。

    ![应用服务上的文件共享输出信息](media/app-service-deploy-ha/07.png)

1. 由于用于安装应用服务的计算机与用于托管应用服务文件共享的文件服务器不在同一 VNet 上，因此无法解析名称。 **此错误是预期行为**。  验证为文件共享 UNC 路径和帐户信息输入的信息是否正确， 然后在警报对话框中按“是”以继续安装应用服务。****

    ![应用服务上的预期错误对话框](media/app-service-deploy-ha/08.png)

1. 提供标识应用程序 ID 以及标识证书的路径和密码，然后单击“下一步”：****
    - 标识应用程序证书（格式为 **sso.appservice.local.azurestack.external.pfx**）
    - Azure 资源管理器根证书 (**AzureStackCertificationAuthority.cer**)

    ![应用服务上的 ID 应用程序证书和根证书](media/app-service-deploy-ha/008.png)

1. 接下来，提供以下证书的剩余所需信息，然后单击“下一步”：****
    - 默认 Azure Stack 中心 SSL 证书（采用**appservice. test-azurestack**格式）。
    - API SSL 证书（格式为 **api.appservice.local.azurestack.external.pfx**）
    - 发布者证书（格式为 **ftp.appservice.local.azurestack.external.pfx**） 

    ![应用服务上的其他配置证书](media/app-service-deploy-ha/09.png)

1. 使用高可用性模板部署输出中的 SQL Server 连接信息提供 SQL Server 连接信息：

    ![应用服务上的 SQL Server 连接信息](media/app-service-deploy-ha/10.png)

1. 由于用于安装应用服务的计算机与用于托管应用服务数据库的 SQL Server 不在同一 VNet 上，因此无法解析名称。  **这是预期的行为**。  验证为 SQL Server 名称和帐户信息输入的信息是否正确，然后按“是”以继续安装应用服务。**** 单击“下一步”  。

    ![应用服务上的 SQL Server 连接信息](media/app-service-deploy-ha/11.png)

1. 接受默认角色配置值或更改为建议的值，然后单击“下一步”。****  对于高可用性配置，我们建议对应用服务基础结构角色实例的默认值做出如下所示的更改：

    |角色|默认|高可用性建议|
    |-----|-----|-----|
    |控制器角色|2|2|
    |管理角色|1|3|
    |“发布者”角色|1|3|
    |前端角色|1|3|
    |共享辅助角色|1|2|
    |     |     |     |

    ![应用服务上的基础结构角色实例值](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > 将默认值更改为本教程中建议的值会提高安装应用服务所要满足的硬件要求。 总共需要18个核心和 32256 MB 的 RAM 来支持推荐的13个 Vm，而不是6个 Vm 的默认9个核心和 16128 MB RAM。

1. 选择用于安装应用服务基础结构 VM 的平台映像，然后单击“下一步”：****

    ![应用服务上的平台映像选择](media/app-service-deploy-ha/13.png)

1. 提供要使用的应用服务基础结构角色凭据信息，然后单击“下一步”：****

    ![应用服务上的基础结构角色凭据](media/app-service-deploy-ha/14.png)

1. 查看用于部署应用服务的信息，然后单击“下一步”开始部署。****

    ![在应用服务上查看安装摘要](media/app-service-deploy-ha/15.png)

1. 查看应用服务部署进度。 此部署可能需要一小时以上，具体取决于部署配置和硬件。 安装程序成功完成后，选择“退出”。****

    ![应用服务的设置完成](media/app-service-deploy-ha/16.png)

::: zone-end

## <a name="post-deployment-steps"></a>部署后步骤

1. **必须**[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)。 此外，必须同步数据库，以防止在发生数据库故障转移时丢失任何服务。

2. 已将应用服务部署到现有的虚拟网络中，并使用内部 IP 地址连接到文件服务器，你**必须**添加出站安全规则，以启用辅助网与文件服务器之间的 SMB 流量。 在管理员门户中，转到 WorkersNsg 网络安全组并添加具有以下属性的出站安全规则：

- 源：任何
- 源端口范围：*
- 目标： IP 地址
- 目标 IP 地址范围：文件服务器的 IP 范围
- 目标端口范围：445
- 协议：TCP
- 操作：允许
- 优先级：700
- 名称：Outbound_Allow_SMB445

## <a name="next-steps"></a>后续步骤

如果已为应用服务资源提供程序提供 SQL Always On 实例，请[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)。 同步数据库以防止在发生数据库故障转移时丢失任何服务。 你还可以运行[脚本](https://blog.sqlauthority.com/2017/11/30/sql-server-alwayson-availability-groups-script-sync-logins-replicas/)，将原始主服务器中的 AppServices 登录名导入到故障转移服务器。

[横向扩展应用服务](azure-stack-app-service-add-worker-roles.md)。 你可能需要添加更多的应用服务基础结构辅助角色，以满足环境中的预期应用需求。 默认情况下，Azure Stack 集线器上的应用服务支持免费和共享的辅助角色层。 若要添加其他辅助角色层，需添加更多的辅助角色。

[配置部署源](azure-stack-app-service-configure-deployment-sources.md)。 需要提供额外的配置来支持从多个源代码管理提供程序（例如 GitHub、BitBucket、OneDrive 和 DropBox）进行的按需部署。

[备份应用服务](app-service-back-up.md)。 成功部署并配置应用服务之后，应确保灾难恢复所需的所有组件均已备份。 备份基本组件有助于防止在恢复操作期间造成数据丢失和不必要的服务中断。
