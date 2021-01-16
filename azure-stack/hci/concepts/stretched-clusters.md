---
title: 延伸群集概述
description: 了解延伸群集
author: v-dasis
ms.topic: how-to
ms.date: 12/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 8e544ff72338149c4d7ffe7a49c9ea47490a5316
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254919"
---
# <a name="stretched-clusters-overview"></a>延伸群集概述

> 适用于 Azure Stack HCI 版本 v20H2

用于灾难恢复的 Azure Stack HCI 延伸群集解决方案提供自动故障转移以快速恢复生产，而无需手动干预。 存储副本提供跨站点的卷复制以进行灾难恢复，所有服务器保持同步。

存储副本支持同步和异步复制：

- 同步复制使用在故障时保持一致的卷在低延迟网络中跨站点创建数据镜像，以确保在发生故障时不会在文件系统级别上丢失任何数据。
- 异步复制通过较高延迟网络链接映射都市范围外的站点之间的数据，但不保证在出现故障时两个站点具有完全相同的数据副本。

>[!NOTE]
> 对于异步复制，需要在故障转移后，手动将目标卷置于另一个站点中。 有关详细信息，请参阅 [异步复制](/windows-server/storage/storage-replica/storage-replica-overview#asynchronous-replication)。

有两种类型的延伸群集：主动-被动和主动-主动。 可设置主动-被动站点复制，其中有一个首选站点和复制方向。 主动-主动复制是指可从任一站点进行双向复制。 本文仅介绍主动/被动配置。

简单地说，主动站点具有资源并为客户端提供要连接的角色和工作负载。 被动站点不为客户端提供任何角色或工作负载，并且将等待来自主动站点的故障转移以进行灾难恢复。

这两个站点可位于两个不同的州、不同的城市、不同的楼层或不同的房间。 使用两个站点的延伸群集在某个站点出现中断或故障时，提供灾难恢复和业务连续性。

使用 Azure Stack HCI，花几分钟时间观看延长群集上的视频：
> [!VIDEO https://www.youtube.com/embed/rYnZL1wMiqU]

## <a name="active-passive-stretched-cluster"></a>主动-被动延伸群集

下图显示站点 1 是主动站点，复制到站点 2（单向复制）。

:::image type="content" source="media/stretched-cluster/active-passive-stretched-cluster.png" alt-text="主动/被动延伸群集方案"  lightbox="media/stretched-cluster/active-passive-stretched-cluster.png":::

## <a name="active-active-stretched-cluster"></a>主动-主动延伸群集

下图显示站点 1 和站点 2 均是主动站点，双向复制到其他站点。

:::image type="content" source="media/stretched-cluster/active-active-stretched-cluster.png" alt-text="主动/主动延伸群集方案" lightbox="media/stretched-cluster/active-active-stretched-cluster.png":::

## <a name="next-steps"></a>后续步骤

- 详细了解存储副本。 请参阅 [存储副本概述](/windows-server/storage/storage-replica/storage-replica-overview)。
- 了解更多有关使用存储副本的信息。 [有关一般使用群集，请参阅配置 Hyper-v 故障转移群集或文件服务器](/windows-server/storage/storage-replica/stretch-cluster-replication-using-shared-storage#configure-a-hyper-v-failover-cluster-or-a-file-server-for-a-general-use-cluster)。
- 了解有关延伸群集的硬件和其他要求。 请参阅[系统要求](system-requirements.md)。
- 了解如何使用 Windows Admin Center 部署延伸群集。 请参阅[使用 Windows Admin Center 创建群集](../deploy/create-cluster.md)。
- 了解如何使用 PowerShell 部署延伸群集。 请参阅[使用 PowerShell 创建群集](../deploy/create-cluster-powershell.md)。
- 了解如何为延伸群集创建卷和设置复制。 请参阅[为延伸群集创建卷和设置复制](../manage/create-stretched-volumes.md)。