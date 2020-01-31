---
title: Azure Stack 集线器上的备份应用服务
description: 了解如何在 Azure Stack 集线器上备份应用服务。
author: bryanla
ms.topic: article
ms.date: 04/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 8e28461f730275b27b9fc252714cdfbfc7be5dc4
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876637"
---
# <a name="back-up-app-service-on-azure-stack-hub"></a>Azure Stack 集线器上的备份应用服务

本文档提供有关如何在 Azure Stack 集线器上备份应用服务的说明。

> [!IMPORTANT]
> Azure Stack 集线器上的应用服务不会作为[Azure Stack 中心基础结构备份](azure-stack-backup-infrastructure-backup.md)的一部分进行备份。 作为 Azure Stack 中心操作员，你必须采取措施以确保可以在必要时成功恢复应用服务。

在规划灾难恢复时，Azure App Service Azure Stack 中心具有四个主要组件需要考虑：
1. 资源提供程序基础结构;服务器角色、辅助角色层等。 
2. 应用服务密码。
3. 应用服务 SQL Server 承载和计量数据库。
4. 应用服务文件共享中存储的应用服务用户工作负荷内容。

## <a name="back-up-app-service-secrets"></a>备份应用服务机密
从备份中恢复应用服务时，需要提供初始部署所使用的应用服务密钥。 成功部署应用服务并将其存储在安全位置后，应立即保存此信息。 使用应用服务恢复 PowerShell cmdlet 在恢复期间从备份重新创建资源提供程序基础结构配置。

按照以下步骤操作，使用管理门户来备份应用服务机密： 

1. 以服务管理员身份登录到 Azure Stack 中心管理员门户。

2. 浏览到**应用服务** -> 的**机密**。 

3. 选择 "**下载机密**"。

   ![在 Azure Stack 中心管理员门户中下载机密](./media/app-service-back-up/download-secrets.png)

4. 当机密可供下载时，单击 "**保存**"，并将应用服务机密（**SystemSecrets**）文件存储在安全的位置。 

   ![在 Azure Stack 中心管理员门户中保存机密](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> 请在每次轮换应用服务机密时重复这些步骤。

## <a name="back-up-the-app-service-databases"></a>备份应用服务数据库
若要还原应用服务，需要**Appservice_hosting**和**Appservice_metering**数据库备份。 建议使用 SQL Server 维护计划或 Azure 备份服务器确保定期备份和保存这些数据库。 但是，可以使用任何方法来确保创建常规的 SQL 备份。

若要在登录到 SQL Server 时手动备份这些数据库，请使用以下 PowerShell 命令：

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> 如果需要备份 SQL AlwaysOn 数据库，请遵循[这些说明](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017)。 

成功备份所有数据库后，请将 .bak 文件复制到安全位置，并将其复制到应用服务机密信息。

## <a name="back-up-the-app-service-file-share"></a>备份应用服务文件共享
应用服务将租户应用信息存储在文件共享中。 必须定期备份此文件共享以及应用服务数据库，以便在需要还原时，尽可能少的数据丢失。

若要备份应用服务文件共享内容，请使用 Azure 备份服务器或其他方法定期将文件共享内容复制到以前保存的所有恢复信息的位置。

例如，你可以使用以下步骤从 Windows PowerShell （而非 PowerShell ISE）控制台会话使用 Robocopy：

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>后续步骤
[在 Azure Stack 中心还原应用服务](app-service-recover.md)