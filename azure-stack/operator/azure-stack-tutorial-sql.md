---
title: 在 Azure Stack 中心提供高度可用的 SQL 数据库
description: 了解如何使用 Azure Stack 中心创建 SQL Server 资源提供程序主机和高度可用的 SQL AlwaysOn 数据库。
services: azure-stack
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 408508119a7535467756b0b45fbfeb59fba7fbf1
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023002"
---
# <a name="offer-highly-available-sql-databases"></a>提供高度可用的 SQL 数据库

作为 Azure Stack 中心操作员，你可以将服务器 Vm 配置为承载 SQL Server 的数据库。 成功创建 SQL 宿主服务器并通过 Azure Stack 中心管理该服务器后，订阅 SQL 服务的用户可以轻松地创建 SQL 数据库。

本文介绍如何使用 Azure Stack 集线器快速入门模板创建[SQL Server AlwaysOn 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)，将其添加为 AZURE STACK 中心 Sql 宿主服务器，然后创建高度可用的 sql 数据库。

学习内容：

> [!div class="checklist"]
> * 从模板创建 SQL Server AlwaysOn 可用性组
> * 创建 Azure Stack 中心 SQL 宿主服务器
> * 创建高度可用的 SQL 数据库

将使用可用的 Azure Stack 中心市场项创建和配置两个 VM SQL Server AlwaysOn 可用性组。 

在开始之前，请确保已成功安装[SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)，并且 Azure Stack 中心市场中提供了以下各项：

> [!IMPORTANT]
> 要使用 Azure Stack 中心快速入门模板，需要满足以下所有要求。

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer) marketplace 映像。
- Windows Server 2016 服务器映像上的 SQL Server 2016 SP1 或 SP2 （企业版、标准版或开发人员版）。 本文使用[Windows Server 2016 marketplace 映像上的 SQL Server 2016 SP2 企业版](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016)。
- [SQL Server IaaS 扩展](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)版本1.2.30 或更高版本。 SQL IaaS 扩展会安装 Marketplace SQL Server 所有 Windows 版本的项目所需的必要组件。 它允许在 SQL 虚拟机上配置 SQL 特定的设置。 如果本地 Marketplace 中未安装该扩展，则 SQL 的预配会失败。
- Windows 版本1.9.1 或更高版本[的自定义脚本扩展](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension)。 自定义脚本扩展是一种工具，可用于自动启动部署后 VM 自定义任务。
- [PowerShell Desired State Configuration （DSC）](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm)版本2.76.0.0 或更高版本。 DSC 是 Windows PowerShell 中的一个管理平台，可用于部署和管理软件服务的配置数据，并管理这些服务的运行环境。

若要了解有关将项目添加到 Azure Stack 中心 Marketplace 的详细信息，请参阅[Azure Stack 中心市场概述](azure-stack-marketplace.md)。

## <a name="create-a-sql-server-alwayson-availability-group"></a>创建 SQL Server AlwaysOn 可用性组
使用本部分中的步骤，通过使用[SQL-2016-AlwaysOn Azure Stack 中心快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson)部署 SQL Server AlwaysOn 可用性组。 此模板在 Always On 可用性组中部署两个 SQL Server Enterprise、标准或开发人员实例。 它创建以下资源：

- 网络安全组
- 虚拟网络
- 四个存储帐户（一个用于 Active Directory （AD），一个用于 SQL，一个用于文件共享见证，另一个用于 VM 诊断）
- 四个公共 IP 地址（一个用于 AD，两个用于每个 SQL VM，另一个用于绑定到 SQL AlwaysOn 侦听器的公共负载均衡器）
- 一个外部负载均衡器，用于将公共 IP 绑定到 SQL AlwaysOn 侦听器的 SQL Vm
- 一个 VM （Windows Server 2016）配置为新林的域控制器，其中包含单一域
- 两个 Vm （Windows Server 2016）配置了 SQL Server 2016 SP1 或 SP2 Enterprise、Standard 或 Developer Edition 和群集。 它们必须是 marketplace 映像。
- 一个 VM （Windows Server 2016）配置为群集的文件共享见证
- 一个包含 SQL 和文件共享见证 Vm 的可用性集  

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 选择 **\+** 创建 > **自定义** **的资源**，然后**模板部署**"。

   ![自定义模板部署](media/azure-stack-tutorial-sqlrp/1.png)


