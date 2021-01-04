---
title: Azure Stack Hub 存储基础结构概述
titleSuffix: Azure Stack
description: 了解如何管理 Azure Stack Hub 的存储基础结构。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8633e31ac60dfb091997657b02b633d24d39b29d
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697601"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Azure Stack Hub 存储基础结构概述

本文介绍了 Azure Stack Hub 存储基础结构的概念。 其中介绍了驱动器和卷的相关信息，以及它们在 Azure Stack Hub 中的使用方式。

## <a name="drives"></a>驱动器

### <a name="drive-types"></a>驱动器类型

Azure Stack Hub 集成式系统合作伙伴提供众多的解决方案版本，包括各种灵活的存储。 可从三种受支持的驱动器类型中选择最多两种驱动器类型：

1. NVMe（非易失性快速存储器）
1. SATA/SAS SSD（固态硬盘）
1. HDD（磁盘驱动器）。

### <a name="performance-vs-capacity"></a>性能与容量

Azure Stack Hub 结合使用存储空间直通 (S2D) 与 Windows Server 故障转移群集。 这种组合提供高性能、可缩放且具有复原能力的存储服务。

Azure Stack 部署可以最大化存储性能，或平衡性能和容量。

存储空间直通提供缓存用于最大化存储性能。

### <a name="how-drive-types-are-used"></a>如何使用驱动器类型

如果 Azure Stack Hub 设备仅具有一种驱动器类型，则所有驱动器都用于提供容量。

如果有两种驱动器类型，存储空间直通会自动使用所有“最快”(NVMe &gt; SSD &gt; HDD) 类型的驱动器进行缓存。 剩余的驱动器用于提供容量。

### <a name="all-flash-or-hybrid"></a>全闪存或混合

驱动器可以分组成“全部闪存”或“混合”部署。

“全部闪存”部署旨在最大化存储性能，不包括旋转式的 HDD。

![此关系图显示了“全闪存”部署分组。](media/azure-stack-storage-infrastructure-overview/image1.png)


混合部署旨在平衡性能和容量或者最大化容量，包括旋转式的 HDD。

![此关系图显示了混合部署分组。](media/azure-stack-storage-infrastructure-overview/image2.png)

### <a name="caching-behavior"></a>缓存行为

根据驱动器类型自动确定缓存的行为。 为 SSD 提供缓存时（例如，为 SSD 提供 NVMe 缓存），只缓存写入内容。 这可以减小容量驱动器的磨损，减少发往容量驱动器的累积流量，延长驱动器的寿命。

不缓存读取内容。 之所以不缓存读取内容，是因为读取操作不会严重影响闪存的寿命，并且 SSD 通常提供较低的读取延迟。

为 HDD 提供缓存时（例如，为 HDD 提供 SSD 缓存），会同时缓存读取和写入内容，以便为读取和写入操作提供类似于闪存的延迟（通常可将延迟改善大约 10 倍）。

![此关系图显示了 Azure Stack Hub 存储缓存行为。](media/azure-stack-storage-infrastructure-overview/image3.svg)

有关存储的可用配置，可以查看 Azure Stack 中心 OEM 合作伙伴 (https://azure.microsoft.com/overview/azure-stack/partners/) 详细说明。

> [!NOTE]
> 可以在同时采用 HDD 和 SSD（或 NVMe）驱动器的混合部署中提供 Azure Stack Hub 设备。 但是，速度较快的驱动器类型将用作缓存驱动器，所有剩余驱动器将以池的形式用作容量驱动器。 租户数据（Blob、表、队列和磁盘）放在容量驱动器上。 预配高级磁盘或选择高级存储帐户类型并不意味着保证将对象分配到 SSD 或 NVMe 驱动器。

## <a name="volumes"></a>卷

存储服务将可用的存储分区成独立的卷，这些卷可分配用于保存系统数据和租户数据。 卷将驱动器合并到存储池中，提供存储空间直通的容错、可伸缩性和性能优势。

![此关系图显示了 Azure Stack Hub 存储服务分区。](media/azure-stack-storage-infrastructure-overview/image4.svg)

### <a name="volume-types"></a>卷类型

可在 Azure Stack Hub 存储池中创建三种类型的卷：

1. 基础结构卷托管 Azure Stack Hub 基础结构 VM 和核心服务使用的文件。
1. VM 临时卷托管附加到租户 VM 的临时磁盘以及这些磁盘中存储的数据。
1. 对象存储卷托管租户数据服务 Blob、表、队列和 VM 磁盘。

### <a name="volumes-in-a-multi-node-deployment"></a>多节点部署中的卷

多节点部署中有三个基础结构卷。

VM 临时卷和对象存储卷的数目等于 Azure Stack Hub 部署中的节点数目：

- 在四节点部署中，有四个等量的 VM 临时卷和四个等量的对象存储卷。

- 如果将新节点添加到群集，则会为这两种资源类型创建新卷。

- 即使节点出现故障或者被删除，卷数也会保持相同。

> [!NOTE]
> 如果使用 [Azure Stack 开发工具包 (ASDK)](../asdk/index.yml)，则会创建包含多个[共享](azure-stack-manage-storage-shares.md)的单个卷。

### <a name="fault-tolerance-and-mirroring"></a>容错和镜像

存储空间直通中的卷提供复原能力来防范硬件问题（例如驱动器或服务器故障）。 它们可在整个服务器维护期间（例如软件更新）实现持续可用性。

镜像功能通过保存所有数据的多个副本来提供容错。 虽然如何对数据进行条带化和放置并不是无关紧要的，但使用镜像功能存储的任何数据都会完整地写入多次。 每个副本将写入不同的物理硬件（位于不同服务器中的不同驱动器），假设每个硬盘各自都有可能发生故障。 

Azure Stack Hub 部署使用三向镜像来确保数据复原能力。 三向镜像可以安全容许至少两个硬件（驱动器或服务器）同时出现问题。 例如，如果你正在重新启动一台服务器，此时另一个驱动器或服务器突然发生故障，在这种情况下，所有数据将保持安全，可供持续访问。

租户数据的三个副本将写入不同的服务器并保存在缓存中：

![此关系图显示了如何将租户数据的三个副本写入不同服务器。](media/azure-stack-storage-infrastructure-overview/image5.png)

## <a name="next-step"></a>后续步骤

[管理存储容量](azure-stack-manage-storage-shares.md) 
