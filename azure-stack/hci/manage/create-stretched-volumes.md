---
title: 创建延伸群集卷并设置复制
description: 如何使用 Windows Admin Center 和 PowerShell 在 Azure Stack HCI 中创建卷并为延伸群集设置复制。
author: v-dasis
ms.author: v-dasis
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 8c885f7a344ff9d34228bad76c79e65f1f345f25
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573677"
---
# <a name="create-stretched-cluster-volumes-and-set-up-replication"></a>创建延伸群集卷并设置复制

> 适用于：Azure Stack HCI 版本 20H2

本文介绍了如何使用 Windows Admin Center 和 PowerShell 在 Azure Stack HCI 中创建卷并为延伸群集设置复制。

我们将在两个站点的四台服务器上创建卷（以每个站点两台服务器为例）。 但请记住，如果要创建三向镜像卷，则至少需要六台服务器，每个站点三台服务器。

## <a name="stretched-volumes-and-replication-using-windows-admin-center"></a>使用 Windows Admin Center 创建延伸卷并设置复制

创建卷并设置复制：

1. 在 Windows Admin Center 中的“工具”下，选择“卷”。
1. 在右侧窗格中，选择“清单”选项卡，然后选择“创建”。
1. 在“创建卷”面板中，选择“在站点之间复制卷”。
1. 从下拉框中选择站点之间的复制方向。
1. 在“复制模式”下，选择“异步”或“同步”。
1. 输入源复制组名称和目标复制组名称。
1. 输入所需的日志卷大小。
1. 在“高级”下，可以选择执行以下操作：
     - 输入/更改“源复制组名称”。
     - 输入/更改“目标复制组名称”。
     - 若要“使用目标上已设定的块”，请选中该复选框。
     - 若要“加密复制流量”，请选中该复选框。
     - 若要“启用一致性组”，请选中该复选框。
1. 完成后，单击“创建”。
1. 在右侧窗格中，验证是否在主要（主动）站点中创建了数据磁盘和日志磁盘，以及是否在辅助（被动）站点中创建了相应的数据和日志副本磁盘。 对于双向复制，应当会看到两组数据和卷磁盘。
1. 在“工具”下，选择“存储副本”。
1. 在右侧窗格中的“合作关系”下，验证复制合作关系是否已成功创建。

然后，应在部署 VM 和其他工作负载之前验证站点之间的数据复制是否已成功。 有关详细信息，请参阅[验证群集](../deploy/validate.md)中的“验证复制”部分。

## <a name="create-stretched-volumes-using-powershell"></a>使用 PowerShell 创建延伸卷

单站点标准群集与延伸（双站点）群集的卷创建方式不同。 但是，对于这两种方案，你都使用 `New-Volume` cmdlet 来创建虚拟磁盘，对其进行分区和格式化，创建具有匹配名称的卷，并将其添加到群集共享卷 (CSV)。

为延伸群集创建卷和虚拟磁盘比为单站点群集创建卷和虚拟磁盘复杂一些。 延伸群集需要最少四个卷 - 两个数据卷和两个日志卷，每个站点中都有一个数据/日志卷对。 然后，你将为每个站点创建一个复制组，并在它们之间设置复制。 我们需要在服务器之间移动资源组。 `Move-ClusterGroup` cmdlet 用于执行此操作。

1. 首先，我们使用 `Move-ClusterGroup` cmdlet 将 `Available Storage` 存储池资源组移到 `Site1` 中的 `Server1`：

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name ‘Available Storage’ -Node Server1
    ```

1. 接下来，为 `Site1` 中的 `Server1` 创建第一个虚拟磁盘 (`Disk1`)：

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk1 -FileSystem REFS -DriveLetter F -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. 为 `Site1` 中的 `Server1` 创建第二个虚拟磁盘 (`Disk2`)：

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk2 -FileSystem REFS -DriveLetter G -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. 现在，使 `Available Storage` 组脱机：

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

1. 将 `Available Storage` 组移动到 `Site2` 中的 `Server3`：

    ```powershell
    Move-ClusterGroup -Name 'Available Storage' -Node Server3
    ```

1. 在 `Site2` 中的 `Server3` 上创建第一个虚拟磁盘 (`Disk3`)：

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk3 -FileSystem REFS -DriveLetter H -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. 在 `Site2` 中的 `Server3` 上创建第二个虚拟磁盘 (`Disk4`)：

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk4 -FileSystem REFS -DriveLetter I -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. 现在，使 `Available Storage` 组脱机，然后将其移回 `Site1` 中的服务器之一：

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage' -Node Server1
    ```

1. 使用 `Get-ClusterResource` cmdlet，确保创建了四个虚拟磁盘卷，每个存储池中两个：

    ```powershell
    Get-ClusterResource -Cluster ClusterS1
    ```

