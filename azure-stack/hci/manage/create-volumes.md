---
title: 在 Azure Stack HCI 中创建卷
description: 如何使用 Windows Admin Center 和 PowerShell 在 Azure Stack HCI 中创建卷。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 9bb0ff34863f8262d5919e5eae6f735709097bf5
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570729"
---
# <a name="create-volumes-in-azure-stack-hci"></a>在 Azure Stack HCI 中创建卷

> 适用于：Azure Stack HCI，版本 20H2

本主题介绍如何使用 Windows Admin Center 和 Windows PowerShell 在 Azure Stack HCI 群集中创建卷、如何处理卷中的文件，以及如何在卷中启用重复数据删除和压缩。 若要了解如何为延伸群集创建卷并设置复制，请参阅[创建延伸卷](create-stretched-volumes.md)。

## <a name="create-a-three-way-mirror-volume"></a>创建三向镜像卷

若要使用 Windows Admin Center 创建三向镜像卷：

1. 在 Windows 管理中心，连接到群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在“卷”页上选择“库存”选项卡，然后选择“创建卷”。  
3. 在“创建卷”窗格中输入卷的名称，并将“复原”保留为“三向镜像”。  
4. 在“HDD 上的大小”中指定卷的大小。 例如 5 TB。
5. 选择“创建”。

创建卷可能需要几分钟时间，具体取决于大小。 卷创建好之后，右上方会显示通知。 新卷将显示在“库存”列表中。

观看有关如何创建三向镜像卷的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/o66etKq70N8]

## <a name="create-a-mirror-accelerated-parity-volume"></a>创建镜像加速奇偶校验卷

镜像加速奇偶校验 (MAP) 降低硬盘驱动器上卷的占用量。 例如，三向镜像卷意味着每 10 TB 的卷大小需要 30 TB 的占用空间。 为了减少占用空间开销，请创建使用镜像加速奇偶校验的卷。 这样，即使只有 4 台服务器，也可以镜像最活跃的 20% 数据，然后使用空间效率更高的奇偶校验来存储剩余数据，从而将占用空间从 30 TB 减到 22 TB。 可以调整奇偶校验和镜像之比，在性能与容量方面做出最适合工作负荷的取舍。 例如，使用 90% 的奇偶校验和 10% 的镜像所带来的性能效果不太理想，但可以进一步优化占用空间。

  >[!NOTE]
  >镜像加速奇偶校验卷需要复原文件系统 (ReFS) 。

若要在 Windows Admin Center 创建使用镜像加速奇偶校验的卷：

1. 在 Windows 管理中心，连接到群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在“卷”页上选择“库存”选项卡，然后选择“创建卷”。 
3. 在“创建卷”窗格中输入卷的名称。
4. 在“复原”中选择“镜像加速奇偶校验”。 
5. 在“奇偶校验百分比”中选择奇偶校验的百分比。
6. 选择“创建”。

观看有关如何创建镜像加速奇偶校验卷的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/R72QHudqWpE]

## <a name="open-volume-and-add-files"></a>打开卷并添加文件

若要在 Windows Admin Center 中打开卷并将文件添加到卷：

1. 在 Windows 管理中心，连接到群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在“卷”页上选择“库存”选项卡。 
2. 在卷列表中，选择要打开的卷的名称。

    在卷详细信息页上，可以看到卷的路径。

4. 在页面顶部选择“打开”。 随即会在 Windows Admin Center 中启动“文件”工具。
5. 导航到卷的路径。 可在此处浏览卷中的文件。
6. 选择“上传”，然后选择要上传的文件。
7. 使用浏览器中的“后退”按钮返回到 Windows Admin Center 中的“工具”窗格。 

观看有关如何打开卷和添加文件的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/j59z7ulohs4]

## <a name="turn-on-deduplication-and-compression"></a>启用重复数据删除和压缩

重复数据删除和压缩是根据每个卷进行管理的。 重复数据删除和压缩使用后处理模型，这意味着，在该功能运行之前，你看不到节省的空间。 该功能在运行时会处理所有文件，甚至包括以前就已存在的文件。

若要了解详细信息，请参阅[启用卷加密、重复数据删除和压缩](volume-encryption-deduplication.md)

## <a name="create-volumes-using-windows-powershell"></a>使用 Windows PowerShell 创建卷

