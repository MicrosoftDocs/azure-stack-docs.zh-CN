---
title: 了解 Azure Stack HCI 中的缓存
description: 存储空间直通和 Azure Stack HCI 中读取和写入缓存的工作原理。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: f1fc40a6475b8e51a063491cc120e2c4236cbeea
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "79025663"
---
# <a name="understanding-the-cache-in-azure-stack-hci"></a>了解 Azure Stack HCI 中的缓存

>适用于：Windows Server 2019

[存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)提供一个内置的服务器端缓存用于实现最佳存储性能。 此缓存是一个大型的持久性实时读取和写入缓存。  启用存储空间直通时，系统会自动配置该缓存。 在大多数情况下，无需进行任何手动管理。
缓存的工作原理取决于现有的驱动器类型。

以下视频详细说明了存储空间直通的缓存工作原理，以及其他设计注意事项。

<strong>存储空间直通设计注意事项</strong><br>（20 分钟）<br>
<iframe src="https://channel9.msdn.com/Blogs/windowsserver/Design-Considerations-for-Storage-Spaces-Direct/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="drive-types-and-deployment-options"></a>驱动器类型和部署选项

存储空间直通目前适用于三种类型的存储设备：

<table>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/NVMe-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            NVMe（高速非易失性内存）
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/SSD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            SATA/SAS SSD（固态硬盘）
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/HDD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            HDD（硬盘驱动器）
        </td>
    </tr>
</table>

有六种组合方式，可以归为两个类别：“全闪存”和“混合”。

### <a name="all-flash-deployment-possibilities"></a>全闪存部署可能性

“全部闪存”部署旨在最大化存储性能，不包括机械硬盘 (HDD)。

![All-Flash-Deployment-Possibilities](media/cache/All-Flash-Deployment-Possibilities.png)

### <a name="hybrid-deployment-possibilities"></a>混合部署可能性

混合部署旨在平衡性能和容量或者最大化容量，包括机械硬盘 (HDD)。

![Hybrid-Deployment-Possibilities](media/cache/Hybrid-Deployment-Possibilities.png)

## <a name="cache-drives-are-selected-automatically"></a>自动选择缓存驱动器

在具有多个驱动器类型的部署中，存储空间直通自动将所有“最快”类型的驱动器用于缓存。 剩余的驱动器用于提供容量。

根据以下层次结构确定“最快的”类型。

![Drive-Type-Hierarchy](media/cache/Drive-Type-Hierarchy.png)

例如，如果同时有 NVMe 和 SSD，NVMe 将为 SSD 提供缓存。

如果同时有 SSD 和 HDD，SSD 将为 HDD 提供缓存。

   >[!NOTE]
   > 缓存驱动器不构成可用的存储容量。 存储在缓存中的所有数据也会存储在其他位置，或在取消暂存后立即存储到其他位置。 这意味着部署的总原始存储容量仅为容量驱动器的总和。

