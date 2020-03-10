---
title: Azure Stack 集线器上的应用服务 update 8 发行说明
description: 了解 Azure Stack 集线器上的应用服务的 update 8 中的内容、已知问题，以及下载更新的位置。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 03/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 82f43028253638f92866bb679a5ccb5478a5a56e
ms.sourcegitcommit: 8198753ebafd69d0dbfc6b3548defbd70f4c79c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78935080"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Azure Stack 集线器上的应用服务 update 8 发行说明

这些发行说明介绍 Azure Stack 中心更新8和任何已知问题的 Azure App Service 改进和修复。 已知问题分为直接与部署、更新过程和生成（安装后）相关的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.8 之前，将1910更新应用到 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 集线器上的应用服务 Update 8 内部版本号为**86.0.2.13**

### <a name="prerequisites"></a>必备条件

开始部署之前[，请参阅入门文档之前](azure-stack-app-service-before-you-get-started.md)的。

开始在 Azure Stack 到1.8 的 Azure App Service 升级之前：

- 确保在 Azure Stack 管理门户的 "Azure App Service 管理" 中，所有角色都已准备就绪

- 备份应用服务和 Master 数据库：
  - AppService_Hosting;
  - AppService_Metering;
  - 主设备

- 备份租户应用内容文件共享

- 将**自定义脚本扩展**版本**1.9.3**与 Marketplace 联合

### <a name="new-features-and-fixes"></a>新增功能和修补程序

Azure Stack Update 8 上的 Azure App Service 包括以下改进和修补程序：

- **应用服务租户、管理、功能门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将**Azure Functions 运行时**更新为**1.0.12615**。

- 核心服务的更新，可提高可靠性和错误消息，从而更容易诊断常见问题。

- **以下应用程序框架和工具的更新**：
  - ASP.NET Core 3.1。0
  - ASP.NET Core 3.0。1
  - ASP.NET Core 2.2。8
  - ASP.NET Core 模块 v2 13.1.19331。0
  - Azul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 7.1.32
  - PHP 7.2.22
  - PHP 7.3。9
  - 已将 Kudu 更新为85.11024.4154
  - Msdeploy.exe 3.5.80916.15
  - NodeJS 10.16。3
  - NPM 6.9。0
  - 适用于 Windows 2.19.1.0 的 Git

- **所有角色的基础操作系统更新**：
  - [2019-12 基于 x64 的系统的 Windows Server 2016 累积更新（KB4530689）](https://support.microsoft.com/help/4530689)

- **托管磁盘对新部署的支持**

Azure Stack 中心的 Azure App Service 的所有新部署都将对所有虚拟机和虚拟机规模集使用托管磁盘。  所有现有部署将继续使用非托管磁盘。

- **前端负载平衡器强制实施的 TLS 1。2**

在此更新中，将为所有应用程序强制实施**TLS 1.2** 。

### <a name="known-issues-upgrade"></a>已知问题（升级）

- 如果 SQL Server Always On 群集故障转移到辅助节点，则升级将失败

在升级过程中，将使用主连接字符串调用 "检查数据库是否存在"，因为登录名在上一个主节点上。

执行下列操作之一，然后单击安装程序中的 "重试"。

- 从 now 辅助 sql 节点复制 appservice_hostingAdmin 登录名;

**OR**

- 将 SQL 群集故障转移到以前的活动节点。

### <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已向应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)，并同步数据库，以防止在发生数据库故障转移时任何服务中断。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 在现有虚拟网络中部署应用服务时，辅助角色无法访问文件服务器，并且文件服务器仅在专用网络上可用，如 Azure Stack 部署文档中的 Azure App Service 中所述。

  如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以启用辅助网和文件服务器之间的 SMB 流量。 在管理门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：
  - 源：任意
  - 源端口范围： *
  - 目标： IP 地址
  - 目标 IP 地址范围：文件服务器的 Ip 范围
  - 目标端口范围：445
  - 协议：TCP
  - 操作：允许
  - 优先级：700
  - 名称： Outbound_Allow_SMB445

- Azure Stack 中心1.8 上 Azure App Service 的新部署要求将数据库转换为包含的数据库

由于此版本中的回归，应用服务数据库（appservice_hosting 和 appservice_metering）必须在**新**部署时转换为包含的数据库。  这**不会**影响**升级**的部署。

> [!IMPORTANT]
> 此过程大约需5-10 分钟。 此过程涉及终止现有数据库登录会话。 规划在迁移后迁移和验证 Azure Stack 中心的 Azure App Service 的停机时间
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

- 无法扩大工作人员

  新辅助进程无法获取所需的数据库连接字符串。  若要纠正这种情况，请连接到某个控制器实例（例如 CN0），并运行以下 PowerShell 脚本：

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure Stack 上的云管理员操作 Azure App Service 的已知问题

请参阅[Azure Stack 1907 发行说明](azure-stack-release-notes-1907.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 概述 Azure App Service](azure-stack-app-service-overview.md)。
- 有关如何准备在 Azure Stack 上部署应用服务的详细信息，请参阅[Azure Stack 上的应用服务入门](azure-stack-app-service-before-you-get-started.md)。
