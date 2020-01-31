---
title: 使用 Azure Stack 集线器中的监视数据
description: 了解用于从 Azure Stack 中心使用监视数据的选项。
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: bb03193c1c4b061a996e3bef135c6cfc630f36b0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883422"
---
# <a name="consume-monitoring-data-from-azure-stack-hub"></a>使用 Azure Stack 集线器中的监视数据

监视数据在具有 Azure Monitor 管道的单个位置中，就像在全球 Azure 中 Azure Monitor 一样。 但并不是全球 Azure 中的所有监视数据都可以在 Azure Stack 集线器中使用。 本文提供了在 Azure Stack 集线器中使用监视数据的各种方式的摘要。
 
## <a name="options-for-data-consumption"></a>数据使用选项

| 数据类型 | 类别 | 支持的服务 | 访问方法 |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor 平台级指标 | 指标 | [Azure Stack 中心 Azure Monitor 支持的指标](azure-stack-metrics-supported.md) | REST API |
| 计算来宾 OS 指标（例如，性能计数） | 指标 | Windows 和 Linux Vm | 存储表或 blob：<br>Windows 或 Linux Azure 诊断 <br>事件中心：<br>Windows Azure 诊断 |
| 存储度量值 | 指标 | Azure 存储器 | 存储表：<br>存储分析 |
| 活动日志 | 活动 | 所有 Azure 服务 | REST API：<br>Azure Monitor 事件 API |
| 计算来宾 OS 日志（例如，IIS、ETW、一种方法） | 活动 | Windows 和 Linux Vm | 存储表或 blob：<br>Windows 或 Linux Azure 诊断 <br>事件中心：<br>Windows Azure 诊断 |
| 存储日志 | 活动 | Azure 存储器 | 存储表：<br>存储分析 |

## <a name="next-steps"></a>后续步骤

详细了解[Azure Stack 集线器上的 Azure monitor](azure-stack-metrics-azure-data.md)。
