---
title: 添加 SQL 资源提供程序的宿主服务器
titleSuffix: Azure Stack Hub
description: 了解如何通过 SQL 资源提供程序适配器添加用于预配的宿主服务器。
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 7bdbe25e204ca00b31f1932c16aab6cdb815d5e1
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814552"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>添加 SQL 资源提供程序的宿主服务器

只要 SQL 资源提供程序可以连接到实例，你就可以在[Azure Stack 中心](azure-stack-overview.md)中的虚拟机（vm）或 Azure Stack 中心环境外的 VM 上创建 SQL Server 数据库宿主服务器。

> [!NOTE]
> 应在默认提供程序订阅中创建 SQL 资源提供程序，而应在计费用户订阅中创建 SQL 宿主服务器。 不应使用资源提供程序服务器来托管用户数据库。

## <a name="overview"></a>概述

在添加 SQL 宿主服务器之前，请查看以下必需和一般要求。

### <a name="mandatory-requirements"></a>必需的要求

* 对 SQL Server 实例启用 SQL 身份验证。 由于 SQL 资源提供程序 VM 未加入域，因此只能使用 SQL 身份验证连接到宿主服务器。
* 在 Azure Stack 集线器上安装 SQL 实例时，将其 IP 地址配置为公共。 资源提供程序和用户（如 web 应用）通过用户网络进行通信，因此需要连接到此网络上的 SQL 实例。

### <a name="general-requirements"></a>一般要求

* 专门用于资源提供程序和用户工作负荷的 SQL 实例。 不能使用其他任何使用者正在使用的 SQL 实例。 此限制也适用于应用服务。
* 为资源提供程序配置具有适当权限级别的帐户（如下所述）。
* 你负责管理 SQL 实例及其宿主。 例如，资源提供程序不会应用更新、处理备份或处理凭据轮换。

### <a name="sql-server-vm-images"></a>SQL Server VM 图像

可以通过 Marketplace 管理功能获取 SQL IaaS VM 映像。 这些映像与 Azure 中提供的 SQL Vm 相同。

在使用 Marketplace 项部署 SQL VM 之前，请确保始终下载最新版本的**Sql IaaS 扩展**。 IaaS 扩展和对应的门户增强功能提供了其他功能，例如自动修补和备份。 有关此扩展的详细信息，请参阅[在具有 SQL Server 代理扩展的 Azure vm 上自动执行管理任务](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)。

> [!NOTE]
> Marketplace 中的所有 SQL on Windows 映像都_需要_Sql IaaS 扩展;如果你没有下载该扩展，VM 将无法部署。 它不用于基于 Linux 的 SQL VM 映像。

还有其他用于部署 SQL Vm 的选项，包括[Azure Stack 中心快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates)中的模板。

> [!NOTE]
> 安装在多节点 Azure Stack 集线器上的所有宿主服务器都必须从用户订阅创建，而不是默认的提供程序订阅。 必须使用相应的登录名从用户门户或 PowerShell 会话创建它们。 所有宿主服务器都是可计费的 Vm，并且必须具有相应的 SQL 许可证。 服务管理员_可以_是该订阅的所有者。

### <a name="required-privileges"></a>必需的特权

您可以创建具有比 SQL sysadmin 更低权限的管理员用户。 用户仅需要以下操作的权限：

* 数据库：创建、更改、包含包含（仅适用于 Always On）、删除、备份
* 可用性组：改变、联接、添加/删除数据库
* 登录名：创建、选择、更改、删除、撤消
* 选择操作： \[master\]。\[sys\]。\[availability_group_listeners\] （AlwaysOn）、sys.databases availability_replicas （AlwaysOn）、sys.databases \[master\]。\[sys\]。\[dm_os_sys_memory\]、SERVERPROPERTY \[master\]。\[sys\]。\[availability_groups\] （AlwaysOn），sys.databases master_files

### <a name="additional-security-information"></a>其他安全信息

以下信息提供了额外的安全指南：

