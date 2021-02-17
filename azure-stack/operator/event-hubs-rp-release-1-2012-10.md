---
title: Azure Stack 集线器上的事件中心1.2012.1.0 发行说明
description: 了解 Azure Stack 集线器上的事件中心的1.2012.1.0 版本，包括 bug 修复、功能以及如何安装更新。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2021
ms.reviewer: kalkeea
ms.lastreviewed: 02/15/2021
ms.openlocfilehash: ef28447b87c0647b65c996112c5739bf7c6caccf
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563489"
---
# <a name="event-hubs-on-azure-stack-hub-1201210-release-notes"></a>Azure Stack 集线器上的事件中心1.2012.1.0 发行说明

这些发行说明介绍了 Azure Stack 集线器版本1.2012.1.0 和任何已知问题的事件中心中的改进和修复。 

[!INCLUDE [Azure Stack Hub update reminder](../includes/event-hubs-hub-update-banner.md)]

## <a name="updates-in-this-release"></a>此版本中的更新

此版本包括以下更新：

- 对于 Azure 门户 SDK 开发人员，现在支持门户版本5.0.303.7361。
- 事件中心群集的内部日志记录改进。

## <a name="issues-fixed-in-this-release"></a>此版本中已修复的问题

此版本包括以下修复：

- 修复了事件中心群集的升级顺序，以解决升级问题。
- 当群集处于 "正在升级" 或 "升级失败" 状态时，事件中心群集的群集运行状况和备份运行状况检查未运行。 此版本已修复此问题。
- 修复了导致使用记录包含错误数量的 bug。 我们)  (，而不是核心。 以前，1CU 群集会在每小时使用中显示1个核心。 现在，用户可以在每小时使用中看到一个 1 CU 群集的正确数量的10个核心。

## <a name="known-issues"></a>已知问题 

此版本没有已知问题。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请从 [Azure Stack 中心操作员概述的事件中心](event-hubs-rp-overview.md)开始。