首先，从 Windows 的“开始”菜单启动 Windows PowerShell。 建议使用 **New-Volume** cmdlet 来为 Azure Stack HCI 创建卷。 它可以提供最快且最直接的体验。 此单个 cmdlet 会自动创建虚拟磁盘，对其进行分区和格式化，使用匹配的名称创建卷，并将其添加到群集共享卷 － 这些全都在一个简单的步骤中完成。

**New-Volume** cmdlet 具有你将始终需要提供的四个参数：

- **FriendlyName：** 所需的任何字符串，例如 *“Volume1”*
- **文件系统：** 建议为所有卷 **CSVFS_ReFS** (;镜像加速奇偶校验卷需要) 或 **CSVFS_NTFS**
- **StoragePoolFriendlyName：** 你的存储池名称，例如 *“S2D on ClusterName”*
- **Size：** 卷的大小，例如 *“10 TB”*

   > [!NOTE]
   > Windows 以及 PowerShell 使用二进制（基数为 2）数字进行计数，而系统经常使用十进制（基数为 10）数字来标记驱动器。 这可以说明定义为 1,000,000,000,000 字节的“1 TB”驱动器在 Windows 中显示为大约“909 GB”的原因。 这是正常情况。 使用 **New-Volume** 创建卷时，你应使用二进制（基数为 2）数字指定 **Size** 参数。 例如，指定“909 GB”或“0.909495TB”将创建大约 1,000,000,000,000 字节的卷。

### <a name="example-with-2-or-3-servers"></a>示例：具有 2 个或 3 个服务器

为使操作更简单，如果你的部署仅涉及两个服务器，则存储空间直通将自动使用双向镜像进行复原。 如果你的部署仅涉及三个服务器，则它将自动使用三向镜像。

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB
```

### <a name="example-with-4-servers"></a>示例：具有 4 个以上的服务器

如果你具有四个或更多个服务器，则可以使用可选的 **ResiliencySettingName** 参数来选择复原类型。

-   **ResiliencySettingName**：**镜像** 或 **奇偶校验**。

在以下示例中，*“Volume2”* 使用三向镜像，*“Volume3”* 使用双奇偶校验（通常称为“擦除编码”）。

```PowerShell
New-Volume -FriendlyName "Volume2" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Mirror
New-Volume -FriendlyName "Volume3" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Parity
```

## <a name="using-storage-tiers"></a>使用存储层

在涉及三种驱动器类型的部署中，一个卷可以跨越 SSD 和 HDD 层以在每类驱动器上驻留一部分。 同样，在涉及四个或更多个服务器的部署中，一个卷可以混合镜像和双奇偶校验以在每个服务器上驻留一部分。

为帮助你创建此类卷，Azure Stack HCI 为性能) 提供了名为 **MirrorOn * 媒体** 名称 * 和 **NestedMirrorOn * 媒体** 名称 (* 的默认层级模板，并为容量) 提供 **ParityOn * 媒体** 名称 * 和 **NestedParityOn * 媒体** 名称 * (，其中 *媒体* 空间为 HDD 或 SSD。 这些模板代表基于介质类型的存储层，并封装更快容量驱动器上的三向镜像的定义， (如果适用) ，以及在慢速容量驱动器上 (双重奇偶校验（如果适用）) 。

可以通过在群集中的任何服务器上运行 **StorageTier** cmdlet 来查看它们。

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

例如，如果有一个只有 HDD 的双节点群集，则输出可能如下所示：

```PowerShell
FriendlyName      ResiliencySettingName PhysicalDiskRedundancy
------------      --------------------- ----------------------
NestedParityOnHDD Parity                                     1
Capacity          Mirror                                     1
NestedMirrorOnHDD Mirror                                     3
MirrorOnHDD       Mirror                                     1
```

若要创建分层卷，请使用 **New-Volume** cmdlet 的 **StorageTierFriendlyNames** 和 **StorageTierSizes** 参数引用这些层模板。 例如，以下 cmdlet 会创建一个按 30:70 的比例混合三向镜像和双奇偶校验的卷。

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames MirrorOnHDD, Capacity -StorageTierSizes 300GB, 700GB
```

根据需要重复操作以创建多个卷。

### <a name="nested-resiliency-volumes"></a>嵌套复原卷

