---
title: 在 Azure Stack Hub 中排查更新问题
description: 作为 Azure Stack Hub 操作员，了解如何解决更新问题，以便 Azure Stack Hub 可以尽快恢复生产状态。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 04/06/2020
ms.author: inhenkel
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 79512b70641626f6e7cc99eb7e92aa0a20f368fb
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80682156"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>排查 Azure Stack Hub 修补程序和更新问题的最佳做法

本文概述了 Azure Stack 集线器修补程序和更新问题进行故障排除的最佳实践，以及对常见修补程序和更新问题的补救措施。

Azure Stack Hub 修补程序和更新过程旨在让操作员以一致且简单的方式应用更新包。 虽然不常见，但在修补和更新过程中可能会出现问题。 如果在修补和更新过程中遇到问题，建议执行以下步骤：

0. **先决条件**：请确保已遵循[更新活动清单](release-notes-checklist.md)，并[配置“日志自动收集”](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)。
1. 按照在更新失败时创建的失败警报中的补救步骤进行操作。
2. 查看[常见的 Azure Stack Hub 修补程序和更新问题](#common-azure-stack-hub-patch-and-update-issues)，并在问题已列出的情况下采取建议的措施。
3. 如果按照上述步骤无法解决问题，请创建 [Azure Stack Hub 支持票证](azure-stack-help-and-support-overview-tzl.md)。 在出现问题时，请确保为时间跨度[收集日志](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)。

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>常见 Azure Stack Hub 修补程序和更新问题

*适用于：Azure Stack Hub 集成系统*

### <a name="preparationfailed"></a>PreparationFailed

**适用于**：此问题适用于所有支持的版本。

**原因：** 尝试安装 Azure Stack Hub 更新时，更新的状态可能会失败并将状态更改为 `PreparationFailed`。 对于连接到 Internet 的系统，这通常表明由于 Internet 连接不稳定，无法正确下载更新包。 

**补救措施**：可以通过再次单击“立即安装”  来解决此问题。 如果此问题仍然存在，建议按照[安装更新](azure-stack-apply-updates.md?#install-updates-and-monitor-progress)部分的说明手动上传更新包。

**发生率**：通用

## <a name="next-steps"></a>后续步骤

- [更新 Azure Stack Hub](azure-stack-updates.md)  
- [Microsoft Azure Stack Hub 帮助和支持](azure-stack-help-and-support-overview-tzl.md)
