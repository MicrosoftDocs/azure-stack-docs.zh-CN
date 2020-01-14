---
title: SQL Server 最佳实践以优化 Azure Stack 中心的性能。 | Microsoft Docs
description: 本文提供了 SQL server 最佳做法，有助于提高性能并优化 Azure Stack 中心 Vm 中的 SQL Server。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 8cd440c67f8f192cb6ed1d0010ddc3f83fbdc281
ms.sourcegitcommit: c4368652f0dd68c432aa1dabddbabf161a4a6399
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914847"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack-hub"></a>优化 Azure Stack 中心性能的 SQL server 最佳做法

本文提供了 SQL server 最佳做法，以优化 SQL Server，并改善 Microsoft Azure Stack 中心虚拟机（Vm）中的性能。 在 Azure Stack 中心 Vm 中运行 SQL Server 时，请使用适用于本地服务器环境中的 SQL Server 的相同数据库性能优化选项。 Azure Stack 中心云中关系数据库的性能取决于多种因素，包括 VM 的系列大小和数据磁盘的配置。

创建 SQL Server 映像时，[请考虑在 Azure Stack 中心门户中预配 vm](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 从 Azure Stack 中心管理员门户的 Marketplace 管理下载 SQL IaaS 扩展，并下载所选的 SQL Server VM 映像。 其中包括 SQL Server 2016 SP1、SQL Server 2016 SP2 和 SQL Server 2017。

> [!NOTE]  
> 尽管本文介绍了如何使用全局 Azure 门户预配 SQL Server VM，但本指南也适用于具有以下差异的 Azure Stack 中心： SSD 不适用于操作系统磁盘，且存储空间有细微差异configuration.

在 VM 映像中，对于 "SQL Server"，只能使用 "自带许可证" （BYOL）。 对于 Windows Server，默认许可证模型为即用即付（PAYG）。 有关 VM 中 Windows Server 许可证模型的详细信息，请参阅[Windows server in Azure Stack Hub MARKETPLACE 常见问题解答](https://docs.microsoft.com/azure-stack/operator/azure-stack-windows-server-faq#what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server)。  

本文重点介绍 Azure Stack 中心 Vm 上 SQL Server 的*最佳*性能。 如果你的工作负荷不太要求，你可能不需要每个建议的优化。 评估这些建议时应考虑性能需求和工作负荷模式。

> [!NOTE]  
> 有关 Azure Vm 中 SQL Server 的性能指南，请参阅[此文](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)。

## <a name="checklist-for-sql-server-best-practices"></a>SQL server 最佳实践清单

以下清单用于 Azure Stack 中心 Vm 上的 SQL Server 的最佳性能：


|区域|优化|
|-----|-----|
|VM 大小 |SQL Server Enterprise 版本的[DS3](azure-stack-vm-sizes.md)或更高版本。<br><br>SQL Server Standard edition 和 Web edition 的[DS2](azure-stack-vm-sizes.md)或更高版本。|
|存储空间 |使用支持[高级存储](azure-stack-acs-differences.md)的 VM 系列。|
|磁盘 |至少使用两个数据磁盘（一个用于日志文件，另一个用于数据文件和 TempDB），并根据容量需求选择磁盘大小。 在 SQL Server 安装过程中，将默认数据文件位置设置为这些磁盘。<br><br>避免使用操作系统或临时磁盘进行数据库存储或日志记录。<br>使用存储空间来条带化多个 Azure 数据磁盘，以提高 IO 吞吐量。<br><br>使用规定的分配大小格式化。|
|I/O|对数据文件启用即时文件初始化。<br><br>以合理的较小固定增量限制数据库的自动增长（64 MB-256 MB）。<br><br>禁用数据库自动收缩。<br><br>在数据磁盘上设置默认的备份和数据库文件位置，而不是操作系统磁盘。<br><br>启用锁定页面。<br><br>应用 SQL Server service pack 和累积更新。|
|特定于功能|直接备份到 blob 存储（如果 SQL Server 使用版本支持）。|
|||

有关*如何*和*为何*进行这些优化的详细信息，请查看以下部分中提供的详细信息和指南。

## <a name="vm-size-guidance"></a>VM 大小指导原则

对于区分性能的应用程序，建议使用以下[VM 大小](azure-stack-vm-sizes.md)：

- **SQL Server Enterprise 版：** DS3 或更高版本

- **SQL Server Standard edition 和 Web edition：** DS2 或更高版本

Azure Stack 中心，DS 和 DS_v2 VM 系列之间没有性能差异。

## <a name="storage-guidance"></a>存储指导原则

DS 系列（以及 DSv2 系列） Azure Stack 集线器中的 Vm 提供最大操作系统磁盘和数据磁盘吞吐量（IOPS）。 DS 或 DSv2 系列中的 VM 为操作系统磁盘提供了高达 1000 IOPS，每个数据磁盘最多可达 2300 IOPS，无论所选磁盘的类型或大小是多少。

数据磁盘吞吐量是根据 VM 系列系列唯一确定的。 可以[参考此文](azure-stack-vm-sizes.md)来确定每个 VM 系列的数据磁盘吞吐量。

> [!NOTE]  
> 对于生产工作负荷，请选择 DS 系列或 DSv2 系列 VM，以便在操作系统磁盘和数据磁盘上提供最大可能的 IOPS。

在 Azure Stack Hub 中创建存储帐户时，地域复制选项不起作用，因为此功能在 Azure Stack 中心内不可用。

## <a name="disks-guidance"></a>磁盘指导原则

Azure Stack 中心 VM 上有三种主要磁盘类型：

- **操作系统磁盘：** 创建 Azure Stack 中心 VM 时，该平台至少将一个磁盘（标记为**C**驱动器）附加到操作系统磁盘的虚拟机。 此磁盘是一个 VHD，在存储空间中存储为一个页 blob。

- **临时磁盘：** Azure Stack 中心 Vm 包含另一个称为临时磁盘的磁盘（标记为**D**驱动器）。 这是可用于暂存空间的节点上的一个磁盘。

- **数据磁盘：** 你可以将其他磁盘作为数据磁盘附加到 VM，并且这些磁盘作为页 blob 存储在存储中。

以下部分说明了有关使用这些不同磁盘的建议。

### <a name="operating-system-disk"></a>操作系统磁盘

操作系统磁盘是可以作为操作系统的运行版本来启动和装载的 VHD，并且标记为 **C** 驱动器。

### <a name="temporary-disk"></a>临时磁盘

标记为**D**驱动器的临时存储驱动器不是永久性的。 不要在**D**驱动器上存储不需要丢失的任何数据。 这包括用户数据库文件和用户事务日志文件。

建议将 TempDB 存储在数据磁盘上，因为每个数据磁盘最多可为每个数据磁盘提供 2300 IOPS。

### <a name="data-disks"></a>数据磁盘数

- **将数据磁盘用于数据和日志文件。** 如果不使用磁盘条带化，请使用支持高级存储的 VM 中的两个数据磁盘，其中一个磁盘包含日志文件，另一个包含数据和 TempDB 文件。 每个数据磁盘根据 VM 系列提供若干 IOPS，如[Azure Stack 集线器中支持的 vm 大小](azure-stack-vm-sizes.md)中所述。 如果使用磁盘条带化方法（例如存储空间），请将所有数据和日志文件放在同一个驱动器（包括 TempDB）上。 此配置提供了可用于 SQL Server 的最大 IOPS 数，无论在任何特定时间都需要文件。

> [!NOTE]  
> 在门户中预配 SQL Server VM 时，可以选择编辑存储配置。 根据你的配置，Azure Stack 集线器会配置一个或多个磁盘。 多个磁盘组合到单个存储池中。 数据文件和日志文件一起位于此配置中。

- **磁盘条带化：** 为了获得更大的吞吐量，你可以添加更多的数据磁盘并使用磁盘条带化。 若要确定所需的数据磁盘数，请分析日志文件以及数据和 TempDB 文件所需的 IOPS 数。 请注意，IOPS 限制基于 VM 系列系列的每个数据磁盘，而不是基于 VM 大小。 不过，网络带宽限制基于 VM 大小。 有关更多详细信息，请参阅[Azure Stack 集线器中有关 VM 大小](azure-stack-vm-sizes.md)的表。 遵循以下指南：

  - 对于 Windows Server 2012 或更高版本，请使用具有以下准则的[存储空间](https://technet.microsoft.com/library/hh831739.aspx)：

    1. 对于数据仓库工作负荷，将交错（条带大小）设置为 64 KB，对于数据仓库工作负荷256，请将其设置为 65536 KB （262144字节），以避免因分区未对齐而导致的性能影响。 这必须使用 PowerShell 设置。

    2. 设置列计数 = 物理磁盘的数量。 在配置8个以上的磁盘（不服务器管理器 UI）时使用 PowerShell。

       例如，以下 PowerShell 会创建一个新的存储池，并将其交错大小设置为 64 KB，将列数设置为2：

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- 根据负载预期确定与你的存储池相关联的磁盘数。 请记住，不同的 VM 大小允许不同数量的附加数据磁盘。 有关详细信息，请参阅[Azure Stack 集线器中支持的 VM 大小](azure-stack-vm-sizes.md)。
- 若要获取数据磁盘的最大可能 IOPS，建议添加[VM 大小](azure-stack-vm-sizes.md)支持的最大数据磁盘数，并使用磁盘条带化。
- **NTFS 分配单元大小：** 在格式化数据磁盘时，建议为数据和日志文件以及 TempDB 使用 64 KB 的分配单元大小。
- **磁盘管理实践：** 删除数据磁盘时，请在更改过程中停止 SQL Server 服务。 此外，请不要更改磁盘上的缓存设置，因为它不会提供任何性能改进。

> [!WARNING]  
> 如果无法在这些操作期间停止 SQL 服务，则会导致数据库损坏。

## <a name="io-guidance"></a>I/O 指导原则

- 请考虑启用即时文件初始化以减少初始文件分配所需的时间。 若要利用即时文件初始化，请向 SQL Server （MSSQLSERVER）服务帐户授予**SE_MANAGE_VOLUME_NAME**并将其添加到 "**执行卷维护任务**" 安全策略。 如果使用的是适用于 Azure 的 SQL Server 平台映像，则不会将默认服务帐户（**NT Service\MSSQLSERVER**）添加到 "**执行卷维护任务**" 安全策略。 换句话说，在 SQL Server Azure 平台映像中不启用即时文件初始化。 将 SQL Server 服务帐户添加到**执行卷维护任务**安全策略后，请重新启动 SQL Server 服务。 使用此功能可能有一些安全注意事项。 有关详细信息，请参阅 [数据库文件初始化](https://msdn.microsoft.com/library/ms175935.aspx)。
- 自动**增长是意外增长的应急**措施。 不要使用自动增长来日常管理数据和日志增长。 如果使用自动增长，请使用**大小**开关预先增长文件。
- 请确保禁用**自动收缩**以避免可能对性能产生负面影响的不必要开销。
- 设置默认的备份和数据库文件位置。 使用本文中的建议，并在服务器的 "属性" 窗口中进行更改。 有关说明，请参阅 [View or Change the Default Locations for Data and Log Files (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)（查看或更改数据和日志文件的默认位置 (SQL Server Management Studio)）。 以下屏幕截图显示了进行这些更改的位置：

    > ![查看或更改默认位置](./media/sql-server-vm-considerations/image1.png)

- 建立锁定的页以减少 IO 和任何分页活动。 有关详细信息，请参阅 [Enable the Lock Pages in Memory Option (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)（启用在内存中锁定页面的选项 (Windows)）。

- 请考虑在传入/传出 Azure Stack 中心（包括备份）时压缩所有数据文件。

## <a name="feature-specific-guidance"></a>功能特定指南

某些部署可以使用更高级的配置技术，获得更多的性能好处。 下面的列表突出显示了一些 SQL Server 功能，这些功能可帮助你获得更好的性能：

- **备份到 Azure** **存储。** SQL Server 在 Azure Stack 中心 Vm 中运行的备份时，可以使用 SQL Server 备份到 URL。 此功能是从 SQL Server 2012 SP1 CU2 开始提供的，在备份到附加数据磁盘时建议使用。

    使用 Azure 存储进行备份或还原时，请按照[SQL Server 备份到 URL 最佳做法和故障排除](https://msdn.microsoft.com/library/jj919149.aspx)以及[从 Microsoft Azure 中存储的备份还原](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017)中提供的建议操作。 还可以使用[Azure vm 中 SQL Server 的自动备份](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)自动执行这些备份。

-   **备份到 Azure Stack 中心存储。** 你可以备份到 Azure Stack 集线器存储，与备份到 Azure 存储一样。 在 SQL Server Management Studio （SSMS）中创建备份时，需要手动输入配置信息。 不能使用 SSMS 来创建存储容器或共享访问签名。 SSMS 只连接到 Azure 订阅，而不是 Azure Stack 中心订阅。 相反，需要在 Azure Stack 中心门户或 PowerShell 中创建存储帐户、容器和共享访问签名。


    ![SQL Server 备份](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > 共享访问签名是来自 Azure Stack 中心门户的 SAS 令牌，没有前导 "？" 位于字符串中。 如果使用门户中的 "复制" 功能，则需要删除前导 "？" 要使令牌在 SQL Server 中工作。

    在 SQL Server 中设置并配置了备份目标后，可以备份到 Azure Stack 中心 blob 存储。

## <a name="next-steps"></a>后续步骤

[使用服务或为 Azure Stack 中心构建应用](azure-stack-considerations.md)
