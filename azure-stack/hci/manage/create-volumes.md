---
title: 在 Azure Stack HCI 中创建卷
description: 如何使用 Windows Admin Center 和 PowerShell 在 Azure Stack HCI 中创建卷。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: b195a55314935282f43f99bf02bda1d2bc298b54
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "78370405"
---
# <a name="creating-volumes-in-azure-stack-hci"></a>在 Azure Stack HCI 中创建卷

> 适用于：Windows Server 2019

本主题介绍如何使用 Windows Admin Center 和 Windows PowerShell 在存储空间直通群集中创建卷、如何处理卷中的文件，以及如何在卷中启用重复数据删除和压缩。

## <a name="create-a-three-way-mirror-volume"></a>创建三向镜像卷

若要使用 Windows Admin Center 创建三向镜像卷：

1. 在 Windows Admin Center 中连接到存储空间直通群集，然后在“工具”窗格中选择“卷”。  
2. 在“卷”页上选择“库存”选项卡，然后选择“创建卷”。   
3. 在“创建卷”窗格中输入卷的名称，并将“复原”保留为“三向镜像”。   
4. 在“HDD 上的大小”中指定卷的大小。  例如 5 TB。
5. 选择“创建”  。

创建卷可能需要几分钟时间，具体取决于大小。 卷创建好之后，右上方会显示通知。 新卷将显示在“库存”列表中。

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

6. 选择“启用”****。

观看有关如何打开重复数据删除和压缩的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

## <a name="create-volumes-using-windows-powershell"></a>使用 Windows PowerShell 创建卷

首先，从 Windows "开始" 菜单启动 Windows PowerShell。 建议使用新的**Volume** cmdlet 创建 Azure Stack HCI 的卷。 它可以提供最快且最直接的体验。 此单个 cmdlet 会自动创建虚拟磁盘，对其进行分区和格式化，使用匹配的名称创建卷，并将其添加到群集共享卷 － 这些全都在一个简单的步骤中完成。

**New-Volume** cmdlet 具有你将始终需要提供的四个参数：

- **FriendlyName：** 所需的任何字符串，例如 *“Volume1”*
- **FileSystem**：**CSVFS_ReFS**（推荐）或 **CSVFS_NTFS**
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

-   **ResiliencySettingName**：**镜像**或**奇偶校验**。

在以下示例中，*“Volume2”* 使用三向镜像，*“Volume3”* 使用双奇偶校验（通常称为“擦除编码”）。

```PowerShell
New-Volume -FriendlyName "Volume2" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Mirror
New-Volume -FriendlyName "Volume3" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Parity
```

### <a name="example-using-storage-tiers"></a>示例：使用存储层

在涉及三种驱动器类型的部署中，一个卷可以跨越 SSD 和 HDD 层以在每类驱动器上驻留一部分。 同样，在涉及四个或更多个服务器的部署中，一个卷可以混合镜像和双奇偶校验以在每个服务器上驻留一部分。

为了帮助你创建此类卷，存储空间直通会提供称为*性能*和*容量*的默认层模板。 它们会在较快的容量驱动器（如果适用）上封装三向镜像的定义，在较慢的容量驱动器（如果适用）上封装双奇偶校验。

你可以通过运行**Get-StorageTier** cmdlet 查看它们。

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

![存储层 PowerShell 屏幕截图](media/creating-volumes/storage-tiers-screenshot.png)

若要创建分层卷，请使用 **New-Volume** cmdlet 的 **StorageTierFriendlyNames** 和 **StorageTierSizes** 参数引用这些层模板。 例如，以下 cmdlet 会创建一个按 30:70 的比例混合三向镜像和双奇偶校验的卷。

```PowerShell
New-Volume -FriendlyName "Volume4" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames Performance, Capacity -StorageTierSizes 300GB, 700GB
```

大功告成！ 根据需要重复操作以创建多个卷。

## <a name="next-steps"></a>后续步骤

若要在存储空间直通中执行其他存储管理任务，请参阅：

- [存储空间直通概述](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [规划存储空间直通中的卷](/windows-server/storage/storage-spaces/plan-volumes)
- [扩展存储空间直通中的卷](/windows-server/storage/storage-spaces/resize-volumes)
- [删除存储空间直通中的卷](/windows-server/storage/storage-spaces/delete-volumes)