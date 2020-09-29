---
title: 了解 Azure Stack HCI 中的存储池缓存
description: 存储空间直通和 Azure Stack HCI 中读取和写入缓存的工作原理。
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/04/2020
ms.openlocfilehash: 9a15b953ffe2229d7f92bea998392b8570f481de
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742522"
---
# <a name="understanding-the-storage-pool-cache-in-azure-stack-hci"></a>了解 Azure Stack HCI 中的存储池缓存

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

Azure Stack HCI 提供内置的服务器端缓存功能，以最大程度地提高存储性能。 此缓存是一个大型的持久性实时读取和写入缓存。 部署 Azure Stack HCI 后，会自动配置缓存。 在大多数情况下，无需进行任何手动管理。 缓存的工作原理取决于现有的驱动器类型。

以下视频详细介绍了如何针对存储空间直通、Azure Stack HCI 后的底层存储虚拟化技术以及其他设计注意事项，了解缓存的工作原理。

<strong>存储空间直通设计注意事项</strong><br>（20 分钟）<br>
<iframe src="https://channel9.msdn.com/Blogs/windowsserver/Design-Considerations-for-Storage-Spaces-Direct/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="drive-types-and-deployment-options"></a>驱动器类型和部署选项

Azure Stack HCI 目前使用四种类型的驱动器：

| 驱动器类型 | 描述 |
|----------------------|--------------------------|
|![PMem](media/choose-drives/pmem-100px.png)|PMem 是指永久性内存，一种新的低延迟、高性能存储类型。|
|![NVMe](media/choose-drives/NVMe-100-px.png)|**NVMe**（非易失性快速内存）是指直接位于 PCIe 总线上的固态硬盘。 常见外形规格为 2.5 英寸 U.2、PCIe 附加卡 (AIC) 和 M.2。 NVMe 提供较高的 IOPS 和 IO 吞吐量，延迟也比目前支持的除 PMem 外的任何其他驱动器类型低。|
|![SSD](media/choose-drives/SSD-100-px.png)|**SSD** 是指通过传统 SATA 或 SAS 连接的固态硬盘。|
|![HDD](media/choose-drives/HDD-100-px.png)|**HDD** 是指旋转式的磁性硬盘，可提供巨量存储容量。|

这些类型可按各种方式进行组合，我们将其划分成两大类别：“全闪存”和“混合”。

### <a name="all-flash-deployment-possibilities"></a>全闪存部署的可行性

“全部闪存”部署旨在最大化存储性能，不包括机械硬盘 (HDD)。

![关系图显示了所有闪存部署，包括适用于容量的 N V M e，N v M e 用于缓存，适用于容量的 s D，S D 用于容量。](media/cache/All-Flash-Deployment-Possibilities.png)

### <a name="hybrid-deployment-possibilities"></a>混合部署的可行性

混合部署旨在平衡性能和容量或者最大化容量，包括机械硬盘 (HDD)。

![关系图显示了混合部署，包括 N V M e for 缓存，其中包含用于容量的 H D D，为容量添加了用于缓存，S D 用于缓存，为容量提供了 h D d，为容量提供了用于缓存的 N V M e。](media/cache/Hybrid-Deployment-Possibilities.png)

## <a name="cache-drives-are-selected-automatically"></a>自动选择缓存驱动器

在具有多种类型的驱动器的部署中，Azure Stack HCI 自动使用 "最快" 类型的所有驱动器进行缓存。 剩余的驱动器用于提供容量。

可根据以下层次结构来确定“最快”的类型。

![关系图显示了顺序为 N V M e、S D、无标记磁盘（表示 H D D）的速度更快的磁盘类型。](media/cache/Drive-Type-Hierarchy.png)

例如，如果你有 NVMe 和 SSD，则 NVMe 将为 SSD 提供缓存。

如果你有 SSD 和 HDD，则 SSD 将为 HDD 提供缓存。

   >[!NOTE]
   > 缓存驱动器不会贡献可用的存储容量。 存储在缓存中的所有数据也会存储在其他位置，或者在解除暂存后存储在其他位置。 这意味着，部署的原始总存储容量只是容量驱动器的总和。