当所有驱动器的类型相同时，不会自动配置缓存。 你可以选择手动配置耐用性较高的驱动器，使其为相同类型但耐用性较低的驱动器提供缓存，请参阅[手动配置](#manual-configuration)一节了解操作方法。

   >[!TIP]
   > 在全 NVMe 或全 SSD 部署中（特别是在规模非常小的部署中），不将任何驱动器“耗费”在缓存上可以极大地提高存储效率。

## <a name="cache-behavior-is-set-automatically"></a>自动设置缓存行为

根据要提供缓存的驱动器类型自动确定缓存的行为。 为固态硬盘提供缓存时（例如，为 SSD 提供 NVMe 缓存），只缓存写入内容。 为硬盘驱动器提供缓存（例如，SSD 为 HDD 提供缓存）时，可以缓存读取和写入。

![Cache-Read-Write-Behavior](media/cache/Cache-Read-Write-Behavior.png)

### <a name="write-only-caching-for-all-flash-deployments"></a>适用于全闪存部署的只写缓存

为固态硬盘（NVMe 或 SSD）提供缓存时，只能缓存写入。 由于许多写入和重新写入可以在缓存中合并，然后仅根据需要取消暂存，因此可以降低容量驱动器的磨损，从而降低容量驱动器的累计流量并延长其生命周期。 因此，我们建议选择[耐用性较高、优化写入](http://whatis.techtarget.com/definition/DWPD-device-drive-writes-per-day)的驱动器用于缓存。 容量驱动器可合理地采用较低的写入耐用性。

由于读取不会显著影响闪存的生命周期，并且固态驱动器通常提供较低的读取延迟，因此不缓存读取：直接从容量驱动器提供读取（除非数据最近写入，尚未取消暂存）。 这允许缓存完全专用于写入，从而最大限度地提高其有效性。

这就导致写入特性（例如，写入延迟）由缓存驱动器规定，而读取特性由容量驱动器规定。 两者一致、可预测并且统一。

### <a name="readwrite-caching-for-hybrid-deployments"></a>混合部署的读取/写入缓存

为硬盘驱动器 (HDD) 提供缓存时可以缓存读取*和*写入，为两者提供了类似闪存的低延迟（速度通常高出 ~10 倍）。 读取缓存存储最近和常用的读取数据以进行快速访问，可最大限度地减少 HDD 的随机流量。 （由于寻找和旋转延迟，对 HDD 的随机访问导致产生了大量的延迟和丢失的时间。）对写入进行缓存以吸收突发流量，并且如前所述，合并写入和重新写入，并最大限度地减少容量驱动器的累计流量。

存储空间直通实现了一种算法，即在取消暂存之前取消随机的写入以模拟磁盘的 IO 模式，即使在来自工作负荷（例如虚拟机）的实际 IO 是随机的情况下，这种模式也似乎是序列化的， 这可以实现 IOPS 和 HDD 吞吐量的最大化。

### <a name="caching-in-deployments-with-drives-of-all-three-types"></a>在部署中使用所有三种类型的驱动器进行缓存

当存在所有三种类型的驱动器时，NVMe 驱动器为 SSD 和 HDD 提供缓存。 缓存行为如上所述：只可为 SSD 缓存写入，可为 HDD 缓存读取和写入。 为 HDD 提供缓存的负担均衡分布在缓存驱动器中。

## <a name="summary"></a>摘要

此表总结了哪些驱动器用于缓存、哪些驱动器用作容量空间，以及每个部署可能性对应于哪些缓存行为。

| 部署     | 缓存驱动器                        | 容量驱动器 | 缓存行为（默认）  |
| -------------- | ----------------------------------- | --------------- | ------------------------- |
| 所有 NVMe         | 无（可选：手动配置） | NVMe            | 只写（如果已配置）  |
| 所有 SSD          | 无（可选：手动配置） | SSD             | 只写（如果已配置）  |
| NVMe + SSD       | NVMe                                | SSD             | 只写                  |
| NVMe + HDD       | NVMe                                | HDD             | 读取 + 写入                |
| SSD + HDD        | SSD                                 | HDD             | 读取 + 写入                |
| NVMe + SSD + HDD | NVMe                                | SSD + HDD       | HDD 为读取 + 写入，SSD 为只写  |

## <a name="server-side-architecture"></a>服务器端体系结构

在驱动器级别实现缓存：一台服务器中的单独缓存驱动器被绑定至相同服务器中的一个或多个容量驱动器。

由于缓存位于其余 Windows 软件定义的存储堆栈之下，它没有（也不需要）任何概念感知（例如存储空间或容错）。 你可以将其视为创建“混合”驱动器（部分闪存、部分磁盘），此驱动器之后将呈现给 Windows。 与实际混合驱动器一样，热数据和冷数据在物理媒介的较快部分和较慢部分之间的实时移动对外部来说几乎是不可见的。

鉴于存储空间直通中的复原至少是服务器级别（这意味着数据副本通常写入不同的服务器中；每个服务中最多存在一个副本），缓存中的数据与不在缓存中的数据一样受益于复原。

![Cache-Server-Side-Architecture](media/cache/Cache-Server-Side-Architecture.png)

例如，使用三向镜像时，任何数据的三个副本都被写入到不同的服务器中，它们将在服务器中进入缓存。 无论它们稍后是否取消暂存，三个副本将始终存在。

## <a name="drive-bindings-are-dynamic"></a>驱动器绑定是动态的

缓存驱动器和容量驱动器之间的绑定比率可以是从 1:1 到 1:12 及更高的任意比率。 无论是添加还是删除驱动器（例如，纵向扩展或失败后），比率都会自动调节。 这意味着，你随时可以独立添加缓存驱动器或容量驱动器。

![Dynamic-Binding](media/cache/Dynamic-Binding.gif)

为了对称，我们建议将容量驱动器的数量设置为缓存驱动器数量的倍数。 例如，如果你有 4 个缓存驱动器，则使用 8 个容量驱动器（1:2 的比率），这样一来，你所能体验到的性能将比使用 7 个或 9 个容量驱动器都要高。

## <a name="handling-cache-drive-failures"></a>处理缓存驱动器故障

当缓存驱动器发生故障时，*本地服务器*将丢失任何尚未取消暂存的写入，这意味着它们只存在于其他副本（位于其他服务器）中。 和其他任何驱动器发生故障后一样，存储空间能够通过咨询正常运行的副本自动恢复。

在短时间内，与丢失缓存的驱动器绑定的容量驱动器将显示不正常。 一旦（自动）发生缓存重绑定且（自动）完成数据修复，它们将恢复显示为正常。

这种情况就是每台服务器需要至少两个缓存驱动器才能保障性能的原因。

![Handling-Failure](media/cache/Handling-Failure.gif)

然后，你可以像更换其他任何驱动器一样，更换缓存驱动器。

   >[!NOTE]
   > 你可能需要关闭电源才能安全更换外接卡 (AIC) 或 M.2 外形规格的 NVMe。

## <a name="relationship-to-other-caches"></a>与其他缓存之间的关系

Windows 软件定义存储堆栈中有多个其他无关的缓存。 示例包括存储空间回写缓存和群集共享卷 (CSV) 内存中读取缓存。

如果具有存储空间直通，不应从其默认行为中修改存储空间回写缓存。 例如，不应使用 **New-Volume** cmdlet 上的 **-WriteCacheSize** 等参数。

你可以选择是否使用 CSV 缓存，由你决定。 存储空间直通中的 CSV 缓存默认关闭，但它们不会以任何方式与本主题中描述的新缓存冲突。 在某些情况下，这可以提供有价值的性能改善。 有关详细信息，请参阅[如何启用 CSV 缓存](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional)。

## <a name="manual-configuration"></a>手动配置

大多数部署无需进行手动配置。 如果需要，请参阅以下各节。 

如果需要在安装后更改缓存设备模型，请编辑运行状况服务的支持组件文档，如[运行状况服务概述](/windows-server/failover-clustering/health-service-overview#supported-components-document)中所述。

### <a name="specify-cache-drive-model"></a>指定缓存驱动器模型

在所有驱动器类型相同的部署（例如，全 NVMe 或全 SSD 部署）中不会配置缓存，这是因为在相同类型的驱动器中，Windows 无法自动区分写入耐用性等特性。

若要使用耐用性较高的驱动器为相同类型但耐用性较低的驱动器提供缓存，你可以指定用于 **Enable-ClusterS2D** cmdlet 的 **-CacheDeviceModel** 参数的驱动器模型。 启用存储空间直通后，这一模型的所有驱动器将全部用于缓存。

   >[!TIP]
   > 请务必与 **Get-PhysicalDisk** 输出中显示的模型字符串完全匹配。

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

通过在 PowerShell 中运行 **Get-PhysicalDisk** 并验证其 **Usage** 属性是否显示 **"Journal"**，你可以验证预期的驱动器是否用于缓存。

### <a name="manual-deployment-possibilities"></a>手动部署可能性

手动配置可以实现以下部署可能性：

![Exotic-Deployment-Possibilities](media/cache/Exotic-Deployment-Possibilities.png)

### <a name="set-cache-behavior"></a>设置缓存行为

可以替代默认缓存行为。 例如，即使在全闪存部署中，你也可以将默认缓存行为设置为缓存读取。 除非确定默认缓存行为不适用于你的工作负荷，否则我们不鼓励修改默认行为。

若要重写此行为，请使用**ClusterStorageSpacesDirect** cmdlet 及其 **-CacheModeSSD**和 **-CacheModeHDD**参数。 为固态硬盘提供缓存时，**CacheModeSSD** 参数设为缓存行为。 为硬盘驱动器提供缓存时，**CacheModeHDD** 参数设为缓存行为。 启用存储空间直通后可以随时进行设置。

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

然后，执行以下操作：

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

应调整缓存大小以适应应用程序和工作负荷的工作集（在任何给定时间主动读取或写入的数据）。

这在使用硬盘驱动器的混合部署中尤其重要。 如果活动工作集超过缓存大小，或如果活动工作集过快设置偏离，会增加读取缓存遗漏并需要更主动地取消写入暂存，从而影响总体性能。

你可以使用 Windows 中的内置性能监视器 (PerfMon.exe) 工具检查缓存遗漏率。 具体来说，你可以将**群集存储混合磁盘**计数器集中的**每秒缓存遗漏读取**与部署中的 IOPS 整体读取进行比较。 每个“混合磁盘”对应一个容量驱动器。

例如，2 个缓存驱动器绑定 4 个容量驱动器会导致每台服务器中有 4 个“混合磁盘”对象实例。

![Performance-Monitor](media/cache/PerfMon.png)

虽然没有通用的法则，但是，如果遗漏缓存的读取过多可能会使容量大小不足，你应考虑添加缓存驱动器以扩展缓存。 你随时可以独立添加缓存驱动器或容量驱动器。

## <a name="next-steps"></a>后续步骤

有关其他存储知识，另请参阅：

- [容错和存储效率](fault-tolerance.md)
- [群集和池仲裁](quorum.md)
