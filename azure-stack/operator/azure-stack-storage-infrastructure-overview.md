---
title: Azure Stack 中心存储基础结构概述
titleSuffix: Azure Stack
description: 了解如何管理 Azure Stack Hub 的存储基础结构。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperfq4
ms.openlocfilehash: a8bc501587c4f4450a07704734391a8e889e3296
ms.sourcegitcommit: 7d4c28353bc138bbae744d9dbca79fe934c2e94b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83624587"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Azure Stack 中心存储基础结构概述

本文提供 Azure Stack 集线器存储基础结构的概念。 其中介绍了驱动器和卷的相关信息，以及它们在 Azure Stack 集线器中的使用方式。

## <a name="drives"></a>驱动器

### <a name="drive-types"></a>驱动器类型

Azure Stack Hub 集成式系统合作伙伴提供众多的解决方案版本，包括各种灵活的存储。 最多可以从三个受支持的驱动器类型中选择**两个**驱动器类型：

1. NVMe （非易失性内存 express）
1. SATA/SAS SSD （固态驱动器）
1. HDD （硬盘驱动器）。

### <a name="performance-vs-capacity"></a>性能与容量

Azure Stack 中心使用 Windows Server 故障转移群集存储空间直通（S2D）。 这种组合提供高性能、可缩放且具有复原能力的存储服务。

Azure Stack 部署可以最大程度地提高存储性能，或平衡性能和容量。

存储空间直通使用缓存来最大程度地提高存储性能。

### <a name="how-drive-types-are-used"></a>如何使用驱动器类型

当 Azure Stack 集线器设备有一个驱动器类型时，所有驱动器将用于容量。

如果有两种驱动器类型，存储空间直通会自动使用 "最快" （NVMe &gt; SSD &gt; HDD）类型的所有驱动器进行缓存。 剩余的驱动器用于提供容量。

### <a name="all-flash-or-hybrid"></a>全部-闪烁或混合

可以将驱动器分组为 "全部刷新" 或 "混合" 部署。

“全部闪存”部署旨在最大化存储性能，不包括旋转式的 HDD。

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image1.png)


混合部署旨在平衡性能和容量或者最大化容量，包括旋转式的 HDD。

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image2.png)

### <a name="caching-behavior"></a>缓存行为

缓存的行为是根据驱动器的类型自动确定的。 为 SSD 提供缓存时（例如，为 SSD 提供 NVMe 缓存），只缓存写入内容。 这可以减小容量驱动器的磨损，减少发往容量驱动器的累积流量，延长驱动器的寿命。

不缓存读取。 之所以不缓存读取内容，是因为读取操作不会严重影响闪存的寿命，并且 SSD 通常提供较低的读取延迟。

为 HDD 提供缓存时（例如，为 HDD 提供 SSD 缓存），会同时缓存读取和写入内容，以便为读取和写入操作提供类似于闪存的延迟（通常可将延迟改善大约 10 倍）。

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image3.svg)

有关存储的可用配置，可以查看 Azure Stack 中心 OEM 合作伙伴（ https://azure.microsoft.com/overview/azure-stack/partners/) 有关详细规范，请参阅。

> [!NOTE]
> 可在混合部署中提供 Azure Stack 集线器设备，同时包含 HDD 和 SSD （或 NVMe）驱动器。 但是，速度较快的驱动器类型将用作缓存驱动器，所有剩余驱动器将以池的形式用作容量驱动器。 租户数据（Blob、表、队列和磁盘）放在容量驱动器上。 预配高级磁盘或选择高级存储帐户类型并不意味着保证将对象分配到 SSD 或 NVMe 驱动器。

## <a name="volumes"></a>卷

存储服务将可用的存储分区成独立的卷，这些卷可分配用于保存系统数据和租户数据。  卷将驱动器合并到存储池中，提供存储空间直通的容错、可伸缩性和性能优势。

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image4.svg)

### <a name="volume-types"></a>卷类型

可在 Azure Stack Hub 存储池中创建三种类型的卷：

1. **基础结构**卷承载 Azure Stack 集线器基础结构 vm 和核心服务使用的文件。
1. **VM 临时**卷承载附加到租户 vm 的临时磁盘，并将该数据存储在这些磁盘上。
1. **对象存储区**卷承载租户数据，提供 blob、表、队列和 VM 磁盘。

### <a name="volumes-in-a-multi-node-deployment"></a>多节点部署中的卷

在多节点部署中，有三个基础结构卷。

VM 临时卷和对象存储卷的数目等于 Azure Stack 中心部署中的节点数：

- 在四节点部署中，有四个等量的 VM 临时卷和四个等量的对象存储卷。

- 如果将新节点添加到群集，则会为这两种资源类型创建新卷。

- 即使节点出现故障或者被删除，卷数也会保持相同。

> [!NOTE]
> 如果使用[Azure Stack 开发工具包（ASDK）](https://docs.microsoft.com/azure-stack/asdk/)，则有一个具有多个[共享](azure-stack-manage-storage-shares.md)的卷。

### <a name="fault-tolerance-and-mirroring"></a>容错和镜像

存储空间直通中的卷提供复原能力来防范硬件问题（例如驱动器或服务器故障）。 它们可在整个服务器维护（如软件更新）中实现连续可用性。

镜像功能通过保存所有数据的多个副本来提供容错。 数据是条带化的，并不是很重要的，但使用镜像存储的任何数据都是完整的。 每个副本将写入不同的物理硬件（位于不同服务器中的不同驱动器），假设每个硬盘各自都有可能发生故障。 

Azure Stack Hub 部署使用三向镜像来确保数据复原能力。 三向镜像可以安全容许至少两个硬件（驱动器或服务器）同时出现问题。 例如，如果你正在重新启动一台服务器，此时另一个驱动器或服务器突然发生故障，在这种情况下，所有数据将保持安全，可供持续访问。

租户数据的三个副本将写入不同的服务器并保存在缓存中：

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image5.png)

## <a name="next-step"></a>后续步骤

[管理存储容量](azure-stack-manage-storage-shares.md) 