如果所有驱动器属于同一类型，则不会自动配置缓存。 可以选择手动将持久性较高的驱动器配置为针对同一类型的持久性较低的驱动器提供缓存 – 请参阅[手动配置](#manual-configuration)部分了解操作方法。

   >[!TIP]
   > 在全部使用 NVMe 或全部使用 SSD 的部署中，尤其是在极小规模的部署中，不让任何驱动器“支出”容量用于缓存可在一定程度上改善存储效率。

## <a name="cache-behavior-is-set-automatically"></a>自动设置缓存行为

根据要提供缓存的驱动器类型自动确定缓存的行为。 为固态硬盘提供缓存时（例如，为 SSD 提供 NVMe 缓存），只缓存写入内容。 为机械硬盘提供缓存时（例如 SSD 为 HDD 提供缓存），读取和写入内容都将缓存。

![比较所有闪存的缓存的关系图，其中缓存了写入，其中缓存了读，但混合了读取和写入。](media/cache/Cache-Read-Write-Behavior.png)

### <a name="write-only-caching-for-all-flash-deployments"></a>全闪存部署的只写缓存

为固态硬盘（NVMe 或 SSD）提供缓存时，只缓存写入内容。 由于许多写入和重写可能在缓存中合并，并只在需要时才解除暂存，因此可以减小容量驱动器的磨损，减少发往容量驱动器的累积流量，并延长驱动器的寿命。 出于此原因，我们建议选择[持久性较高且已经过写入优化](http://whatis.techtarget.com/definition/DWPD-device-drive-writes-per-day)的驱动器用于缓存。 容量驱动器可能合理地具有较低的写入持久性。

由于读取操作不会显著影响闪存的寿命，并且固态硬盘一贯提供较低的读取延迟，因此不会缓存读取数据：读取数据直接从容量驱动器提供（除非数据是最近才写入的，以致尚未解除暂存）。 这样，缓存就可以专门用于写入，并将其有效性最大化。

因此，写入特征（例如写入延迟）由缓存驱动器来支配，而读取特征则是由容量驱动器来支配。 两者都是一致、可预测且统一的。

### <a name="readwrite-caching-for-hybrid-deployments"></a>混合部署的读取/写入缓存

为机械硬盘 (HDD) 提供缓存时，会同时缓存读取和写入内容，以便为读取和写入操作提供类似于闪存的延迟（通常可将延迟改善大约 10 倍）。 读取缓存存储最近和经常读取的数据以进行快速访问，并尽量减少发往 HDD 的随机流量。 （由于 HDD 的寻轨和旋转延迟，随机访问 HDD 时出现的滞后时间和时间损失会很明显。）写入内容将会缓存，以缓解数据突发，并与前面所述一样，写入和重写将会合并，以将发往容量驱动器的累积流量减到最小。

Azure Stack HCI 实现了一种算法，该算法在取消对它们的执行操作之前进行反随机化写入，将 IO 模式模拟到磁盘上，即使是从工作负荷 (的实际 IO （例如虚拟机) 是随机的）。 这可以将 HDD 的 IOPS 和吞吐量最大化。

### <a name="caching-in-deployments-with-nvme-ssd-and-hdd"></a>在 NVMe、SSD 和 HDD 的部署中进行缓存

使用所有上述三种类型的驱动器时，NVMe 驱动器将为 SSD 和 HDD 提供缓存。 行为如上所述：对于 SSD，仅缓存写入；对于 HDD，同时缓存读取和写入。 HDD 的缓存负担均匀分散在各个缓存驱动器之间。

## <a name="summary"></a>摘要

下表汇总了哪些驱动器用于缓存、哪些驱动器用于容量，以及每种部署可行性的缓存行为。

| 部署     | 缓存驱动器                        | 容量驱动器 | 缓存行为（默认）  |
| -------------- | ----------------------------------- | --------------- | ------------------------- |
| 全使用 NVMe         | 无（可选：手动配置） | NVMe            | 只写（如果已配置）  |
| 全使用 SSD          | 无（可选：手动配置） | SSD             | 只写（如果已配置）  |
| NVMe + SSD       | NVMe                                | SSD             | 只写                  |
| NVMe + HDD       | NVMe                                | HDD             | 读取 + 写入                |
| SSD + HDD        | SSD                                 | HDD             | 读取 + 写入                |
| NVMe + SSD + HDD | NVMe                                | SSD + HDD       | HDD 为读取 + 写入，SSD 为只写  |

## <a name="server-side-architecture"></a>服务器端体系结构

缓存在驱动器级别实现：一台服务器中的每个缓存驱动器绑定到同一台服务器中的一个或多个容量驱动器。

由于缓存的级别低于剩余 Windows 软件定义的存储堆栈，因此它不运用，也无需意识到“存储空间”或“容错”之类的概念。 可将它看作是创建“混合”（一部分是快闪，一部分是磁盘）驱动器，然后将其提供给 Windows。 与实际的混合驱动器一样，在外部几乎看不到物理媒体上速度较快和较慢部分之间冷热数据的实时移动。

考虑到 Azure Stack HCI 中的复原至少是服务器级 (意味着数据副本始终写入不同的服务器;对于每个服务器) ，每个服务器最多只有一个副本，因为缓存中的数据与缓存中的数据不是相同的复原。

![关系图表示通过存储空间层中的三向镜像联接的三个服务器，这些服务器访问 N V M e 驱动器（可访问未标记容量驱动器）的缓存层。](media/cache/Cache-Server-Side-Architecture.png)

例如，在使用三向镜像时，任何数据的三个副本将写入不同的服务器，然后从服务器进入缓存。 无论以后是否解除暂存这些副本，都始终有三个副本。

## <a name="drive-bindings-are-dynamic"></a>驱动器绑定是动态的

缓存驱动器与容量驱动器之间的绑定可以采用任何比率，从 1:1 到 1:12 甚至更高都可以。 每当添加或移除驱动器（例如扩展时或在故障后）时，这种绑定会动态调整。 这意味着，只要有需要，都可以单独添加缓存驱动器或容量驱动器。

![动态关系图显示了两个 N V M e 缓存驱动器，它们动态映射到前四个，然后是六个容量驱动器。](media/cache/Dynamic-Binding.gif)

为了对称，容量驱动器的数目最好是缓存驱动器数目的倍数。 例如，如果你有 4 个缓存驱动器，则配置 8 个容量驱动器（1:2 比例）的性能比配置 7 个或 9 个容量驱动器更均衡。

## <a name="handling-cache-drive-failures"></a>处理缓存驱动器故障

当某个缓存驱动器发生故障时，任何尚未解除暂存的写入内容都不会丢进本地服务器，这意味着，它们只存在于其他副本（其他服务器）中。 就像在其他任何驱动器发生故障后一样，存储空间可以通过查阅幸存的副本来自动恢复。

有一小段时间，绑定到丢失的缓存驱动器的容量驱动器会显示为不正常。 发生缓存重新绑定（自动）并完成数据修复（自动）之后，这些驱动器将继续显示为正常。

因为会发生这种情况，每台服务器至少需要两个缓存驱动器才能保留良好的性能。

![动态关系图显示两个映射到六个容量驱动器的 D 缓存驱动器，直到一个缓存驱动器发生故障时，这会导致所有六个驱动器映射到剩余的缓存驱动器。](media/cache/Handling-Failure.gif)

然后，可以像更换其他任何驱动器一样更换缓存驱动器。

   >[!NOTE]
   > 可能需要关闭电源来安全更换外形规格为附加卡 (AIC) 或 M.2 的 NVMe。

## <a name="relationship-to-other-caches"></a>与其他缓存之间的关系

Windows 软件定义的存储堆栈中有多个其他不相关的缓存。 例如，存储空间写回缓存，以及群集共享卷 (CSV) 内存中读取缓存。

对于 Azure Stack HCI，不应根据默认行为修改存储空间回写缓存。 例如，不应在 **New-Volume** cmdlet 中使用 **-WriteCacheSize** 之类的参数。

你可以自行决定是否使用 CSV 缓存。 默认情况下，它在 Azure Stack HCI 中处于启用状态，但不会以任何方式与本主题中所述的缓存发生冲突。 在某些情况下，它可以提供宝贵的性能增益。 有关详细信息，请参阅 [将 CSV 内存中读取缓存与 AZURE STACK HCI 配合使用](../manage/use-csv-cache.md)。

## <a name="manual-configuration"></a>手动配置

大多数部署无需进行手动配置。 如果需要手动配置，请参阅以下部分。

如果需要在设置后对缓存设备型号进行更改，请根据[运行状况服务概述](/windows-server/failover-clustering/health-service-overview#supported-components-document)中所述，编辑运行状况服务的支持组件文档。

### <a name="specify-cache-drive-model"></a>指定缓存驱动器型号

在所有驱动器都属于同一类型的部署中（例如，全使用 NVMe 或全使用 SSD 的部署），由于 Windows 无法自动分辨同一类型的驱动器的特征（例如写入持久性），因此不会配置缓存。

若要使用持久性较高的驱动器为类型相同但持久性较低的驱动器提供缓存，可以使用 **Enable-ClusterS2D** cmdlet 的 **-CacheDeviceModel** 参数指定要使用的驱动器型号。 将使用该模型的所有驱动器进行缓存。

   >[!TIP]
   > 请确保型号字符串与 **Get-PhysicalDisk** 输出中显示的字符串完全一样。

####  <a name="example"></a>示例

首先获取物理磁盘的列表：

```PowerShell
Get-PhysicalDisk | Group Model -NoElement
```

下面是一些示例输出：

```
Count Name
----- ----
    8 FABRIKAM NVME-1710
   16 CONTOSO NVME-1520
```

然后输入以下命令并指定缓存设备型号：

```PowerShell
Enable-ClusterS2D -CacheDeviceModel "FABRIKAM NVME-1710"
```

可以验证所需的驱动器是否正用于缓存，方法是在 PowerShell 中运行 **Get-PhysicalDisk**，并确认其 **Usage** 属性是否显示 **"Journal"** 。

### <a name="manual-deployment-possibilities"></a>手动部署的可行性

手动配置支持以下部署可行性：

![关系图显示了部署的可能性，包括 N V M e 用于缓存和容量、S D 用于缓存和容量、S D 用于缓存以及混合 S D 和 x d D 的容量。](media/cache/Exotic-Deployment-Possibilities.png)

### <a name="set-cache-behavior"></a>设置缓存行为

可以重写缓存的默认行为。 例如，即使在全闪存部署中，也可将其设置为缓存读取。 除非你确定默认行为不符合工作负荷，否则我们不建议修改行为。

若要重写行为，请结合 **-CacheModeSSD** 与 **-CacheModeHDD** 参数使用 **Set-ClusterStorageSpacesDirect** cmdlet。 **CacheModeSSD** 参数设置在为固态硬盘提供缓存时的缓存行为。 **CacheModeHDD** 参数设置在为机械硬盘提供缓存时的缓存行为。

可以使用 **Get-ClusterStorageSpacesDirect** 来验证是否已设置该行为。

#### <a name="example"></a>示例

首先获取存储空间直通设置：

```PowerShell
Get-ClusterStorageSpacesDirect
```

下面是一些示例输出：

```
CacheModeHDD : ReadWrite
CacheModeSSD : WriteOnly
```

然后执行以下操作：

```PowerShell
Set-ClusterStorageSpacesDirect -CacheModeSSD ReadWrite

Get-ClusterS2D
```

下面是一些示例输出：

```
CacheModeHDD : ReadWrite
CacheModeSSD : ReadWrite
```

## <a name="sizing-the-cache"></a>调整缓存大小

应该根据应用程序和工作负荷的工作集（在任何给定时间主动读取或写入的数据）来调整缓存大小。

在使用机械硬盘的混合部署中，调整缓存大小特别重要。 如果活动工作集超过缓存大小，或者活动工作集改变速度太快，则读取缓存未命中次数将会增加，并且需要更频繁地解除暂存写入内容，因而会损害整体性能。

可以使用 Windows 的内置性能监视器 (PerfMon.exe) 实用工具来检查缓存未命中率。 具体而言，可将“群集存储混合磁盘”计数器集中的“缓存未命中读取次数/秒”与部署的整体读取 IOPS 进行比较。  每个“混合磁盘”对应于一个容量驱动器。

例如，绑定到 4 个容量驱动器的 2 个缓存驱动器在每台服务器上产生 4 个“混合磁盘”对象实例。

![Performance-Monitor](media/cache/PerfMon.png)

不存在通用的法则，但如果过多的读取未命中缓存，则可能表示缓存的大小不足，你应该考虑添加缓存驱动器来扩展缓存。 随时都可以单独添加缓存驱动器或容量驱动器。

## <a name="next-steps"></a>后续步骤

若要学习更多存储知识，另请参阅：

- [容错和存储效率](fault-tolerance.md)
- [群集和池仲裁](quorum.md)
