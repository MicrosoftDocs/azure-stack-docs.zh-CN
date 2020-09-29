---
title: 创建高可用 SQL 数据库
titleSuffix: Azure Stack Hub
description: 了解如何使用 Azure Stack Hub 创建 SQL Server 资源提供程序主机和高可用 SQL AlwaysOn 数据库。
author: BryanLa
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: ce3f6e0542678fe2d399e101a90a916cf412599f
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487713"
---
# <a name="create-highly-available-sql-databases-with-azure-stack-hub"></a>使用 Azure Stack Hub 创建高可用 SQL 数据库

作为 Azure Stack Hub 操作员，你可以配置服务器 VM 来承载 SQL Server 数据库。 通过 Azure Stack Hub 创建和管理 SQL 宿主服务器后，订阅了 SQL 服务的用户可以轻松地创建 SQL 数据库。

本文介绍了如何使用 Azure Stack Hub 快速入门模板创建 [SQL Server AlwaysOn 可用性组](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)，将其添加为 Azure Stack Hub SQL 宿主服务器，然后创建高可用 SQL 数据库。

学习内容：

> [!div class="checklist"]
> * 基于模板创建 SQL Server AlwaysOn 可用性组。
> * 创建 Azure Stack Hub SQL 宿主服务器。
> * 创建高可用 SQL 数据库。

将使用可用的 Azure Stack 市场项创建并配置包含两个 VM SQL Server 的 AlwaysOn 可用性组。

在开始之前，请确保已成功安装了 [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)并且以下项在 Azure Stack 市场中可用：

> [!IMPORTANT]
> 要使用 Azure Stack Hub 快速入门模板，需要备齐以下所有项。

- Windows Server 2016 Datacenter。
- Windows Server 2016 服务器映像上的 SQL Server 2016 SP1 或 SP2（企业版、标准版或开发人员版）。 
- [SQL Server IaaS 扩展](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) 1.3.20180 或更高版本。 SQL IaaS 扩展会为所有 Windows 版本安装市场 SQL Server 项所需的必要组件。 它允许在 SQL 虚拟机 (VM) 上配置 SQL 特定的设置。 如果未在本地市场安装该扩展，则 SQL 的预配将会失败。
- [适用于 Windows 的自定义脚本扩展](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) 1.9.1 或更高版本。 自定义脚本扩展是一种工具，可用于自动启动部署后的 VM 自定义任务。
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) 2.76.0.0 或更高版本。 DSC 是 Windows PowerShell 中的一个管理平台，可用于部署和管理软件服务的配置数据。 该平台还管理这些服务的运行环境。

若要详细了解如何向 Azure Stack 市场中添加项，请参阅 [Azure Stack Hub 市场概述](azure-stack-marketplace.md)。

## <a name="create-a-sql-server-alwayson-availability-group"></a>创建 SQL Server AlwaysOn 可用性组

执行本部分中的步骤，通过使用 [sql-2016-alwayson Azure Stack Hub 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson)来部署 SQL Server AlwaysOn 可用性组。 此模板在 Always On 可用性组中部署两个 SQL Server Enterprise 或 Developer 实例。 它创建以下资源：

- 一个网络安全组。
- 一个虚拟网络。
- 四个存储帐户（一个用于 Active Directory (AD)，一个用于 SQL，一个用于文件共享见证，另一个用于 VM 诊断）。
- 四个公共 IP 地址（一个用于 AD，两个用于单个 SQL VM，另一个用于绑定到 SQL AlwaysOn 侦听器的公共负载均衡器）。
- 一个用于 SQL VM 的外部负载均衡器，该 VM 的公共 IP 已绑定到 SQL AlwaysOn 侦听器。
- 一个 VM (Windows Server 2016)，配置为包含单个域的新林的域控制器。
- 两个 VM (Windows Server 2016)，配置有 SQL Server 2016 SP1 或 SP2 Enterprise 或 Developer Edition 并加入了群集。 它们必须是市场映像。
- 一个 VM (Windows Server 2016)，配置为群集的文件共享见证。
- 一个包含 SQL 和文件共享见证 VM 的可用性集。

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. 选择“\+ 创建资源” > “自定义”，然后选择“模板部署”。**** **** **** ****

   ![Azure Stack Hub 管理员门户中的自定义模板部署](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-1.png)

