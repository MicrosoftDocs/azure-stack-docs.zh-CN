---
title: 在高可用性配置中部署 Azure Stack 应用服务 | Microsoft Docs
description: 了解如何使用高可用性配置在 Azure Stack 中部署应用服务。
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 2d2aab654f2283cf019e609e9de14790ed44a76a
ms.sourcegitcommit: e51cdc84a09250e8fa701bb2cb09de38d7de2c07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837043"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>在高可用性配置中部署应用服务

本文介绍如何使用 Azure Stack 市场项，在高可用性配置中部署 Azure Stack 的应用服务。 除了可用的市场项以外，此解决方案还使用了 [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入门模板。 此模板可以自动创建高可用性基础结构用于用于托管应用服务资源提供程序。 然后，将在此高可用性 VM 基础结构上安装应用服务。 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>部署高可用性应用服务基础结构 VM
[appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入门模板简化了在高可用性配置中部署应用服务的过程。 应在默认提供程序订阅中部署该模板。 

使用该模板在 Azure Stack 中创建自定义资源时，该模板将会创建：
- 虚拟网络和所需的子网。
- 文件服务器、SQL Server 和 Active Directory 域服务 (AD DS) 子网的网络安全组。
- VM 磁盘和群集云见证的存储帐户。
- 一个用于 SQL VM 的内部负载均衡器，其专用 IP 已绑定到 SQL AlwaysOn 侦听器。
- AD DS、文件服务器群集和 SQL 群集的三个可用性集。
- 双节点 SQL 群集。
- 双节点文件服务器群集。
- 两个域控制器。

### <a name="required-azure-stack-marketplace-items"></a>所需的 Azure Stack 市场项
使用此模板之前，请确保 Azure Stack 实例中存在以下 [Azure Stack 市场项](azure-stack-marketplace-azure-items.md)：

- Windows Server 2016 Datacenter Core 映像（用于 AD DS 和文件服务器 VM）
- Windows Server 2016 上的 SQL Server 2016 SP2 (Enterprise)
- 最新的 SQL IaaS 扩展 
- 最新的 PowerShell Desired State Configuration 扩展 

> [!TIP]
> 有关模板要求和默认值的更多详细信息，请查看 GitHub 上的[模板自述文件](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha)。 

### <a name="deploy-the-app-service-infrastructure"></a>部署应用服务基础结构
参考本部分所述的步骤，使用 **appservice-fileshare-sqlserver-ha** Azure Stack 快速入门模板创建自定义部署。

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 选择“\+ 创建资源” > “自定义”，然后选择“模板部署”。    

   ![自定义模板部署](media/app-service-deploy-ha/1.png)


3. 在“自定义部署”边栏选项卡上，选择“编辑模板” > “快速入门模板”，在可用自定义模板下拉列表中选择“appservice-fileshare-sqlserver-ha”模板，单击“确定”，然后单击“保存”。      

   ![选择 appservice-fileshare-sqlserver-ha 快速入门模板](media/app-service-deploy-ha/2.png)

4. 在“自定义部署”边栏选项卡上选择“编辑参数”，然后向下滚动以查看默认模板值。   根据需要修改这些值以提供全部所需的参数信息，然后单击“确定”。 <br><br> 最起码需要为 ADMINPASSWORD、FILESHAREOWNERPASSWORD、FILESHAREUSERPASSWORD、SQLSERVERSERVICEACCOUNTPASSWORD 和 SQLLOGINPASSWORD 参数提供复杂密码。
    
   ![编辑自定义部署参数](media/app-service-deploy-ha/3.png)

5. 在“自定义部署”边栏选项卡上，确保选择“默认提供程序订阅”作为要使用的订阅，然后为自定义部署创建新的资源组或选择现有的资源组。  <br><br> 接下来，选择资源组的位置（对于 ASDK 安装，请选择“本地”），然后单击“创建”。   在模板部署开始之前，系统将验证自定义部署设置。

    ![创建自定义部署](media/app-service-deploy-ha/4.png)

6. 在管理门户中选择“资源组”，然后选择针对自定义部署创建的资源组名称（在本示例中为 **app-service-ha**）。  查看部署状态，确保所有部署已成功完成。

   > [!NOTE]
   > 模板部署大约需要一小时才能完成。

   [![](media/app-service-deploy-ha/5-sm.png "查看模板部署状态")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>记下模板输出
模板部署成功完成后，请记下模板部署输出。 运行应用服务安装程序时，需要提供此信息。 

请务必记下以下每个输出值：
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

遵循以下步骤找到模板输出值：

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 在管理门户中选择“资源组”，然后选择针对自定义部署创建的资源组名称（在本示例中为 **app-service-ha**）。  

3. 单击“部署”，然后选择“Microsoft.Template”。  

    ![Microsoft.Template 部署](media/app-service-deploy-ha/6.png)

4. 选择“Microsoft.Template”部署后，选择“输出”并记下模板参数输出。   部署应用服务时需要此信息。

    ![参数输出](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>在高可用性配置中部署应用服务
遵循本部分所述的步骤，基于 [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入门模板在高可用性配置中部署 Azure Stack 的应用服务。 

安装应用服务资源提供程序后，可以将其包括在套餐和计划中。 然后，用户可以订阅以获取服务并开始创建应用程序。

> [!IMPORTANT]
> 在运行资源提供程序安装程序之前，请确保已阅读每个应用服务版本随附的发行说明，以了解新功能、修复程序，以及可能影响部署的任何已知问题。

### <a name="prerequisites"></a>必备组件
在运行应用服务安装程序之前，需要执行[开始使用 Azure Stack 上的应用服务之前](azure-stack-app-service-before-you-get-started.md)一文中所述的几个步骤：

> [!TIP]
> 不一定要执行“准备工作”一文中所述的所有步骤，因为模板部署会自动配置基础结构 VM。 

- [下载应用服务安装程序与帮助器脚本](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts)。
- [将最新的自定义脚本扩展下载到 Azure Stack 市场](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace)。
- [生成所需的证书](azure-stack-app-service-before-you-get-started.md#get-certificates)。
- 根据你为 Azure Stack 选择的标识提供者创建 ID 应用程序。 可为 [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) 或 [Active Directory 联合身份验证服务](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application)创建 ID 应用程序，并记下应用程序 ID。
- 确保将 Windows Server 2016 Datacenter 映像添加到 Azure Stack 市场。 应用服务安装必须使用该映像。

### <a name="deploy-app-service-in-highly-available-configuration"></a>在高可用性配置中部署应用服务
安装应用服务资源提供程序至少需要一小时。 所需时长取决于部署的角色实例数。 部署期间，安装程序运行以下任务：

- 在指定的 Azure Stack 存储帐户中创建 blob 容器。
- 为应用服务中创建 DNS 区域和条目。
- 注册应用服务资源提供程序。
- 注册应用服务库项。

若要部署应用服务资源提供程序，请执行以下步骤：

1. 在可以访问“Azure Stack 管理”Azure 资源管理终结点的计算机上，以管理员身份运行前面下载的应用服务安装程序 (**appservice.exe**)。

2. 选择“部署应用服务或升级到最新版本”。 

    ![部署或升级](media/app-service-deploy-ha/01.png)

3. 接受 Microsoft 许可条款，然后单击“下一步”。 

    ![Microsoft 许可条款](media/app-service-deploy-ha/02.png)

4. 接受非 Microsoft 许可条款，然后单击“下一步”。 

    ![非 Microsoft 许可条款](media/app-service-deploy-ha/03.png)

5. 为 Azure Stack 环境提供应用服务云终结点配置。

    ![应用服务云终结点配置](media/app-service-deploy-ha/04.png)

6. **连接**到要用于安装的 Azure Stack 订阅，并选择位置。 

    ![连接到 Azure Stack 订阅](media/app-service-deploy-ha/05.png)

7. 选择“使用现有 VNet 和子网”，并选择用于部署高可用性模板的资源组的“资源组名称”。  <br><br>接下来，选择在模板部署过程中创建的虚拟网络，然后从下拉列表选项中选择相应的角色子网。 

    ![VNet 选择](media/app-service-deploy-ha/06.png)

8. 提供前面记下的文件共享路径和文件共享所有者参数的模板输出信息。 完成后，单击“下一步”。 

    ![文件共享输出信息](media/app-service-deploy-ha/07.png)

9. 由于用于安装应用服务的计算机与用于托管应用服务文件共享的文件服务器不在同一 VNet 上，因此无法解析名称。 **这是预期行为**。<br><br>验证为文件共享 UNC 路径和帐户信息输入的信息是否正确，然后在警报对话框中按“是”以继续安装应用服务。 

    ![预期错误对话框](media/app-service-deploy-ha/08.png)

    如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理门户中的 WorkersNsg 并添加包含以下属性的出站安全规则：
    - 源：任意
    - 源端口范围：*
    - 目标：IP 地址
    - 目标 IP 地址范围：文件服务器的 IP 范围
    - 目标端口范围：445
    - 协议：TCP
    - 操作：允许
    - 优先级：700
    - 姓名：Outbound_Allow_SMB445

10. 提供标识应用程序 ID 以及标识证书的路径和密码，然后单击“下一步”： 
    - 标识应用程序证书（格式为 **sso.appservice.local.azurestack.external.pfx**）
    - Azure 资源管理器根证书 (**AzureStackCertificationAuthority.cer**)

    ![ID 应用程序证书和根证书](media/app-service-deploy-ha/008.png)

10. 接下来，提供以下证书的剩余所需信息，然后单击“下一步”： 
    - 默认的 Azure Stack SSL 证书（格式为 **_.appservice.local.azurestack.external.pfx**）
    - API SSL 证书（格式为 **api.appservice.local.azurestack.external.pfx**）
    - 发布者证书（格式为 **ftp.appservice.local.azurestack.external.pfx**） 

    ![其他配置证书](media/app-service-deploy-ha/09.png)

11. 使用高可用性模板部署输出中的 SQL Server 连接信息提供 SQL Server 连接信息：

    ![SQL Server 连接信息](media/app-service-deploy-ha/10.png)

12. 由于用于安装应用服务的计算机与用于托管应用服务数据库的 SQL Server 不在同一 VNet 上，因此无法解析名称。  **这是预期行为**。<br><br>验证为 SQL Server 名称和帐户信息输入的信息是否正确，然后按“是”以继续安装应用服务。  单击“下一步”。 

    ![SQL Server 连接信息](media/app-service-deploy-ha/11.png)

13. 接受默认角色配置值或更改为建议的值，然后单击“下一步”。 <br><br>对于高可用性配置，我们建议对应用服务基础结构角色实例的默认值做出如下所示的更改：

    |角色|默认|高可用性建议|
    |-----|-----|-----|
    |控制器角色|2|2|
    |管理角色|第|3|
    |发布者角色|第|3|
    |前端角色|第|3|
    |共享辅助角色|第|10|
    |     |     |     |

    ![基础结构角色实例值](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > 将默认值更改为本教程中建议的值会提高安装应用服务所要满足的硬件要求。 总共需要提供 26 个核心和 46,592 MB 的 RAM 来支持建议的 21 个 VM，而不是提供 18 个核心和 32,256 MB 的 RAM 来支持 15 个 VM。

14. 选择用于安装应用服务基础结构 VM 的平台映像，然后单击“下一步”： 

    ![平台映像选择](media/app-service-deploy-ha/13.png)

15. 提供要使用的应用服务基础结构角色凭据信息，然后单击“下一步”： 

    ![基础结构角色凭据](media/app-service-deploy-ha/14.png)

16. 查看用于部署应用服务的信息，然后单击“下一步”开始部署。  

    ![查看安装摘要](media/app-service-deploy-ha/15.png)

17. 查看应用服务部署进度。 这可能需要一小时以上，具体取决于部署配置和硬件。 安装程序成功完成后，选择“退出”。 

    ![安装完成](media/app-service-deploy-ha/16.png)

## <a name="next-steps"></a>后续步骤

[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)如果使用 SQL Alwayson 实例提供了应用服务资源提供程序。 同步数据库以防止数据库故障转移时的服务的任何损失。

[横向扩展应用服务](azure-stack-app-service-add-worker-roles.md)。 你可能需要添加更多的应用服务基础结构辅助角色，以满足环境中的预期应用程序需求。 基于 Azure Stack 的应用服务默认支持免费的和共享的辅助角色层。 若要添加其他辅助角色层，需添加更多的辅助角色。

[配置部署源](azure-stack-app-service-configure-deployment-sources.md)。 需要提供额外的配置来支持从多个源代码管理提供程序（例如 GitHub、BitBucket、OneDrive 和 DropBox）进行的按需部署。

[备份应用服务](app-service-back-up.md)。 成功部署并配置应用服务之后，请确保用于灾难恢复的所有组件均已备份，以防止数据丢失，并避免在恢复操作期间造成不必要的服务中断。