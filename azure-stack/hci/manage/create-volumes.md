---
title: 在 Azure Stack HCI 中创建卷
description: 如何使用 Windows 管理中心和 PowerShell 在 Azure Stack HCI 中创建卷。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: b195a55314935282f43f99bf02bda1d2bc298b54
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370405"
---
# <a name="creating-volumes-in-azure-stack-hci"></a>在 Azure Stack HCI 中创建卷

> 适用于： Windows Server 2019

本主题介绍如何使用 Windows 管理中心和 Windows PowerShell 在存储空间直通群集上创建卷，如何处理卷上的文件，以及如何在卷上启用重复数据删除和压缩。

## <a name="create-a-three-way-mirror-volume"></a>创建三向镜像卷

使用 Windows 管理中心创建三向镜像卷：

1. 在 Windows 管理中心，连接到存储空间直通群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在 "**卷**" 页上，选择 "**清单**" 选项卡，然后选择 "**创建卷**"。
3. 在 "**创建卷**" 窗格中，输入卷的名称，并将**复原**保留为**三向镜像**。
4. 在**HDD 上**，指定卷的大小。 例如 5 TB （tb）。
5. 选择“创建”。

创建卷可能需要几分钟的时间，具体取决于大小。 右上方的通知将通知你创建卷的时间。 新卷将出现在 "清单" 列表中。

观看有关如何创建三向镜像卷的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/o66etKq70N8]

## <a name="create-a-mirror-accelerated-parity-volume"></a>创建镜像加速奇偶校验卷

镜像加速奇偶校验降低了硬盘驱动器上卷的占用量。 例如，三向镜像卷意味着每 10 tb 大小都需要 30 tb 的空间。 若要减少占用空间，请创建具有镜像加速奇偶校验的卷。 这会将内存占用量从 30 tb 减少到 22 tb，甚至只有4台服务器，通过镜像最活跃的20% 的数据，并使用奇偶校验（更节省空间）来存储其余数据。 你可以调整奇偶校验和镜像的比率，以使性能与容量平衡最适合你的工作负荷。 例如，90% 的奇偶校验和10% 的镜像性能降低，但会进一步简化占用空间。

使用 Windows 管理中心中的镜像加速奇偶校验创建卷：

1. 在 Windows 管理中心，连接到存储空间直通群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在 "卷" 页上，选择 "**清单**" 选项卡，然后选择 "**创建卷**"。
3. 在 "**创建卷**" 窗格中，输入卷的名称。
4. 在**复原能力**中，选择**镜像加速的奇偶校验**。
5. 在 "**奇偶校验百分比**" 中，选择奇偶校验百分比。
6. 选择“创建”。

观看有关如何创建镜像加速奇偶校验卷的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/R72QHudqWpE]

## <a name="open-volume-and-add-files"></a>打开卷并添加文件

若要在 Windows 管理中心中打开卷并将文件添加到卷，请执行以下操作：

1. 在 Windows 管理中心，连接到存储空间直通群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在 "**卷**" 页上，选择 "**清单**" 选项卡。
2. 在卷列表中，选择要打开的卷的名称。

    在 "卷详细信息" 页上，可以查看卷的路径。

4. 在页面顶部，选择 "**打开**"。 这将启动 Windows 管理中心中的 "**文件**" 工具。
5. 导航到卷的路径。 可在此处浏览卷中的文件。
6. 选择 "**上传**"，然后选择要上传的文件。
7. 使用浏览器的 "**后退**" 按钮返回到 Windows 管理中心中的 "**工具**" 窗格。

观看有关如何打开卷和添加文件的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/j59z7ulohs4]

## <a name="turn-on-deduplication-and-compression"></a>启用重复数据删除和压缩

重复数据删除和压缩按卷进行管理。 重复数据删除和压缩使用后处理模型，这意味着在运行之前不会出现节省费用。 在此过程中，它将处理所有文件，甚至包括之前存在的文件。

1. 在 Windows 管理中心，连接到存储空间直通群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在 "**卷**" 页上，选择 "**清单**" 选项卡。
3. 在卷列表中，选择要管理的卷的名称。
4. 在 "卷详细信息" 页上，单击标记为**重复数据删除和压缩**的开关。
5. 在 "**启用重复数据删除**" 窗格中，选择 "重复数据删除" 模式。

    Windows 管理中心使你可以在不同工作负荷的现成配置文件之间进行选择，而不是复杂的设置。 如果你不确定，请使用默认设置。

