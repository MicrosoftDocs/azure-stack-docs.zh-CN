---
title: 在 Azure Stack HCI 中规划卷
description: 如何在 Azure Stack HCI 中规划存储卷。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: c6410e4f0d60138ce773f7f0abfae1a5c1850bd2
ms.sourcegitcommit: 900332596d0bb473d82b1d1a28c3fe3aa6522add
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79095011"
---
# <a name="planning-volumes-in-storage-spaces-direct"></a>规划存储空间直通中的卷

> 适用于： Windows Server 2019

本主题提供有关如何在存储空间直通中规划卷以满足工作负荷的性能和容量需求（包括选择文件系统、复原类型和大小）的指导。

## <a name="review-what-are-volumes"></a>查看：什么是卷

卷是放置工作负荷所需的文件的位置，例如 Hyper-v 虚拟机的 VHD 或 VHDX 文件。 卷将存储池中的驱动器合并，以引入存储空间直通的容错能力、可伸缩性和性能优势。

   >[!NOTE]
   > 在存储空间直通的所有文档中，我们使用术语 "卷" 来将其下的卷和虚拟磁盘结合起来，包括其他内置 Windows 功能（如群集共享卷（CSV）和 ReFS）提供的功能。 了解这些实现级别的区别不是成功规划和部署存储空间直通所必需的。

![什么是卷](media/plan-volumes/what-are-volumes.png)

群集中的所有服务器同时访问所有卷。 创建后，它们将显示在所有服务器上的**C:\ClusterStorage\\** 中。

![csv-文件夹-屏幕截图](media/plan-volumes/csv-folder-screenshot.png)

## <a name="choosing-how-many-volumes-to-create"></a>选择要创建的卷数量

建议将卷数量设为群集中的服务器数的倍数。 例如，如果有4台服务器，则与3或5总数相比，使用4个卷时，将获得更一致的性能。 这允许群集在服务器之间平均分配卷 "所有权" （一个服务器处理每个卷的元数据业务流程）。

建议将 Windows Server 2019 的卷总数限制为每个群集64卷。

## <a name="choosing-the-filesystem"></a>选择文件系统

