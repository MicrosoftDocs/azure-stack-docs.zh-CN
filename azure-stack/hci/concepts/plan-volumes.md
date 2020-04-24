---
title: 在 Azure Stack HCI 中规划卷
description: 如何在 Azure Stack HCI 中规划存储卷。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: c6410e4f0d60138ce773f7f0abfae1a5c1850bd2
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "79095011"
---
# <a name="planning-volumes-in-storage-spaces-direct"></a>规划存储空间直通中的卷

> 适用于：Windows Server 2019

本主题指导你根据工作负荷的性能和容量需求规划存储空间直通中的卷，包括选择其文件系统、复原类型和大小。

## <a name="review-what-are-volumes"></a>内容回顾：什么是卷

卷用于存放工作负荷所需的文件，例如 Hyper-V 虚拟机的 VHD 或 VHDX 文件。 卷将驱动器合并到存储池中，带来存储空间直通的容错、可伸缩性和性能优势。

   >[!NOTE]
   > 在有关存储空间直通的整篇文档中，我们使用术语“卷”共指卷及其下的虚拟磁盘，包括群集共享卷 (CSV) 和 ReFS 等其他内置 Windows 功能所提供的功能。 不了解这些实现级别的差异也能成功规划和部署存储空间直通。

![what-are-volumes](media/plan-volumes/what-are-volumes.png)

群集中的所有服务器可以同时访问所有卷。 创建卷后，这些卷将显示在所有服务器上的 **C:\ClusterStorage\\** 中。

![csv-folder-screenshot](media/plan-volumes/csv-folder-screenshot.png)

## <a name="choosing-how-many-volumes-to-create"></a>选择要创建的卷数

建议的卷数为群集中服务器数的倍数。 例如，如果你有 4 台服务器，则相比创建 3 个或 5 个卷，创建 4 个卷可以获得更稳定的性能。 这样，群集就可以在服务器之间平均分配“所有权”（由一台服务器处理每个卷的元数据业务流程）。

对于 Windows Server 2019，我们建议将卷总数限制为每个群集最多 64 个卷。

## <a name="choosing-the-filesystem"></a>选择文件系统

