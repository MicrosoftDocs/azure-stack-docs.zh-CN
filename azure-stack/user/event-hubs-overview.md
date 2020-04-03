---
title: Azure Stack 集线器上的事件中心概述
description: 了解 Azure Stack 集线器上的事件中心资源提供程序。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: c7c1d11d2ef432ddc8b750fde5cb6970c4076db4
ms.sourcegitcommit: 48e493256b0b8bd6cea931cd68a9bd932ca77090
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80614379"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Azure Stack 集线器上的事件中心概述

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

利用 Azure Stack 集线器上的事件中心，你可以实现混合云方案。 支持流式处理和基于事件的解决方案，适用于本地和 Azure 云处理。 无论方案是混合（连接）还是断开连接，解决方案都可支持大规模处理事件/流。 你的方案仅受事件中心群集大小的约束，你可以根据需要对其进行设置。 

## <a name="run-event-processing-tasks-and-build-event-driven-applications-on-site"></a>在站点上运行事件处理任务并生成事件驱动的应用程序

使用 Azure Stack 集线器上的事件中心，可以实现以下业务方案：

- AI 和机器学习工作负荷，其中事件中心是事件流引擎。
- 在 Azure 数据中心之外的网站中实现事件驱动的体系结构。
- 本地部署的 web 应用程序的点击流分析。
- 设备遥测分析。
- 使用 Apache Spark、Flink、风暴和 Samza 等 Apache Kafka 的开源框架进行流处理。
- [使用计算来宾 OS 指标和事件](azure-stack-metrics-monitor.md)。

## <a name="build-hybrid-solutions"></a>构建混合解决方案

构建可在 Azure Stack 中心本地摄取并处理边缘数据的混合解决方案。 将聚合数据发送到 Azure 以便进一步处理、可视化和存储。 如果需要，请在 Azure 上利用无服务器计算。

[![混合解决方案关系图](media/event-hubs-overview/hybrid-architecture-ehoash.png)](media/event-hubs-overview/hybrid-architecture-ehoash.png#lightbox)

## <a name="features"></a>功能 

事件中心版本（在 Azure Stack 中心和 Azure 上）提供了高性能的奇偶校验。 此奇偶校验意味着 Sdk、示例、PowerShell、CLI 和门户提供类似的体验，但有几个不同之处。 下表总结了各版本之间存在的功能可用性的高级别差异。  

| Feature | Azure Stack 集线器上的事件中心 | Azure 事件中心 |
|-|-|-|-|
| 操作员管理员体验 | ✔ | ✘ |
| Kafka 支持 | ✔ | ✔ |
| 一组 Sdk | ✔ | ✔ |
| 使用 Azure Active Directory 授予对事件中心的访问权限 | ✘ | ✔ |
| 捕获功能 | ✘ | ✔ |
| 异地灾难恢复 | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| 自动陀螺形功能 | ✘ | ✔ |

还可以使用 Azure 资源管理器模板、 [PowerShell](/powershell/module/azurerm.eventhub/)和[Azure CLI](/cli/azure/eventhubs/eventhub/)完成 azure 资源管理操作。 目前，PowerShell 和 Azure CLI 中不支持操作员管理操作。

## <a name="feature-documentation"></a>功能文档

[Azure 事件中心文档](/azure/event-hubs/)适用于两个版本的事件中心： Azure Stack 集线器上的事件中心和 Azure 事件中心。 本文档介绍有关使用事件中心和活动的主题，例如：

- 有关[事件中心概念](/azure/event-hubs/event-hubs-features)的详细信息
- 如何[创建事件中心群集和命名空间](event-hubs-quickstart-cluster-portal.md)
- 如何创建[事件中心](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- 如何[使用 Kafka 协议](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)进行流式处理

### <a name="operator-documentation"></a>操作员文档 
 
若要详细了解 Azure Stack 集线器运营商体验的事件中心，请参阅[事件中心操作员文档](/azure-stack/operator/event-hubs-rp-overview)。 此文档提供有关活动的信息，例如：

- 正在安装事件中心。
- 使事件中心对用户可用。
- 获取有关服务运行状况的信息。
- 正在收集日志。


## <a name="next-steps"></a>后续步骤

如果你的订阅中未提供事件中心，请与管理员联系，以[在 Azure Stack 中心资源提供程序上安装事件中心](../operator/event-hubs-rp-overview.md)。

如果已安装事件中心并且已准备好使用它，请参阅[事件中心文档](/azure/event-hubs/event-hubs-about)以获取有关服务的更多详细信息。