6. 选择“启用”。

观看有关如何打开重复数据删除和压缩的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

## <a name="create-volumes-using-windows-powershell"></a>使用 Windows PowerShell 创建卷

首先，从 Windows "开始" 菜单启动 Windows PowerShell。 建议使用新的**Volume** cmdlet 创建 Azure Stack HCI 的卷。 它提供最快、最直接的体验。 此单个 cmdlet 自动创建虚拟磁盘、对其进行分区和格式化、创建具有匹配名称的卷，并将其添加到群集共享卷-所有操作都可以轻松完成。

**新卷**cmdlet 具有四个参数，你将始终需要提供这些参数：

- **FriendlyName：** 任何所需的字符串，例如 *"Volume1"*
- **文件系统：** **CSVFS_ReFS** （推荐）或**CSVFS_NTFS**
- **StoragePoolFriendlyName：** 存储池的名称，例如 *"S2D On ClusterName"*
- **大小：** 卷的大小，例如 *"10tb"*

   > [!NOTE]
   > Windows （包括 PowerShell）使用二进制（以2为底）数字进行计数，而驱动器通常使用十进制（以10为基数）数字进行标记。 这说明了在 Windows 中出现 "1 tb" 驱动器（定义为1000000000000字节）的原因，如 "909 GB" 中所示。 这是正常情况。 使用**新卷**创建卷时，应在二进制（以2为基数）数字中指定**大小**参数。 例如，指定 "909GB" 或 "0.909495 TB" 将创建一个大约为1000000000000字节的卷。

### <a name="example-with-2-or-3-servers"></a>示例：包含2个或3个服务器

为了更轻松地进行部署，如果部署只有两台服务器，存储空间直通会自动使用双向镜像以实现复原。 如果你的部署只有三台服务器，它会自动使用三向镜像。

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB
```

### <a name="example-with-4-servers"></a>示例：包含4台以上的服务器

如果有4个或更多服务器，可以使用可选的**ResiliencySettingName**参数来选择复原类型。

-   **ResiliencySettingName：** **镜像**或**奇偶校验**。

在下面的示例中， *"Volume2"* 使用三向镜像，而 *"Volume3"* 使用双重奇偶校验（通常称为 "擦除编码"）。

```PowerShell
New-Volume -FriendlyName "Volume2" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Mirror
New-Volume -FriendlyName "Volume3" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Parity
```

### <a name="example-using-storage-tiers"></a>示例：使用存储层

在具有三种类型的驱动器的部署中，一个卷可以跨越 SSD 和 HDD 层，使其部分保存在每个驱动器上。 同样，在具有四个或更多服务器的部署中，一个卷可以混合使用镜像和双奇偶校验，使其部分保存在每个服务器上。

为了帮助你创建此类卷，存储空间直通提供了名为*性能*和*容量*的默认层级模板。 它们封装了更快容量驱动器（如果适用）上的三向镜像的定义，以及速度较慢的容量驱动器上的双重奇偶校验（如果适用）。

可以通过运行**StorageTier** cmdlet 来查看这些文件。

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

![存储层 PowerShell 屏幕快照](media/creating-volumes/storage-tiers-screenshot.png)

若要创建分层卷，请使用**新的 Volume** Cmdlet 的**StorageTierFriendlyNames**和**StorageTierSizes**参数引用这些层模板。 例如，以下 cmdlet 创建了一个卷，其中混合了三向镜像和双奇偶校验（30:70 比例）。

```PowerShell
New-Volume -FriendlyName "Volume4" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames Performance, Capacity -StorageTierSizes 300GB, 700GB
```

大功告成！ 根据需要重复此步骤以创建多个卷。

## <a name="next-steps"></a>后续步骤

若要在存储空间直通中执行其他存储管理任务，请参阅：

- [存储空间直通概述](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [规划存储空间直通中的卷](/windows-server/storage/storage-spaces/plan-volumes)
- [扩展存储空间直通中的卷](/windows-server/storage/storage-spaces/resize-volumes)
- [删除存储空间直通中的卷](/windows-server/storage/storage-spaces/delete-volumes)