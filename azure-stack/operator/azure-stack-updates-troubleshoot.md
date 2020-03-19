---
title: Azure Stack 中心中的更新疑难解答
description: 作为 Azure Stack 中心运营商，了解如何解决更新的问题，以便 Azure Stack 中心尽快返回到生产环境。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 6cf5d83b9fdf71ba68506c760112635910d26eb3
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512309"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Azure Stack 中心修补程序和更新问题进行故障排除的最佳实践

本文概述了对 Azure Stack 集线器修补程序和更新问题进行故障排除的最佳实践，以及修正常见的修补程序和更新问题。


Azure Stack 中心修补程序和更新过程旨在允许操作员以一致且简单的方式应用更新包。 虽然不常见，但在修补和更新过程中可能会出现问题。 如果在修补和更新过程中遇到问题，建议执行以下步骤：

0. **先决条件**：确保已遵循[更新活动清单](release-notes-checklist.md)并已[配置自动日志收集](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)。
1. 按照在更新失败时创建的失败警报中的补救步骤进行操作。
2. 如果列出了问题，请查看[常见的 Azure Stack 中心修补程序和更新问题](#common-azure-stack-hub-patch-and-update-issues)，并采取建议的操作。
3. 如果你无法解决上述步骤中的问题，请创建[Azure Stack 集线器支持票证](azure-stack-help-and-support-overview-tzl.md)。 请确保已为时间跨度收集了出现问题的[日志](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection)。

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>常见 Azure Stack 中心修补程序和更新问题

*适用于： Azure Stack 集线器集成系统*

### <a name="preparationfailed"></a>PreparationFailed

**适用**：此问题适用于所有受支持的版本。

**原因**：尝试安装 Azure Stack 中心更新时，更新的状态可能会失败，并将状态更改为 `PreparationFailed`。 对于连接到 internet 的系统，这通常表明由于 internet 连接薄弱，更新包无法正确下载。 

**修正**：你可以通过单击 "**立即安装**" 来解决此问题。 如果此问题仍然存在，建议使用 "[安装更新](azure-stack-apply-updates.md?#install-updates-and-monitor-progress)" 部分手动上载更新包。

**发生次数**：常见

## <a name="next-steps"></a>后续步骤

- [更新 Azure Stack 中心](azure-stack-updates.md)  
- [Microsoft Azure Stack 中心帮助和支持](azure-stack-help-and-support-overview-tzl.md)
