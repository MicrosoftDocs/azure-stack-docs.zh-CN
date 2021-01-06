---
title: 使用 PowerShell 启用 Azure Stack 的备份-MDC
description: 了解如何使用 PowerShell 启用基础结构备份服务，以便出现故障时可以还原 Azure Stack。 对于模块化数据中心 (MDC) 。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: f3d28fa9f5610f91ab8b35ad017c836745f5117d
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910868"
---
# <a name="configure-backup-for-azure-stack-with-powershell---modular-data-center-mdc"></a>为 Azure Stack 配置 PowerShell-模块化数据中心 (MDC) 

*适用于：模块化数据中心、Azure Stack 中心耐用*

可以使用 PowerShell 将基础结构备份服务配置为将基础结构备份导出到外部存储位置。 基础结构备份可由支持人员用来修复降级的服务。

## <a name="prepare-powershell-environment"></a>准备 PowerShell 环境

有关配置 PowerShell 环境的说明，请参阅[安装适用于 Azure Stack 的 PowerShell](../../operator/powershell-install-az-module.md)。 若要登录到 Azure Stack，请参阅[配置操作员环境并登录到 Azure Stack](../../operator/azure-stack-powershell-configure-admin.md)。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>提供备份共享、凭据和加密密钥以启用备份

在同一 PowerShell 会话中，通过添加环境变量编辑以下 PowerShell 脚本。 运行更新的脚本，以向基础结构备份服务提供备份共享、凭据和加密密钥。

|变量  |说明  |
|---------|---------|
|$username     | 使用共享驱动器位置具有足够访问权限的域和用户名输入 **用户名**，以便读取和写入文件。 例如，Contoso \\ backupshareuser。        |
|$password     | 键入用户的 **密码**。        |
|$path     | 键入 **备份存储位置** 的路径。 必须使用通用命名约定 (UNC) 字符串表示单独的设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 若要确保备份数据的可用性，设备应放置在单独的位置。        |
|$backupfrequencyinhours     | “频率(小时)”决定了以何频率创建备份。 默认值为 12。 计划程序支持的最小值为4，最大值为12。        |
|$backupretentionperiodindays     | “保留期(天)”决定了备份在外部位置保留多少天。 默认值为 7。 计划程序支持的最小值为2，最大值为14。 超过保留期的备份会自动从外部位置删除。        |
|$encryptioncertpath     | 在部署过程中提供的证书。 在配置外部存储位置时，无需提供新的。 加密证书路径指定 .CER 文件的文件路径，文件中的公钥用于数据加密。        |
|$isbackupschedulerenabled     | 启用/禁用自动备份。 默认情况下，在提供共享和凭据后启用自动备份。        |

## <a name="configure-backup"></a>配置备份

```powershell
# Example username
$username = "domain\backupadmin"

# Example share path
$sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

$password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

# Set the backup settings with the name, password, share, and CER certificate file.
Set-AzsBackupConfiguration -BackupShare path -Username $username -Password $password
```

## <a name="confirm-backup-settings"></a>确认备份设置

在同一 PowerShell 会话中，运行以下命令：

```powershell
Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
```

结果应类似于以下示例输出：

```shell
Path : \\serverIP\AzsBackupStore\contoso.com\seattle
UserName : domain\backupadmin
```

## <a name="update-backup-settings"></a>更新备份设置

在同一 PowerShell 会话中，你可以更新保留期和备份频率的默认值：

```powershell
# Set the backup frequency and retention period values.
$frequencyInHours = 10
$retentionPeriodInDays = 5

Set-AzsBackupConfiguration -BackupFrequencyInHours $backupfrequencyInHours -BackupRetentionPeriodInDays $backupretentionPeriodInDays

Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
```

## <a name="update-encryption-certificate"></a>更新加密证书

在同一 PowerShell 会话中，可以更新用于加密备份数据的加密证书。 只有新的备份将使用新的加密证书。 所有现有备份都将保留以前的证书加密。 请确保保留以前证书的副本至少一个月，以确保已清除使用旧证书加密的以前的备份：

```powershell
#Set the new encryption certificate by providing local path to CER file.
Set-AzsBackupConfiguration -EncryptionCertPath "c:\temp\cert.cer"
```

## <a name="enable-or-disable-automatic-backups"></a>启用或禁用自动备份

在同一 PowerShell 会话中，可以启用或禁用自动备份。 默认情况下启用自动备份。

```powershell
#Disable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $false

#Enable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $true
```

## <a name="next-steps"></a>后续步骤

若要了解如何验证备份是否已运行，请参阅 [在管理门户中确认备份已完成](../../operator/azure-stack-backup-back-up-azure-stack.md)。
