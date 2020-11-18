---
title: Azure Stack 集线器2020第3季度的应用服务发行说明
description: 了解 Azure Stack 集线器上应用服务的 2020 Q3 版本中的内容、已知问题，以及下载更新的位置。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 11/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: f9d575a91c55379326beaac2d76b3400172c8dd0
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94785866"
---
# <a name="app-service-on-azure-stack-hub-2020-q3-release-notes"></a>Azure Stack 集线器2020第3季度的应用服务发行说明

这些发行说明介绍 Azure Stack 中心2020和任何已知问题的 Azure App Service 改进和修复。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>内部版本参考

Azure Stack 中心2020年3季度内部版本号上的应用服务是 **89.0.2.15**

## <a name="prerequisites"></a>先决条件

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

开始将 Azure Stack Azure App Service 升级2020到第3季度之前：

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

Azure Stack Update Q3 上的 Azure App Service 包括以下改进和修补程序：

- 针对 **应用服务租户、管理员、函数门户和 Kudu 工具** 的更新。 与 Azure Stack 门户 SDK 版本一致。

- 为 Web 和函数应用添加全屏创建体验

- 与 Web 应用保持一致的新 Azure Functions 门户体验

- 将 **Azure Functions 运行时** 更新为 **1.0.13154**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - ASP.NET Core 2.1.22
  - ASP.NET Core 2.2.14
  - ASP.NET Core 3.1。8
  - ASP.NET Core 模块 v2 13.1.19331.0
  - Azul OpenJDK
    - 8.42.0.23
    - 8.44.0.11
    - 11.35.15
    - 11.37.17
  - 卷7.55。1
  - 适用于 Windows 2.28.0.1 的 Git
  - Msdeploy.exe 3.5.90702.36
  - NodeJS
    - 14.10.1
  - NPM
    - 6.14.8
  - PHP 7.4。5
  - Tomcat
    - 8.5.47
    - 8.5.51
    - 9.0.273
    - 9.0.31
  - 已将 Kudu 更新为90.21005.4823

- **对所有角色的基础操作系统的更新**：
  - [2020-10 (KB4580346) 为基于 x64 的系统的 Windows Server 2016 累积更新 ](https://support.microsoft.com/help/4580346)
  - [2020-09 (KB4576750) 的适用于基于 x64 的系统的 Windows Server 2016 的服务堆栈更新 ](https://support.microsoft.com/help/4576750)
  - Defender 定义1.325.755。0

- **Windows Server 的累积更新现在会在部署和升级过程中应用到控制器角色**

## <a name="issues-fixed-in-this-release"></a>此版本中已修复的问题

- 租户现在可以使用租户门户中的 "新应用服务计划" 视图创建应用服务计划

- 租户可以在租户门户中管理其应用程序的证书

- 函数监视现在可以从强制 TLS 1.2 的存储终结点中检索数据

- 在安装过程中移动了 "等待管理服务器部署" 步骤，以提高部署和升级的可靠性

- 由于在清理逻辑中出错后工作线程运行时日志文件文件夹大小违反了配额限制，导致工作线程无法完成运行状况检查的问题。  此更新中已修复了清理逻辑。

## <a name="pre-update-steps"></a>更新前步骤

查看[更新的已知问题](#known-issues-update)，并采取规定的操作。

## <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已经为应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](/sql/database-engine/availability-groups/windows/availability-group-add-a-database)并同步数据库，以免在进行数据库故障转移时丢失服务。

## <a name="known-issues-update"></a>已知问题（更新）

- 在客户已将 appservice_hosting 和 appservice_metering 数据库转换为包含的数据库的情况下，如果未将登录名成功迁移到包含的用户，则升级可能会失败

如果在部署后客户已将 appservice_hosting 和 appservice_metering 数据库转换为包含的数据库，但尚未将数据库登录名成功迁移到包含的用户，则可能会遇到升级失败的情况。  

在将 Azure Stack 中心安装上的 Azure App Service 升级到 2020 Q3 之前，客户必须 SQL Server 针对托管 appservice_hosting 和 appservice_metering 执行以下脚本。  **此脚本是非破坏性的，不会导致停机**。

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

- 自定义域在离线环境中不受支持

应用服务针对公共 DNS 终结点执行域所有权验证，因此，在离线场景下不支持自定义域。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
