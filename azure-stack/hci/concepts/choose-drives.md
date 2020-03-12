---
title: 为 Azure Stack HCI 选择驱动器
description: 如何为 Azure Stack HCI 中的存储空间直通选择驱动器。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 42fb0703ed1e4947a1f35cc14a8708c8372f220e
ms.sourcegitcommit: 900332596d0bb473d82b1d1a28c3fe3aa6522add
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79094978"
---
# <a name="choosing-drives-for-azure-stack-hci"></a>为 Azure Stack HCI 选择驱动器

>适用于： Windows Server 2019

本主题提供有关如何为[存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)选择驱动器以满足 Azure Stack HCI 的性能和容量要求的指导。

## <a name="drive-types"></a>驱动器类型

存储空间直通当前适用于三种类型的驱动器：

|||
|----------------------|--------------------------|
|![NVMe](media/choose-drives/NVMe-100-px.png)|**NVMe** （非易失性内存 Express）是指直接位于 PCIe 总线上的固态硬盘。 常见的外形规格为 2.5 "U. 2，PCIe 外接程序卡（AIC）和 M. 2。 NVMe 提供更高的 IOPS 和 IO 吞吐量，延迟时间比目前支持的任何其他类型的驱动器的延迟更低。|
|![SSD](media/choose-drives/SSD-100-px.png)|**SSD**是指固态驱动器，通过传统 SATA 或 SAS 连接。|
|![HDD](media/choose-drives/HDD-100-px.png)|**HDD**指的是可提供巨大存储容量的旋转磁盘驱动器。|

## <a name="built-in-cache"></a>内置缓存

存储空间直通功能是内置的服务器端缓存。 它是一个大型、持久的实时读写缓存。 在具有多种类型的驱动器的部署中，会自动将其配置为使用 "最快" 类型的所有驱动器。 剩余的驱动器用于容量。

有关详细信息，请参阅[了解存储空间直通中的缓存](/windows-server/storage/storage-spaces/understand-the-cache)。

## <a name="option-1--maximizing-performance"></a>选项1–最大化性能

