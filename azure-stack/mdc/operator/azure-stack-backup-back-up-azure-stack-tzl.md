---
title: 备份 Azure Stack | Microsoft Docs
description: 了解如何在 Azure Stack 上执行按需备份。
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
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 0455c96449d0547d94d8d904fe59f268145da3a8
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329005"
---
# <a name="back-up-azure-stack"></a>备份 Azure Stack

*适用于：模块化数据中心、Azure Stack 中心耐用*

本文介绍如何执行 Azure Stack 的按需备份。

## <a name="start-backup"></a>启动备份

备份自动进行计划。 仅当收到手动触发备份的警报时才需要启动按需备份。 若要启动按需备份，请选择 " **立即备份** "。 按需备份不会修改已计划的下次备份的时间。 任务完成后，可以在 " **概要" 边栏选项卡** 中确认设置：

![备份设置](media/azure-stack-backup-back-up-azure-stack-tzl/on-demand-backup.png)

## <a name="start-azure-stack-backup"></a>启动 Azure Stack 备份

还可以在 Azure Stack 管理计算机上运行 PowerShell cmdlet **Start-AzsBackup** 。

### <a name="start-a-new-backup-without-job-progress-tracking"></a>启动新备份，而不进行作业进度跟踪

使用 **start-azsbackup** 可立即启动新备份，无作业进度跟踪。

```powershell
Start-AzsBackup -Force
```

## <a name="start-azure-stack-backup-with-job-progress-tracking"></a>启动 Azure Stack 备份，并进行作业进度跟踪

使用 **start-azsbackup** 启动具有 **-AsJob** 参数的新备份，并将其另存为变量以跟踪备份作业进度。

> [!NOTE]
> 备份作业将在作业完成前约 10-15 分钟在门户中显示为成功完成。
>
> 通过以下代码，可更好地观察实际状态。

引入初始1毫秒延迟的原因是代码太快，无法正确注册作业。 该代码不会报告任何 **PSBeginTime** ，也不会报告作业的 **状态** 。

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
    $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Mohs>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
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

使用以下 PowerShell 命令确保备份已成功完成：

```powershell
Get-AzsBackup
```

结果应类似于以下输出：

```shell
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

### <a name="confirm-backup-has-completed-in-the-administrator-portal"></a>在管理员门户中确认备份已完成

使用 Azure Stack 管理员门户按照以下步骤验证备份是否已成功完成：

1. 打开 [Azure Stack 管理员门户](../../operator/azure-stack-manage-portals.md)。

2. 选择 " **所有服务** "，然后在 " **管理** " 类别下选择 " **基础结构备份** "。 在“基础结构备份”  边栏选项卡中选择“配置”  。

3. 在“可用备份”列表中查找备份的 **名称** 和 **完成日期** 。

4. 验证 **状态** 是否为“成功”。

## <a name="next-steps"></a>后续步骤

了解有关 [基础结构备份最佳实践](azure-stack-backup-best-practices-tzl.md)的详细信息。
