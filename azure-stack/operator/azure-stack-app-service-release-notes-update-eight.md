---
title: Azure Stack Hub 上的应用服务 Update 8 发行说明
description: Azure Stack Hub 上的 Azure 应用服务 Update 8 发行说明，包括新功能、修复和已知问题。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: f19d64331e7ef64095bc91a04eb817e2f056d3a9
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489702"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Azure Stack Hub 上的应用服务 Update 8 发行说明

这些发行说明介绍 Azure Stack Hub 上的 Azure 应用服务 Update 8 中的新功能、修复和已知问题。 已知问题分为两部分：与升级过程相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 请将 1910 更新应用于 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包 (ASDK)，然后部署 Azure 应用服务 1.8。

## <a name="build-reference"></a>内部版本参考

Azure Stack Hub 上的应用服务 Update 8 的内部版本号为 **86.0.2.13**。

## <a name="prerequisites"></a>先决条件

在开始部署之前，请参阅[在 Azure Stack Hub 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

开始将 Azure Stack Hub 上的 Azure 应用服务升级到 1.8 之前：

- 请确保所有角色在 Azure Stack Hub 管理员门户的 Azure 应用服务管理中处于“就绪”状态。

- 在 Azure Stack Hub 管理员门户中使用应用服务管理来备份应用服务机密

- 备份应用服务和 master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - 主

- 备份租户应用内容文件共享。

  > [!Important]
  > 云操作员负责文件服务器和 SQL Server 的维护和操作。  资源提供程序不管理这些资源。  云操作员负责备份应用服务数据库和租户内容文件共享。

- 同步发布 Azure Stack Hub 市场的自定义脚本扩展版本 **1.9.3** 

## <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack Hub 上的 Azure 应用服务 Update 8 包含以下改进和修复：

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将 Azure Functions 运行时更新到 **v1.0.12615** 。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用框架和工具的更新**：

  - ASP.NET Core 3.1.0
  - ASP.NET Core 3.0.1
  - ASP.NET Core 2.2.8
  - ASP.NET Core 模块 v2 13.1.19331.0
  - Azul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 7.1.32
  - PHP 7.2.22
  - PHP 7.3.9
  - 已将 Kudu 更新到 85.11024.4154
  - MSDeploy 3.5.80916.15
  - NodeJS 10.16.3
  - NPM 6.9.0
  - Git for Windows 2.19.1.0

- **对所有角色的基础操作系统的更新**：
  - [适用于 x64 系统的 Windows Server 2016 的 2019-12 累积更新 (KB4530689)](https://support.microsoft.com/help/4530689)

- **新部署对托管磁盘的支持**

Azure Stack Hub 上的 Azure 应用服务的所有新部署将对所有虚拟机和虚拟机规模集都使用托管磁盘。 所有现有部署将继续使用非托管磁盘。

- **前端负载均衡器实施的 TLS 1.2**

现已对所有应用强制实施 TLS 1.2。

## <a name="known-issues-upgrade"></a>已知问题（升级）

- 如果 SQL Server Always On 群集已故障转移到辅助节点，升级将会失败。

在升级期间，使用主连接字符串检查数据库存在性的调用将会失败，因为登录名位于前一个主节点上。

请执行以下操作之一，然后在安装程序中选择“重试”。

- 从当前的辅助 SQL 节点复制 `appservice_hostingAdmin` 登录名；

    **或者**

- 将 SQL 群集故障转移到前一个活动节点。

## <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已经为应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](/sql/database-engine/availability-groups/windows/availability-group-add-a-database)并同步数据库，以免在进行数据库故障转移时丢失服务。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

- 如 Azure Stack Hub 上的 Azure 应用服务部署文档中所述，当应用服务部署在现有虚拟网络中并且文件服务器仅在专用网络上可用时，工作人员将无法访问文件服务器。

  如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理员门户中的 WorkersNsg 并添加包含以下属性的出站安全规则：

  - 源：任意
  - 源端口范围：*
  - 目标：IP 地址
  - 目标 IP 地址范围：文件服务器的 IP 范围
  - 目标端口范围：445
  - 协议：TCP
  - 操作：允许
  - 优先级：700
  - 姓名：Outbound_Allow_SMB445

- Azure Stack Hub 1.8 上新的 Azure 应用服务部署要求将数据库转换为包含的数据库。

    由于此版本中的功能回退，必须将**新部署**的两个应用服务数据库（appservice_hosting 和 appservice_metering）转换为包含的数据库。  这**不会**影响**已升级的**部署。

    > [!IMPORTANT]
    > 此过程大约需要花费 5-10 分钟。 此过程涉及终止现有的数据库登录会话。 计划停机时间来进行迁移，并在迁移后验证 Azure Stack Hub 上的 Azure 应用服务。

    1. [将 AppService 数据库（appservice_hosting 和 appservice_metering）添加到可用性组](/sql/database-engine/availability-groups/windows/availability-group-add-a-database)。

    1. 启用包含的数据库。

        ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
        ```

    1. 将数据库转换为“部分包含”。 此步骤会导致停机，因为需要终止所有活动会话。

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

    **验证**

    1. 检查 SQL Server 是否启用了包含。

        ```sql
        sp_configure  @configname='contained database authentication'
        ```

    1. 检查现有的包含的行为。

        ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
        ```

- 无法横向扩展辅助角色

  新辅助角色无法获取所需的数据库连接字符串。  若要纠正这种情况，请连接到某个控制器实例（例如 CN0-VM），并运行以下 PowerShell 脚本：

    ```powershell

    [System.Reflection.Assembly]::LoadWithPartialName("Microsoft.Web.Hosting")
    $siteManager = New-Object Microsoft.Web.Hosting.SiteManager

    $builder = New-Object System.Data.SqlClient.SqlConnectionStringBuilder -ArgumentList (Get-AppServiceConnectionString -Type Hosting)
    $conn = New-Object System.Data.SqlClient.SqlConnection -ArgumentList $builder.ToString()

    $siteManager.RoleServers | Where-Object {$_.IsWorker} | ForEach-Object {
        $worker = $_
        $dbUserName = "WebWorker_" + $worker.Name

        if (!$siteManager.ConnectionContexts[$dbUserName]) {
            $dbUserPassword = [Microsoft.Web.Hosting.Common.Security.PasswordHelper]::GenerateDatabasePassword()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "CREATE USER [$dbUserName] WITH PASSWORD = '$dbUserPassword'"
            $command.ExecuteNonQuery()
            $conn.Close()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "ALTER ROLE [WebWorkerRole] ADD MEMBER [$dbUserName]"
            $command.ExecuteNonQuery()
            $conn.Close()

            $builder.Password = $dbUserPassword
            $builder["User ID"] = $dbUserName

            $siteManager.ConnectionContexts.Add($dbUserName, $builder.ToString())
        }
    }

    $siteManager.CommitChanges()

    ```

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>云管理员在操作基于 Azure Stack Hub 的 Azure 应用服务时的已知问题

请参阅 [Azure Stack Hub 1907 发行说明](./release-notes.md?view=azs-2002)中的文档。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅 [Azure Stack Hub 上的 Azure 应用服务和 Azure Functions 概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack Hub 的应用服务的部署准备，请参阅[在 Azure Stack Hub 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