若要实现跨任意数据的随机读取和写入的可预测且一致的 submillisecond 延迟，或实现非常高的 IOPS （我们已[在 6000000](https://www.youtube.com/watch?v=0LviCzsudGY&t=28m)到 1 Tb 以上）或 IO 吞吐量（我们已[超过 1 Tb/s](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=16m50s)！），应执行 "全部刷新"。

目前有三种方法可以实现此目的：

![所有-Flash 部署-可能](media/choose-drives/All-Flash-Deployment-Possibilities.png)

1. **所有 NVMe。** 使用所有 NVMe 可提供无与伦比的性能，包括最能预测的低延迟。 如果所有驱动器都是同一型号，则没有缓存。 你还可以混合使用较高的高耐用性和低耐用性的 NVMe 模型，并将前者配置为对后者缓存写入（[需要](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)安装）。

2. **NVMe + SSD。** 将 NVMe 与 Ssd 一起使用时，NVMe 会自动将写入缓存到 Ssd 中。 这样，就可以在缓存中进行合并，并只根据需要取消暂存，以减少 Ssd 的磨损。 这提供了与 NVMe 类似的写入特征，而读取是直接从快速 Ssd 提供的。

3. **所有 SSD。** 与所有-NVMe 一样，如果所有驱动器都是同一型号，则没有缓存。 如果混合使用高耐用性和低耐用性模型，则可以将前者配置为缓存后端的写入（[需要](/windows-server/storage/storage-spaces/understand-the-cache.md#manual-configuration)安装）。

   >[!NOTE]
   > 使用不带缓存的所有-NVMe 或全部-SSD 的优点是从每个驱动器获得可用的存储容量。 在缓存上没有 "消耗" 容量，这在规模较小的情况下可能很有吸引力。

## <a name="option-2--balancing-performance-and-capacity"></a>选项 2-平衡性能和容量

对于具有多种应用程序和工作负荷的环境，某些应用程序和工作负荷具有严格的性能要求，而另一些需要相当大的存储容量，因此，对于较大的 Hdd，你应该使用 NVMe 或 Ssd 缓存。

![混合部署-可能](media/choose-drives/Hybrid-Deployment-Possibilities.png)

1. **NVMe + HDD**。 NVMe 驱动器将通过缓存来加速读取和写入。 缓存读取使 Hdd 能够专注于写入。 缓存写入可消减猝发，只需根据需要按人工序列化方式（可最大限度地提高 HDD IOPS 和 IO 吞吐量）进行合并，并根据需要取消暂存。 这会提供类似于 NVMe 的写入特征，并为频繁或最近读取的数据提供类似于 NVMe 的读取特征。

2. **SSD + HDD**。 与上面类似，Ssd 将通过缓存来加速读取和写入。 这为经常或最近读取的数据提供了类似于 SSD 的写入特征和 SSD 的读取特征。

    还有一个附加的现选项：使用*所有三种*类型的驱动器。

3. **NVMe + SSD + HDD。** 对于所有这三种类型的驱动器，NVMe 驱动器都缓存 Ssd 和 Hdd。 其吸引力在于，你可以在 Ssd 上创建卷，并在同一群集中并行创建硬盘上的卷，所有这些卷都是 NVMe 的加速。 前者与 "全部刷新" 部署完全一样，后者与上述 "混合" 部署完全相同。 这在概念上类似于具有两个池，在很大程度上独立的容量管理、故障和修复周期等。

   >[!IMPORTANT]
   > 建议使用 SSD 层将性能最高的工作负荷放置在所有闪存上。

## <a name="option-3--maximizing-capacity"></a>选项 3-最大化容量

对于需要大量容量和不经常写入的工作负荷（例如存档、备份目标、数据仓库或 "冷" 存储），应将一些 Ssd 用于缓存，使其容量更大。

![最大化容量的部署选项](media/choose-drives/maximizing-capacity.png)

1. **SSD + HDD**。 Ssd 将缓存读取和写入，从而吸收突发并提供与 SSD 类似的写入性能，并将优化的取消暂存放到 Hdd 后面。

>[!IMPORTANT]
>仅支持带有 Hdd 的配置。 不建议将高耐用性 Ssd 缓存到低耐用性 Ssd。

## <a name="sizing-considerations"></a>大小调整注意事项

### <a name="cache"></a>高速缓存

每台服务器必须至少具有两个缓存驱动器（冗余需要的最低要求）。 建议将容量驱动器数增加到缓存驱动器数的倍数。 例如，如果有4个缓存驱动器，则使用8个容量驱动器（1:2 比）会获得比7或9更一致的性能。

应该调整缓存的大小，以适应应用程序和工作负荷的工作集，即它们在任何给定时间都在主动读取和写入的所有数据。 除了这样的缓存大小要求。 对于使用 Hdd 的部署，一个合理的开始位置是10% 的容量–例如，如果每个服务器具有 4 x 4 TB HDD = 16 TB 的容量，则 2 x 800 GB SSD = 1.6 TB 的缓存每个服务器。 对于所有闪存部署，尤其是对于非常[高的耐用](https://blogs.technet.microsoft.com/filecab/2017/08/11/understanding-dwpd-tbw/)ssd，这可能是最接近5% 的容量（例如，如果每台服务器具有 24 x 1.2 TB SSD = 28.8 TB 的容量），则 2 x 750 GB NVMe = 每个服务器的缓存大小为 1.5 tb。 以后随时可以添加或删除缓存驱动器进行调整。

### <a name="general"></a>常规

建议将每个服务器的总存储容量限制为约 400 tb。 每个服务器的存储容量越多，停机或重新启动后重新同步数据所需的时间就越长，这种情况下，应用软件更新。 对于 Windows Server 2019，每个存储池的当前最大大小为 4 pb （PB）（4000 TB）。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Stack HCI 概述](../overview.md)
- [了解 Azure Stack HCI 中的缓存](cache.md)
- [存储空间直通硬件要求](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
- [在 Azure Stack HCI 中规划卷](plan-volumes.md)
- [容错和存储效率](fault-tolerance.md)