1. 现在，将 `Disk1` 添加到群集共享卷：

    ```powershell
    Add-ClusterSharedVolume -Name 'Cluster Virtual Disk (Disk1)'
    ```

你已完成卷的创建，并已准备好设置用于复制的存储副本。

## <a name="set-up-replication-using-powershell"></a>使用 PowerShell 设置复制

使用 PowerShell 为延伸群集设置存储副本时，需要将用于源数据的磁盘添加为群集共享卷 (CSV)。 所有其他磁盘必须在“可用存储”组中保持为非 CSV 驱动器。 然后，在存储副本创建过程中，这些磁盘将添加为群集共享卷。

在上一步中，使用驱动器号添加了虚拟磁盘，以使其更易于识别。 存储副本是一对一复制，这意味着单个磁盘可复制到另一个磁盘。

### <a name="step-1-validate-the-topology-for-replication"></a>步骤 1：验证用于复制的拓扑

在开始之前，应长时间运行 `Test-SRTopology` cmdlet（例如运行几个小时）。 `Test-SRTopology` cmdlet 验证潜在的复制合作关系，并验证本地主机到目标服务器之间的复制合作关系，或远程验证源服务器与目标服务器之间的复制合作关系。

此 cmdlet 将验证：

- 可以通过网络访问 SMB，这意味着 TCP 端口 445 和端口 5445 双向开放。
- 可以通过 HTTP 在网络上访问 WS-MAN，这意味着 TCP 端口 5985 和 5986 已打开。
- SR WMIv2 提供程序可供访问并可接受请求。
- 源和目标数据卷存在并且是可写的。
- 源和目标日志卷存在，进行了 NTFS 格式化或 ReFS 格式化并有足够的可用空间。
- 存储以 GPT 格式而不是 MBR 格式初始化，具有匹配的扇区大小。
- 有足够的物理内存用来运行复制。

此外，`Test-SRTopology` cmdlet 还会度量：

- ICMP 的往返延迟，并报告平均值。
- 写入输入/输出的性能计数器，并报告该卷上观察到的平均值。
- 估计的初始同步时间。

在 Test-SRTopology 完成后，它将在你的 Windows Temp 文件夹中创建一个 .html 文件（包含日期和时间的 TestSrTopologyReport）。 应当查看任何警告或故障，因为它们可能会导致无法正确创建存储副本。

下面是一个将运行 5 小时的示例命令：

```powershell
Test-SRTopology -SourceComputerName Server1 -SourceVolumeName W: -SourceLogVolumeName X: -DestinationComputerName Server3 -DestinationVolumeName Y: -DestinationLogVolumeName Z: -DurationInMinutes 300 -ResultPath c:\temp
```

### <a name="step-2-create-the-replication-partnership"></a>步骤 2：创建复制合作关系

现在，你已完成了 `Test-SRTopology` 测试，可以配置存储副本并创建复制合作关系了。 在某个 nutshell 中，我们将通过以下方式来配置存储副本：为每个站点创建复制组 (RG)，然后为 Site1 中的源服务器节点（Server1、Server2）和 Site2 中的目标（已复制）服务器节点（Server3、Server4）指定数据卷和日志卷。

让我们开始以下操作：

1. 将 Site1 数据磁盘添加为群集共享卷 (CSV)：

   ```powershell
   Add-ClusterSharedVolume -Name "Cluster Virtual Disk (Site1)"
   ```

1. “可用存储”组应该归它当前所在的节点“所有”。 可以使用以下命令将组移动到 Server1：

   ```powershell
   Move-ClusterGroup -Name “Available Storage” -Node Server1
   ```

1. 若要创建复制合作关系，请使用 `New-SRPartnership` cmdlet。 此 cmdlet 还可用于指定源数据卷和日志卷名称：

   ```powershell
   New-SRPartnership -SourceComputerName "Server1" -SourceRGName "Replication1" -SourceVolumeName "C:\ClusterStorage\Disk1\" -SourceLogVolumeName "G:" -DestinationComputerName "Server3" -DestinationRGName "Replication2" -DestinationVolumeName "H:" -DestinationLogVolumeName "I:"
   ```

`New-SRPartnership` cmdlet 在两个站点的两个复制组之间创建复制合作关系。 在此示例中，`Replication1` 是 Site1 中主要节点 Server1 的复制组，`Replication2` 是 Site2 中目标节点 Server3 的复制组。

现在，存储副本将设置所有内容。 如果存在任何要复制的数据，它将在此处进行复制。 这可能需要一些时间，具体取决于需要复制的数据量。 在此过程完成之前，建议不要移动任何组。

## <a name="next-steps"></a>后续步骤

若要了解相关的主题和其他存储管理任务，另请参阅：

- [延伸群集概述](../concepts/stretched-clusters.md)
- [规划卷](../concepts/plan-volumes.md)
- [扩展卷](extend-volumes.md)
- [删除卷](delete-volumes.md)
