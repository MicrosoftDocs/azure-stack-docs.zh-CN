---
title: Azure Stack 集线器上的应用服务 Update 5 发行说明 |Microsoft Docs
description: 了解 Azure Stack 集线器上的应用服务更新5中的改进、修复和已知问题。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 4fb08632d994a1d565e949eb42fa264cf520ced6
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75880441"
---
# <a name="app-service-on-azure-stack-hub-update-5-release-notes"></a>Azure Stack 集线器上的应用服务 Update 5 发行说明

这些发行说明介绍 Azure Stack 中心更新 5 Azure App Service 的改进、修复和已知问题。 已知问题分为三部分：直接与部署相关的问题、更新过程的问题以及生成（安装后）的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.5 之前，将1901更新应用到 Azure Stack 集线器集成系统或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="build-reference"></a>内部版本参考

Azure Stack 集线器 Update 5 内部版本号上的应用服务为**80.0.2.15**。

### <a name="prerequisites"></a>必备组件

开始部署之前，请参阅在[Azure Stack 中心部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

开始将 Azure Stack 中心的 Azure App Service 升级到1.5 之前：

- 确保在 Azure Stack 中心管理员门户的 Azure App Service 管理中，所有角色都已准备就绪。

- 备份应用服务和 master 数据库：
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- 备份租户应用内容文件共享。

- 将**自定义脚本扩展**版本**1.9.1**从 Azure Marketplace 联合。

### <a name="new-features-and-fixes"></a>新增功能和修补程序

Azure Stack 中心 Update 5 上的 Azure App Service 包括以下改进和修补程序：

- **应用服务租户、管理、功能门户和 Kudu 工具**的更新。 与 Azure Stack 中心门户 SDK 版本一致。

- 将**Azure Functions 运行时**更新为**1.0.12205**。

- 对**Kudu 工具**的更新，以解决 Azure Stack 集线器**断开连接**的客户的样式和功能问题。 

- 核心服务的更新，可提高可靠性和错误消息，从而更容易诊断常见问题。

- **以下应用框架和工具的更新**：
  - 添加了 ASP.NET Core 2.1.6 和2.2。0
  - 添加了 NodeJS 10.14。1
  - 添加了 NPM 6.4。1
  - 已将 Kudu 更新为79.20129.3767
  
- **所有角色的基础操作系统更新**：
  - [2019-02 基于 x64 的系统的 Windows Server 2016 累积更新（KB4487006）](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]  
> 如果已向应用服务资源提供程序提供 SQL Always On 实例，则*必须*[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)，并同步数据库，以防止在发生数据库故障转移时任何服务中断。

### <a name="post-update-steps"></a>更新后步骤

对于想要迁移到 Azure Stack 中心部署的现有 Azure App Service 的包含数据库的客户，请在 Azure Stack 中心1.5 更新上的 Azure App Service 完成后执行以下步骤：

> [!IMPORTANT]
> 迁移过程大约需5-10 分钟。 此过程涉及终止现有的数据库登录会话。 计划在迁移后迁移和验证 Azure Stack 中心的 Azure App Service 的停机时间。 如果在 Azure Stack 中心1.3 更新到 Azure App Service 之后完成这些步骤，则不需要执行这些步骤。

1. 将[AppService 数据库（appservice_hosting 和 appservice_metering）添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)。

1. 启用包含的数据库。
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. 将数据库转换为部分包含的数据库时，转换将导致停机，因为需要终止所有活动会话。

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    ```

1. 将登录名迁移到包含的数据库用户。

    ```sql
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

**验证**

1. 检查 SQL Server 是否启用了包含。

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. 检查现有的现有行为。
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 在现有虚拟网络中部署应用服务时，辅助角色无法访问文件服务器，并且文件服务器仅在专用网络上可用。 Azure Stack 中心部署文档的 Azure App Service 中会出现此问题。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加一个出站安全规则，该规则启用辅助子网和文件服务器之间的 SMB 流量。 在管理员门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：

 * 源：任意
 * 源端口范围： *
 * 目标： IP 地址
 * 目标 IP 地址范围：文件服务器的 Ip 范围
 * 目标端口范围：445
 * 协议：TCP
 * 操作：允许
 * 优先级：700
 * 名称： Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心的云管理员操作 Azure App Service 的已知问题

请参阅[Azure Stack 中心1809发行说明](azure-stack-update-1903.md)中的文档。

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 集线器概述](azure-stack-app-service-overview.md)中的 Azure App Service。
- 有关如何准备在 Azure Stack 集线器上部署应用服务的详细信息，请参阅[在 Azure Stack 中心部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
