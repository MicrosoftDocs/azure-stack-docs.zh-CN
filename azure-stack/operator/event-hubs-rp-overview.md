---
title: Azure Stack 集线器上的事件中心概述
description: 了解 Azure Stack 集线器上的事件中心资源提供程序。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 4a7e7987a5b5b7bb60e6acc882c07b80519bc052
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423945"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Azure Stack 集线器上的事件中心概述

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

利用 Azure Stack 集线器上的事件中心，你可以实现混合云方案。 支持流式处理和基于事件的解决方案，适用于本地和 Azure 云处理。 无论方案是混合（连接）还是断开连接，解决方案都可支持大规模处理事件/流。 方案仅受群集大小的约束，可根据需要进行设置。 

## <a name="features"></a>功能

请参阅在 Azure Stack 与 Azure 事件中心之间的事件中心之间的功能比较[Azure Stack 集线器用户文档](/azure-stack/user/event-hubs-overview)。

## <a name="feature-documentation"></a>功能文档

若要了解有关事件中心用户体验的详细信息，请参阅[Azure 事件中心文档](/azure/event-hubs/)。 本文档适用于两个版本的事件中心，并包含如下主题：

- 有关[事件中心概念](/azure/event-hubs/event-hubs-features)的详细信息
- 如何[创建事件中心群集和命名空间](/azure/event-hubs/event-hubs-dedicated-cluster-create-portal)
- 如何创建[事件中心](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- 如何[使用 Kafka 协议](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)进行流式处理


## <a name="next-steps"></a>后续步骤

开始安装过程之前，请查看[Azure Stack 集线器上的事件中心的容量规划](event-hubs-rp-capacity-planning.md)。 了解容量规划有助于确保用户拥有所需的容量。