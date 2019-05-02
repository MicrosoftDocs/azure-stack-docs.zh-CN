---
title: Azure Stack 上的应用服务 Update 4 发行说明 | Microsoft Docs
description: 了解 Azure Stack 上的应用服务 Update 4 的功能、已知问题和更新下载位置。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/25/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: fb9a6b88832dde48c47ce6361e090a7508e42332
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290447"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>Azure Stack 上的应用服务 Update 4 发行说明

适用于：Azure Stack 集成系统和 Azure Stack 开发工具包

本发行说明介绍 Azure Stack 上的 Azure 应用服务 Update 4 中的改进和修复，以及任何已知问题。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 请将 1809 更新应用于 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包，然后部署 Azure 应用服务 1.4。
>
>

## <a name="build-reference"></a>内部版本参考

Azure Stack 上的应用服务 Update 4 的内部版本号为 **78.0.13698.5**

### <a name="prerequisites"></a>必备组件

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

开始将 Azure Stack 上的 Azure 应用服务升级到 1.4 之前：

- 确保所有角色在 Azure Stack 管理门户的 Azure应用服务管理中处于“就绪”状态

- 备份应用服务和 Master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - Master

- 备份租户应用内容文件共享

- 从市场合成自定义脚本扩展版本 1.9

### <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack 上的 Azure 应用服务 Update 4 包含以下改进和修复：

- 解决了 [CVE 2018-8600](https://aka.ms/CVE20188600) 跨站点脚本漏洞。

- 添加了对应用服务2018-02-01 API 版本的支持

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 更新**Azure Functions 运行时**到**v1.0.11959**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - 添加了 NodeJS 10.6.0
  - 添加了 NPM 6.1.0
  - 添加了 Zulu OpenJDK 8.31.0.2
  - 添加了 Tomcat 8.5.34 和 9.0.12
  - 添加了 PHP 版本：
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Python 版本更新：
    - 2.7.15
    - 3.6.6
  - 已将适用于 Windows 的 Git 更新到 v 2.17.1.2
  - 已将 Kudu 更新到 78.11022.3613
  
- **对所有角色的基础操作系统的更新**：
  - [适用于 x64 系统的 Windows Server 2016 的 2018-10 累积更新 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- 解决了部署 Wordpress、DNN 和 Orchard CMS 库项时的模板验证问题

- 解决了 Azure Stack 轮换 Azure 资源管理器客户端证书时的配置问题

- 还原了应用服务租户门户中跨域资源共享设置中的功能

- 当资源提供程序控制平面无法连接到配置的 SQL Server 实例时，应用服务管理员门户体验中会显示错误消息

- 确保在新的函数应用程序中指定终结点时，也会在自定义存储连接字符串中指定终结点

### <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]  
> 如果已经为应用服务 RP 提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)并同步数据库，以免在进行数据库故障转移时丢失服务。

### <a name="post-update-steps-optional"></a>更新后的步骤（可选）

对于希望为基于 Azure Stack 的现有 Azure 应用服务部署迁移到包含的数据库的客户，请在完成基于 Azure Stack 的 Azure 应用服务 1.4 更新后执行以下步骤：

> [!IMPORTANT]
> 迁移过程大约需要花费 5-10 分钟。  该过程涉及终止现有的数据库登录会话。  计划停机时间来进行迁移，并在迁移后验证 Azure Stack 上的 Azure 应用服务。  如果在更新到 Azure Stack 上的 Azure 应用服务 1.3 之后已完成这些步骤，则无需再次执行这些步骤。
>
>

1. [将 AppService 数据库（appservice_hosting 和 appservice_metering）添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. 启用包含的数据库
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. 将数据库转换为“部分包含”会造成停机，因为需要终止所有活动的会话

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

1. Migrate Logins to Contained Database Users

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

验证

1. 检查 SQL Server 是否启用了包含

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. 检查现有的包含的行为
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 如 Azure Stack 上的 Azure 应用服务部署文档中所述，当应用服务部署在现有虚拟网络中并且文件服务器仅在专用网络上可用时，工作人员将无法访问文件服务器。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理门户中的 WorkersNsg 并添加具有以下属性的出站安全规则：
 * 源：任意
 * 源端口范围：*
 * 目标：IP 地址
 * 目标 IP 地址范围：文件服务器的 IP 范围
 * 目标端口范围：445
 * 协议：TCP
 * 操作：允许
 * 优先级：700
 * 姓名：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

请参阅 [Azure Stack 1809 发行说明](azure-stack-update-1903.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
