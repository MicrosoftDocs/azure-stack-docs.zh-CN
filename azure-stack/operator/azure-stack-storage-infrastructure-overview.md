---
title: Azure Stack 中心存储基础结构概述
titleSuffix: Azure Stack
description: 了解如何管理 Azure Stack Hub 的存储基础结构。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 5/5/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.openlocfilehash: de6da5b42a88d2c1f9689fe8c43d898bc7a0bf5e
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848160"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Azure Stack 中心存储基础结构概述

本文概述 Azure Stack 集线器存储基础结构。

## <a name="understand-drives-and-volumes"></a>了解驱动器和卷

### <a name="drives"></a>驱动器

由 Windows Server 软件驱动的 Azure Stack Hub 定义了存储功能，其中包括存储空间直通 (S2D) 和 Windows Server 故障转移群集的组合。 这种组合提供高性能、可缩放且具有复原能力的存储服务。

Azure Stack Hub 集成式系统合作伙伴提供众多的解决方案版本，包括各种灵活的存储。 当前最多可以从三个支持的驱动器类型中选择两个驱动器类型： NVMe （非易失性内存 express）、SATA/SAS SSD （固态驱动器）、HDD （硬盘驱动器）。 

存储空间直通提供缓存用于最大化存储性能。 在具有一种驱动器类型（即 NVMe 或 SSD）的 Azure Stack 集线器设备上，所有驱动器都用于容量。 如果有两种驱动器类型，存储空间直通会自动使用 "最快" （NVMe &gt; SSD &gt; HDD）类型的所有驱动器进行缓存。 剩余的驱动器用于提供容量。 驱动器可以分组成“全部闪存”或“混合”部署：

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image1.png)

“全部闪存”部署旨在最大化存储性能，不包括旋转式的 HDD。

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image2.png)

混合部署旨在平衡性能和容量或者最大化容量，包括旋转式的 HDD。

根据要提供缓存的驱动器类型自动确定缓存的行为。 为 SSD 提供缓存时（例如，为 SSD 提供 NVMe 缓存），只缓存写入内容。 这可以减小容量驱动器的磨损，减少发往容量驱动器的累积流量，延长驱动器的寿命。 同时，不会缓存读取内容。 之所以不缓存读取内容，是因为读取操作不会严重影响闪存的寿命，并且 SSD 通常提供较低的读取延迟。 为 HDD 提供缓存时（例如，为 HDD 提供 SSD 缓存），会同时缓存读取和写入内容，以便为读取和写入操作提供类似于闪存的延迟（通常可将延迟改善大约 10 倍）。

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image3.png)

有关存储的可用配置，可以查看 Azure Stack 中心 OEM 合作伙伴（https://azure.microsoft.com/overview/azure-stack/partners/)有关详细规范，请参阅。

> [!Note]  
> 可以在同时采用 HDD 和 SSD（或 NVMe）驱动器的混合部署中提供 Azure Stack Hub 设备。 但是，速度较快的驱动器类型将用作缓存驱动器，所有剩余驱动器将以池的形式用作容量驱动器。 租户数据（Blob、表、队列和磁盘）放在容量驱动器上。 预配高级磁盘或选择高级存储帐户类型并不意味着保证将对象分配到 SSD 或 NVMe 驱动器。

### <a name="volumes"></a>卷

存储服务将可用的存储分区成独立的卷，这些卷可分配用于保存系统数据和租户数据。  卷将驱动器合并到存储池中，提供存储空间直通的容错、可伸缩性和性能优势。

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image4.png)

可在 Azure Stack Hub 存储池中创建三种类型的卷：

- 基础结构卷：托管 Azure Stack Hub 基础结构 VM 和核心服务使用的文件。

- VM 临时卷：托管附加到租户 VM 的临时磁盘以及这些磁盘中存储的数据。

- 对象存储卷：托管租户数据服务 Blob、表、队列和 VM 磁盘。

在多节点部署中可以看到三个基础结构卷，VM 临时卷和对象存储卷的数量等于 Azure Stack Hub 部署中的节点数量：

- 在四节点部署中，有四个等量的 VM 临时卷和四个等量的对象存储卷。

- 如果将新节点添加到群集，则会为这两种资源类型创建新卷。

- 即使节点出现故障或者被删除，卷数也会保持相同。

- 如果使用 Azure Stack 开发工具包，则会创建包含多个共享的单个卷。

存储空间直通中的卷提供复原能力来防范硬件问题（例如驱动器或服务器故障）。 它们还可在整个服务器维护期间（例如软件更新）实现持续可用性。 Azure Stack Hub 部署使用三向镜像来确保数据复原能力。 租户数据的三个副本将写入不同的服务器并保存在缓存中：

![Azure Stack Hub 存储基础结构](media/azure-stack-storage-infrastructure-overview/image5.png)

镜像功能通过保存所有数据的多个副本来提供容错。 如何对数据进行条带化和放置并不是无关紧要的，但肯定的是，使用镜像功能存储的任何数据都会完整地写入多次。 每个副本将写入不同的物理硬件（位于不同服务器中的不同驱动器），假设每个硬盘各自都有可能发生故障。 三向镜像可以安全容许至少两个硬件（驱动器或服务器）同时出现问题。 例如，如果你正在重新启动一台服务器，此时另一个驱动器或服务器突然发生故障，在这种情况下，所有数据将保持安全，可供持续访问。

## <a name="next-step"></a>后续步骤

[管理存储容量](azure-stack-manage-storage-shares.md) 
