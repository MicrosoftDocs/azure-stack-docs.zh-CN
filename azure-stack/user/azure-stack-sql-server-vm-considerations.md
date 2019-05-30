---
title: SQL Server 最佳做法来优化 Azure Stack 中的性能。 | Microsoft Docs
description: 本文提供的 SQL Server 最佳做法有助于提高 Azure Stack VM 中 SQL Server 的性能并对其进行优化。
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
ms.openlocfilehash: deed8e358c339e5a55cf2928002b9c0e6910f0d4
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269421"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack"></a>用于优化 Azure Stack 性能的 SQL Server 最佳做法

本文提供了 SQL server 最佳实践以优化 SQL Server 并改进 Microsoft Azure Stack 虚拟机 (Vm) 中的性能。 在 Azure Stack Vm 中运行 SQL Server 时, 使用的相同数据库性能优化选项适用于 SQL Server 的本地服务器环境中。 在 Azure Stack 云关系数据库的性能取决于许多因素，包括系列 VM 的大小和数据磁盘的配置。

创建 SQL Server 映像时[预配 Azure Stack 门户中的 Vm，请考虑](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 从 Azure Stack 管理员门户中的 Marketplace 管理下载 SQL IaaS 扩展并下载所选的 SQL VM 虚拟硬盘 (Vhd)。 其中包括 SQL2014SP2、SQL2016SP1 和 SQL2017。

> [!NOTE]  
> 虽然本文介绍如何使用全局 Azure 门户的 SQL Server VM 预配，该指南也适用于 Azure Stack 具有以下差异：SSD 不可用的操作系统磁盘、 托管的磁盘不可用，但有一些细微差别存储配置中。

获取*最佳*Azure Stack Vm 上的 SQL Server 的性能是本文的重点。 如果工作负荷要求较低，可能不需要每项建议的优化。 评估这些建议时应考虑性能需求和工作负荷模式。

> [!NOTE]  
> 有关在 Azure Vm 中的 SQL Server 的性能指南，请参阅[这篇文章](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)。

## <a name="checklist-for-sql-server-best-practices"></a>SQL Server 最佳做法清单

下列清单是为了获得最佳性能的 Azure Stack Vm 上的 SQL Server:


|区域|优化|
|-----|-----|
|VM 大小 |[DS3](azure-stack-vm-sizes.md) 或更高级别适合 SQL Server Enterprise 版本。<br><br>[DS2](azure-stack-vm-sizes.md) 或更高级别适合 SQL Server Standard 版本和 Web 版本。|
|存储 |使用支持的虚拟机家族[高级存储](azure-stack-acs-differences.md)。|
|磁盘 |至少使用两个数据磁盘（一个用于日志文件，一个用于数据文件和 TempDB），并根据容量需求选择磁盘大小。 在 SQL Server 安装过程中将默认的数据文件位置设置为这些磁盘。<br><br>避免使用操作系统或临时磁盘进行数据库存储或日志记录。<br>使用存储空间条带化多个 Azure 数据磁盘，提高 IO 吞吐量。<br><br>使用规定的分配大小格式化。|
|I/O|对数据文件启用即时文件初始化。<br><br>使用合理的小的固定增量 (64 MB-256 MB) 限制数据库自动增长。<br><br>禁用数据库自动收缩。<br><br>在数据磁盘而非操作系统磁盘上设置默认的备份和数据库文件位置。<br><br>启用锁定页面。<br><br>应用 SQL Server 服务包和累积更新|
|特定于功能|直接备份到 Blob 存储（如果受正在使用的 SQL Server 版本支持）。|
|||

有关详细信息*如何*并*为什么*若要使这些优化，请查看详细信息和以下各节中提供的指南。

## <a name="vm-size-guidance"></a>VM 大小指导原则

对于性能敏感型应用程序，以下[VM 大小](azure-stack-vm-sizes.md)建议：

- **SQL Server Enterprise 版本：** DS3 或更高

- **SQL Server Standard 版本和 Web 版本：** DS2 或更高

使用 Azure Stack DS 和 DS_v2 VM 系列序列之间没有性能差异。

## <a name="storage-guidance"></a>存储指导原则

在 Azure Stack 中的 DS 系列 （以及 DSv2 系列） Vm 提供最高操作系统磁盘和数据磁盘吞吐量 (IOPS)。 操作系统磁盘和多达 2300 IOPS，每个数据磁盘，无论类型或所选的磁盘的大小，从 DS 或 DSv2 系列 VM 提供最多 1,000 台 IOPS。

数据磁盘吞吐量取决于唯一基于的 VM 系列时序。 你可以[，请参阅此文章](azure-stack-vm-sizes.md)来标识每个 VM 系列序列的数据磁盘吞吐量。

> [!NOTE]  
> 对于生产工作负荷，选择要在操作系统磁盘和数据磁盘提供的最大 IOPS 的 DS 系列或 DSv2 系列 VM。

在 Azure Stack 中创建存储帐户时, 的异地复制选项没有任何作用，因为此功能不是 Azure Stack 中提供。

## <a name="disks-guidance"></a>磁盘指导原则

Azure Stack VM 上有三种主要磁盘类型：

- **操作系统磁盘：** 创建 Azure Stack VM 时，该平台将附加至少一个磁盘 (标记为**C**驱动器) 到 VM，以便在操作系统磁盘。 此磁盘是一个 VHD，在存储空间中存储为一个页 blob。

- **临时磁盘：** Azure Stack 虚拟机包含另一个称为临时磁盘 (标记为**D**驱动器)。 这是可用于暂存空间的节点上的一个磁盘。

- **数据磁盘：** 作为数据磁盘附加到 VM，更多的磁盘和这些磁盘作为页 blob 存储在存储中。

以下部分说明了有关使用这些不同磁盘的建议。

### <a name="operating-system-disk"></a>操作系统磁盘

操作系统磁盘是可以作为操作系统的运行版本来启动和装载的 VHD，并且标记为 **C** 驱动器。

### <a name="temporary-disk"></a>临时磁盘

临时存储驱动器，标记为**D**驱动器、 本地不具有持久性。 不要将您是愿意上会丢失任何数据存储**D**驱动器。 这包括用户数据库文件和用户事务日志文件。

建议将 TempDB 存储在数据磁盘上，因为每个数据磁盘提供高达 2,300 的 IOPS。

### <a name="data-disks"></a>数据磁盘数

- **将数据磁盘用于数据和日志文件。** 如果您不使用磁盘条带化，使用两个数据磁盘从 VM 支持高级存储，其中一个磁盘包含日志文件，另一个包含数据和 TempDB 文件。 每个数据磁盘提供了许多 IOP 和带宽 （MB/秒），具体取决于 VM 系列，如中所述[Azure Stack 中支持的 VM 大小](azure-stack-vm-sizes.md)。 如果正在使用磁盘条带化方法，例如存储空间，放置在同一驱动器 （包括 TempDB） 的所有数据和日志文件。 此配置可以为你提供最大数目的 IOPS 供 SQL Server 使用，不管哪个文件在特定时刻需要它们。

> [!NOTE]  
> 在门户中预配 SQL Server VM 时，可以选择编辑存储配置。 Azure Stack 根据配置来配置一个或多个磁盘。 多个磁盘会组合成一个存储池。 数据文件和日志文件一起位于此配置中。

- **磁盘条带化：** 为提高吞吐量，可以添加更多的数据磁盘，并使用磁盘条带化。 若要确定所需数据磁盘数，请分析日志文件以及数据和 TempDB 文件所需的 IOPS 数和带宽。 请注意，IOPS 限制是每个数据磁盘根据 VM 系列，而不基于 VM 大小。 网络带宽限制，但是，基于 VM 大小。 上会看到表[在 Azure Stack 中的 VM 大小](azure-stack-vm-sizes.md)以了解详细信息。 遵循以下指南：

  - 对于 Windows Server 2012 或更高版本，请按照以下指南使用[存储空间](https://technet.microsoft.com/library/hh831739.aspx)：

    1. 对于联机事务处理 (OLTP) 工作负荷，请将交错（条带大小）设置为 64 KB（65,536 字节），对于数据仓库工作负荷，请将交错（条带大小）设置为 256 KB（262,144 字节），以避免分区定位错误导致的性能影响。 这必须使用 PowerShell 设置。

    2. 设置列计数 = 物理磁盘的数量。 配置的磁盘超过八个时，请使用 PowerShell（而不是服务器管理器 UI）。

       例如，以下 PowerShell 创建新的存储池时将交错大小设为 64 KB，将列数设为 2：

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- 根据负载预期确定与你的存储池相关联的磁盘数。 请记住，不同的 VM 大小允许不同数量的附加数据磁盘。 有关详细信息，请参阅[Azure Stack 中支持的 VM 大小](azure-stack-vm-sizes.md)。
- 若要获取数据磁盘的最大 IOPS，建议将添加支持的数据磁盘的最大数目你[VM 大小](azure-stack-vm-sizes.md)，并使用磁盘条带化。
- **NTFS 分配单元大小：** 当格式化数据磁盘，我们建议为数据和日志文件以及 TempDB 使用 64-KB 分配单元大小。
- **磁盘管理做法：** 删除数据磁盘时，请在更改过程中停止 SQL Server 服务。 另外，请勿更改磁盘上的缓存设置，因为这样做不会改进性能。

> [!WARNING]  
> 在进行这些操作时，如果没有停止 SQL 服务，则可能导致数据库损坏。

## <a name="io-guidance"></a>I/O 指导原则

- 请考虑启用即时文件初始化以减少初始文件分配所需的时间。 若要利用即时文件初始化，请将 **SE_MANAGE_VOLUME_NAME** 授予 SQL Server (MSSQLSERVER) 服务帐户并将其添加到“执行卷维护任务”  安全策略。 如果使用 azure SQL Server 平台映像，默认服务帐户 (**NT Service\MSSQLSERVER**) 不会添加到**执行卷维护任务**安全策略。 换而言之，在 SQL Server Azure 平台映像中未启用即时文件初始化。 将 SQL Server 服务帐户添加到**执行卷维护任务**安全策略后，请重新启动 SQL Server 服务。 使用此功能可能有一些安全注意事项。 有关详细信息，请参阅[数据库文件初始化](https://msdn.microsoft.com/library/ms175935.aspx)。
- **自动增长**是非预期增长的偶发情况。 不管理您在日常工作中使用自动增长的数据和日志增长。 如果使用自动增长，请使用“大小”开关预先增长文件。 
- 请确保禁用**自动收缩**以避免可能对性能产生负面影响的不必要开销。
- 设置默认的备份和数据库文件位置。 使用本文中的建议，并在“服务器属性”窗口中进行更改。 有关说明，请参阅 [View or Change the Default Locations for Data and Log Files (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)（查看或更改数据和日志文件的默认位置 (SQL Server Management Studio)）。 下面的屏幕截图显示了进行这些更改的位置：

    > ![查看或更改默认配置](./media/sql-server-vm-considerations/image1.png)

- 建立锁定的页以减少 IO 和任何分页活动。 有关详细信息，请参阅 [Enable the Lock Pages in Memory Option (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)（启用在内存中锁定页面的选项 (Windows)）。

- 请考虑在传入/传出 Azure Stack 时压缩所有数据文件，包括备份。

## <a name="feature-specific-guidance"></a>功能特定指南

某些部署可以使用更高级的配置技术，获得更多的性能好处。 以下列表主要介绍可能有助于改进性能的一些 SQL Server 功能：

- **备份到 Azure** **存储。** 当在 Azure Stack Vm 中运行的 SQL Server 中进行备份，可以使用 SQL Server 备份到 URL。 此功能是从 SQL Server 2012 SP1 CU2 开始提供的，在备份到附加数据磁盘时建议使用。

    在备份或还原使用 Azure 存储，请遵循中提供的建议[SQL Server 备份到 URL 最佳实践和故障排除](https://msdn.microsoft.com/library/jj919149.aspx)和[还原从备份存储在 Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). 此外可以自动使用这些备份[在 Azure Vm 中的 SQL Server 的自动备份](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)。

-   **备份到 Azure Stack 存储。** 可以备份到 Azure Stack 存储，所用方式类似于备份到 Azure 存储。 在 SQL Server Management Studio (SSMS) 中创建备份时，需手动输入配置信息。 SSMS 不能用于创建存储容器或共享访问签名。 SSMS 仅连接到 Azure 订阅，不连接到 Azure Stack 订阅。 只需通过 Azure Stack 门户或 PowerShell 创建存储帐户、容器和共享访问签名。


    ![SQL Server 备份](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > 共享访问签名是 SAS 令牌在 Azure Stack 门户中，没有前导？ “?”。 如果使用从门户复制函数，您需要先删除前导？ 然后令牌才能在 SQL Server 中使用。

    在 SQL Server 中设置并配置备份目标以后，即可备份到 Azure Stack Blob 存储。

## <a name="next-steps"></a>后续步骤

[使用 Azure Stack 的服务或开发适用于 Azure Stack 的应用](azure-stack-considerations.md)