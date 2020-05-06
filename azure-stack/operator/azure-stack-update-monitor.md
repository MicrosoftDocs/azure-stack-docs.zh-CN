---
title: 使用 PowerShell 监视 Azure Stack 集线器中的更新
description: 了解如何使用 PowerShell 监视 Azure Stack 集线器中的更新
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 0adeafb421c30eaf8753c735b16b85471173a5fc
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848126"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>使用 PowerShell 监视 Azure Stack 集线器中的更新

可以使用 Azure Stack Hub 管理终结点来监视和管理更新。 它们可通过 PowerShell 进行访问。 有关在 Azure Stack Hub 上使用 PowerShell 进行设置的说明，请参阅[安装适用于 Azure Stack Hub 的 PowerShell](azure-stack-powershell-install.md)。

可以使用以下 PowerShell cmdlet 来管理更新：

| Cmdlet | 说明 |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.8.0) | 获取可用更新的列表。 |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.8.0)| 获取更新位置列表。 |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.8.0) | 获取更新运行列表。  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.8.0) | 在更新位置应用特定更新。 |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.8.0) | 恢复以前启动的失败的更新运行。 |

## <a name="get-a-list-of-update-runs"></a>获取更新运行列表

若要获取更新运行命令的列表，请执行以下操作：

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>恢复失败的更新操作

如果更新失败，可以通过运行以下命令从停止的地方继续更新运行：

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```
## <a name="troubleshoot"></a>故障排除
有关更新疑难解答的详细信息，请参阅[Azure Stack 疑难解答](azure-stack-troubleshooting.md)

## <a name="next-steps"></a>后续步骤

-   [管理 Azure Stack 集线器中的更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
