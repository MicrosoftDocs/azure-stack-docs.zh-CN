---
title: 使用 DISKSPD 测试工作负荷存储性能
description: 本主题提供有关如何使用 DISKSPD 来测试工作负荷存储性能的指导。
author: jasonnyi
ms.author: jasonyi
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 03d5bf97e29009c67e9520ea59a802c55659db3b
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94811146"
---
# <a name="use-diskspd-to-test-workload-storage-performance"></a>使用 DISKSPD 测试工作负荷存储性能

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

本主题提供有关如何使用 DISKSPD 来测试工作负荷存储性能的指导。 已设置 Azure Stack HCI 群集，一切都准备就绪。 很好，但你如何知道是否会获得承诺的性能指标（无论是延迟、吞吐量还是 IOPS）？ 当你可能想要转到 DISKSPD 时。 阅读本主题后，你将了解如何运行 DISKSPD、了解参数子集、解释输出，并大致了解影响工作负荷存储性能的变量。

## <a name="what-is-diskspd"></a>什么是 DISKSPD？
DISKSPD 是一个生成的、用于微基准测试的命令行工具。 很好，这些术语的含义是什么？ 设置 Azure Stack HCI 群集或物理服务器的任何人都有理由。 它可以是设置 web 托管环境，或为员工运行虚拟桌面。 无论实际使用情况如何，都可能需要在部署实际应用程序之前模拟测试。 但是，在实际情况下测试应用程序通常是很困难的，这是 DISKSPD 的情况。

DISKSPD 是一种可自定义的工具，可用于创建自己的综合工作负荷，并在部署前测试应用程序。 此工具的一项很棒的做法是让你能够自由地配置和调整参数，以创建类似于你的实际工作负荷的特定方案。 在部署之前，DISKSPD 可让你深入了解你的系统的能力。 DISKSPD 的核心只是发出一系列读写操作。

现在你知道什么是 DISKSPD，但当你何时使用它？ DISKSPD 在模拟复杂工作负荷时遇到困难。 但当单线程文件复制不能使工作负荷非常近似，而您需要一种可产生可接受的基线结果的简单工具时，DISKSPD 很好。

## <a name="quick-start-install-and-run-diskspd"></a>快速入门：安装并运行 DISKSPD
如果没有更多 ado，让我们开始吧：

1. 在管理 PC 中，以管理员身份打开 PowerShell 以连接到要使用 DISKSPD 测试的目标计算机，然后键入以下命令并按 Enter。

     ```powershell
     Enter-PSSession -ComputerName <TARGET_COMPUTER_NAME>
    ```

    在此示例中，我们将运行一个名为 "节点 1" (VM) 的虚拟机。

1. 若要下载 DISKSPD 工具，请键入以下命令并按 Enter：

     ```powershell
     $client = new-object System.Net.WebClient
    ```

     ```powershell
     $client.DownloadFile("https://github.com/Microsoft/diskspd/releases/download/v2.0.21a/DiskSpd-2.0.21a.zip","<ENTER_PATH>\DiskSpd-2.0.21a.zip")
    ```

1. 使用以下命令将下载的文件解压缩：

     ```powershell
     Expand-Archive -LiteralPath <ENTERPATH>\DiskSpd-2.0.21a.zip -DestinationPath C:\DISKSPD
    ```

