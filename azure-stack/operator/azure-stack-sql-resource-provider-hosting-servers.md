---
title: 为 SQL 资源提供程序添加托管服务器
titleSuffix: Azure Stack Hub
description: 了解如何添加宿主服务器以通过 SQL 资源提供程序适配器进行预配。
author: bryanla
ms.topic: article
ms.date: 12/07/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2020
ms.openlocfilehash: 146ce73bb28b70d44f6eff03a135e6a3a9f22249
ms.sourcegitcommit: 62eb5964a824adf7faee58c1636b17fedf4347e9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778200"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>为 SQL 资源提供程序添加托管服务器

可以在 [Azure Stack Hub](azure-stack-overview.md) 中的虚拟机 (VM) 上或者在 Azure Stack Hub 环境外部的 VM 上创建 SQL Server 数据库宿主服务器，前提是 SQL 资源提供程序能够连接到该实例。

> [!NOTE]
> SQL 资源提供程序应在默认提供程序订阅中创建，而 SQL 托管服务器则应在可计费用户订阅中创建。 资源提供程序服务器不应用于托管用户数据库。

## <a name="overview"></a>概述

在添加 SQL 宿主服务器之前，请查看以下强制要求和一般请求。

### <a name="mandatory-requirements"></a>强制要求

* 在 SQL Server 实例上启用 SQL 身份验证。 由于 SQL 资源提供程序 VM 未加入域，它只能使用 SQL 身份验证连接到宿主服务器。
* 将 Azure Stack Hub 上安装的 SQL 实例的 IP 地址配置为“公共”。 资源提供程序和用户（例如 Web 应用）通过用户网络通信，因此需要连接到此网络上的 SQL 实例。

### <a name="general-requirements"></a>一般要求

* 专门指定资源提供程序和用户工作负荷使用的 SQL 实例。 不能使用其他任何使用者正在使用的 SQL 实例。 此限制同样适用于应用服务。
* 为资源提供程序配置具有相应特权级别的帐户（如下所述）。
* 你要负责管理 SQL 实例及其主机。 例如，资源提供程序不会应用更新、处理备份或处理凭据轮换。

### <a name="sql-server-vm-images"></a>SQL Server VM 映像

可通过市场管理功能获取 SQL IaaS VM 映像。 这些映像与 Azure 中提供的 SQL VM 相同。

在使用市场项部署 SQL VM 之前，请确保始终下载最新版本的 **SQL IaaS 扩展**。 IaaS 扩展和相应的门户增强功能可提供自动修补和备份等附加功能。 有关此扩展的详细信息，请参阅[使用 SQL Server 代理扩展在 Azure VM 上自动完成管理任务](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)。

> [!NOTE]
> 对于市场上 Windows 映像中的所有 SQL，SQL IaaS 扩展都是 _必需_ 的；如果没有下载该扩展，则 VM 将无法部署。 该扩展不用于基于 Linux 的 SQL VM 映像。

可以使用其他选项部署 SQL VM，包括 [Azure Stack Hub 快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates)中的模板。

> [!NOTE]
> 必须通过用户订阅而不是默认的提供程序订阅创建安装在多节点 Azure Stack Hub 上的任何宿主服务器。 必须通过用户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器都是可计费的 VM，并且必须具有相应的 SQL 许可证。 服务管理员可以是订阅的所有者。 

### <a name="required-privileges"></a>所需的特权

可以创建一个特权比 SQL sysadmin 更低的管理用户。 该用户只需以下操作的权限：

* 数据库：创建、更改、使用包含（仅限 Always On）、删除、备份
* 可用性组：更改、联接、添加/删除数据库
* 登录名：创建、选择、更改、删除、撤销
* 选择操作：\[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn)、sys.availability_replicas (AlwaysOn)、sys.databases、\[master\].\[sys\].\[dm_os_sys_memory\]、SERVERPROPERTY、\[master\].\[sys\].\[availability_groups\] (AlwaysOn)、sys.master_files

### <a name="additional-security-information"></a>其他安全信息

以下信息提供了其他安全指导：

