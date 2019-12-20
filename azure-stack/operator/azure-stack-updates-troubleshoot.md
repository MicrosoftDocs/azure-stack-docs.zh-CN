---
title: Azure Stack 中的更新疑难解答 |Microsoft Docs
description: 作为 Azure Stack 运算符，了解如何解决更新的问题，以便 Azure Stack 可以尽快返回到生产环境。
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
ms.date: 09/23/2019
ms.author: mabrigg
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 0021776f5b3be93a82305f41a9aa70c524273e4b
ms.sourcegitcommit: e57a53caac50d1f8762307e065fe886fcda7eadf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2019
ms.locfileid: "75190887"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Azure Stack 中心修补程序和更新问题进行故障排除的最佳实践

本文概述了用于排查 Azure Stack 修补程序和更新问题以及修正常见修补程序和更新问题的最佳实践。


Azure Stack 中心修补程序和更新过程旨在允许操作员以一致且简单的方式应用更新包。 虽然不常见，但在修补和更新问题期间可能会出现问题。 如果在修补和更新过程中遇到问题，建议执行以下步骤：

0. **先决条件**：确保已遵循[更新活动清单](release-notes-checklist.md)并已[配置自动日志收集](azure-stack-configure-automatic-diagnostic-log-collection.md)。
1. 按照在更新失败时创建的失败警报中的补救步骤进行操作。
2. 如果列出了问题，请查看[常见的 Azure Stack 中心修补程序和更新问题](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates-troubleshoot#Common-Azure-Stack-Hub-patch-and-update-issues)，并采取建议的操作。
3. 如果你无法解决上述步骤中的问题，请创建[Azure Stack 支持票证](azure-stack-help-and-support-overview.md)。 请确保已为时间跨度收集了出现问题的[日志](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection)。

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>常见 Azure Stack 中心修补程序和更新问题

*适用于： Azure Stack 集线器集成系统*

### <a name="preparationfailed"></a>PreparationFailed

**适用**：此问题适用于所有受支持的版本。

**原因**：尝试安装 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为 `PreparationFailed`。 对于连接到 internet 的系统，这通常表明由于 internet 连接薄弱，更新包无法正确下载。 

**修正**：你可以通过单击 "**立即安装**" 来解决此问题。 如果此问题仍然存在，建议使用 "[安装更新](azure-stack-apply-updates.md?#install-updates-and-monitor-progress)" 部分手动上载更新包。

**发生次数**：常见

## <a name="next-steps"></a>后续步骤

- [更新 Azure Stack](azure-stack-updates.md)  
- [Microsoft Azure Stack 帮助和支持](azure-stack-help-and-support-overview.md)
