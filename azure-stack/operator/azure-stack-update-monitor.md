---
title: 使用 Powershell 监视 Azure Stack 集线器中的更新
description: 了解如何使用 Powershell 监视 Azure Stack 集线器中的更新
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: fe4f63149af62d60391ab38bffe37cd3d8a39957
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696713"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>使用 Powershell 监视 Azure Stack 集线器中的更新

可以使用 Azure Stack 集线器管理终结点来监视和管理更新。 它们可通过 PowerShell 进行访问。 有关在 Azure Stack 集线器上设置 PowerShell 的说明，请参阅[安装适用于 Azure Stack 中心的 powershell](azure-stack-powershell-install.md)。

你可以使用以下 PowerShell cmdlet 来管理你的更新：

| Cmdlet | 说明 |
|------------------------------------------------------|-------------|
| [AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.8.0) | 获取可用更新的列表。 |
| [AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.8.0)| 获取更新位置的列表。 |
| [AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.8.0) | 获取更新运行的列表。  |
| [安装-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.8.0) | 在更新位置应用特定更新。 |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.8.0) | 恢复失败的以前启动的更新运行。 |

## <a name="get-a-list-of-update-runs"></a>获取更新运行的列表

若要获取 "更新运行" 命令的列表，请执行以下操作：

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>恢复失败的更新操作

如果更新失败，你可以运行以下命令，从中断的位置继续更新运行：

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="next-steps"></a>后续步骤

-   [管理 Azure Stack 集线器中的更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
