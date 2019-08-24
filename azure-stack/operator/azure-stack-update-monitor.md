---
title: 使用 Powershell 监视 Azure Stack 中的更新 |Microsoft Docs
description: 了解如何使用 Powershell 监视 Azure Stack 中的更新
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: a49f2f5c6b06f3362e4cb85553b765ea9fea1a72
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70010205"
---
# <a name="monitor-updates-in-azure-stack-using-powershell"></a>使用 Powershell 监视 Azure Stack 中的更新

适用范围：*Azure Stack 集成系统*

可以使用 Azure Stack 管理终结点来监视和管理更新。 它们可通过 PowerShell 进行访问。 有关在 Azure Stack 上设置 PowerShell 的说明, 请参阅[安装适用于 Azure Stack 的 powershell](azure-stack-powershell-install.md)。

你可以使用以下 PowerShell cmdlet 来管理你的更新:

| Cmdlet | 描述 |
|------------------------------------------------------|-------------|
| [AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.7.2) | 获取可用更新的列表。 |
| [AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.7.2)| 获取更新位置的列表。 |
| [AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.7.2) | 获取更新运行的列表。  |
| [安装-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.7.2) | 在更新位置应用特定更新。 |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.7.2) | 恢复失败的以前启动的更新运行。 |

## <a name="get-a-list-of-update-runs"></a>获取更新运行的列表

若要获取 "更新运行" 命令的列表, 请执行以下操作:

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>恢复失败的更新操作

如果更新失败, 你可以运行以下命令, 从中断的位置继续更新运行:

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="next-steps"></a>后续步骤

-   [在 Azure Stack 中管理更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)