---
title: 在高度可用的配置中部署 Azure Stack 应用服务 |Microsoft Docs
description: 了解如何使用高可用配置在 Azure Stack 中部署应用服务。
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
ms.date: 01/02/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 9e5b99a5787e6472b2e9d25a509f615a1b02a732
ms.sourcegitcommit: a6c02421069ab9e72728aa9b915a52ab1dd1dbe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75654948"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>在高可用性配置中部署应用服务

本文介绍如何使用 Azure Stack marketplace 项在高度可用的配置中部署 Azure Stack 的应用服务。 除了可用的 marketplace 项外，此解决方案还使用[appservice](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入门模板。 此模板自动创建用于托管应用服务资源提供程序的高度可用的基础结构。 然后，应用服务将安装在此高度可用的 VM 基础结构上。 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>部署高度可用的应用服务基础结构 Vm
[Appservice](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入门模板可在高可用性配置中简化应用服务的部署。 它应部署在默认提供程序订阅中。 

当用于在 Azure Stack 中创建自定义资源时，该模板将创建：
- 虚拟网络和所需的子网。
- 用于文件服务器、SQL Server 和 Active Directory 域服务（AD DS）子网的网络安全组。
- VM 磁盘和群集云见证的存储帐户。
- 一个内部负载均衡器，用于将专用 IP 绑定到 SQL AlwaysOn 侦听器的 SQL Vm。
- AD DS、文件服务器群集和 SQL 群集的三个可用性集。
- 两个节点 SQL 群集。
- 双节点文件服务器群集。
- 两个域控制器。

### <a name="required-azure-stack-marketplace-items"></a>需要 Azure Stack marketplace 项
使用此模板之前，请确保 Azure Stack 实例中有以下[Azure Stack marketplace 项](azure-stack-marketplace-azure-items.md)：

- Windows Server 2016 Datacenter Core 映像（适用于 AD DS 和文件服务器 Vm）
- Windows Server 2016 上的 SQL Server 2016 SP2 （企业版）
- 最新 SQL IaaS 扩展 
- 最新的 PowerShell Desired State Configuration 扩展 

> [!TIP]
> 查看 GitHub 上[的模板自述文件](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha)，了解有关模板要求和默认值的其他详细信息。 

### <a name="deploy-the-app-service-infrastructure"></a>部署应用服务基础结构
使用此部分中的步骤，可以使用**appservice-sqlserver** Azure Stack 快速入门模板创建自定义部署。

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 选择 **\+** 创建 > **自定义** **的资源**，然后**模板部署**"。

   ![自定义模板部署](media/app-service-deploy-ha/1.png)


3. 在 "**自定义部署**" 边栏选项卡上，选择 "**编辑模板** > **快速入门模板**"，然后使用可用自定义模板下拉列表选择**appservice-sqlserver-ha**模板。 单击 **"确定"** ，然后单击 "**保存**"。

   ![选择 "appservice"-"文件共享" 快速入门模板](media/app-service-deploy-ha/2.png)

4. 在 "**自定义部署**" 边栏选项卡中，选择 "**编辑参数**" 并向下滚动以查看默认模板值。 根据需要修改这些值以提供所有必需的参数信息，然后单击 **"确定"** 。<br><br> 至少，提供 `ADMINPASSWORD`、`FILESHAREOWNERPASSWORD`、`FILESHAREUSERPASSWORD`、`SQLSERVERSERVICEACCOUNTPASSWORD`和 `SQLLOGINPASSWORD` 参数的复杂密码。
    
   ![编辑自定义部署参数](media/app-service-deploy-ha/3.png)

5. 在 "**自定义部署**" 边栏选项卡上，确保选择**默认提供程序订阅**作为要使用的订阅，然后为自定义部署创建新的资源组或选择现有的资源组。<br><br> 接下来，选择资源组位置（ASDK 安装的**本地**安装），然后单击 "**创建**"。 在模板部署开始之前验证自定义部署设置。

    ![创建自定义部署](media/app-service-deploy-ha/4.png)

6. 在管理员门户中，选择 "**资源组**"，然后选择为自定义部署创建的资源组的名称（在本示例中为**应用服务-ha** ）。 查看部署状态，以确保所有部署均已成功完成。

   > [!NOTE]
   > 模板部署需要大约一小时才能完成。

   [![](media/app-service-deploy-ha/5-sm.png "Review template deployment status")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>记录模板输出
模板部署成功完成后，记录模板部署输出。 运行应用服务安装程序时需要此信息。

确保记录以下每个输出值：
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

按照以下步骤发现模板输出值：

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 在管理员门户中，选择 "**资源组**"，然后选择为自定义部署创建的资源组的名称（在本示例中为**应用服务-ha** ）。 

3. 单击 "**部署**"，然后选择 " **Microsoft 模板**"。

    ![Microsoft. 模板部署](media/app-service-deploy-ha/6.png)

4. 选择 " **Microsoft 模板**部署" 后，选择 "**输出**" 并记录模板参数输出。 部署应用服务时需要此信息。

    ![参数输出](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>在高可用性配置中部署应用服务
按照本部分中的步骤，根据[appservice-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入门模板，在高度可用的配置中部署 Azure Stack 的应用服务。 

安装应用服务资源提供程序后，可以将其包含在产品/服务和计划中。 然后，用户可以订阅以获取服务并开始创建应用。

> [!IMPORTANT]
> 在运行资源提供程序安装程序之前，请确保已阅读每个应用服务版本随附的发行说明，以了解新功能、修复程序以及可能影响部署的任何已知问题。

### <a name="prerequisites"></a>必备组件
在运行应用服务安装程序之前，需要执行多个步骤，如 "在[Azure Stack 开始应用服务之前](azure-stack-app-service-before-you-get-started.md)" 一文中所述：

> [!TIP]
> 并非需要在[应用服务入门一文](azure-stack-app-service-before-you-get-started.md)中介绍的所有步骤都是必需的，因为模板部署会为你配置基础结构 vm。

- [下载应用服务安装程序和帮助程序脚本](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts)。
- [从 Azure Stack Marketplace 下载项目](azure-stack-app-service-before-you-get-started.md#download-items-from-the-azure-marketplace)。
- [生成所需的证书](azure-stack-app-service-before-you-get-started.md#get-certificates)。
- 基于为 Azure Stack 选择的标识提供者创建 ID 应用程序。 可以为[Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app)或[Active Directory 联合身份验证服务](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-app)创建 ID 应用程序，并记录应用程序 ID。
- 确保已将 Windows Server 2016 Datacenter 映像添加到 Azure Stack marketplace。 此映像是应用服务安装所必需的。

### <a name="steps-for-app-service-deployment"></a>应用服务部署的步骤
安装应用服务资源提供程序至少要花一小时。 所需的时间长度取决于你部署的角色实例数。 在部署过程中，安装程序会运行以下任务：

- 在指定的 Azure Stack 存储帐户中创建一个 blob 容器。
- 创建应用服务的 DNS 区域和条目。
- 注册应用服务资源提供程序。
- 注册应用服务库项。

若要部署应用服务资源提供程序，请执行以下步骤：

1. 从可访问 Azure Stack 管理员 Azure 资源管理终结点的计算机上以管理员身份运行之前下载的应用服务安装程序（**appservice**）。

2. 选择 **"部署应用服务或升级到最新版本"** 。

    ![部署或升级应用服务](media/app-service-deploy-ha/01.png)

3. 接受 Microsoft 许可条款，然后单击 "**下一步**"。

    ![应用服务上的 Microsoft 许可条款](media/app-service-deploy-ha/02.png)

4. 接受非 Microsoft 许可条款，然后单击 "**下一步**"。

    ![应用服务上的非 Microsoft 许可条款](media/app-service-deploy-ha/03.png)

5. 提供 Azure Stack 环境的应用服务云终结点配置。

    ![应用服务上的应用服务云终结点配置](media/app-service-deploy-ha/04.png)

6. **连接**到要用于安装的 Azure Stack 订阅，并选择位置。 

    ![在应用服务上连接到 Azure Stack 订阅](media/app-service-deploy-ha/05.png)

7. 选择 "**使用现有 VNet 和子网**" 和用于部署高可用模板的资源组的**资源组名称**。<br><br>接下来，选择作为模板部署的一部分创建的虚拟网络，然后从下拉列表选项中选择相应的角色子网。 

    ![应用服务上的 Vnet 选择](media/app-service-deploy-ha/06.png)

8. 为文件共享路径和文件共享所有者参数提供之前记录的模板输出信息。 完成后，单击 **“下一步”** 。

    ![应用服务上的文件共享输出信息](media/app-service-deploy-ha/07.png)

9. 由于用于安装应用服务的计算机与用于承载应用服务文件共享的文件服务器不在同一个 VNet 中，因此无法解析该名称。 **此错误是预期的行为**。<br><br>验证输入的文件共享 UNC 路径和帐户信息是否正确。 然后在警报对话框上按 **"是"** 以继续安装应用服务。

    ![应用服务上的预期错误对话框](media/app-service-deploy-ha/08.png)

    如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则。 此规则启用辅助子网和文件服务器之间的 SMB 流量。 在管理员门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：
    - 源：任意
    - 源端口范围： *
    - 目标： IP 地址
    - 目标 IP 地址范围：文件服务器的 Ip 范围
    - 目标端口范围：445
    - 协议：TCP
    - 操作：允许
    - 优先级：700
    - 名称： Outbound_Allow_SMB445

10. 向标识证书提供标识应用程序 ID 以及路径和密码，然后单击 "**下一步**"：
    - 标识应用程序证书（采用 appservice. **test-azurestack**）的格式
    - Azure 资源管理器根证书（**AzureStackCertificationAuthority**）

    ![应用服务上的 ID 应用程序证书和根证书](media/app-service-deploy-ha/008.png)

11. 接下来，为以下证书提供剩余的必需信息，然后单击 "**下一步**"：
    - 默认 Azure Stack SSL 证书（格式为 appservice. **test-azurestack.** ）
    - API SSL 证书（采用**appservice. test-azurestack**的格式）。
    - 发行者证书（格式为 appservice. **test-azurestack.** ） 

    ![应用服务上的其他配置证书](media/app-service-deploy-ha/09.png)

12. 使用高可用性模板部署输出中的 SQL Server 连接信息提供 SQL Server 连接信息：

    ![SQL Server 应用服务的连接信息](media/app-service-deploy-ha/10.png)

13. 由于用于安装应用服务的计算机与用于承载应用服务数据库的 SQL server 不在同一 VNet 中，因此无法解析该名称。  **这是预期的行为**。<br><br>验证输入的 "SQL Server 名称" 和 "帐户信息" 的信息是否正确，按 **"是"** 继续安装应用服务。 单击“下一步”。

    ![SQL Server 应用服务的连接信息](media/app-service-deploy-ha/11.png)

14. 接受默认角色配置值或更改为建议的值，然后单击 "**下一步**"。<br><br>对于高度可用的配置，我们建议更改应用服务基础结构角色实例的默认值，如下所示：

    |角色|默认|高可用建议|
    |-----|-----|-----|
    |控制器角色|2|2|
    |管理角色|第|3|
    |“发布者”角色|第|3|
    |前端角色|第|3|
    |共享辅助角色|第|2|
    |     |     |     |

    ![应用服务上的基础结构角色实例值](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > 将默认值更改为此 tutoral 中建议的值会增加安装应用服务的硬件要求。 总共需要18个核心和 32256 MB 的 RAM 来支持推荐的13个 Vm，而不是6个 Vm 的默认9个核心和 16128 MB RAM。

15. 选择要用于安装应用服务基础结构 Vm 的平台映像，然后单击 "**下一步**"：

    ![应用服务上的平台映像选择](media/app-service-deploy-ha/13.png)

16. 提供要使用的应用服务基础结构角色凭据信息，然后单击 "**下一步**"：

    ![应用服务上的基础结构角色凭据](media/app-service-deploy-ha/14.png)

17. 查看用于部署应用服务的信息，然后单击 "**下一步**" 开始部署。

    ![查看应用服务上的安装摘要](media/app-service-deploy-ha/15.png)

18. 查看应用服务部署进度。 此部署可能需要一小时以上的时间，具体取决于特定的部署配置和硬件。 安装程序成功完成后，选择 "**退出**"。

    ![安装程序已完成应用服务](media/app-service-deploy-ha/16.png)

## <a name="next-steps"></a>后续步骤

如果已为应用服务资源提供程序提供 SQL Always On 实例，请[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)。 同步数据库以防止在发生数据库故障转移时任何服务中断。 你还可以运行[脚本](https://blog.sqlauthority.com/2017/11/30/sql-server-alwayson-availability-groups-script-sync-logins-replicas/)，将原始主服务器中的 AppServices 登录名导入到故障转移服务器。

[横向扩展应用服务](azure-stack-app-service-add-worker-roles.md)。 你可能需要添加其他应用服务基础结构角色辅助角色，以满足你的环境中的预期应用需求。 默认情况下，Azure Stack 上的应用服务支持免费和共享辅助角色层。 若要添加其他辅助角色层，需要添加更多辅助角色。

[配置部署源](azure-stack-app-service-configure-deployment-sources.md)。 需要进行其他配置以支持来自多个源代码管理提供程序（例如 GitHub、BitBucket、OneDrive 和 DropBox）的按需部署。

[备份应用服务](app-service-back-up.md)。 成功部署并配置应用服务后，应确保备份灾难恢复所需的所有组件。 备份你的基本组件有助于防止在恢复操作期间发生数据丢失和不必要的服务停机。
