---
title: Azure Stack Hub 上的事件中心概述
description: 了解 Azure Stack Hub 上的事件中心资源提供程序。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2c7574ef54176e4e4b91f5bee8af9e6953d13732
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742635"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Azure Stack Hub 上的事件中心概述

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

使用 Azure Stack Hub 上的事件中心可以实现混合云方案。 支持使用基于流式处理和事件的解决方案进行本地处理和 Azure 云处理。 无论方案是混合（联网）还是离线的，解决方案都支持大规模的事件/流处理。 方案仅受群集大小的限制，可以根据需要进行设置。 

## <a name="features"></a>功能

请参阅 [Azure Stack Hub 用户文档](../user/event-hubs-overview.md)，以便在 Azure Stack 上的事件中心与 Azure 事件中心之间进行功能比较。

## <a name="feature-documentation"></a>功能文档

若要详细了解事件中心用户体验，请参阅 [Azure 事件中心文档](/azure/event-hubs/)。 此文档适用于两个版本的事件中心，并且包含以下主题：

- 有关[事件中心概念](/azure/event-hubs/event-hubs-features)的详细信息
- 如何[创建事件中心群集和命名空间](/azure/event-hubs/event-hubs-dedicated-cluster-create-portal)
- 如何创建[事件中心](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- 如何[使用 Kafka 协议](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)进行流式传输


## <a name="next-steps"></a>后续步骤

在开始安装过程之前，请查看 [Azure Stack Hub 上事件中心的容量规划](event-hubs-rp-capacity-planning.md)。 了解容量规划有助于确保用户拥有所需的容量。
