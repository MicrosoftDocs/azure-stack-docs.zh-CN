---
title: Azure Stack 集线器2002第2季度上的应用服务发行说明
description: 了解2002第2季度版本的应用服务在 Azure Stack 集线器上发布的内容、已知问题，以及下载更新的位置。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/30/2020
ms.openlocfilehash: 36c2fabf06df1725938b512752f40f70728dd8e9
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848279"
---
# <a name="app-service-on-azure-stack-hub-2020-q2-release-notes"></a>Azure Stack 集线器2020第2季度上的应用服务发行说明

这些发行说明介绍 Azure Stack 中心2020第2季度和任何已知问题的 Azure App Service 改进和修复。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>内部版本参考

Azure Stack 集线器2020第2季度内部版本号上的应用服务是**87.0.2.10**

## <a name="prerequisites"></a>先决条件

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

开始在 Azure Stack 到2020第2季度 Azure App Service 升级之前：

- 确保所有角色都已准备就绪，可在 Azure Stack 中心管理门户中 Azure App Service 管理

- 在 Azure Stack 中心管理员门户中使用应用服务管理来备份应用服务机密

- 备份应用服务和 Master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - 主设备

- 备份租户应用内容文件共享

  > [!Important]
  > 云操作员负责维护和操作文件服务器，并 SQL Server。  资源提供程序不管理这些资源。  云操作员负责备份应用服务数据库和租户内容文件共享。

- 同步发布市场的**自定义脚本扩展**版本 **1.9.3**



## <a name="updates"></a>更新

Azure Stack Update 第2季度 Azure App Service 包括以下改进和修补程序：

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将**Azure Functions 运行时**更新为**1.0.13021**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - ASP.NET 框架4.7。2
  - ASP.NET Core 3.1。3
  - ASP.NET Core 模块 v2 13.1.19331。0
  - PHP 7.4。2
  - 已将 Kudu 更新为86.20224.4450
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
  - [2020-04 基于 x64 的系统的 Windows Server 2016 累积更新（KB4550929）](https://support.microsoft.com/help/4550929)
  - [2020-04 基于 x64 的系统的 Windows Server 2016 （KB4550994）的服务堆栈更新](https://support.microsoft.com/help/4550994)

- **Windows Server 累积更新现在适用于在部署和升级过程中的控制器角色**

- **更新了新部署的默认虚拟机和规模集 sku**：若要保持与我们的公有云服务保持一致，Azure Stack 中心的 Azure App Service 的新部署将为适用于运行资源提供程序的基础计算机和规模集使用以下 sku
  
  | 角色 | 最小实例数 | 最小 SKU |
  | --- | --- | --- |
  | 控制器 | 2 | Standard_A4_v2-（4核，8192 MB） |
  | 管理 | 1 | Standard_D3_v2-（4核，14336 MB） |
  | 发布者 | 1 | Standard_A2_v2-（2核，4096 MB） |
  | FrontEnd | 1 | Standard_A4_v2-（4核，8192 MB） |
  | 共享辅助角色 | 1 | Standard_A4_v2-（4核，8192 MB） |
  | 小型专用辅助角色 | 1 | Standard_A1_v2-（1个核心，2048 MB） |
  | 中型专用工作线程 | 1 | Standard_A2_v2-（2核，4096 MB） |
  | 大型专用辅助角色 | 1 | Standard_A4_v2-（4核，8192 MB） |

对于 ASDK 部署，可以将实例向下扩展到更低的 Sku，以减少核心和内存提交，但会遇到性能下降。

## <a name="issues-fixed-in-this-release"></a>此版本中已修复的问题

- 如果 SQL Always On 群集故障转移到辅助节点，升级现在将完成
- Azure Stack 中心的 Azure App Service 的新部署不再要求将数据库手动转换为包含的数据库
- 无需手动干预即可正确完成添加其他辅助角色实例或基础结构角色实例
- 添加自定义辅助角色层将正确完成，无需手动干预
- 现在无需门户错误即可删除自定义辅助角色层
- 如果本地磁盘空间用尽，辅助角色将不再标记为 "就绪"
- 检索 Azure 资源管理器证书的超时增加
- 从服务器日志中检索并显示在管理门户中的消息数仅限于最大 Azure 资源管理器请求大小下
- 导致使用情况服务启动问题的超时问题
- 创建 Orchard CMS 站点时已解决的数据库部署问题
- 在部署和升级过程中，控制器现在已更新为 Windows 累积更新
- 自定义域验证失败时，应用服务不再锁定操作

## <a name="pre-update-steps"></a>更新前步骤

查看[更新的已知问题](#known-issues-update)，并采取规定的任何操作。

## <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已经为应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)并同步数据库，以免在进行数据库故障转移时丢失服务。

## <a name="known-issues-update"></a>已知问题（更新）

- 在客户已将 appservice_hosting 和 appservice_metering 数据库转换为包含的数据库的情况下，如果未将登录成功迁移到包含的用户，则升级可能会失败

如果客户已将 appservice_hosting 和 appservice_metering 数据库转换为包含的数据库后部署，并且尚未将数据库登录成功迁移到包含的用户，则可能会遇到升级失败。  

在将 Azure Stack 中心安装上的 Azure App Service 升级到2020第2季度之前，客户必须 SQL Server 针对托管 appservice_hosting 和 appservice_metering 执行以下脚本。  **此脚本是非破坏性脚本，不会导致停机**。

此脚本必须在以下条件下运行

1. 由具有系统管理员权限的用户（例如 SQL SA 帐户）提供;
1. 如果使用 SQL Always on，请确保从包含所有应用服务登录名的 SQL 实例中运行该脚本，格式为：
    - appservice_hosting_FileServer
    - appservice_hosting_HostingAdmin
    - appservice_hosting_LoadBalancer
    - appservice_hosting_Operations
    - appservice_hosting_Publisher
    - appservice_hosting_SecurePublisher
    - appservice_hosting_WebWorkerManager
    - appservice_metering_Common
    - appservice_metering_Operations
    - 所有 WebWorker 登录名，格式 WebWorker_<instance ip address>

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
  - 源：任何
  - 源端口范围：*
  - 目标：IP 地址
  - 目标 IP 地址范围：文件服务器的 IP 范围
  - 目标端口范围：445
  - 协议：TCP
  - 操作：允许
  - 优先级：700
  - 名称：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

- 租户无法使用租户门户中的 "应用服务计划" 视图创建应用服务计划

创建新应用程序时，租户可以在 "创建应用工作流" 过程中创建应用服务计划，或者在更改当前应用的应用服务计划或通过应用服务计划 marketplace 项时创建应用服务计划

- 自定义域在断开连接的环境中不受支持

应用服务针对公共 DNS 终结点执行域所有权验证，因此在断开连接的情况下不支持自定义域。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