1. 将目录更改为 DISKSPD 目录，并找到目标计算机正在运行的 Windows 操作系统的相应可执行文件。

    在此示例中，我们使用的是 amd64 版本。

    > [!NOTE]
    > 你还可以直接从包含开源代码的 [GitHub 存储库](https://github.com/microsoft/diskspd) 下载 DISKSPD 工具，并在 wiki 页面上详细介绍所有参数和规范。 在存储库中的 " **版本**" 下，选择 "自动下载 ZIP 文件" 链接。

    在 ZIP 文件中，你将看到三个子文件夹： amd64 (64 位系统) 、x86 (32 位系统) 和 ARM64 (ARM 系统) 。 使用这些选项，可以在每个 Windows 客户端或服务器版本中运行该工具。

    :::image type="content" source="media/diskspd/download-directory.png" alt-text="用于下载 DISKSPD 文件的目录。" lightbox="media/diskspd/download-directory.png":::

1. 在下面的 PowerShell 命令中运行 DISKSPD。 替换方括号内的所有内容，包括方括号本身以及适当的设置。

    ```powershell
     .\[INSERT_DISKSPD_PATH] [INSERT_SET_OF_PARAMETERS] [INSERT_CSV_PATH_FOR_TEST_FILE] > [INSERT_OUTPUT_FILE.txt]
    ```

    下面是可以运行的示例命令：

    ```powershell
     .\diskspd -t2 -o32 -b4k -r4k -w0 -d120 -Sh -D -L -c5G C:\ClusterStorage\test01\targetfile\IO.dat > test01.txt
    ```

    > [!NOTE]
    > 如果没有测试文件，请使用 **-c** 参数来创建一个。 如果使用此参数，请确保在定义路径时包含测试文件名。 例如： [INSERT_CSV_PATH_FOR_TEST_FILE] = C:\ClusterStorage\CSV01\IO.dat。 在示例命令中，"DISKSPD" 是测试文件的名称，test01.txt 是 "" 输出文件名。

## <a name="specify-key-parameters"></a>指定密钥参数
很简单，对吧？ 遗憾的是，它比这更多。 我们来解包我们所做的工作。 首先，您可以 tinker 多个参数，并且它可以获得特定的参数。 但是，我们使用了以下基准参数集：

> [!NOTE]
> DISKSPD 参数区分大小写。

**-t2**：这表示每个目标/测试文件的线程数。 此数字通常基于 CPU 内核数。 在这种情况下，两个线程用于对所有 CPU 内核施加压力。

**-o32**：表示每个线程的每个目标的未完成 i/o 请求数。 这也称为队列深度，在这种情况下，使用32来对 CPU 进行压力。

**-b4K**：此项指示块大小（以字节为单位）、KiB、MiB 或 GiB。 在这种情况下，使用4K 块大小来模拟随机 i/o 测试。

**-r4K**：此项指示随机 i/o 与指定的大小（以字节、KiB、MiB、Gib 或块为单位） (重写 **-s** 参数) 。 通用4K 字节大小用于与块大小正确对齐。

**-w0**：指定写入请求的操作的百分比 (-w0 等效于 100% read) 。 在这种情况下，0% 写入用于简单测试目的。

**-d120**：指定测试的持续时间，不包括冷却或预热。 默认值为10秒，但对于任何严重的工作负荷，建议至少使用60秒。 在这种情况下，使用120秒来最大程度地减少任何离群值。

**-Suw**：禁用等效于 **-Sh**)  (软件和硬件写入缓存。

**-D**：捕获 IOPS 统计信息，如标准偏差，以毫秒为单位 (每个按目标) 。

**-L**：度量延迟统计信息。

**-c5g**：设置测试中使用的示例文件大小。 可以按字节、KiB、MiB、GiB 或块进行设置。 在这种情况下，使用了 5 GB 目标文件。

有关参数的完整列表，请参阅 [GitHub 存储库](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters)。

## <a name="understand-the-environment"></a>了解环境
性能严重取决于您的环境。 那么，我们的环境是什么？ 我们的规范涉及到 Azure Stack HCI 群集，其中包含存储池， (S2D) 存储空间直通。 更具体地说，有五个 Vm： DC、节点1、节点2、node3 和管理节点。 群集本身是一个三节点群集，其中包含一个三向镜像恢复结构。 因此，会保留三个数据副本。 群集中的每个 "节点" 是一个 Standard_B2ms VM，最大 IOPS 限制为1920。 在每个节点中，有四个高级 P30 SSD 驱动器，最大 IOPS 限制为5000。 最后，每个 SSD 驱动器都有 1 TB 的内存。

你将在群集共享卷 (CSV) 提供 (C:\clusteredstorage 下) 的统一命名空间下生成测试文件，以使用整个驱动器池。

>[!NOTE]
> 示例 *环境没有 hyper-v* 或嵌套虚拟化结构。

正如您所看到的，完全可以按 VM 或驱动器限制独立地命中 IOPS 或带宽上限。 因此，了解 VM 大小和驱动器类型非常重要，因为两者都具有最大 IOPS 限制和带宽上限。 此知识有助于找出瓶颈并了解性能结果。 若要详细了解适合你的工作负荷的大小，请参阅以下资源：

- [VM 大小](https://docs.microsoft.com/azure/virtual-machines/sizes-general?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)
- [磁盘类型](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="understand-the-output"></a>了解输出
了解参数和环境后，就可以解释输出了。 首先，之前测试的目标是在不考虑延迟的情况下最大的 IOPS。 这样，你就可以在 Azure 中以可视方式查看是否达到人工 IOPS 限制。 如果要以图形方式直观显示总 IOPS，请使用 Windows 管理中心或任务管理器。

下图显示了 DISKSPD 进程在我们的示例环境中的外观。 其中显示了一个来自非协调器节点的 1 MiB 写入操作的示例。 与非协调器节点的操作一样，三向复原结构会导致两个网络跃点，从而降低性能。 如果想知道什么是协调器节点，别担心！ 你将在 " [注意事项](#things-to-consider) " 部分中了解相关信息。 红色方块表示 VM 和驱动器瓶颈。

:::image type="content" source="media/diskspd/environment.png" alt-text="用于测试 DISKSPD 的性能的示例环境。" lightbox="media/diskspd/environment.png":::

现在，你已经了解了视觉对象，接下来我们来看一下 .txt 文件输出的四个主要部分：
1. 输入设置
   
    本部分介绍你运行的命令、输入参数和有关测试运行的其他详细信息。

    :::image type="content" source="media/diskspd/command-input-parameters.png" alt-text="示例输出显示命令和输入参数。" lightbox="media/diskspd/command-input-parameters.png":::

1. CPU 使用率详细信息
   
    本部分重点介绍测试时间、线程数、可用处理器数以及测试期间每个 CPU 内核的平均利用率等信息。 在这种情况下，有两个 CPU 核心，平均使用量为4.67%。

    :::image type="content" source="media/diskspd/cpu-details.png" alt-text="示例 CPU 详细信息。" lightbox="media/diskspd/cpu-details.png":::

1. I/o 总数
   
    本部分包含三个子节。 第一节重点介绍包括读和写操作的总体性能数据。 第二个和第三个节将读取和写入操作分成单独的类别。

    在此示例中，可以看到，120秒内的总 i/o 计数为234408。 因此，IOPS = 234408/120 = 1953.30。 平均延迟为32.763 毫秒，吞吐量为 7.63 MiB/s。 从以前的信息开始，我们知道，1953.30 IOPS 接近 Standard_B2ms VM 的 1920 IOPS 限制。 不相信？ 如果使用不同的参数重新运行此测试，如增加队列深度，你会发现结果仍按此数字上限。

    最后三列显示了 17.72 (的 IOPS 标准偏差，从-D 参数) ，从-L 参数) 到20.994 毫秒的延迟标准偏差 (，以及文件路径。

      :::image type="content" source="media/diskspd/total-io.png" alt-text="示例显示总 i/o 性能数据的总数。" lightbox="media/diskspd/total-io.png":::

    在结果中，您可以快速确定群集配置是否严重。 你可以看到，在对5000进行 SSD 限制之前，它达到了1920的 VM 限制。 如果你受 SSD 而不是 VM 的限制，则可以通过跨多个驱动器跨越测试文件来充分利用 20000 IOPS (4 个驱动器 * 5000) 。

    最终，你需要确定哪些值对于你的特定工作负荷是可接受的。 下图显示了一些重要的关系，这些关系有助于您考虑权衡：

    :::image type="content" source="media/diskspd/tradeoffs.png" alt-text="图显示了工作负荷关系折衷。" lightbox="media/diskspd/tradeoffs.png":::

    图中的第二个关系是重要的，有时也称为小法律。 这一理念引入了三种特征来控制处理行为，只需更改其中一项以影响另两个，进而将整个过程。 因此，如果您对系统性能不满意，则可以有三种可自由地对其进行影响的维度。 一点定律规定，在我们的示例中，IOPS 为 "吞吐量" (每秒输入输出操作数) ，延迟为 "队列时间"，队列深度为 "清点"。

1. 延迟百分比分析
   
    最后一部分详细说明了每个操作的每个操作类型的百分位延迟，从最小值到最大值。

    本节非常重要，因为它确定 IOPS 的 "质量"。 它显示了多少个 i/o 操作能够实现某个延迟值。 这取决于您决定该百分点的可接受延迟。

    而且，"九" 指的是9的数目。 例如，"3-9" 等效于99% 百分位。 "9" 值用于显示在该百分点运行的 i/o 操作数。 最终，您将会看到一个点，在这种情况下，需要认真考虑延迟值。 在这种情况下，可以看到滞后时间值在 "4-9" 之后保持不变。 此时，延迟值仅基于234408操作中的一个 i/o 操作。

    :::image type="content" source="media/diskspd/storage-performance.png" alt-text="示例显示了每个操作类型的存储性能的每百分个延迟。" lightbox="media/diskspd/storage-performance.png":::

## <a name="things-to-consider"></a>注意事项
现在，已开始使用 DISKSPD，需要考虑几个事项以获取实际的测试结果。 其中包括密切关注所设置的参数、存储空间运行状况和变量、CSV 所有权，以及 DISKSPD 与文件复制之间的差异。

### <a name="diskspd-vs-real-world"></a>DISKSPD 与现实世界
DISKSPD 的人工测试为你的实际工作负荷提供相对比较的结果。 但是，您需要密切注意您所设置的参数以及这些参数是否与您的真实方案相符。 重要的是要了解，综合工作负载在部署期间绝不会完全代表应用程序的实际工作负荷。

### <a name="preparation"></a>准备
在运行 DISKSPD 测试之前，建议执行一些操作。 其中包括验证存储空间的运行状况，检查资源使用情况，以便其他程序不会影响测试，并准备好要收集其他数据的性能管理器。 但由于本主题的目标是快速 DISKSPD 地运行，因此它不会深入探讨这些操作。 若要了解详细信息，请参阅[在 Windows Server 中使用综合工作负载测试存储空间性能](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn894707(v=ws.11))。

### <a name="variables-that-affect-performance"></a>影响性能的变量
存储性能是一项微妙的事情。 意思是，有许多因素会影响性能。 因此，可能会遇到不符合预期的数字。 下面重点介绍了一些影响性能的变量，尽管它不是一个完整的列表：
- 网络带宽
- 复原选择
- 存储磁盘配置： NVME、SSD、HDD
- I/o 缓冲区
- 缓存
- RAID 配置
- 网络跃点
- 硬盘纺锤速度

### <a name="csv-ownership"></a>CSV 所有权
节点称为 "卷所有者" 或 " **协调器** " 节点 (非协调器节点将是不拥有特定卷) 的节点。 每个标准卷都分配有一个节点，其他节点可以通过网络跃点访问此标准卷，这会导致性能降低 (更高的延迟) 。

同样，群集共享卷 (CSV) 还具有 "所有者"。 但是，CSV 为 "动态"，因为它会在每次重新启动系统 (RDP) 时，绕过并更改所有权。 因此，请务必确认 DISKSPD 是从拥有 CSV 的协调器节点运行的。 否则，可能需要手动更改 CSV 所有权。

确认 CSV 所有权：
1. 通过运行以下 PowerShell 命令来检查所有权：

    ```powershell
     Get-ClusterSharedVolume
    ```

1. 如果 CSV 所有权不正确 (例如，在节点1上，节点2拥有 CSV) ，然后运行以下 PowerShell 命令，将 CSV 移动到正确的节点：

    ```powershell
     Get-ClusterSharedVolume <INSERT_CSV_NAME> | Move-ClusterSharedVolume <INSERT _NODE_NAME>
    ```
### <a name="file-copy-vs-diskspd"></a>文件复制与 DISKSPD
有些人相信他们可以通过复制和粘贴超大文件并度量该过程所花费的时间来 "测试存储性能"。 此方法的主要原因很可能是因为简单快捷。 这种做法在测试特定工作负荷时并不错误，但难以将此方法归类为 "测试存储性能"。

如果你的实际目标是测试文件复制性能，则这可能是使用此方法的一个非常有效的理由。 但是，如果你的目标是衡量存储性能，我们建议不要使用此方法。 您可以将文件复制过程看作是使用一组不同的 "参数" (例如，队列、并行化等特定于文件服务的) 。

下面的简短摘要说明了为什么使用文件复制来度量存储性能可能无法提供所需的结果：
- **文件副本可能未经过优化，** 发生了两个级别的并行，一个是内部，另一个由外部。 在内部，如果文件副本用于远程目标，则 CopyFileEx 引擎会应用一些并行化。 在外部，可以通过不同的方式来调用 CopyFileEx 引擎。 例如，从文件资源管理器复制是单线程的，而 Robocopy 是多线程的。 出于这些原因，请务必了解测试的含义是否是你要查找的内容。
- **每个副本都有两个边。** 如果只是复制和粘贴文件，则可能使用两个磁盘：源磁盘和目标磁盘。 如果一个比另一个慢，你实质上就是衡量速度较慢的磁盘的性能。 在其他情况下，源、目标和复制引擎之间的通信可能会以独特的方式影响性能。
    
    若要了解详细信息，请参阅 [使用文件复制来度量存储性能](https://docs.microsoft.com/archive/blogs/josebda/using-file-copy-to-measure-storage-performance-why-its-not-a-good-idea-and-what-you-should-do-instead?ranMID=24542&ranEAID=je6NUbpObpQ&ranSiteID=je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q&epi=je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q&irgwc=1&OCID=AID2000142_aff_7593_1243925&tduid=%28ir__rcvu3tufjwkftzjukk0sohzizm2xiezdpnxvqy9i00%29%287593%29%281243925%29%28je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q%29%28%29&irclickid=_rcvu3tufjwkftzjukk0sohzizm2xiezdpnxvqy9i00)。

## <a name="experiments-and-common-workloads"></a>试验和常见工作负载
本部分包含一些其他示例、试验和工作负荷类型。

### <a name="confirming-the-coordinator-node"></a>确认协调器节点
如前所述，如果你当前正在测试的 VM 不是 CSV，你会看到性能下降 (IOPS、吞吐量和延迟) 相对于在节点拥有 CSV 时进行测试。 这是因为每次发出 i/o 操作时，系统都会向协调器节点执行网络跃点，以执行该操作。

对于三节点的三向镜像方案，编写操作始终进行网络跃点，因为它需要在三个节点上的所有驱动器上存储数据。 因此，无论何时，写操作都将创建一个网络跃点。 但是，如果使用不同的复原结构，则可能会更改。

以下是示例：
- **在本地节点上运行：** .\DiskSpd-2.0.21a\amd64\diskspd.exe-o32-b4k-r4k-w0-C:\ClusterStorage\test01\targetfile\IO.dat-
- **在非本地节点上运行：** .\DiskSpd-2.0.21a\amd64\diskspd.exe-o32-b4k-r4k-w0-C:\ClusterStorage\test01\targetfile\IO.dat

在此示例中，可以在下图的结果中清楚地看到延迟时间、增加的 IOPS 以及当协调器节点拥有 CSV 时增加的吞吐量。

:::image type="content" source="media/diskspd/coordinator-node-data.png" alt-text="示例演示协调器节点数据输出。" lightbox="media/diskspd/coordinator-node-data.png":::

### <a name="online-transaction-processing-oltp-workload"></a>联机事务处理 (OLTP) 工作负荷
联机事务处理 (OLTP) 工作负荷查询 (更新、插入、删除) 将重点放在面向事务的任务上。 与联机分析处理 (OLAP) 相比，OLTP 是依赖于存储延迟的。 由于每个操作都有很少的 i/o，你所关心的是每秒可以承受的操作数。

您可以设计一个 OLTP 工作负载测试来集中精力处理随机的小规模 i/o 性能。 对于这些测试，重点关注可以在多长时间内推送吞吐量，同时保持可接受的延迟。

此工作负荷测试的基本设计选择应该至少包括：
- 8 KB 块大小 => 类似于 SQL Server 用于其数据文件的页大小
- 70% 读取，30% 写入 => 类似于典型 OLTP 行为

### <a name="online-analytical-processing-olap-workload"></a>OLAP) 工作负荷的联机分析处理 (
OLAP 工作负荷重点介绍数据检索和分析，使用户能够执行复杂的查询来提取多维数据。 与 OLTP 相反，这些工作负荷不会区分存储延迟。 它们强调了多个操作的队列，而没有关心的带宽。 因此，OLAP 工作负荷常常会导致更长的处理时间。

您可以设计一个 OLAP 工作负荷测试，以注重顺序、大规模的 i/o 性能。 对于这些测试，重点关注每秒处理的数据量，而不是 IOPS 数。 延迟要求也不太重要，但这是主观的。

此工作负荷测试的基本设计选择应该至少包括：
- 512 KB 块 size => 当 SQL Server 使用预读技术加载表扫描的一批64数据页时，与 i/o 大小类似。
- 每个文件1个线程 => 当前，需要将测试限制为每个文件一个线程，因为在测试多个顺序线程时，DISKSPD 中可能会出现问题。
    如果使用多个线程（如两个）和 **-s** 参数，则线程将开始不确定地在同一位置中的每个线程上发出 i/o 操作。 这是因为它们各自跟踪其自己的顺序偏移量。

    要解决此问题，有两个 "解决方案"：
    - 第一个解决方案涉及使用 **-si** 参数。 使用此参数时，两个线程都共享单个互锁偏移量，以便线程以协作方式向目标文件发出单一顺序访问。 这使得文件中的一个点不能多次操作。 不过，由于它们仍会相互争用以向队列发出 i/o 操作，因此这些操作可能不按顺序到达。

        如果一个线程的 CPU 有限，则此解决方案可正常工作。 你可能想要在第二个 CPU 内核上使用另一个线程来向 CPU 系统提供更多的存储 i/o，以便进一步使其饱和。

    - 第二个解决方案涉及使用-T\<offset>\. 这使你可以指定在不同线程在同一目标文件上执行 i/o 间隔 (的偏移大小) 。 例如，线程通常在偏移量0处开始，但此规范允许您将两个线程进行距离，使它们不会相互重叠。 在任何多线程环境中，线程可能会位于工作目标的不同部分，这是模拟该情况的一种方法。

## <a name="next-steps"></a>后续步骤
有关优化复原设置的详细信息和详细示例，另请参阅：
- [OLTP 和 OLAP](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn894707(v=ws.11))
- [复原选择](https://techcommunity.microsoft.com/t5/storage-at-microsoft/volume-resiliency-and-efficiency-in-storage-spaces-direct/ba-p/425831)