嵌套复原仅适用于两个服务器的群集;如果群集有三个或更多服务器，则无法使用嵌套复原功能。 嵌套复原允许两个服务器的群集同时承受多个硬件故障，而不会丢失存储可用性，从而允许用户、应用和虚拟机在不中断的情况下继续运行。 若要了解详细信息，请参阅 [计划卷：选择复原类型](../concepts/plan-volumes.md#choosing-the-resiliency-type)。

#### <a name="create-nested-storage-tiers"></a>创建嵌套存储层

创建 NestedMirror 层：

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedMirror -ResiliencySettingName Mirror -NumberOfDataCopies 4 -MediaType HDD -CimSession 2nodecluster
```

创建 NestedParity 层：

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedParity -ResiliencySettingName Parity -NumberOfDataCopies 2 -PhysicalDiskRedundancy 1 -NumberOfGroups 1 -FaultDomainAwareness StorageScaleUnit -ColumnIsolation PhysicalDisk -MediaType HDD -CimSession 2nodecluster
```

#### <a name="create-nested-volumes"></a>创建嵌套卷

若要创建 NestedMirror 卷：

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyMirrorNestedVolume -StorageTierFriendlyNames NestedMirror -StorageTierSizes 500GB -CimSession 2nodecluster
```

若要创建 NestedParity 卷：

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyParityNestedVolume -StorageTierFriendlyNames NestedMirror,NestedParity -StorageTierSizes 200GB, 1TB -CimSession 2nodecluster
```

### <a name="storage-tier-summary-table"></a>存储层摘要表

下表汇总了可在 Azure Stack HCI 中创建/的存储层。

**NumberOfNodes：2**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | 备注         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | 镜像                | 2                  | 1                      | 1              | StorageScaleUnit     | 物理磁盘    | 自动创建 |
| MirrorOnSSD       | SSD       | 镜像                | 2                  | 1                      | 1              | StorageScaleUnit     | 物理磁盘    | 自动创建 |
| MirrorOnSCM       | SCM       | 镜像                | 2                  | 1                      | 1              | StorageScaleUnit     | 物理磁盘    | 自动创建 |
| NestedMirrorOnHDD | HDD       | 镜像                | 4                  | 3                      | 1              | StorageScaleUnit     | 物理磁盘    | manual       |
| NestedMirrorOnSSD | SSD       | 镜像                | 4                  | 3                      | 1              | StorageScaleUnit     | 物理磁盘    | manual       |
| NestedMirrorOnSCM | SCM       | 镜像                | 4                  | 3                      | 1              | StorageScaleUnit     | 物理磁盘    | manual       |
| NestedParityOnHDD | HDD       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | 物理磁盘    | manual       |
| NestedParityOnSSD | SSD       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | 物理磁盘    | manual       |
| NestedParityOnSCM | SCM       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | 物理磁盘    | manual       |

**NumberOfNodes：3**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | 备注         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | 镜像                | 3                  | 2                      | 1              | StorageScaleUnit     | 物理磁盘    | 自动创建 |
| MirrorOnSSD       | SSD       | 镜像                | 3                  | 2                      | 1              | StorageScaleUnit     | 物理磁盘    | 自动创建 |
| MirrorOnSCM       | SCM       | 镜像                | 3                  | 2                      | 1              | StorageScaleUnit     | 物理磁盘    | 自动创建 |

**NumberOfNodes： 4 +**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | 备注         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | 镜像                | 3                  | 2                      | 1              | StorageScaleUnit     | 物理磁盘    | 自动创建 |
| MirrorOnSSD       | SSD       | 镜像                | 3                  | 2                      | 1              | StorageScaleUnit     | 物理磁盘    | 自动创建 |
| MirrorOnSCM       | SCM       | 镜像                | 3                  | 2                      | 1              | StorageScaleUnit     | 物理磁盘    | 自动创建 |
| ParityOnHDD       | HDD       | Parity                | 1                  | 2                      | 自动           | StorageScaleUnit     | StorageScaleUnit| 自动创建 |
| ParityOnSSD       | SSD       | Parity                | 1                  | 2                      | 自动           | StorageScaleUnit     | StorageScaleUnit| 自动创建 |
| ParityOnSCM       | SCM       | Parity                | 1                  | 2                      | 自动           | StorageScaleUnit     | StorageScaleUnit| 自动创建 |

## <a name="next-steps"></a>后续步骤

有关相关主题和其他存储管理任务，另请参阅：

- [存储空间直通概述](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [规划卷](../concepts/plan-volumes.md)
- [扩展卷](extend-volumes.md)
- [删除卷](delete-volumes.md)