---
title: 基于 Azure Stack Hub 的应用服务 2020 Q2 发行说明
description: 了解2020第2季度版本的应用服务在 Azure Stack 集线器上发布的内容、已知问题，以及下载更新的位置。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 11/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/30/2020
ms.openlocfilehash: 6534a4539fc4e0fd699b21e84490f1d25be1dfe1
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94785849"
---
# <a name="app-service-on-azure-stack-hub-2020-q2-release-notes"></a>基于 Azure Stack Hub 的应用服务 2020 Q2 发行说明

这些发行说明介绍了基于 Azure Stack Hub 的 Azure 应用服务 2020 Q2 中的改进和修复，以及任何已知问题。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>内部版本参考

基于 Azure Stack Hub 的应用服务 2020 Q2 的内部版本号为 **87.0.2.10**

## <a name="prerequisites"></a>先决条件

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

开始将基于 Azure Stack 的 Azure 应用服务升级到 2020 Q2 之前，请满足以下先决条件：

- 确保所有角色在 Azure Stack Hub 管理门户的 Azure应用服务管理中处于“就绪”状态

- 在 Azure Stack Hub 管理员门户中使用“应用服务管理”来备份应用服务机密

- 备份应用服务和 Master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - Master

- 备份租户应用内容文件共享

  > [!Important]
  > 云操作员负责文件服务器和 SQL Server 的维护和操作。  资源提供程序不管理这些资源。  云操作员负责备份应用服务数据库和租户内容文件共享。

- 同步发布市场的 **自定义脚本扩展** 版本 **1.9.3**



## <a name="updates"></a>更新

基于 Azure Stack 的 Azure 应用服务 Update Q2 包含以下改进和修复：

- 针对 **应用服务租户、管理员、函数门户和 Kudu 工具** 的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将 **Azure Functions 运行时** 更新到 **v1.0.13021**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - ASP.NET Framework 4.7.2
  - ASP.NET Core 3.1.3
  - ASP.NET Core 模块 v2 13.1.19331.0
  - PHP 7.4.2
  - 已将 Kudu 更新到 86.20224.4450
  - NodeJS 
    - 8.17.0
    - 10.19.0
    - 12.13.0
    - 12.15.0
  - NPM
    - 5.6.0
    - 6.1.0
    - 6.12.0
    - 6.13.4
  
