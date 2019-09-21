---
title: Azure Stack Update 3 发行说明中的应用服务 |Microsoft Docs
description: 了解 Azure Stack 上应用服务的更新3中的改进、修复和已知问题。
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
ms.lastreviewed: 08/20/2018
ms.openlocfilehash: d226be9bad3bd6ddf775d8415329ea1fa8099eb0
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159545"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>Azure Stack 上的应用服务更新3发行说明

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍 Azure Stack Update 3 Azure App Service 的改进、修复和已知问题。 已知问题分为三部分：直接与部署相关的问题、更新过程的问题以及生成（安装后）的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.3 之前，将1807更新应用到 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="build-reference"></a>内部版本参考

Azure Stack Update 3 内部版本号的应用服务为**74.0.13698.31**。

### <a name="prerequisites"></a>先决条件

开始部署之前，请参阅[Azure Stack 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

在开始将 Azure Stack Azure App Service 升级到1.3 之前，请确保 Azure Stack 管理员门户的 Azure App Service 管理中已准备好所有角色。

![应用服务角色状态](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>新功能和修复

基于 Azure Stack 的 Azure 应用服务 Update 3 包含以下改进和修复：

- 支持将 SQL Server Always On 用于 Azure App Service 资源提供程序数据库。

- 已将新的环境参数添加到 AADIdentityApp helper 脚本，以帮助定位不同 Azure AD 区域。

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将 **Azure Functions 运行时**更新到 **v1.0.11820**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - 添加了 ASP.NET Core 2.1.2
  - 添加了 NodeJS 10.0.0
  - 添加了 Zulu OpenJDK 8.30.0.1
  - 添加了 Tomcat 8.5.31 和 9.0.8
  - 添加了 PHP 版本：
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - 添加了 Wincache 2.0.0.8
  - 已将适用于 Windows 的 Git 更新到 v 2.17.1.2
  - 已将 Kudu 更新到 74.10611.3437
  
- **对所有角色的基础操作系统的更新**：
  - [适用于基于 x64 的系统的 Windows Server 2016 的服务堆栈更新 (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [适用于基于 x64 的系统的 Windows Server 2016 的 2018-07 累积更新 (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>更新后步骤（可选）

对于希望迁移到 Azure Stack 部署上现有 Azure App Service 的包含数据库的客户，请在 Azure Stack 1.3 更新的 Azure App Service 完成后执行以下步骤：

> [!IMPORTANT]
> 此过程大约需要花费 5-10 分钟。 此过程涉及终止现有的数据库登录会话。 计划停机时间来进行迁移，并在迁移后验证基于 Azure Stack 的 Azure 应用服务
>
>

1. 将[AppService 数据库（appservice_hosting 和 appservice_metering）添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)。

1. 启用包含的数据库。
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. 将数据库转换为部分包含的数据库。 此步骤将导致停机，因为需要终止所有活动会话。

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
    '''

1. Migrate logins to contained database users.

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

**证实**

1. 检查 SQL Server 是否启用了包含。

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. 检查现有的现有行为。
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 当应用服务部署在现有虚拟网络中并且文件服务器仅在专用网络上可用时，工作人员将无法访问文件服务器。 Azure Stack 部署文档的 Azure App Service 中会出现此问题。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加一个出站安全规则，该规则启用辅助子网和文件服务器之间的 SMB 流量。 在管理员门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：

 * 源：任意
 * 源端口范围：*
 * 目标：IP 地址
 * 目标 IP 地址范围：文件服务器的 IP 范围
 * 目标端口范围：445
 * 协议：TCP
 * 操作：Allow
 * 优先级：700
 * 姓名：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure Stack 上的云管理员操作 Azure App Service 的已知问题

请参阅 Azure Stack 1807 发行说明中的文档。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 有关如何准备在 Azure Stack 上部署应用服务的详细信息，请参阅[在 Azure Stack 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