* 所有 Azure Stack 中心存储都使用 BitLocker 进行加密，因此 Azure Stack 中心的任何 SQL 实例都将使用加密的 blob 存储。
* SQL 资源提供程序完全支持 TLS 1.2。 确保通过 SQL RP 管理的任何 SQL Server_仅_配置为使用 TLS 1.2，并且 RP 将默认为。 SQL Server 的所有支持版本都支持 TLS 1.2。 有关详细信息，请参阅[TLS 1.2 对 Microsoft SQL Server 的支持](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)。
* 使用 SQL Server 配置管理器设置**ForceEncryption**选项，以确保与 SQL Server 的所有通信始终加密。 有关详细信息，请参阅将[服务器配置为强制进行加密连接](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#to-configure-the-server-to-force-encrypted-connections)。
* 确保所有客户端应用程序也通过加密连接进行通信。
* RP 配置为信任 SQL Server 实例使用的证书。

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>通过连接到独立的托管 SQL server 来提供容量

可以使用任何版本的 SQL Server 2014 或 SQL Server 2016 来使用独立（非 HA） SQL server。 请确保具有具有 sysadmin 权限的帐户的凭据。

若要添加已设置的独立宿主服务器，请执行以下步骤：

1. 以服务管理员身份登录到 Azure Stack 中心管理员门户。

2. 选择 "**所有服务**" &gt; &gt; **SQL 宿主服务器**的**管理资源**。

   ![Azure Stack 中心管理员门户中的 SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   在 " **SQL 宿主服务器**" 下，你可以将 SQL 资源提供程序连接到将充当资源提供程序后端的 SQL Server 实例。

   ![Azure Stack 中心管理员门户中的 SQL 适配器仪表板](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. 单击 "**添加**"，然后在 "**添加 SQL 宿主服务器**" 边栏选项卡上提供 SQL Server 实例的连接详细信息。

   ![在 Azure Stack 中心管理员门户中添加 SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    （可选）提供实例名称，并指定端口号（如果未将实例分配给默认端口1433）。

   > [!NOTE]
   > 只要用户和管理员 Azure 资源管理器可以访问 SQL 实例，就可以将其置于资源提供程序的控制之下。 __必须__以独占方式将 SQL 实例分配给资源提供程序。

4. 添加服务器时，必须将它们分配给现有 SKU，或创建新的 SKU。 在 "**添加宿主服务器**" 下，选择 " **sku**"。

   * 若要使用现有 SKU，请选择可用的 SKU，然后选择 "**创建**"。
   * 若要创建 SKU，请选择 " **+ 新建 sku**"。 在 "**创建 SKU**" 中输入所需信息，然后选择 **"确定"** 。

     ![在 Azure Stack 中心管理员门户中创建 SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>使用 SQL Always On 可用性组提供高可用性

配置 SQL Always On 实例需要额外的步骤，并且需要三台 Vm （或物理计算机）。本文假定您已了解 Always On 可用性组。 有关详细信息，请参阅以下文章：

* [在 Azure 虚拟机上引入 SQL Server Always On 可用性组](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [AlwaysOn 可用性组 (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> SQL 适配器资源提供程序_仅_支持 Always On 可用性组的 SQL 2016 SP1 企业版或更高版本的实例。 此适配器配置需要新的 SQL 功能，如自动种子设定。

### <a name="automatic-seeding"></a>自动种子设定

必须在每个 SQL Server 实例的每个可用性组上启用[自动种子设定](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)。

若要对所有实例启用自动种子设定，请在每个辅助实例的主副本上编辑并运行以下 SQL 命令：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

可用性组必须用方括号括起来。

在辅助节点上运行以下 SQL 命令：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>配置包含的数据库身份验证

在将包含的数据库添加到某个可用性组前，请确保在托管该可用性组的可用性副本的每个服务器实例上将包含的数据库身份验证服务器选项设置为 1。 有关详细信息，请参阅 [contained database authentication Server Configuration Option](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017)。

使用以下命令为每个实例设置包含的数据库身份验证服务器选项：

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>添加 SQL Always On 宿主服务器

1. 以服务管理员身份登录到 Azure Stack 中心管理员门户。

2. 选择 "**浏览**" &gt; &gt; **+ Add**&gt; **SQL 宿主服务器**的**管理资源**。

   在 " **SQL 宿主服务器**" 下，你可以将 SQL Server 资源提供程序连接到充当资源提供程序后端的 SQL Server 的实际实例。

3. 在表单中填写 SQL Server 实例的连接详细信息。 请确保使用 Always On 侦听器的 FQDN 地址（和可选的端口号和实例名称）。 提供配置有 sysadmin 权限的帐户的信息。

4. 选中 "Always On 可用性组" 框，启用对 SQL Always On 可用性组实例的支持。

   ![在 Azure Stack 中心管理员门户中启用 Always On 可用性组](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. 将 SQL Always On 实例添加到 SKU。

   > [!IMPORTANT]
   > 不能将独立服务器与同一 SKU 中的 Always On 实例混合使用。 添加第一个宿主服务器后尝试混合类型会导致错误。

## <a name="sku-notes"></a>SKU 说明
使用描述 SKU 中服务器功能的 SKU 名称，如容量和性能。 该名称用作帮助用户将其数据库部署到相应 SKU 的帮助。 例如，你可以使用 SKU 名称通过以下特性来区分服务产品：
  
* 高容量
* 高性能
* 高可用性

作为最佳做法，SKU 中的所有宿主服务器都应具有相同的资源和性能特征。

不能将 Sku 分配给特定用户或组。

Sku 最长可能需要一小时才能在门户中显示。 在完全创建 SKU 之前，用户无法创建数据库。

若要编辑 SKU，请 > **sku**中的 "**所有服务**" > **SQL 适配器**。 选择要修改的 SKU，进行任何必要的更改，然后单击 "**保存**" 以保存更改。 

若要删除不再需要的 SKU，请 > **SQL 适配器** > **sku**中转到 "**所有服务**"。 右键单击 SKU 名称，然后选择 "**删除**" 将其删除。

> [!IMPORTANT]
> 最长可能需要一小时才能在用户门户中使用新 Sku。

## <a name="make-sql-databases-available-to-users"></a>使 SQL 数据库对用户可用

创建计划和产品/服务以使 SQL 数据库可供用户使用。 将**microsoft.sqladapter**服务添加到计划并创建新的配额。

> [!IMPORTANT]
> 可能需要长达两个小时的时间，新配额才能在用户门户中使用，或在强制更改配额之前出现。

## <a name="next-steps"></a>后续步骤

[添加数据库](azure-stack-sql-resource-provider-databases.md)