- **对所有角色的基础操作系统的更新**：
  - [适用于基于 x64 的系统的 Windows Server 2016 的 2020-04 累积更新 (KB4550929)](https://support.microsoft.com/help/4550929)
  - [适用于基于 x64 的系统的 Windows Server 2016 的 2020-04 服务堆栈更新 (KB4550994)](https://support.microsoft.com/help/4550994)

- **Windows Server 的累积更新现在会在部署和升级过程中应用到控制器角色**

- **更新了新部署的默认虚拟机和规模集 SKU**：为了保持与公有云服务的一致性，基于 Azure Stack Hub 的 Azure 应用服务的新部署将对用于运行资源提供程序的基础计算机和规模集使用以下 SKU
  
  | 角色 | 最小 SKU |
  | --- | --- |
  | 控制器 | Standard_A4_v2 -（4 核，8192 MB） |
  | 管理 | Standard_D3_v2 -（4 核，14336 MB） |
  | 发布者 | Standard_A2_v2 -（2 核，4096 MB） |
  | FrontEnd | Standard_A4_v2 -（4 核，8192 MB） |
  | 共享辅助角色 | Standard_A4_v2 -（4 核，8192 MB） |
  | 小型专用辅助角色 | Standard_A1_v2 -（1 核，2048 MB） |
  | 中型专用辅助角色 | Standard_A2_v2 -（2 核，4096 MB） |
  | 大型专用辅助角色 | Standard_A4_v2 -（4 核，8192 MB） |

对于 ASDK 部署，可以将实例纵向缩减到更低的 SKU，以减少核心和内存提交，但性能会下降。

## <a name="issues-fixed-in-this-release"></a>此版本修复的问题

- 现在，如果 SQL Always On 群集已故障转移到辅助节点，则会完成升级
- 对基于 Azure Stack Hub 的 Azure 应用服务进行全新部署时，不再要求将数据库手动转换为包含的数据库
- 无需手动干预即可正确完成添加其他辅助角色或基础结构角色实例
- 无需手动干预即可正确完成添加自定义辅助角色层
- 现在可以删除自定义辅助角色层而不出现门户错误
- 如果本地磁盘空间用尽，辅助角色将不再标记为“就绪”
- 检索 Azure 资源管理器证书的超时已增加
- 从服务器日志中检索并在管理门户中显示的消息的数目受限于 Azure 资源管理器的最大请求大小
- 导致使用服务启动问题的超时问题
- 已解决了创建 Orchard CMS 站点时出现的数据库部署问题
- 在部署和升级过程中，控制器现在会使用 Windows 累积更新进行更新
- 自定义域验证失败时，应用服务不再锁定操作

## <a name="pre-update-steps"></a>更新前步骤

查看[更新的已知问题](#known-issues-update)，并采取规定的操作。

## <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已经为应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](/sql/database-engine/availability-groups/windows/availability-group-add-a-database)并同步数据库，以免在进行数据库故障转移时丢失服务。

## <a name="known-issues-update"></a>已知问题（更新）

- 在客户已将 appservice_hosting 和 appservice_metering 数据库转换为包含的数据库的情况下，如果未将登录名成功迁移到包含的用户，则升级可能会失败

如果在部署后客户已将 appservice_hosting 和 appservice_metering 数据库转换为包含的数据库，但尚未将数据库登录名成功迁移到包含的用户，则可能会遇到升级失败的情况。  

在将基于 Azure Stack Hub 的 Azure 应用服务安装升级到 2020 Q2 之前，客户必须对托管 appservice_hosting 和 appservice_metering 的 SQL Server 执行以下脚本。  **此脚本是非破坏性的，不会导致停机**。

必须在满足以下条件的情况下运行此脚本

- 此脚本由具有系统管理员权限的用户（例如 SQL SA 帐户）运行；
- 如果使用 SQL Always On，请确保从包含了以下格式的所有应用服务登录名的 SQL 实例中运行该脚本：

    - appservice_hosting_FileServer
    - appservice_hosting_HostingAdmin
    - appservice_hosting_LoadBalancer
    - appservice_hosting_Operations
    - appservice_hosting_Publisher
    - appservice_hosting_SecurePublisher
    - appservice_hosting_WebWorkerManager
    - appservice_metering_Common
    - appservice_metering_Operations
    - 所有 WebWorker 登录名，其格式为 WebWorker_<instance ip address>

```sql
        USE appservice_hosting
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO

        USE appservice_metering
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
```

## <a name="known-issues-post-installation"></a>已知问题（安装后）

- 如 Azure Stack 上的 Azure 应用服务部署文档中所述，当应用服务部署在现有虚拟网络中并且文件服务器仅在专用网络上可用时，工作人员将无法访问文件服务器。

  如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理门户中的 WorkersNsg 并添加具有以下属性的出站安全规则：
  - 源：任意
  - 源端口范围：*
  - 目标：IP 地址
  - 目标 IP 地址范围：文件服务器的 IP 范围
  - 目标端口范围：445
  - 协议：TCP
  - 操作：允许
  - 优先级：700
  - 姓名：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

- 租户无法使用租户门户中的“应用服务计划”视图上的“新建”创建应用服务计划

创建新应用程序时，租户可以在“创建应用”工作流中创建应用服务计划，或者在更改当前应用的应用服务计划时创建应用服务计划，也可通过应用服务计划市场项创建应用服务计划

- 自定义域在离线环境中不受支持

应用服务针对公共 DNS 终结点执行域所有权验证，因此，在离线场景下不支持自定义域。

- 在某些情况下，工作线程无法满足健康检查 (磁盘空间不足) 

在某些情况下，将大量站点分配给辅助角色或站点时，会处理大量请求，辅助角色将在 C:\DWAS\LogFiles. 中生成大量运行时日志文件。  这是因为这些日志文件的清理逻辑中存在 bug。  

若要缓解此问题，请远程访问单个辅助角色并清除文件夹的内容。

此问题已在 [Azure Stack Hub 2020 年3季度的应用服务](app-service-release-notes-2020-Q3.md)中得到解决，我们鼓励客户尽快升级到 2020 Q3 版本。

> [!IMPORTANT]
> 若要更新到 Azure Stack 中心2020第3季度的 Azure App Service， **必须** 升级到 Azure Stack 中心2008

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
