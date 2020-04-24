---
title: Azure Stack Hub 上的应用服务恢复
description: 了解如何对 Azure Stack Hub 上的应用服务进行灾难恢复。
author: bryanla
ms.topic: article
ms.date: 03/21/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: e04fa2f3e9d03f1982ef67d4d19549b7b73a1f1a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701677"
---
# <a name="app-service-recovery-on-azure-stack-hub"></a>Azure Stack Hub 上的应用服务恢复

本主题提供有关应用服务灾难恢复应采取的操作的说明。

若要从备份恢复 Azure Stack Hub 上的应用服务，必须执行以下操作：
1. 还原应用服务数据库。
2. 还原文件服务器共享内容。
3. 还原应用服务角色和服务。

如果使用 Azure Stack Hub 存储来存储函数应用，则还必须执行还原函数应用的步骤。

## <a name="restore-the-app-service-databases"></a>还原应用服务数据库
应用服务 SQL Server 数据库应在生产就绪的 SQL Server 实例上还原。 

[准备](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance)好用于托管应用服务数据库的 SQL Server 实例之后，请使用以下步骤从备份还原数据库：

1. 使用管理员权限登录到要托管已恢复应用服务数据库的 SQL Server。
2. 从以管理员权限运行的命令提示符，使用以下命令还原应用服务数据库：
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. 验证两个应用服务数据库是否都已成功还原，然后退出 SQL Server Management Studio。

> [!NOTE]
> 若要从故障转移群集实例故障中恢复，请参阅[从故障转移群集实例故障中恢复](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017)。 

## <a name="restore-the-app-service-file-share-content"></a>还原应用服务文件共享内容
[准备](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)好用于托管应用服务文件共享的文件服务器之后，需要从备份还原租户文件共享内容。 可以通过任何可用方法将文件复制到新建的应用服务文件共享位置。 在文件服务器上运行此示例会使用 PowerShell 和 Robocopy 连接到远程共享，并将文件复制到共享：

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

除了复制文件共享内容以外，还必须重置文件共享本身的权限。 若要重置权限，请在文件服务器计算机上打开管理命令提示符，然后运行 **ReACL.cmd** 文件。 **ReACL.cmd** 文件位于 **BCDR** 目录下的应用服务安装文件中。

## <a name="restore-app-service-roles-and-services"></a>还原应用服务角色和服务
还原应用服务数据库和文件共享内容之后，接下来需要使用 PowerShell 还原应用服务角色和服务。 以下步骤将还原应用服务机密和服务配置。  

1. 使用在应用服务安装期间提供的密码，以 **roleadmin** 身份登录到应用服务控制器 **CN0-VM** VM。 
    > [!TIP]
    > 需要修改该 VM 的网络安全组，以允许 RDP 连接。 
2. 将本地的 **SystemSecrets.JSON** 文件复制到控制器 VM。 在下一步骤中，需要提供此文件的路径作为 `$pathToExportedSecretFile` 参数。
3. 在权限提升的 PowerShell 控制台窗口中，使用以下命令还原应用服务角色和服务：

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> 强烈建议在命令完成时关闭此 PowerShell 会话。

## <a name="restore-function-apps"></a>还原函数应用 
适用于 Azure Stack Hub 的应用服务不支持还原租户用户应用，或者除文件共享内容以外的数据。 必须在应用服务备份和还原操作之外备份和恢复所有其他数据。 如果使用 Azure Stack Hub 存储来存储函数应用，则应执行以下步骤来恢复丢失的数据：

1. 创建函数应用使用的新存储帐户。 此存储可以是 Azure Stack Hub 存储、Azure 存储或任何兼容的存储。
2. 检索存储的连接字符串。
3. 打开函数门户，并浏览到该函数应用。
4. 浏览到“平台功能”选项卡，然后单击“应用程序设置”。  
5. 将 **AzureWebJobsDashboard** 和 **AzureWebJobsStorage** 更改为新的连接字符串，然后单击“保存”。 
6. 切换到“概述”。 
7. 重新启动应用。 可能需要多次尝试才能清除所有错误。

## <a name="next-steps"></a>后续步骤
[Azure Stack Hub 上的应用服务概述](azure-stack-app-service-overview.md)