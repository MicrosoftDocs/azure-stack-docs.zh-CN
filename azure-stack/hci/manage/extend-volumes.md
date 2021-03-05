---
title: 在 Azure Stack HCI 中展开卷
description: 如何使用 Windows 管理中心和 PowerShell 在 Azure Stack HCI 中扩展卷。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/04/2021
ms.openlocfilehash: 275cafad3f813b28e2b926ad2008bca777904e0e
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187327"
---
# <a name="expanding-volumes-in-azure-stack-hci"></a>在 Azure Stack HCI 中扩展卷

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

本主题提供使用 Windows 管理中心和 PowerShell 展开群集上的卷的说明。

> [!WARNING]
> **不支持：重设存储空间直通使用的基础存储的大小。** 如果在虚拟化存储环境（包括 Azure）中运行存储空间直通，则不支持重设虚拟机所用存储设备的大小或更改其特征，那样做会导致数据变得不可访问。 相反，请按照 [添加服务器或驱动器](/windows-server/storage/storage-spaces/add-nodes) 部分中的说明进行操作，然后再扩展卷。

## <a name="expand-volumes-using-windows-admin-center"></a>使用 Windows 管理中心展开卷

1. 在 Windows Admin Center 中连接到 Azure Stack HCI 群集，然后在“工具”窗格中选择“卷”。 
2. 在 " **卷** " 页上，选择 " **清单** " 选项卡，然后选择要扩展的卷。

    卷的存储容量在卷详细信息页上指示。 还可以直接从“仪表板”打开卷详细信息页。 在“仪表板”上的“警报”窗格中选择警报（此警报会通知你卷的存储容量是否足够），然后选择“转到卷”  。

3. 在卷详细信息页的顶部，选择 " **展开**"。
4. 输入较大的新大小，然后选择 " **展开**"。

    卷的这个更大的存储容量在卷详细信息页上指示，“仪表板”上的警报会清除。

## <a name="expand-volumes-using-powershell"></a>使用 PowerShell 展开卷

### <a name="capacity-in-the-storage-pool"></a>存储池中的容量

扩展卷之前，请确保存储池中有足够的容量来容纳新的、更大的占用空间。 例如，将三向镜像卷从 1 TB 扩展到 2 TB 时，其占用量将从 3 TB 增长到 6 TB。 若要成功扩展，至少需要 (6-3) = 3 TB 的存储池中可用容量。

### <a name="familiarity-with-volumes-in-storage-spaces"></a>熟悉存储空间中的卷

在存储空间直通中，每个卷都包含若干堆积对象：群集共享卷 (CSV)，这是一个卷；分区；磁盘，这是一个虚拟磁盘；一个或多个存储层（如果适用）。 若要重设卷的大小，需要重设其中几个对象的大小。

![关系图显示了卷的各个层，包括群集分片卷层、卷层、分区层、磁盘层、虚拟磁盘层和存储层。](media/extend-volumes/volumes-in-smapi.png)

若要自行熟悉这些卷，请尝试在 PowerShell 中运行具有相应名词的 **Get-** cmdlet，。

例如：

```PowerShell
Get-VirtualDisk
```

若要跟踪堆栈中对象之间的关联，请通过管道将一个 **Get-** cmdlet 传递到下一个中。

例如，下面展示了如何将 Get- cmdlet 从虚拟磁盘传递到其卷：

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-Disk | Get-Partition | Get-Volume
```

### <a name="step-1--expand-the-virtual-disk"></a>步骤1–展开虚拟磁盘

虚拟磁盘可能使用存储层，也可能不使用，具体取决于其创建方式。

若要进行检查，请运行以下 cmdlet：

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier
```

如果该 cmdlet 不返回任何内容，则表明虚拟磁盘不使用存储层。

#### <a name="no-storage-tiers"></a>无存储层

如果虚拟磁盘没有存储层，可以直接使用 **VirtualDisk** cmdlet 将其展开。

在 **-Size** 参数中提供新大小。

```PowerShell
Get-VirtualDisk <FriendlyName> | Resize-VirtualDisk -Size <Size>
```

展开 **VirtualDisk** 时， **磁盘** 会自动执行并调整大小。

![动态图显示了卷的虚拟磁盘变大，而其上紧邻的磁盘层会因此而自动变大。](media/extend-volumes/Resize-VirtualDisk.gif)

#### <a name="with-storage-tiers"></a>有存储层

如果虚拟磁盘使用存储层，则可以使用 **StorageTier** cmdlet 单独展开每个层。

按照虚拟磁盘中的关联获取存储层的名称。

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier | Select FriendlyName
```

然后，在 **-Size** 参数中为每个层提供新大小。

```PowerShell
Get-StorageTier <FriendlyName> | Resize-StorageTier -Size <Size>
```

> [!TIP]
> 如果层是不同的物理媒体类型（例如 **MediaType = SSD**，**MediaType = HDD**），则需确保存储池中每个媒体类型有足够的容量来容纳每个层占用量更大的新项目。

展开 **StorageTier** (s) 时，会自动执行 **VirtualDisk** 和 **磁盘** 操作并调整其大小。

![动态图显示了两个存储层先后变大，而其上的虚拟磁盘层和磁盘层也变大了。](media/extend-volumes/Resize-StorageTier.gif)

### <a name="step-2--expand-the-partition"></a>步骤2–展开分区

接下来，使用重 **设大小分区** cmdlet 展开分区。 虚拟磁盘应有两个分区：第一个分区为保留分区，不应进行修改；第二个分区是需要重设大小的分区，应使用 **PartitionNumber = 2** 和 **Type = Basic** 进行设置。

在 **-Size** 参数中提供新大小。 建议使用受支持的最大大小，如下所示。

```PowerShell
# Choose virtual disk
$VirtualDisk = Get-VirtualDisk <FriendlyName>

# Get its partition
$Partition = $VirtualDisk | Get-Disk | Get-Partition | Where PartitionNumber -Eq 2

# Resize to its maximum supported size
$Partition | Resize-Partition -Size ($Partition | Get-PartitionSupportedSize).SizeMax
```

展开 **分区** 时， **卷** 和 **add-clustersharedvolume** 将自动进行跟踪并调整大小。

![动态图显示了卷底部的虚拟磁盘层随着其上各层变大也变大。](media/extend-volumes/Resize-Partition.gif)

就这么简单！

> [!TIP]
> 可以通过运行 **Get-Volume** 来验证该卷是否有新大小。

## <a name="next-steps"></a>后续步骤

如需其他重要的存储管理任务的分步说明，另请参阅：

- [规划卷](../concepts/plan-volumes.md)
- [创建卷](create-volumes.md)
- [删除卷](delete-volumes.md)