* 使用 BitLocker 加密所有 Azure Stack Hub 存储，因此 Azure Stack Hub 上的任何 SQL 实例都将使用加密的 Blob 存储。
* SQL 资源提供程序完全支持 TLS 1.2。 确保通过 SQL RP 管理的任何 SQL Server 仅针对 TLS 1.2 进行配置，并且 RP 默认使用该配置。  SQL Server 的所有支持版本都支持 TLS 1.2。 有关详细信息，请参阅[针对 Microsoft SQL Server 的 TLS 1.2 支持](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)。
* 使用 SQL Server 配置管理器设置 **ForceEncryption** 选项，确保与 SQL Server 之间的所有通信始终经过加密。 有关详细信息，请参阅[将服务器配置为强制加密连接](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017&preserve-view=true#to-configure-the-server-to-force-encrypted-connections)。
* 确保任何客户端应用也通过加密的连接进行通信。
* RP 配置为信任 SQL Server 实例使用的证书。

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>通过连接到独立宿主 SQL 服务器来提供容量。

可以运行使用任何版本的 SQL Server 2014 或 SQL Server 2016 的独立（非 HA）SQL 服务器。 确保已准备好拥有 sysadmin 特权的帐户的凭据。 

若要添加已设置的独立宿主服务器，请遵循以下步骤：

1. 以服务管理员身份登录到 Azure Stack Hub 管理员门户。

2. 选择“所有服务”  &gt;“管理资源”  &gt;“SQL 宿主服务器”  。

   ![Azure Stack Hub 管理员门户中的 SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   在 " **SQL 宿主服务器**" 下，你可以将 SQL 资源提供程序连接到将充当资源提供程序后端的 SQL Server 实例。

   ![Azure Stack Hub 管理员门户中的 SQL 适配器仪表板](./media/azure-stack-sql-rp-deploy/sql-rp-hosting-server.png)

3. 单击“添加”  ，然后在“添加 SQL 宿主服务器”  边栏选项卡上提供 SQL Server 实例的连接详细信息。

   > [!IMPORTANT]
   > 不要选择 " **资源组** `system.<region>.sqladapter` "，它是在部署过程中由 SQL 资源提供程序安装程序创建的。 必须为独立宿主服务器提供不同的资源组。 

   ![在 Azure Stack 中心管理员门户中添加 SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sql-rp-new-hosting-server.png)

    （可选）提供实例名称；如果实例未分配到默认端口 1433，请指定端口号。

   > [!NOTE]
   > 只要用户和管理员 Azure 资源管理器可以访问 SQL 实例，资源提供程序就能控制此实例。 必须专门将 SQL 实例分配给资源提供程序。

4. 添加服务器时，必须将其分配到现有的 SKU，或创建新的 SKU。 在“添加宿主服务器”下，选择“SKU”。

   * 若要使用现有 SKU，请选择可用的 SKU，然后选择“创建”。
   * 若要创建 SKU，请选择“+ 创建新 SKU”。 在“创建 SKU”中输入所需的信息，然后选择“确定”。

     ![在 Azure Stack 中心管理员门户中创建 SKU](./media/azure-stack-sql-rp-deploy/sqlrp-new-sku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>使用 SQL Always On 可用性组提供高可用性

配置 SQL Always On 实例需要额外的步骤，并且需要三个 Vm (或物理计算机。 ) 本文假定您已了解 Always On 可用性组。 有关详细信息，请参阅以下文章：

* [在 Azure 虚拟机上引入 SQL Server Always On 可用性组](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [AlwaysOn 可用性组 (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017&preserve-view=true)

> [!NOTE]
> SQL 适配器资源提供程序仅支持适用于 Always On 可用性组的 SQL 2016 SP1 企业版或更高版本的实例。 此适配器配置需要新的 SQL 功能，例如自动种子设定。

### <a name="automatic-seeding"></a>自动种子设定

必须在每个 SQL Server 实例的每个可用性组上启用[自动种子设定](/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)。

若要在所有实例上启用自动种子设定，请在每个辅助实例的主要副本上编辑并运行以下 SQL 命令：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

可用性组必须括在方括号中。

在辅助节点上运行以下 SQL 命令：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>配置包含的数据库身份验证

将包含的数据库添加到可用性组之前，请确保在托管可用性组可用性副本的每个服务器实例上，包含的数据库身份验证服务器选项已设置为 1。 有关详细信息，请参阅 [contained database authentication Server Configuration Option](/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017&preserve-view=true)。

使用以下命令设置每个实例的包含的数据库身份验证服务器选项：

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>添加 SQL Always On 宿主服务器

1. 以服务管理员身份登录到 Azure Stack Hub 管理员门户。

2. 选择“浏览”“管理资源”“SQL 宿主服务器”“+添加”。 &gt;  &gt;  &gt; 

   在“SQL 宿主服务器”下，可将 SQL Server 资源提供程序连接到充当资源提供程序后端的实际 SQL Server 实例。

3. 在表单中填写 SQL Server 实例的连接详细信息。 确保使用 Always On 侦听器的 FQDN 地址（以及可选的端口号和实例名称）。 提供使用 sysadmin 特权配置的帐户的信息。

   > [!IMPORTANT]
   > 不要选择 " **资源组** `system.<region>.sqladapter` "，它是在部署过程中由 SQL 资源提供程序安装程序创建的。 必须为独立宿主服务器提供不同的资源组。 

4. 选中“Always On 可用性组”框，启用 SQL Always On 可用性组实例的支持。

   ![在 Azure Stack 中心管理员门户中启用 Always On 可用性组](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. 将 SQL Always On 实例添加到 SKU。

   > [!IMPORTANT]
   > 不能在同一 SKU 中混合使用独立服务器与 Always On 实例。 尝试在添加第一个托管服务器后混合类型会导致错误。

## <a name="sku-notes"></a>SKU 说明
使用可以描述 SKU 中服务器容量（例如容量和性能）的 SKU 名称。 名称可以协助用户将其数据库部署到相应的 SKU。 例如，可以使用 SKU 名称通过以下特征来区分服务产品/服务：
  
* 高容量
* 高性能
* 高可用性

最佳做法是使 SKU 中的所有宿主服务器具有相同的资源和性能特征。

无法将 SKU 分配到特定的用户或组。

SKU 最长可能需要在一小时后才显示在门户中。 在完全创建 SKU 之前，用户无法创建数据库。

若要编辑 SKU，请参阅 "**所有服务**" "  >  **SQL 适配器**  >  **sku**"。 选择要修改的 SKU，进行任何必要的更改，然后单击“保存”以保存更改。 

若要删除不再需要的 SKU，请参阅 **所有服务**  >  **SQL 适配器**  >  **sku**。 右键单击 SKU 名称，然后选择“删除”将其删除。

> [!IMPORTANT]
> 可能需要长达一小时的时间新的 SKU 才会在用户门户中可用。

## <a name="make-sql-databases-available-to-users"></a>将 SQL 数据库提供给用户使用

创建计划和套餐，使用户能够使用 SQL 数据库。 将 **Microsoft.SqlAdapter** 服务添加到计划，并创建新配额。

> [!IMPORTANT]
> 在用户门户中出现可用的新配额或者强制实施更改的配额可能需要长达两小时的时间。

## <a name="next-steps"></a>后续步骤

[添加数据库](azure-stack-sql-resource-provider-databases.md)
