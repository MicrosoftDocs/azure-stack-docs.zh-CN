---
title: Azure Stack 集线器上的事件中心概述
description: 了解 Azure Stack 集线器上的事件中心。 请参阅如何构建混合解决方案。 在 Azure Stack 集线器上比较 Azure 事件中心和事件中心的功能。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: adb00ebbe3f5ea3cf233ebaa485913d48ea63e23
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546950"
---
# <a name="overview-of-event-hubs-on-azure-stack-hub"></a>Azure Stack 集线器上的事件中心概述

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

使用 Azure Stack Hub 上的事件中心可以实现混合云方案。 支持使用基于流式处理和事件的解决方案进行本地处理和 Azure 云处理。 无论方案是混合（联网）还是离线的，解决方案都支持大规模的事件/流处理。 方案仅受事件中心群集大小的约束，但你可以根据需要预配群集大小。 

## <a name="run-event-processing-tasks-and-build-event-driven-applications-on-site"></a>在站点上运行事件处理任务并生成事件驱动的应用程序

使用 Azure Stack Hub 上的事件中心，可以实现如下业务方案：

- AI 和机器学习工作负荷，其中的事件中心是事件流式处理引擎。
- 在 Azure 数据中心之外你自己的站点中实现事件驱动的体系结构。
- 本地部署的 Web 应用程序的点击流分析。
- 设备遥测分析。
- 通过那些使用 Apache Kafka 的开源框架（如 Apache Spark、Flink、Storm 和 Samza）进行流处理。
- [使用计算来宾 OS 指标和事件](azure-stack-metrics-monitor.md)。

## <a name="build-hybrid-solutions"></a>构建混合解决方案

构建在 Azure Stack Hub 上以本地方式引入和处理边缘数据的混合解决方案。 将聚合数据发送到 Azure 进行进一步处理、可视化和存储。 在适当情况下，利用 Azure 上的无服务器计算。

[![混合解决方案关系图](media/event-hubs-overview/hybrid-architecture-ehoash.png)](media/event-hubs-overview/hybrid-architecture-ehoash.png#lightbox)

## <a name="features"></a>功能 

（Azure Stack Hub 和 Azure 上的）事件中心版本提供高度的功能奇偶一致性。 这种奇偶一致性意味着 SDK、示例、PowerShell、CLI 和门户提供类似的体验（差异很小）。 下表总结了各版本之间存在的功能可用性的大致差异。  

| 功能 | Azure Stack Hub 上的事件中心 | Azure 事件中心 |
|-|-|-|-|
| 操作员管理员体验 | ✔ | ✘ |
| Kafka 支持 | ✔ | ✔ |
| 同一套 SDK | ✔ | ✔ |
| 使用 Azure Active Directory 授予对事件中心的访问权限 | ✘ | ✔ |
| 捕获功能 | ✘ | ✔ |
| 异地灾难恢复 | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| 自动膨胀功能 | ✘ | ✔ |

还可以使用 Azure 资源管理器模板、[PowerShell](/powershell/module/Az.eventhub/) 和 [Azure CLI](/cli/azure/eventhubs/eventhub/) 完成 Azure 资源管理操作。 目前，PowerShell 和 Azure CLI 不支持操作员管理操作。

## <a name="feature-documentation"></a>功能文档

[Azure 事件中心文档](/azure/event-hubs/)适用于以下两个版本的事件中心：Azure Stack Hub 上的事件中心和 Azure 事件中心。 此文档介绍的主题涉及如何使用事件中心和活动，例如：

- 有关[事件中心概念](/azure/event-hubs/event-hubs-features)的详细信息
- 如何[创建事件中心群集和命名空间](event-hubs-quickstart-cluster-portal.md)
- 如何创建[事件中心](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- 如何[使用 Kafka 协议](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)进行流式传输

### <a name="operator-documentation"></a>操作员文档 
 
若要详细了解 Azure Stack Hub 上的事件中心操作员体验，请参阅[事件中心操作员文档](../operator/event-hubs-rp-overview.md)。 此文档提供有关活动的信息，例如：

- 安装事件中心。
- 使事件中心可供用户使用。
- 获取有关该服务运行状况的信息。
- 收集日志。


## <a name="next-steps"></a>后续步骤

如果事件中心在订阅中不可用，请与管理员协作，[在 Azure Stack Hub 资源提供程序上安装事件中心](../operator/event-hubs-rp-overview.md)。

如果已安装事件中心并已准备好使用它，请参阅[事件中心文档](/azure/event-hubs/event-hubs-about)，了解有关该服务的更多详细信息。