3. 在 "**自定义部署**" 边栏选项卡上，选择 "**编辑模板** > **快速入门模板**"，然后使用可用自定义模板下拉列表选择 " **2016-alwayson** " 模板，单击 **"确定**"，然后单击 "**保存**"。

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Select quickstart template")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. 在 "**自定义部署**" 边栏选项卡中，选择 "**编辑参数**" 并查看默认值。 根据需要修改值以提供所有必需的参数信息，然后单击 **"确定"** 。<br><br> 至少：

    - 为 ADMINPASSWORD、SQLSERVERSERVICEACCOUNTPASSWORD 和 SQLAUTHPASSWORD 参数提供复杂密码。
    - 输入用于反向查找的 DNS 后缀 DNSSUFFIX 参数的所有小写字母（ASDK 安装的**test-azurestack** ）。
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Edit custom deployment parameters")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. 在 "**自定义部署**" 边栏选项卡中，选择要使用的订阅并创建新的资源组，或选择现有的资源组进行自定义部署。<br><br> 接下来，选择资源组位置（ASDK 安装的**本地**安装），然后单击 "**创建**"。 将验证自定义部署设置，然后启动部署。

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Create custom deployment")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. 在管理门户中，选择 "**资源组**"，然后选择为自定义部署创建的资源组的名称（此示例的**资源组**）。 查看部署状态，以确保所有部署均已成功完成。<br><br>接下来，查看 "资源组" 项，然后选择 " **SQLPIPsql\<资源组名称"\>** "公共 IP 地址" 项。 记录负载均衡器公共 IP 的公共 IP 地址和完全 FQDN。 需要为 Azure Stack 中心操作员提供此服务，以便他们可以利用此 SQL AlwaysOn 可用性组创建 SQL 宿主服务器。

   > [!NOTE]
   > 模板部署需要几个小时才能完成。

   ![自定义部署完成](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>启用自动种子设定
模板成功部署并配置 SQL AlwaysON 可用性组之后，必须在可用性组中 SQL Server 的每个实例上启用[自动种子设定](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)。 

创建具有自动种子设定的可用性组时，SQL Server 会自动为该组中的每个数据库创建辅助副本，而无需任何其他手动干预来确保 AlwaysOn 数据库的高可用性。

使用这些 SQL 命令为 AlwaysOn 可用性组配置自动种子设定。 根据需要，将 \<InstanceName\> 替换为主实例 SQL Server 名称，将 < availability_group_name > 替换为 AlwaysOn 可用性组名称。 

在主 SQL 实例上：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![主 SQL 实例脚本](./media/azure-stack-tutorial-sqlrp/sql1.png)

在辅助 SQL 实例上：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![辅助 SQL 实例脚本](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>配置包含的数据库身份验证
在将包含的数据库添加到某个可用性组前，请确保在托管该可用性组的可用性副本的每个服务器实例上将包含的数据库身份验证服务器选项设置为 1。 有关详细信息，请参阅[包含的数据库身份验证](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017)。

使用以下命令为可用性组中的每个 SQL Server 实例设置包含的数据库身份验证服务器选项：

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![设置包含数据库身份验证](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-hub-sql-hosting-server"></a>创建 Azure Stack 中心 SQL 宿主服务器
创建并正确配置 SQL Server AlwayOn 可用性组之后，Azure Stack 中心操作员必须创建 Azure Stack 中心 SQL 宿主服务器，以使用户能够使用额外的容量来创建数据库。 

请确保在创建 SQL AlwaysOn 可用性组的资源组（**SQLPIPsql\<资源组名称\>** ）时，为之前记录的 sql 负载均衡器的公共 ip 使用公共 ip 或完全 FQDN。 此外，还需要了解用于访问 AlwaysOn 可用性组中的 SQL 实例的 SQL Server 身份验证凭据。

> [!NOTE]
> 必须通过 Azure Stack 中心操作员从 Azure Stack 中心管理门户运行此步骤。

使用 SQL AlwaysOn 可用性组的负载均衡器的负载均衡器公共 IP 和 SQL 身份验证登录信息，Azure Stack 集线器操作员现在可以[使用 Sql alwayson 可用性组创建 Sql 宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups)。 

此外，请确保已创建计划和产品/服务，以便为用户提供 SQL AlwaysOn 数据库创建。 操作员需要将**microsoft.sqladapter**服务添加到计划并为高度可用的数据库创建新的配额。 有关创建计划的详细信息，请参阅 "[服务、计划、产品/服务、订阅概述](service-plan-offer-subscription-overview.md)"。

> [!TIP]
> 在[部署 SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)之前，将不能向计划中添加**microsoft.sqladapter**服务。

## <a name="create-a-highly-available-sql-database"></a>创建高度可用的 SQL 数据库
通过 Azure Stack 中心操作员创建、配置并添加 SQL AlwaysOn 可用性组作为 Azure Stack 中心 SQL 宿主服务器后，具有订阅的租户用户（包括 SQL Server 数据库功能）可以创建 SQL 数据库按照本部分中的步骤支持 AlwaysOn 功能。 

> [!NOTE]
> 使用订阅提供 SQL Server 功能（Microsoft.sqladapter 服务）的租户用户身份从 Azure Stack 中心用户门户运行这些步骤。

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. 选择 **\+** **创建资源** > **数据 \+ 存储**，然后选择 " **SQL 数据库**"。<br><br>提供所需的数据库属性信息，包括名称、排序规则、最大大小和要用于部署的订阅、资源组和位置。 

   ![创建 SQL 数据库](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. 选择 " **SKU** "，然后选择要使用的相应 SQL 宿主服务器 SKU。 在此示例中，Azure Stack 中心操作员已创建**企业 HA** SKU，以支持 SQL AlwaysOn 可用性组的高可用性。

   ![选择 SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. 选择 "**登录**" > **创建新的登录名**，然后提供要用于新数据库的 SQL 身份验证凭据。 完成后，单击 **"确定"** ，然后单击 "**创建**" 以开始数据库部署过程。

   ![创建登录名](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. 当 SQL 数据库部署成功完成时，查看数据库属性以发现用于连接到新的高可用性数据库的连接字符串。 

   ![查看连接字符串](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>后续步骤

[更新 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)