建议为存储空间直通使用新的[复原文件系统（ReFS）](/en-us/windows-server/storage/refs/refs-overview) 。 ReFS 是用于虚拟化的顶级文件系统目的，它具有许多优势，包括显著的性能加速和针对数据损坏的内置保护。 它支持几乎所有重要的 NTFS 功能，包括 Windows Server 版本1709及更高版本中的重复数据删除。 有关详细信息，请参阅 ReFS[功能比较表](/windows-server/storage/refs/refs-overview#feature-comparison)。

如果工作负荷需要 ReFS 尚不支持的功能，则可以改为使用 NTFS。

   > [!TIP]
   > 具有不同文件系统的卷可以共存于同一群集。

## <a name="choosing-the-resiliency-type"></a>选择复原类型

存储空间直通中的卷可以提供复原功能，以防止出现硬件问题（例如驱动器或服务器故障），并在整个服务器维护（如软件更新）中实现连续可用性。

   > [!NOTE]
   > 您可以选择哪些复原类型与您拥有的驱动器类型无关。

### <a name="with-two-servers"></a>具有两个服务器

对于群集中的两个服务器，可以使用双向镜像。 如果运行的是 Windows Server 2019，还可以使用嵌套复原。

双向镜像保存所有数据的两个副本，每个服务器上的驱动器上有一个副本。 其存储效率为 50%;若要写入 1 TB 的数据，则存储池中至少需要 2 TB 的物理存储容量。 双向镜像一次可安全容忍一个硬件故障（一个服务器或驱动器）。

![双向镜像](media/plan-volumes/two-way-mirror.png)

嵌套复原（仅在 Windows Server 2019 上可用）提供具有双向镜像的服务器之间的数据复原能力，然后在具有双向镜像的服务器和镜像加速的奇偶校验中增加复原能力。 即使在一台服务器重新启动或不可用时，嵌套也能提供数据复原能力。 对于嵌套的双向镜像，其存储效率为25%，嵌套的镜像加速奇偶校验约为35-40%。 嵌套复原可以安全地允许两个硬件故障（两个驱动器或服务器以及剩余服务器上的驱动器）。 由于此功能增加了数据复原能力，因此，如果您运行的是 Windows Server 2019，则建议在两服务器群集的生产部署上使用嵌套复原功能。 有关详细信息，请参阅[嵌套复原](/windows-server/storage/storage-spaces/nested-resiliency)。

![嵌套镜像加速奇偶校验](media/plan-volumes/nested-mirror-accelerated-parity.png)

### <a name="with-three-servers"></a>带有三台服务器

对于三台服务器，应使用三向镜像，以获得更好的容错能力和性能。 三向镜像保留所有数据的三个副本，每个服务器上的驱动器上有一个副本。 其存储效率为33.3% –写入 1 TB 的数据，则存储池中至少需要 3 TB 的物理存储容量。 三向镜像一次可以安全地容忍[至少两个硬件问题（驱动器或服务器）](/windows-server/storage/storage-spaces/storage-spaces-fault-tolerance#examples)。 如果2个节点不可用，则存储池将失去仲裁，因为2/3 磁盘不可用，虚拟磁盘将被不可。 但是，节点可能会关闭，另一个节点上的一个或多个磁盘可能会失败，并且虚拟磁盘仍将保持联机状态。 例如，如果你在突然另一个驱动器或服务器发生故障时重启一台服务器，则所有数据都将保持安全且持续可访问。

![三向镜像](media/plan-volumes/three-way-mirror.png)

### <a name="with-four-or-more-servers"></a>包含四台或更多服务器

使用四台或更多服务器时，可以为每个卷选择是否使用三向镜像、双重奇偶校验（通常称为 "擦除编码"），或者将两者混合为镜像加速的奇偶校验。

双重奇偶校验提供与三向镜像相同的容错，但具有更高的存储效率。 有四台服务器，其存储效率为 50.0%;若要存储 2 TB 的数据，则存储池中需要 4 TB 的物理存储容量。 这会增加7个服务器的存储效率80.0，提高到66.7% 的存储效率。 缺点是奇偶校验编码更多计算密集型，这会限制其性能。

![双重奇偶校验](media/plan-volumes/dual-parity.png)

使用哪种复原类型取决于工作负荷的需求。 下面是一个表，其中汇总了适合每个复原类型的工作负荷，以及每个复原类型的性能和存储效率。

| 复原类型 | 容量效率 | 速度 | 工作负荷 |
| ------------------- | ----------------------  | --------- | ------------- |
| **镜像**         | ![存储效率显示33%](media/plan-volumes/3-way-mirror-storage-efficiency.png)<br>三向镜像：33% <br>双向镜像：50%     |![性能显示100%](media/plan-volumes/three-way-mirror-perf.png)<br> 最高性能  | 虚拟化工作负荷<br> 数据库<br>其他高性能工作负荷 |
| **镜像加速的奇偶校验** |![存储效率大约50%](media/plan-volumes/mirror-accelerated-parity-storage-efficiency.png)<br> 取决于镜像和奇偶校验的比例 | ![性能显示大约20%](media/plan-volumes/mirror-accelerated-parity-perf.png)<br>比镜像慢得多，但最多可达两倍于双重奇偶校验<br> 最适用于大型顺序写入和读取 | 存档和备份<br> 虚拟桌面基础结构     |
| **双重奇偶校验**               | ![存储效率大约80%](media/plan-volumes/dual-parity-storage-efficiency.png)<br>4台服务器：50% <br>16台服务器：最高80% | ![性能显示大约10%](media/plan-volumes/dual-parity-perf.png)<br>写入时 CPU 使用率 & 最高 i/o 延迟<br> 最适用于大型顺序写入和读取 | 存档和备份<br> 虚拟桌面基础结构  |

#### <a name="when-performance-matters-most"></a>当性能最重要时

对于具有严格延迟要求或需要大量混合随机 IOPS （如 SQL Server 数据库或性能敏感的 Hyper-v 虚拟机）的工作负荷，应在使用镜像的卷上运行，以最大程度地提高性能。

   >[!TIP]
   > 镜像比任何其他复原类型要快。 我们使用镜像来实现几乎所有的性能示例。

#### <a name="when-capacity-matters-most"></a>如果容量最重要

不常写入的工作负荷（如数据仓库或 "冷" 存储）应在使用双重奇偶校验的卷上运行，以最大限度地提高存储效率。 某些其他工作负荷（如传统的文件服务器、虚拟桌面基础结构（VDI））或其他不会创建大量快速偏移随机 IO 流量和/或不需要最佳性能的工作负荷也可以根据自己的意愿使用双重奇偶校验。 与镜像相比，奇偶校验必然会增加 CPU 利用率和 IO 延迟，特别是对写入操作。

#### <a name="when-data-is-written-in-bulk"></a>大容量写入数据时

写入大型的顺序传递（如存档或备份目标）的工作负荷有另一个选项：一个卷可以混合使用镜像和双重奇偶校验。 首先将土地写入镜像部分，并在以后逐渐移到奇偶校验部分。 这样一来，通过允许在更长时间内进行计算密集型奇偶校验编码，可加速引入并降低资源利用率。 当调整部分的大小时，请考虑一次发生的写入量（如每日备份）应适合镜像部分。 例如，如果每日引入了 100 GB 一次，请考虑使用 150 GB 到 200 GB 的镜像，并使用双重奇偶校验。

产生的存储效率取决于你选择的比例。 有关一些示例，请参阅[此演示](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s)。

   > [!TIP]
   > 如果通过数据引入来观察写入性能时发生的突然降低，则可能表示镜像部分不够大，或者镜像加速奇偶校验不太适合用例。 例如，如果写入性能从 400 MB/秒降低到 40 MB/秒，则考虑展开镜像部分或切换到三向镜像。

### <a name="about-deployments-with-nvme-ssd-and-hdd"></a>关于具有 NVMe、SSD 和 HDD 的部署

在具有两种类型的驱动器的部署中，加速驱动器提供缓存，而速度较慢的驱动器提供容量。 这会自动发生–有关详细信息，请参阅[了解存储空间直通中的缓存](/windows-server/storage/storage-spaces/understand-the-cache)。 在这种部署中，所有卷最终驻留在同一类型的驱动器上-容量驱动器。

在所有三种类型的驱动器的部署中，只有最快的驱动器（NVMe）提供缓存，从而提供两种类型的驱动器（SSD 和 HDD）来提供容量。 对于每个卷，你可以选择该卷完全驻留在 SSD 层上，还是完全驻留在 HDD 层上，或者是否跨越两个卷。

   > [!IMPORTANT]
   > 建议使用 SSD 层将性能最高的工作负荷放置在所有闪存上。

## <a name="choosing-the-size-of-volumes"></a>选择卷的大小

建议在 Windows Server 2019 中将每个卷的大小限制为 64 TB。

   > [!TIP]
   > 如果使用的备份解决方案依赖于卷影复制服务（VSS）和 Volsnap 软件提供程序（与文件服务器工作负荷相同），则将卷大小限制为 10 TB 将提高性能和可靠性。 使用较新的 Hyper-v .RCT API 和/或 ReFS 块克隆和/或本机 SQL 备份 Api 的备份解决方案的性能高达 32 TB。

### <a name="footprint"></a>需求量

卷的大小指其可用容量，即它可以存储的数据量。 这由**新的 volume** cmdlet 的 **-size**参数提供，并在你运行该**获取卷**cmdlet 时显示在**大小**属性中。

大小不同于卷*占用*空间，它在存储池中占用的总物理存储容量。 占用空间取决于其复原类型。 例如，使用三向镜像的卷的大小占用了三倍。

卷的痕迹需要存储池中。

![大小与占用空间](media/plan-volumes/size-versus-footprint.png)

### <a name="reserve-capacity"></a>保留容量

如果在存储池中保留一些容量，则在驱动器发生故障后，卷空间会 "就地" 修复，从而提高数据安全和性能。 如果有足够的容量，即时的就地并行修复可将卷还原到完全复原能力，即使在更换了故障的驱动器之前也是如此。 这会自动发生。

建议保留每个服务器一个容量驱动器的等效项，最多支持4个驱动器。 您可以根据自己的意愿预留更多的信息，但这一最低的建议保证在任何驱动器发生故障后立即可以成功进行并行修复。

![reserve](media/plan-volumes/reserve.png)

例如，如果有2个服务器并且使用 1 TB 容量驱动器，请将池保留 2 x 1 = 2 TB 作为预留空间。 如果有3个服务器和 1 TB 容量驱动器，请将 3 x 1 = 3 TB 预留为预留。 如果有4个或更多服务器和 1 TB 容量驱动器，请将 4 x 1 = 4 TB 预留为预留。

   >[!NOTE]
   > 在包含三种类型（NVMe + SSD + HDD）的驱动器的群集中，我们建议保留相当于每台服务器一个 SSD 加1个 HDD，每个服务器最多4个驱动器。

## <a name="example-capacity-planning"></a>示例：容量规划

请考虑 1 4-服务器群集。 每台服务器都有一些缓存驱动器和十六 2 TB 驱动器的容量。

```
4 servers x 16 drives each x 2 TB each = 128 TB
```

从存储池中的这一 128 TB 开始，我们设置了四个驱动器（即 8 TB），这样就可以在没有任何高峰的情况下进行就地修复，因为驱动器发生故障。 这会将 120 TB 的物理存储容量保留在池中，以便我们可以创建卷。

```
128 TB – (4 x 2 TB) = 120 TB
```

假设我们需要我们的部署来托管一些高度可用的 Hyper-v 虚拟机，但我们也有大量的冷存储-需要保留的旧文件和备份。 由于我们有四个服务器，我们来创建四个卷。

接下来，将虚拟机放在前两个卷（ *Volume1*和*Volume2*）上。 我们选择 ReFS 作为文件系统（以实现更快的创建和检查点）和三向镜像以实现最高性能。 接下来，将冷存储置于*第3卷*和第*4*卷上。 我们选择 NTFS 作为 filesystem （用于重复数据删除）和双重奇偶校验，以最大限度地提高容量。

我们不需要使所有卷都具有相同的大小，但为了简单起见，我们可以将它们全部设置为 12 TB。

每个*Volume1*和*Volume2*都占用 12 tb x 33.3% 的效率 = 36 tb 的物理存储容量。

每个*Volume3*和*Volume4*都占用 12 tb x 50.0% 的效率 = 24 tb 的物理存储容量。

```
36 TB + 36 TB + 24 TB + 24 TB = 120 TB
```

这四个卷完全适合于池中可用的物理存储容量。 极!

![示例](media/plan-volumes/example.png)

   >[!TIP]
   > 不需要立即创建所有卷。 稍后，你始终可以扩展卷或创建新卷。

为简单起见，此示例在整个中使用十进制（以10为基数）单位，表示 1 TB = 1000000000000 字节。 但是，Windows 中的存储数量以二进制（以2为底）单位显示。 例如，在 Windows 中，每个 2 TB 驱动器都将显示为 1.82 TiB。 同样，128 TB 存储池会显示为 116.41 TiB。 这是正常情况。

## <a name="usage"></a>用法

请参阅[在 AZURE STACK HCI 中创建卷](../manage/create-volumes.md)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Stack HCI 概述](../overview.md)
- [为存储空间直通选择驱动器](choose-drives.md)
- [容错和存储效率](fault-tolerance.md)