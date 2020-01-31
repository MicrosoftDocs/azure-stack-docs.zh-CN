---
title: 备份 Azure Stack 中心
description: 了解如何在 Azure Stack 集线器上执行按需备份。
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 85006def262ed689aabba741e7f08f679c9f98c4
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76877827"
---
# <a name="back-up-azure-stack-hub"></a>备份 Azure Stack 中心

本文介绍如何在 Azure Stack 中心进行按需备份。 有关配置 PowerShell 环境的说明，请参阅为[Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。 若要登录到 Azure Stack 中心，请参阅[使用 Azure Stack 中心的管理员门户](azure-stack-manage-portals.md)。

## <a name="start-azure-stack-hub-backup"></a>开始 Azure Stack 中心备份

### <a name="start-a-new-backup-without-job-progress-tracking"></a>在不使用作业进度跟踪的情况下启动新备份
使用 Start-azsbackup 可立即启动新备份，无作业进度跟踪。

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-hub-backup-with-job-progress-tracking"></a>通过作业进度跟踪开始 Azure Stack 中心备份
使用 Start-azsbackup 启动具有 **-AsJob**参数的新备份，并将其另存为变量以跟踪备份作业进度。

> [!NOTE]
> 备份作业在门户中显示为已成功完成，大约在该作业完成之前10-15 分钟。
>
> 通过以下代码，可更好地观察实际状态。

> [!IMPORTANT]
> 之所以引入初始1毫秒的延迟，是因为代码太快，无法正确地注册作业，并且它会在无**PSBeginTime**的情况后返回，而不是作业的**状态**。

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
```

## <a name="confirm-backup-has-completed"></a>确认备份已完成

### <a name="confirm-backup-has-completed-using-powershell"></a>使用 PowerShell 确认备份已完成
使用以下 PowerShell 命令来确保备份已成功完成：

```powershell
   Get-AzsBackup
```

结果应类似于以下输出：

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administrator-portal"></a>在管理员门户中确认已完成备份
按照以下步骤，使用 Azure Stack 中心管理员门户验证备份是否已成功完成：

1. 打开[Azure Stack 中心管理员门户](azure-stack-manage-portals.md)。
2. 选择 "**所有服务**"，然后在 "**管理**" 类别下选择 ">**基础结构备份**"。 在 "**基础结构备份**" 边栏选项卡中选择 "**配置**"。
3. 在 "**可用备份**" 列表中查找备份的 "**名称**" 和 "**完成日期**"。
4. 验证**状态**是否为 "**成功**"。

## <a name="next-steps"></a>后续步骤

详细了解用于[从数据丢失事件中恢复](azure-stack-backup-recover-data.md)的工作流。
