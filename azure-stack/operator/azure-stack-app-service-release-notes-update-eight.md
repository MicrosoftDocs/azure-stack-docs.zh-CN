---
title: Azure Stack Hub 上的应用服务 Update 8 发行说明
description: 更新 Azure Stack 集线器上应用服务的8发行说明，包括新功能、修复和已知问题。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 03/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: ccbe8abd3a8d427005c34084d875af08ed3f5134
ms.sourcegitcommit: 3fd4a38dc8446e0cdb97d51a0abce96280e2f7b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580091"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Azure Stack Hub 上的应用服务 Update 8 发行说明

这些发行说明介绍 Azure Stack 中心更新 8 Azure App Service 的新功能、修复和已知问题。 已知问题分为两部分：与升级过程相关的问题和生成（安装后）问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.8 之前，将1910更新应用到 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="build-reference"></a>内部版本参考

Azure Stack 集线器 update 8 内部版本号上的应用服务是**86.0.2.13**。

### <a name="prerequisites"></a>必备条件

开始部署之前，请参阅[在 Azure Stack 集线器上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

开始将 Azure Stack 上的 Azure 应用服务升级到 1.8 之前：

- 确保所有角色都已准备好在 Azure Stack 中心管理员门户中 Azure App Service 管理。

- 备份应用服务和 master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - 主设备

- 备份租户应用内容文件共享。

- 将**自定义脚本扩展**版本**1.9.3**与 Azure Stack 中心市场联合。

### <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack update 8 上的 Azure App Service 包括以下改进和修补程序：

- **应用服务租户、管理、功能门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将**Azure 函数运行时**更新到**v 1.0.12615**。

- 核心服务的更新，可提高可靠性和错误消息，从而能够更轻松地诊断常见问题。

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

- **托管磁盘对新部署的支持**

Azure Stack 中心的 Azure App Service 的所有新部署都将对所有虚拟机和虚拟机规模集使用托管磁盘。 所有现有部署将继续使用非托管磁盘。

- **前端负载平衡器强制实施的 TLS 1。2**

现在为所有应用强制实施**TLS 1.2** 。

### <a name="known-issues-upgrade"></a>已知问题（升级）

- 如果 SQL Server Always On 群集故障转移到辅助节点，则升级将失败。

在升级过程中，会使用主连接字符串调用检查数据库是否存在，因为登录名在上一个主节点上。

执行下列操作之一，然后在安装程序中选择 "重试"。

- 从 now `appservice_hostingAdmin`辅助 SQL 节点复制登录名;

    **OR**

- 将 SQL 群集故障转移到前一个活动节点。

### <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已向应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)，并同步数据库，以防止在发生数据库故障转移时任何服务中断。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 在现有虚拟网络中部署应用服务时，辅助角色无法访问文件服务器，并且文件服务器仅在专用网络上可用，如 Azure Stack 部署文档中的 Azure App Service 中所述。

  如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理员门户中的 WorkersNsg 并添加包含以下属性的出站安全规则：

  - 源：任何
  - 源端口范围：*
  - 目标： IP 地址
  - 目标 IP 地址范围：文件服务器的 IP 范围
  - 目标端口范围：445
  - 协议：TCP
  - 操作：允许
  - 优先级：700
  - 名称：Outbound_Allow_SMB445

- Azure Stack 中心1.8 上 Azure App Service 的新部署要求将数据库转换为包含的数据库。

    由于此版本中的功能回退，必须将**新部署**的两个应用服务数据库（appservice_hosting 和 appservice_metering）转换为包含的数据库。  这**不会**影响**已升级的**部署。

    > [!IMPORTANT]
    > 此过程大约需要花费 5-10 分钟。 此过程涉及终止现有的数据库登录会话。 计划在迁移后迁移和验证 Azure Stack 中心的 Azure App Service 的停机时间。

    1. [将 AppService 数据库（appservice_hosting 和 appservice_metering）添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)。

    1. 启用包含的数据库。

        ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
        ```

    1. 将数据库转换为部分包含的数据库。 此步骤会导致停机，因为需要终止所有活动会话。

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

    1. 检查现有的包含的行为。

        ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
        ```

- 无法横向扩展辅助角色

  新辅助角色无法获取所需的数据库连接字符串。  若要解决此问题，请连接到其中一个控制器实例（例如，CN0），并运行以下 PowerShell 脚本：

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

请参阅[Azure Stack 1907 发行说明](azure-stack-release-notes-1907.md)中的文档。

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 集线器概述中的 Azure App Service 和 Azure Functions](azure-stack-app-service-overview.md)。
- 有关如何准备在 Azure Stack 上部署应用服务的详细信息，请参阅[在 Azure Stack 中心部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
