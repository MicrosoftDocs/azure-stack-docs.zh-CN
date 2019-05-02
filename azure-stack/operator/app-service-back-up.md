---
title: 备份 Azure Stack 上的应用服务 | Microsoft Docs
description: 有关 Azure Stack 应用服务备份的详细指导。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digiomobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/21/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: cb4542d7926fd119fab38d281c6d6e44ecd0029c
ms.sourcegitcommit: cd54fc560ebf2f4efbb42021232716d6c331dba9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "64329573"
---
# <a name="back-up-app-service-on-azure-stack"></a>备份 Azure Stack 上的应用服务

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*  

此文档提供有关如何备份 Azure Stack 上的应用服务的说明。

> [!IMPORTANT]
> Azure Stack 上的应用服务不是作为 [Azure Stack 基础结构备份](azure-stack-backup-infrastructure-backup.md)的一部分备份的。 在必要的情况下，Azure Stack 操作员必须执行相应的步骤来确保应用服务可成功恢复。

规划灾难恢复时，需要考虑到 Azure Stack 上的 Azure 应用服务的四个主要组件：
1. 资源提供程序基础结构、服务器角色、辅助角色层等。 
2. 应用服务机密
3. 应用服务SQL Server 托管和计量数据库
4. 存储在应用服务文件共享中的应用服务用户工作负荷内容   

## <a name="back-up-app-service-secrets"></a>备份应用服务机密
从备份恢复应用服务时，需要提供初始部署所用的应用服务密钥。 成功部署应用服务并将其存储在安全位置后，应立即保存此信息。 在使用应用服务恢复 PowerShell cmdlet 进行恢复期间，将从备份重新创建资源提供程序基础结构配置。

请遵循以下步骤，使用管理门户备份应用服务机密： 

1. 以服务管理员身份登录到 Azure Stack 管理门户。

2. 浏览到“应用服务” -> “机密”。 

3. 选择“下载机密”。

   ![下载机密](./media/app-service-back-up/download-secrets.png)

4. 准备好下载机密时，单击“保存”，并将应用服务机密 (**SystemSecrets.JSON**) 文件存储到安全位置。 

   ![保存机密](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> 每次轮换应用服务机密时都需要重复这些步骤。

## <a name="back-up-the-app-service-databases"></a>备份应用服务数据库
若要还原应用服务，需要创建 **Appservice_hosting** 和 **Appservice_metering** 数据库备份。 我们建议使用 SQL Server 维护计划或 Azure 备份服务器来确保定期安全备份和保存这些数据库。 但是，可以使用任何可确保创建例行 SQL 备份的方法。

若要在登录到 SQL Server 时手动备份这些数据库，可使用以下 PowerShell 命令：

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> 如果需要备份 SQL AlwaysOn 数据库，请遵照[这些说明](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017)操作。 

成功备份所有数据库之后，请将 .bak 文件连同应用服务机密信息一起复制到安全位置。

## <a name="back-up-the-app-service-file-share"></a>备份应用服务文件共享
应用服务将租户应用信息存储在文件共享中。 必须定期将这些信息连同应用服务数据库一起备份，以便在还原时尽量减少丢失的数据量。 

若要备份应用服务文件共享内容，可以使用 Azure 备份服务器或其他方法，定期将文件共享内容复制到上述所有恢复信息的保存位置。 

例如，可以遵循这些步骤从 Windows PowerShell（不是 PowerShell ISE）控制台会话使用 Robocopy：

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>后续步骤
[还原 Azure Stack 上的应用服务](app-service-recover.md)