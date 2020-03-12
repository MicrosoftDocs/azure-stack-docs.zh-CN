---
title: 了解 Azure Stack HCI 中的缓存
description: 读写缓存的工作原理存储空间直通和 Azure Stack HCI。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: f1fc40a6475b8e51a063491cc120e2c4236cbeea
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025663"
---
# <a name="understanding-the-cache-in-azure-stack-hci"></a>了解 Azure Stack HCI 中的缓存

>适用于： Windows Server 2019

[存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)提供内置的服务器端缓存功能，以最大程度地提高存储性能。 它是一个大型、持久的*实时读写*缓存。 启用存储空间直通时，将自动配置缓存。 在大多数情况下，无需手动管理。
缓存的工作方式取决于存在的驱动器类型。

以下视频详细介绍了如何对存储空间直通进行缓存，以及其他设计注意事项。

<strong>存储空间直通设计注意事项</strong><br>（20分钟）<br>
<iframe src="https://channel9.msdn.com/Blogs/windowsserver/Design-Considerations-for-Storage-Spaces-Direct/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="drive-types-and-deployment-options"></a>驱动器类型和部署选项

存储空间直通当前适用于三种类型的存储设备：

<table>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/NVMe-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            NVMe （非易失性内存 Express）
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/SSD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            SATA/SAS SSD （固态驱动器）
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/HDD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            HDD （硬盘驱动器）
        </td>
    </tr>
</table>

这些方法可以分为六种方法，组合成两个类别： "全部闪烁" 和 "混合"。

### <a name="all-flash-deployment-possibilities"></a>所有-flash 部署可能性

所有闪存部署都旨在最大程度地提高存储性能，不包括旋转硬盘驱动器（HDD）。

![所有-Flash 部署-可能](media/cache/All-Flash-Deployment-Possibilities.png)

### <a name="hybrid-deployment-possibilities"></a>混合部署可能性

混合部署旨在平衡性能和容量，或最大限度地提高容量并包括旋转硬盘驱动器（HDD）。

![混合部署-可能](media/cache/Hybrid-Deployment-Possibilities.png)

## <a name="cache-drives-are-selected-automatically"></a>自动选择缓存驱动器

在具有多种类型的驱动器的部署中，存储空间直通自动使用 "最快" 类型的所有驱动器进行缓存。 剩余的驱动器用于容量。

根据下面的层次结构确定哪个类型为 "最快"。

![驱动器类型-层次结构](media/cache/Drive-Type-Hierarchy.png)

例如，如果有 NVMe 和 Ssd，则 NVMe 会缓存 Ssd。

如果你有 Ssd 和 Hdd，Ssd 将缓存 Hdd。

   >[!NOTE]
   > 缓存驱动器不提供可用存储容量。 缓存中存储的所有数据也存储在其他位置，或者在执行完步骤之后。 这意味着部署的总原始存储容量是仅容量驱动器之和。

如果所有驱动器的类型相同，则不会自动配置缓存。 你可以选择将较高耐用性的驱动器手动配置为相同类型的低耐用性驱动器的缓存–请参阅 "[手动配置](#manual-configuration)" 部分以了解如何操作。

   >[!TIP]
   > 在所有 NVMe 或全部 SSD 部署中，特别是在规模非常小的情况下，缓存上没有 "消耗" 任何驱动器可以有意义地提高存储效率。

## <a name="cache-behavior-is-set-automatically"></a>自动设置缓存行为

缓存的行为是根据要为其缓存的驱动器的类型自动确定的。 缓存固态驱动器（如 Ssd 的 NVMe 缓存）时，仅缓存写入。 对硬盘驱动器进行缓存（例如 Hdd 的 Ssd 缓存）时，将缓存读取和写入。

![缓存-读写-行为](media/cache/Cache-Read-Write-Behavior.png)

### <a name="write-only-caching-for-all-flash-deployments"></a>所有闪存部署的仅写缓存

