---
title: 在 Azure Stack HCI 中扩展卷
description: 如何使用 Windows 管理中心和 PowerShell 在 Azure Stack HCI 中调整卷的大小。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 703931b0dccb533b2b924847eb3302f0efa46d1a
ms.sourcegitcommit: 900332596d0bb473d82b1d1a28c3fe3aa6522add
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79089293"
---
# <a name="extending-volumes-in-storage-spaces-direct"></a>扩展存储空间直通中的卷
> 适用于： Windows Server 2019

本主题提供使用 Windows 管理中心调整[存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)群集上的卷大小的说明。

> [!WARNING]
> **不支持：调整存储空间直通使用的基础存储的大小。** 如果在虚拟化存储环境（包括在 Azure 中）中运行存储空间直通，则不支持调整或更改虚拟机所使用的存储设备的特性，将导致数据变得不可访问。 相反，请按照[添加服务器或驱动器](/windows-server/storage/storage-spaces/add-nodes)部分中的说明进行操作，然后再扩展卷。

观看有关如何调整卷大小的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/hqyBzipBoTI]

## <a name="extending-volumes-using-windows-admin-center"></a>使用 Windows 管理中心扩展卷

1. 在 Windows 管理中心，连接到存储空间直通群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在 "**卷**" 页上，选择 "**清单**" 选项卡，然后选择要调整大小的卷。

    在 "卷详细信息" 页上，指出了卷的存储容量。 还可以直接从仪表板打开 "卷详细信息" 页。 在仪表板上的 "警报" 窗格中，选择警报，该警报将通知你卷的存储容量是否不足，然后选择 "**转向卷**"。

4. 在卷详细信息页的顶部，选择 "**调整大小**"。
5. 输入新的大小，然后选择 "**调整**大小"。

    在 "卷详细信息" 页上，指出了卷的较大存储容量，并清除了仪表板上的警报。

## <a name="extending-volumes-using-powershell"></a>使用 PowerShell 扩展卷

### <a name="capacity-in-the-storage-pool"></a>存储池中的容量

在调整卷大小之前，请确保存储池中有足够的容量来容纳新的、更大的占用空间。 例如，当将三向镜像卷的大小从 1 TB 调整到 2 TB 时，其占用量将从 3 TB 增长到 6 TB。 为了使调整大小成功，你至少需要在存储池中有（6-3） = 3 TB 的可用容量。

### <a name="familiarity-with-volumes-in-storage-spaces"></a>熟悉存储空间中的卷

在存储空间直通中，每个卷都包含若干堆积对象：群集共享卷（CSV），这是一个卷;分区;磁盘，它是一个虚拟磁盘;和一个或多个存储层（如果适用）。 若要调整卷的大小，需要调整其中几个对象的大小。

![卷-smapi](media/extend-volumes/volumes-in-smapi.png)

若要熟悉它们，请尝试在 PowerShell 中运行具有相应名词的**Get** 。

例如：

```PowerShell
Get-VirtualDisk
```

若要跟踪堆栈中对象之间的关联，请通过管道将一个**Get** cmdlet 传递到下一个。

例如，下面介绍了如何从虚拟磁盘到其卷：

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-Disk | Get-Partition | Get-Volume 
```

### <a name="step-1--resize-the-virtual-disk"></a>步骤1–调整虚拟磁盘的大小

虚拟磁盘可能使用存储层，而不是根据创建的方式。

若要检查，请运行以下 cmdlet：

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier 
```

如果该 cmdlet 不返回任何内容，则虚拟磁盘不使用存储层。

#### <a name="no-storage-tiers"></a>无存储层

如果虚拟磁盘没有存储层，可以使用**VirtualDisk** cmdlet 直接调整其大小。

提供 **-size**参数中的新大小。

```PowerShell
Get-VirtualDisk <FriendlyName> | Resize-VirtualDisk -Size <Size>
```

调整**VirtualDisk**的大小时，**磁盘**会自动跟随，并调整大小。

![调整大小-VirtualDisk](media/extend-volumes/Resize-VirtualDisk.gif)

#### <a name="with-storage-tiers"></a>具有存储层

如果虚拟磁盘使用存储层，则可以使用**StorageTier** cmdlet 单独调整每个层的大小。

按照虚拟磁盘中的关联获取存储层的名称。

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier | Select FriendlyName
```

然后，对于每个层，在 **-size**参数中提供新大小。

```PowerShell
Get-StorageTier <FriendlyName> | Resize-StorageTier -Size <Size>
```

> [!TIP]
> 如果你的级别不同于物理媒体类型（如**媒体大小 = SSD** ，媒体类型 **= HDD**），则需要确保存储池中的每个媒体类型都有足够的容量来容纳每个层的新的更大内存占用量。

调整**StorageTier**的大小时， **VirtualDisk**和**磁盘**会自动执行并调整大小。

![调整大小-StorageTier](media/extend-volumes/Resize-StorageTier.gif)

### <a name="step-2--resize-the-partition"></a>步骤 2-调整分区大小

接下来，使用重**设大小分区**cmdlet 调整分区大小。 虚拟磁盘应具有两个分区：第一个分区是保留的，不应进行修改;需要调整大小的是**PartitionNumber = 2** ，**类型 = Basic**。

提供 **-size**参数中的新大小。 建议使用受支持的最大大小，如下所示。

```PowerShell
# Choose virtual disk
$VirtualDisk = Get-VirtualDisk <FriendlyName>

# Get its partition
$Partition = $VirtualDisk | Get-Disk | Get-Partition | Where PartitionNumber -Eq 2

# Resize to its maximum supported size 
$Partition | Resize-Partition -Size ($Partition | Get-PartitionSupportedSize).SizeMax
```

当调整**分区**大小时，**卷**和**add-clustersharedvolume**会自动跟随并调整大小。

![调整大小-分区](media/extend-volumes/Resize-Partition.gif)

就这么简单！

> [!TIP]
> 可以通过运行 "**获取卷**" 来验证卷是否具有新大小。

## <a name="next-steps"></a>后续步骤

有关其他必要的存储管理任务的分步说明，另请参阅：

- [规划存储空间直通中的卷](/windows-server/storage/storage-spaces/plan-volumes)
- [在存储空间直通中创建卷](/windows-server/storage/storage-spaces/create-volumes)
- [删除存储空间直通中的卷](/windows-server/storage/storage-spaces/delete-volumes)