3. 在“自定义部署”边栏选项卡上，选择“编辑模板” > “快速入门模板”，然后在可用自定义模板的下拉列表中选择“sql-2016-alwayson”模板。**** **** **** **** 选择“确定”****，然后选择“保存”****。

   [![在 Azure Stack Hub 管理员门户中编辑模板](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-2.png "选择快速入门模板")](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-2.png#lightbox)

4. 在“自定义部署”边栏选项卡上，选择“编辑参数”并查看默认值。**** **** 根据需要修改这些值以提供全部所需的参数信息，然后选择“确定”。****

    至少：
    - 为 ADMINPASSWORD、SQLSERVERSERVICEACCOUNTPASSWORD 和 SQLAUTHPASSWORD 参数提供复杂的密码。
    - 对于 DNSSUFFIX 参数，以全小写字母输入用于反向查找的 DNS 后缀（对于 ASDK 安装，此后缀为 **azurestack.external**）。
    
   [![在 Azure Stack Hub 管理员门户中编辑参数](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-3.png "编辑自定义部署参数")](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-3.png#lightbox)

5. 在“自定义部署”边栏选项卡上，选择要使用的订阅，然后为自定义部署创建新的资源组或选择现有的资源组。****

    接下来，选择资源组的位置（对于 ASDK 安装，请选择“本地”），然后单击“创建”。**** **** 系统将验证自定义部署设置，然后开始部署。

    [![在 Azure Stack Hub 管理员门户中选择订阅](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-4.png "创建自定义部署")](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-4.png#lightbox)

6. 在用户门户中，选择“资源组”，然后选择针对自定义部署创建的资源组的名称（在本示例中为 **resource-group**）。**** 查看部署状态，确保所有部署已成功完成。
    
    接下来，查看资源组项，并选择 **SQLPIPsql\<resource group name\>** 公共 IP 地址项。 记录负载均衡器的公共 IP 地址和完整 FQDN。 你需要将此信息提供给 Azure Stack Hub 操作员，以便他们可以利用此 SQL AlwaysOn 可用性组创建一个 SQL 宿主服务器。

   > [!NOTE]
   > 模板部署需要几个小时才能完成。

### <a name="enable-automatic-seeding"></a>启用自动种子设定

在模板成功部署并配置 SQL AlwaysON 可用性组之后，必须在可用性组中的每个 SQL Server 实例上启用[自动种子设定](/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)。

当你创建可以自动设定种子的可用性组时，SQL Server 会自动为该组中的每个数据库创建次要副本，不需要你进行任何其他的手动干预。 此度量值可确保 AlwaysOn 数据库的高可用性。

使用这些 SQL 命令为 AlwaysOn 可用性组配置自动种子设定功能。 根据需要，将 `<PrimaryInstanceName>` 替换为主实例 SQL Server 名称，将 `<SecondaryInstanceName>` 替换为辅助实例 SQL Server 名称，将 `<availability_group_name>` 替换为 AlwaysOn 可用性组名称。

在主 SQL 实例上：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<PrimaryInstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<SecondaryInstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

![主 SQL 实例脚本](./media/azure-stack-tutorial-sqlrp/sql1.png)

在辅助 SQL 实例上：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

![辅助 SQL 实例脚本](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>配置包含的数据库身份验证

将包含的数据库添加到可用性组之前，请确保在托管可用性组可用性副本的每个服务器实例上，包含的数据库身份验证服务器选项已设置为 1。 有关详细信息，请参阅[包含的数据库身份验证](/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017)。

使用以下命令为可用性组中的每个 SQL Server 实例设置包含的数据库身份验证服务器选项：

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

![设置包含的数据库身份验证](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-hub-sql-hosting-server"></a>创建 Azure Stack Hub SQL 宿主服务器

创建并正确配置 SQL Server AlwayOn 可用性组后，Azure Stack Hub 操作员必须创建 Azure Stack Hub SQL 宿主服务器。 SQL 宿主服务器提供更多的容量供用户创建数据库。

在创建 SQL AlwaysOn 可用性组的资源组 (**SQLPIPsql\<resource group name\>** ) 时，请确保使用先前记录的 SQL 负载均衡器的公共 IP 或完整 FQDN。 此外，你还需要知道用于访问 AlwaysOn 可用性组中的 SQL 实例的 SQL Server 身份验证凭据。

> [!NOTE]
> 此步骤必须由 Azure Stack Hub 操作员从 Azure Stack Hub 管理员门户运行。

使用 SQL AlwaysOn 可用性组的负载均衡器侦听器公共 IP 和 SQL 身份验证登录信息，Azure Stack Hub 操作员可以[使用 SQL AlwaysOn 可用性组创建 SQL 宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups)。 

此外，请确保你已创建计划和套餐，使得用户可以创建 SQL AlwaysOn 数据库。 操作员需要将 **Microsoft.SqlAdapter** 服务添加到计划并专门为高可用数据库创建新配额。 有关创建计划的详细信息，请参阅[服务、计划、套餐和订阅概述](service-plan-offer-subscription-overview.md)。

> [!TIP]
> 在[部署 SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)之前，无法将 **Microsoft.sqladapter** 服务添加到计划中。

## <a name="create-a-highly-available-sql-database"></a>创建高可用 SQL 数据库

在 Azure Stack Hub 操作员创建、配置并添加 SQL AlwaysOn 可用性组作为 Azure Stack Hub SQL 宿主服务器之后，订阅中包含 SQL Server 数据库功能的租户用户可以创建支持 AlwaysOn 功能的 SQL 数据库。 他们可以按照本部分中的步骤创建这些数据库。

> [!NOTE]
> 从 Azure Stack Hub 用户门户以订阅中提供了 SQL Server 功能（Microsoft.SQLAdapter 服务）的租户用户身份运行这些步骤。

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. 选择“\+ 创建资源” > “数据 \+ 存储”，然后选择“SQL 数据库”****。**** **** ****

    提供所需的数据库属性信息。 此信息包括名称、排序规则、最大大小，以及要用于部署的订阅、资源组和位置。

   ![在 Azure Stack Hub 用户门户中创建 SQL 数据库](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. 选择“SKU”****，然后选择要使用的相应 SQL 宿主服务器 SKU。 在此示例中，Azure Stack Hub 操作员创建了“企业高可用性”**** SKU，以支持 SQL AlwaysOn 可用性组的高可用性。

   ![在 Azure Stack Hub 用户门户中选择 SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. 选择“登录名” > “创建新的登录名”，然后提供要用于新数据库的 SQL 身份验证凭据。**** **** 完成后，选择“确定”****，然后选择“创建”**** 以开始数据库部署过程。

   ![在 Azure Stack Hub 用户门户中创建登录名](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. 当 SQL 数据库部署成功完成时，查看数据库属性以发现用于连接到新的高可用数据库的连接字符串。

   ![在 Azure Stack Hub 用户门户中查看连接字符串](./media/azure-stack-tutorial-sqlrp/createdb4.png)

## <a name="next-steps"></a>后续步骤

[更新 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)