我们建议将新的[弹性文件系统 (ReFS)](/en-us/windows-server/storage/refs/refs-overview) 用于存储空间直通。 ReFS 是专为虚拟化打造的高级文件系统，其优点包括大幅的性能加速和内置的数据损坏防护。 它支持几乎所有主要 NTFS 功能，包括 Windows Server 1709 和更高版本中的重复数据删除。 有关详细信息，请参阅 ReFS [功能比较表格](/windows-server/storage/refs/refs-overview#feature-comparison)。

如果工作负荷需要 ReFS 尚不支持的功能，你可以改用 NTFS。

   > [!TIP]
   > 具有不同文件系统的卷可以共置在同一群集中。

## <a name="choosing-the-resiliency-type"></a>选择复原类型

存储空间直通中的卷提供复原能力来防范硬件问题（例如驱动器或服务器故障），并在整个服务器维护期间（例如软件更新）实现持续可用性。

   > [!NOTE]
   > 可选择的复原类型与驱动器类型无关。

### <a name="with-two-servers"></a>使用两台服务器

如果群集中有两台服务器，可以使用双向镜像。 如果运行 Windows Server 2019，则还可以使用嵌套复原功能。

双向镜像为所有数据保留两个副本，每台服务器中的驱动器上有一个副本。 其存储效率为 50%；若要写入 1 TB 数据，存储池中至少需要 2 TB 的物理存储容量。 双向镜像每次可安全承受一次硬件故障（一台服务器或驱动器的故障）。

![two-way-mirror](media/plan-volumes/two-way-mirror.png)

嵌套复原（仅适用于 Windows Server 2019）在具有双向镜像的服务器之间提供数据复原，然后在具有双向镜像或镜像加速奇偶校验的服务器中添加复原功能。 即使其中一台服务器正在重启或不可用，嵌套功能也提供数据复原。 具有嵌套双向镜像的存储效率为 25%，而镜像加速奇偶校验的效率大约为 35-40%。 嵌套复原可以安全承受同时发生两次硬件故障（两个驱动器，或者一台服务器加上剩余服务器上的驱动器的故障）。 由于添加了数据复原功能，如果运行的是 Windows Server 2019，我们建议在两个服务器群集的生产部署上使用嵌套复原功能。 有关详细信息，请参阅[嵌套复原](/windows-server/storage/storage-spaces/nested-resiliency)。

![嵌套镜像加速奇偶校验](media/plan-volumes/nested-mirror-accelerated-parity.png)

### <a name="with-three-servers"></a>使用三台服务器

使用三台服务器时，应使用三向镜像来获得更好的容错能力和性能。 三向镜像为所有数据保留三个副本，每台服务器中的驱动器上有一个副本。 其存储效率为 33.3% – 若要写入 1 TB 数据，存储池中至少需要 3 TB 的物理存储容量。 三向镜像可以安全承受[至少两个硬件（驱动器或服务器）同时出现问题](/windows-server/storage/storage-spaces/storage-spaces-fault-tolerance#examples)。 如果有 2 个节点不可用，存储池将由于 2/3 的磁盘不可用而失去仲裁，并且虚拟磁盘将不可访问。 但是，某个节点可以关闭，另一个节点上的一个或多个磁盘可以发生故障，在此情况下，虚拟磁盘仍会保持联机。 例如，如果你正在重新启动一台服务器，此时另一个驱动器或服务器突然发生故障，在这种情况下，所有数据将保持安全，可供持续访问。

![three-way-mirror](media/plan-volumes/three-way-mirror.png)

### <a name="with-four-or-more-servers"></a>使用四台或更多服务器

使用四台或更多服务器时，可对每个卷选择是要使用三向镜像、双重奇偶校验（通常称作“擦除编码”），还是将此两者与镜像加速奇偶校验混合使用。

双重奇偶校验提供与三向镜像相同的容错能力，但是存储效率更高。 使用四台服务器时，其存储效率为 50.0%；若要存储 2 TB 数据，存储池中需要 4 TB 物理存储容量。 使用七台服务器时，其存储效率增大至 66.7%，最高可提升至 80.0%。 其缺点是奇偶校验编码需要消耗更多的计算资源，这可能会限制其性能。

![dual-parity](media/plan-volumes/dual-parity.png)

使用哪种复原类型取决于工作负荷的需求。 下表汇总了每种复原类型适用的工作负荷，以及每种复原类型的性能和存储效率。

| 复原类型 | 容量效率 | Speed | 工作负荷 |
| ------------------- | ----------------------  | --------- | ------------- |
| **镜像**         | ![存储效率表现为 33%](media/plan-volumes/3-way-mirror-storage-efficiency.png)<br>三向镜像：33% <br>双向镜像：50%     |![性能表现为 100%](media/plan-volumes/three-way-mirror-perf.png)<br> 最高性能  | 虚拟化工作负荷<br> 数据库<br>其他高性能工作负荷 |
| **镜像加速奇偶校验** |![存储效率表现为大约 50%](media/plan-volumes/mirror-accelerated-parity-storage-efficiency.png)<br> 取决于镜像和奇偶校验之比 | ![性能表现为大约 20%](media/plan-volumes/mirror-accelerated-parity-perf.png)<br>速度比镜像慢很多，但比双重奇偶校验最多快两倍<br> 最适合用于大规模有序写入和读取 | 存档和备份<br> 虚拟化桌面基础结构     |
| **双重奇偶校验**               | ![存储效率表现为大约 80%](media/plan-volumes/dual-parity-storage-efficiency.png)<br>4 台服务器：50% <br>16 台服务器：最高 80% | ![性能表现为大约 10%](media/plan-volumes/dual-parity-perf.png)<br>写入时的最高 I/O 延迟和 CPU 使用率<br> 最适合用于大规模有序写入和读取 | 存档和备份<br> 虚拟化桌面基础结构  |

#### <a name="when-performance-matters-most"></a>当性能最重要时

具有严格延迟要求或需要大量混合随机 IOPS（例如 SQL Server 数据库或性能敏感型 Hyper-V 虚拟机）的工作负荷应在使用镜像的卷上运行，以实现最佳性能。

   >[!TIP]
   > 镜像的速度比其他任何复原类型更快。 我们的性能示例几乎都使用镜像。

#### <a name="when-capacity-matters-most"></a>如果容量最重要

不常写入的工作负荷（例如数据仓库或“冷”存储）应在使用双重奇偶校验的卷上运行，以实现最高的存储效率。 其他某些工作负荷，例如传统文件服务器、虚拟桌面基础结构 (VDI)，或其他不创建大量快速偏移随机 IO 流量和/或无需最佳性能的工作负荷，也可以根据你的决定使用双重奇偶校验。 相较于镜像，奇偶校验势必会增大 CPU 利用率和 IO 延迟，尤其是在写入时。

#### <a name="when-data-is-written-in-bulk"></a>批量写入数据时

按顺序大量写入数据的工作负荷（例如存档或备份目标）有另一个选项：可在一个卷上使用镜像和双重奇偶校验。 写入内容先进入镜像部分，然后逐渐移入奇偶校验部分。 此选项使计算密集型的奇偶校验编码可持续更长时间，因而可以加速引入速度并减少资源利用率。 调整每个部分的大小时，请考虑到一次性发生的写入数量（例如每天一次的备份）是否可以轻松装入镜像部分。 例如，如果每日引入 100 GB 数据，请考虑对 150 GB 到 200 GB 数据使用镜像，对剩余的数据使用双重奇偶校验。

最终的存储效率取决于所选的比例。 有关一些示例，请参阅[此演示](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s)。

   > [!TIP]
   > 如果通过数据引入来观察写入性能时发生的突然降低，则可能表示镜像部分不够大，或者镜像加速奇偶校验不太适合用例。 例如，如果写入性能从 400 MB/秒降低到 40 MB/秒，则考虑展开镜像部分或切换到三向镜像。

### <a name="about-deployments-with-nvme-ssd-and-hdd"></a>关于具有 NVMe、SSD 和 HDD 的部署

在涉及两种驱动器类型的部署中，较快的驱动器提供缓存，而较慢的驱动器提供容量。 该过程将自动进行 – 有关详细信息，请参阅[了解存储空间直通中的缓存](/windows-server/storage/storage-spaces/understand-the-cache)。 在此类部署中，所有卷最终都位于相同类型的驱动器（容量驱动器）上。

在涉及所有三种驱动器类型的部署中，只有最快的驱动器 (NVMe) 会提供缓存，剩下两种类型的驱动器（SSD 和 HDD）会提供容量。 对于每个卷，你可以选择它是完全驻留在 SSD 层上、完全驻留在 HDD 层上，还是跨越这两种层。

   > [!IMPORTANT]
   > 我们建议使用 SSD 层将对性能最敏感的工作负载放在全闪存中。

## <a name="choosing-the-size-of-volumes"></a>选择卷的大小

建议在 Windows Server 2019 中将每个卷的大小限制为 64 TB。

   > [!TIP]
   > 如果使用的备份解决方案依赖于卷影复制服务（VSS）和 Volsnap 软件提供程序（与文件服务器工作负荷相同），则将卷大小限制为 10 TB 将提高性能和可靠性。 使用较新 Hyper-V RCT API 和/或 ReFS 块克隆和/或本机 SQL 备份 API 的备份解决方案的性能可完全达到 32 TB 及更高。

### <a name="footprint"></a>占用空间

卷的大小指的是其可用容量，即它可以存储的数据量。 此大小由 **New-Volume**cmdlet 的 **-Size** 参数提供，然后在运行 **Get-Volume** cmdlet 时显示在 **Size** 属性中。

大小完全不同于卷的*占用空间*，其指的是卷在存储池中占用的总物理存储容量。 占用空间取决于其复原类型。 例如，使用三向镜像的卷具有的占用空间是其大小的三倍。

卷的占用空间需要能够容纳到存储池中。

![大小与占用空间](media/plan-volumes/size-versus-footprint.png)

### <a name="reserve-capacity"></a>保留容量

如果使存储池中的一些容量处于未分配状态，则会给卷提供空间，以便在驱动器发生故障后“就地”进行修复，从而提高数据安全性和性能。 如果有足够的容量，那么甚至在更换故障驱动器之前，即时、就地、并行修复也可以将卷还原到完全恢复状态。 此过程自动发生。

我们建议为每个服务器保留相当于一个容量驱动器的容量，最多可保留 4 个驱动器的容量。 你可以自行决定保留更多容量，但此最低容量建议可以保证在任何驱动器发生故障后均能够成功进行即时、就地、并行修复。

![保留](media/plan-volumes/reserve.png)

例如，如果你安装了 2 个服务器，并且你使用的是 1 TB 的容量驱动器，请留出 2 x 1 = 2 TB 的池作为保留容量。 如果你安装了 3 个服务器和 1TB 的容量驱动器，请留出 3 x 1 = 3 TB 作为保留容量。 如果你安装了 4 个或更多个服务器以及 1TB 的容量驱动器，请留出 4 x 1 = 4 TB 作为保留容量。

   >[!NOTE]
   > 在具有所有三种类型 (NVMe + SSD + HDD) 的驱动器的群集中，我们建议为每个服务器保留相当于一个 SSD 与一个 HDD 总和的容量，每种类型最多可保留 4 个驱动器的容量。

## <a name="example-capacity-planning"></a>示例：容量规划

假设有一个由四个服务器组成的群集。 每个服务器都安装了一些缓存驱动器以及十六个 2 TB 的容量驱动器。

```
4 servers x 16 drives each x 2 TB each = 128 TB
```

从存储池内的此 128 TB 中，我们留出四个驱动器或 8 TB，以便可以进行就地修复，而不需要在驱动器发生故障后匆忙去更换驱动器。 这样，池中还剩下 120 TB 的物理存储容量，我们可以使用这些容量来创建卷。

```
128 TB – (4 x 2 TB) = 120 TB
```

假设我们需要进行部署以托管一些高度活跃的 Hyper-V 虚拟机，但是我们还有许多冷存储 － 即我们需要保留的旧文件和备份。 由于我们安装了四个服务器，所以我们将创建四个卷。

我们在前两个卷（*Volume1* 和 *Volume2*）上放置虚拟机。 我们选择 ReFS 作为文件系统（用于加快创建速度和检查点），选择三向镜像以让复原达到最大性能。 我们将冷存储放在其他两个卷（*Volume 3* 和 *Volume 4*）上。 我们选择 NTFS 作为文件系统（用于重复数据删除），选择双奇偶校验以让复原达到最大容量。

我们不必将所有卷都设为相同大小，但是为了简单起见，我们就这样设置 - 例如，我们可以将它们都设置为 12 TB。

每个*Volume1*和*Volume2*都占用 12 tb x 33.3% 的效率 = 36 tb 的物理存储容量。

每个*Volume3*和*Volume4*都占用 12 tb x 50.0% 的效率 = 24 tb 的物理存储容量。

```
36 TB + 36 TB + 24 TB + 24 TB = 120 TB
```

我们池中的可用物理存储容量完全能够装下这四个卷。 太完美了！

![示例](media/plan-volumes/example.png)

   >[!TIP]
   > 你无需立即创建所有卷。 你始终可以在稍后扩展卷或创建新卷。

为简单起见，此示例从头到尾都使用十进制（基数为 10）单位，这意味着 1 TB = 1,000,000,000,000 字节。 但是，Windows 中的存储数量按二进制（基数为 2）单位显示。 例如，在 Windows 中，每个 2 TB 的驱动器都将显示为 1.82 TiB。 同样，128 TB 的存储池将显示为 116.41 TiB。 这是正常情况。

## <a name="usage"></a>用法

请参阅[在 AZURE STACK HCI 中创建卷](../manage/create-volumes.md)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Stack HCI 概述](../overview.md)
- [选择存储空间直通驱动器](choose-drives.md)
- [容错和存储效率](fault-tolerance.md)