缓存固态驱动器（NVMe 或 Ssd）时，仅缓存写入。 这会减少容量驱动器上的磨损，因为许多写入和重新写入可以在缓存中合并，然后仅在需要时进行转移，从而降低容量驱动器的累计流量并延长其生存期。 出于此原因，我们建议为缓存选择[较高的、写入优化](http://whatis.techtarget.com/definition/DWPD-device-drive-writes-per-day)的驱动器。 容量驱动器可能合理具有更低的写入耐用性。

由于读取操作并不会显著影响闪存的生存期，并且固态驱动器全局提供较低的读取延迟，因此不会缓存读取：它们直接从容量驱动器中提供但已被取消。 这允许缓存完全专用于写入，从而最大限度地提高其效率。

这会导致缓存驱动器指示写入特征，如写入延迟，而读取特征由容量驱动器决定。 两者都是一致的、可预测的和统一的。

### <a name="readwrite-caching-for-hybrid-deployments"></a>混合部署的读/写缓存

如果缓存了硬盘驱动器（Hdd），则会缓存读*写，* 以便为两者提供类似于闪烁的延迟（对于这两种情况，速度通常更快约10倍）。 读缓存会存储最近和经常读取的数据，以便快速访问，并将随机流量降至最低。 （由于寻找和旋转延迟，随机访问 HDD 产生的延迟和丢失时间都很重要。）写入缓存可吸收突发，并与之前一样，用于合并写入并重新写入数据，并最大程度地减少到容量驱动器的累计流量。

存储空间直通实现了一种算法，该算法在取消对它们进行反转储之前进行随机化写入，将 IO 模式模拟为即使在工作负荷的实际 IO （例如虚拟机）是随机的情况下才出现顺序。 这样可以最大限度地提高 Hdd 的 IOPS 和吞吐量。

### <a name="caching-in-deployments-with-drives-of-all-three-types"></a>在部署中缓存所有三个类型的驱动器

当存在所有三个类型的驱动器时，NVMe 驱动器将为 Ssd 和 Hdd 提供缓存。 如上所述，此行为如下所述：仅对 Ssd 缓存写入，并为 Hdd 缓存读取和写入。 Hdd 缓存的负担在缓存驱动器之间平均分布。

## <a name="summary"></a>摘要

此表汇总了用于缓存的驱动器，用于容量的驱动器，以及每个部署可能的缓存行为。

| 部署     | 缓存驱动器                        | 容量驱动器 | 缓存行为（默认值）  |
| -------------- | ----------------------------------- | --------------- | ------------------------- |
| 所有 NVMe         | 无（可选：手动配置） | NVMe            | 只写（如果已配置）  |
| 所有 SSD          | 无（可选：手动配置） | SSD             | 只写（如果已配置）  |
| NVMe + SSD       | NVMe                                | SSD             | 只写                  |
| NVMe + HDD       | NVMe                                | HDD             | 读取 + 写入                |
| SSD + HDD        | SSD                                 | HDD             | 读取 + 写入                |
| NVMe + SSD + HDD | NVMe                                | SSD + HDD       | 对 HDD 进行读取 + 写入，为 SSD 写入只读  |

## <a name="server-side-architecture"></a>服务器端体系结构

缓存在驱动器级别实现：一台服务器中的单个缓存驱动器绑定到同一个服务器中的一个或多个容量驱动器。

因为缓存低于 Windows 软件定义的存储堆栈的其余部分，所以它不需要任何概念（例如存储空间或容错）。 可以将其视为创建 "混合" （部件闪存、部分磁盘）驱动器，然后将其提供给 Windows。 与实际混合驱动器一样，物理媒体的速度更快、速度较慢的部分之间的热数据和冷数据的实时移动几乎不可见。

考虑到存储空间直通中的复原至少为服务器级别（也就是说，数据副本始终写入到不同的服务器上; 每台服务器最多只写入一个副本），缓存中的数据与缓存中的数据的复原能力相同。

![缓存-服务器端体系结构](media/cache/Cache-Server-Side-Architecture.png)

例如，使用三向镜像时，会将所有数据的三个副本写入到它们驻留在缓存中的不同服务器。 无论以后是否取消暂存，都将始终存在三个副本。

## <a name="drive-bindings-are-dynamic"></a>驱动器绑定是动态的

缓存和容量驱动器之间的绑定可以有从1:1 到1:12 及更高的比率。 添加或删除驱动器时，它会动态进行调整，例如在增加或故障后进行扩展。 这意味着，只要需要，就可以独立地添加缓存驱动器或容量驱动器。

![动态绑定](media/cache/Dynamic-Binding.gif)

建议将容量驱动器数增加为对称的缓存驱动器数的倍数。 例如，如果有4个缓存驱动器，使用8个容量驱动器（1:2 比）可以获得比7或9更高的性能。

## <a name="handling-cache-drive-failures"></a>处理缓存驱动器故障

缓存驱动器发生故障时，尚未取消暂存的任何写入都将丢失*到本地服务器*，这意味着它们只存在于其他副本（在其他服务器中）。 就像在任何其他驱动器发生故障后，存储空间可通过咨询保留下来的副本自动恢复。

短暂的一段时间内，绑定到丢失缓存驱动器的容量驱动器将会出现不正常。 发生缓存重新绑定（自动）并且数据修复完成（自动）后，它们将恢复显示为正常。

这种情况的原因是每台服务器至少需要两个缓存驱动器来保持性能。

![处理-失败](media/cache/Handling-Failure.gif)

然后，可以替换缓存驱动器，就像替换任何其他驱动器。

   >[!NOTE]
   > 你可能需要断电，以安全地替换作为插件卡（AIC）或 M. 2 外形规格的 NVMe。

## <a name="relationship-to-other-caches"></a>与其他缓存的关系

Windows 软件定义的存储堆栈中有一些其他不相关的缓存。 示例包括存储空间回写缓存和群集共享卷（CSV）内存中读取缓存。

对于存储空间直通，不应根据默认行为修改存储空间回写缓存。 例如，不应使用 WriteCacheSize **cmdlet 上的参数**，如 **--** 。

您可以选择使用 CSV 缓存，而不是由您来决定。 默认情况下，它在存储空间直通关闭，但不会以任何方式与本主题中所述的新缓存发生冲突。 在某些情况下，它可以提高性能。 有关详细信息，请参阅[如何启用 CSV 缓存](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional)。

## <a name="manual-configuration"></a>手动配置

对于大多数部署，不需要手动配置。 如果需要，请参阅以下各节。 

如果需要在安装后更改缓存设备模型，请编辑运行状况服务的支持组件文档，如[运行状况服务概述](/windows-server/failover-clustering/health-service-overview#supported-components-document)中所述。

### <a name="specify-cache-drive-model"></a>指定缓存驱动器型号

在所有驱动器都属于相同类型的部署中，如全部 NVMe 或全部 SSD 部署，不会配置缓存，因为 Windows 无法在同一类型的驱动器之间自动区分写入耐用性等特性。

若要使用较高耐用性的驱动器来缓存相同类型的低耐用性驱动器，可以通过**enable-clusters2d** cmdlet 的 **-CacheDeviceModel**参数指定要使用的驱动器型号。 启用存储空间直通后，该模型的所有驱动器将用于缓存。

   >[!TIP]
   > 请确保与**PhysicalDisk**的输出中显示的模型字符串完全相同。

####  <a name="example"></a>示例

首先，获取物理磁盘列表：

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

然后输入以下命令，并指定缓存设备模型：

```PowerShell
Enable-ClusterS2D -CacheDeviceModel "FABRIKAM NVME-1710"
```

可以通过在 PowerShell 中运行**PhysicalDisk**来验证要用于缓存的驱动器，并验证其**使用情况**属性显示 **"日记"** 。

### <a name="manual-deployment-possibilities"></a>手动部署可能性

手动配置支持以下部署：

![现-可能的部署](media/cache/Exotic-Deployment-Possibilities.png)

### <a name="set-cache-behavior"></a>设置缓存行为

可以重写缓存的默认行为。 例如，可以将其设置为缓存读取，即使在所有闪存部署中也是如此。 除非你确信默认值不适合你的工作负荷，否则我们将不鼓励修改该行为。

若要重写此行为，请使用**ClusterStorageSpacesDirect** cmdlet 及其 **-CacheModeSSD**和 **-CacheModeHDD**参数。 **CacheModeSSD**参数用于设置固态驱动器缓存时的缓存行为。 **CacheModeHDD**参数在缓存硬盘驱动器时设置缓存行为。 启用存储空间直通后，可以随时执行此操作。

你可以使用**ClusterStorageSpacesDirect**来验证是否已设置了行为。

#### <a name="example"></a>示例

首先，获取存储空间直通设置：

```PowerShell
Get-ClusterStorageSpacesDirect
```

下面是一些示例输出：

```
CacheModeHDD : ReadWrite
CacheModeSSD : WriteOnly
```

然后执行下列操作：

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

应该调整缓存的大小，以适应你的应用程序和工作负荷的工作集（在任意给定时间主动读取或写入的数据）。

这在具有硬盘驱动器的混合部署中尤其重要。 如果活动工作集超出缓存的大小，或者活动的工作集偏离过快，则读取缓存未命中数将增加，需要更主动地取消写入，从而影响整体性能。

你可以使用 Windows 中的内置性能监视器（PerfMon）实用程序来检查缓存未命中速率。 具体而言，你可以将 "**群集存储混合磁盘**" 计数器中的 "**缓存未命中读取数/秒**" 与部署的总体读取 IOPS 进行比较。 每个 "混合磁盘" 对应一个容量驱动器。

例如，绑定到4个容量驱动器的2个缓存驱动器生成每个服务器4个混合磁盘对象实例。

![性能-监视器](media/cache/PerfMon.png)

没有通用规则，但是如果缓存太多而缺少缓存，则它可能会很小，并且应考虑添加缓存驱动器来扩展缓存。 您可以根据需要随时单独添加缓存驱动器或容量驱动器。

## <a name="next-steps"></a>后续步骤

有关其他存储知识，另请参阅：

- [容错和存储效率](fault-tolerance.md)
- [群集和池仲裁](quorum.md)
