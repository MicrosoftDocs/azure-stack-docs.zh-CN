---
title: 在 Azure Stack Hub 中使用 Powershell 监视更新
description: 了解如何在 Azure Stack Hub 中使用 Powershell 监视更新。
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 4e80c4677bf2c21689586de574f56ecb413b7f18
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868941"
---
# <a name="monitor-updates-with-powershell-in-azure-stack-hub"></a>在 Azure Stack Hub 中使用 Powershell 监视更新

可以使用 Azure Stack Hub 管理终结点来监视和管理更新。 它们可通过 PowerShell 进行访问。 有关在 Azure Stack Hub 上使用 PowerShell 进行设置的说明，请参阅[安装适用于 Azure Stack Hub 的 PowerShell](powershell-install-az-module.md)。

可以使用以下 PowerShell cmdlet 来管理更新：

| Cmdlet | 说明 |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](/powershell/module/azs.update.admin/get-azsupdate?view=azurestackps-1.8.0) | 获取可用更新的列表。 |
| [Get-AzsUpdateLocation](/powershell/module/azs.update.admin/get-azsupdatelocation?view=azurestackps-1.8.0)| 获取更新位置列表。 |
| [Get-AzsUpdateRun](/powershell/module/azs.update.admin/get-azsupdaterun?view=azurestackps-1.8.0) | 获取更新运行列表。  |
| [Install-AzsUpdate](/powershell/module/azs.update.admin/install-azsupdate?view=azurestackps-1.8.0) | 在更新位置应用特定更新。 |
| [Resume-AzsUpdateRun](/powershell/module/azs.update.admin/resume-azsupdaterun?view=azurestackps-1.8.0) | 恢复以前启动的失败的更新运行。 |

## <a name="get-a-list-of-update-runs"></a>获取更新运行列表

若要获取更新运行列表，请运行以下命令：

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>恢复失败的更新操作

如果更新失败，可以通过运行以下命令从停止的地方继续更新运行：

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="troubleshoot"></a>故障排除

有关对更新进行故障排除的详细信息，请参阅 [Azure Stack 故障排除](azure-stack-troubleshooting.md)。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack Hub 中管理更新](./azure-stack-updates.md)
