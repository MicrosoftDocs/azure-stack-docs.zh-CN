---
title: 管理 Azure Stack Hub 的存储基础结构
titleSuffix: Azure Stack
description: 了解如何管理 Azure Stack Hub 的存储基础结构。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 03/11/2019
ms.reviewer: jiaha
ms.openlocfilehash: 4ac1d0de3775c22c0c982d79713847e7cd171f41
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80152237"
---
# <a name="manage-storage-infrastructure-for-azure-stack-hub"></a>管理 Azure Stack Hub 的存储基础结构

本文介绍 Azure Stack Hub 存储基础结构资源的运行状况和工作状态。 这些资源包括存储驱动器和卷。 本主题中的信息可帮助你排查各种问题，例如，无法将驱动器添加到池的问题。

## <a name="understand-drives-and-volumes"></a>了解驱动器和卷

### <a name="drives"></a>驱动器

由 Windows Server 软件驱动的 Azure Stack Hub 定义了存储功能，其中包括存储空间直通 (S2D) 和 Windows Server 故障转移群集的组合。 这种组合提供高性能、可缩放且具有复原能力的存储服务。

Azure Stack Hub 集成式系统合作伙伴提供众多的解决方案版本，包括各种灵活的存储。 当前最多可以从三个支持的驱动器类型中选择两个驱动器类型： NVMe （非易失性内存 express）、SATA/SAS SSD （固态驱动器）、HDD （硬盘驱动器）。 

存储空间直通提供缓存用于最大化存储性能。 在具有一种驱动器类型（即 NVMe 或 SSD）的 Azure Stack 集线器设备上，所有驱动器都用于容量。 如果有两种驱动器类型，存储空间直通自动使用 "最快" （NVMe &gt; SSD &gt; HDD）类型的所有驱动器进行缓存。 剩余的驱动器用于提供容量。 驱动器可以分组成“全部闪存”或“混合”部署：

![Azure Stack 中心存储基础结构](media/azure-stack-storage-infrastructure-overview/image1.png)

所有闪存部署都旨在最大程度地提高存储性能，不包括旋转 Hdd。

![Azure Stack 中心存储基础结构](media/azure-stack-storage-infrastructure-overview/image2.png)

混合部署旨在平衡性能和容量，或最大限度地提高容量并包括旋转 Hdd。

根据要提供缓存的驱动器类型自动确定缓存的行为。 缓存 Ssd （例如 Ssd 的 NVMe 缓存）时，仅缓存写入。 这可以减小容量驱动器的磨损，减少发往容量驱动器的累积流量，延长驱动器的寿命。 同时，不缓存读取。 它们不会被缓存，因为读取不会显著影响闪存的生存期，并且 Ssd 会广泛地提供较低的读取延迟。 当缓存 Hdd （如用于 Hdd 的 Ssd 缓存）时，读取和写入都将缓存，以便为两者提供类似于闪烁的延迟（通常/约10倍）。

![Azure Stack 中心存储基础结构](media/azure-stack-storage-infrastructure-overview/image3.png)

有关存储的可用配置，可以查看 Azure Stack 中心 OEM 合作伙伴（https://azure.microsoft.com/overview/azure-stack/partners/)有关详细规范，请参阅。

> [!Note]  
> 可在混合部署中提供 Azure Stack 集线器设备，同时包含 HDD 和 SSD （或 NVMe）驱动器。 但是，速度较快的驱动器类型将用作缓存驱动器，所有剩余驱动器将以池的形式用作容量驱动器。 租户数据（blob、表、队列和磁盘）将放在容量驱动器上。 预配高级磁盘或选择高级存储帐户类型不能保证对象将分配到 SSD 或 NVMe 驱动器上。

### <a name="volumes"></a>卷

存储服务将可用的存储分区成独立的卷，这些卷可分配用于保存系统数据和租户数据。** 卷将存储池中的驱动器组合在一起，以提供存储空间直通的容错能力、可伸缩性和性能优势。

![Azure Stack 中心存储基础结构](media/azure-stack-storage-infrastructure-overview/image4.png)

Azure Stack 中心存储池上创建了三种类型的卷：

- 基础结构： Azure Stack 集线器基础结构 Vm 和核心服务使用的主机文件。

- VM 临时卷：托管附加到租户 VM 的临时磁盘以及这些磁盘中存储的数据。

- 对象存储：主机租户数据维护 blob、表、队列和 VM 磁盘。

在多节点部署中，你会看到三个基础结构卷，而 VM 临时卷和对象存储卷的数目等于 Azure Stack 中心部署中的节点数：

- 在四节点部署中，有四个等量的 VM 临时卷和四个等量的对象存储卷。

- 如果向群集添加新节点，则会为这两种类型创建新卷。

- 即使节点出现故障或者被删除，卷数也会保持相同。

- 如果使用 Azure Stack 开发工具包，则有一个具有多个共享的卷。

存储空间直通中的卷可以提供复原功能，防止出现硬件问题，如驱动器或服务器故障。 它们还在服务器维护（如软件更新）中实现持续可用性。 Azure Stack 集线器部署使用三向镜像来确保数据恢复能力。 租户数据的三个副本将写入不同的服务器并保存在缓存中：

![Azure Stack 中心存储基础结构](media/azure-stack-storage-infrastructure-overview/image5.png)

镜像功能通过保存所有数据的多个副本来提供容错。 该数据是条带化的，并不是很重要的，但是，假设使用镜像存储的任何数据都将被完整地多次写入。 每个副本将写入不同的物理硬件（位于不同服务器中的不同驱动器），假设每个硬盘各自都有可能发生故障。 三向镜像可以安全容许至少两个硬件（驱动器或服务器）同时出现问题。 例如，如果你正在重新启动一台服务器，此时另一个驱动器或服务器突然发生故障，在这种情况下，所有数据将保持安全，可供持续访问。

## <a name="volume-states"></a>卷状态

若要确定卷所处的状态，请使用以下 PowerShell 命令：

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsVolume -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object VolumeLabel, HealthStatus, OperationalStatus, RepairStatus, Description, Action, TotalCapacityGB, RemainingCapacityGB
```

以下示例输出显示有一个卷已分离，并且有一个卷已降级/不完整：

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | 未知 | 分离 |
| ObjStore_2 | 警告 | {已降级，不完整} |

以下部分列出了运行状况和运行状态：

### <a name="volume-health-state-healthy"></a>卷运行状况状态：正常

| 操作状态 | 说明 |
|---|---|
| OK | 卷处于正常状态。 |
| 欠佳 | 数据未均匀写入到各个驱动器。<br> <br>**操作：** 请联系支持人员以优化存储池中的驱动器使用情况。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 恢复失败的连接后，可能需要从备份还原数据。 |

### <a name="volume-health-state-warning"></a>卷运行状况状态：警告

当卷处于警告运行状况状态时，表示数据的一个或多个副本不可用，但 Azure Stack 集线器仍可读取至少一个数据副本。

| 操作状态 | 说明 |
|---|---|
| 运行中 | Azure Stack 集线器正在修复卷，如添加或删除驱动器后。 修复完成后，卷应该会恢复“正常”运行状况。<br> <br>**操作：** 等待 Azure Stack 集线器完成卷修复，然后检查状态。 |
| 不完整 | 由于一个或多个驱动器出现故障或缺失，卷的复原能力下降。 但是，缺失的驱动器包含数据的最新副本。<br> <br>**操作：** 重新连接任何缺失的驱动器、更换任何故障的驱动器，并使任何脱机的服务器联机。 |
| 已降级 | 由于驱动器上有一个或多个故障驱动器和过时的数据副本，卷的复原能力会降低。<br> <br>**操作：** 重新连接任何缺失的驱动器、更换任何故障的驱动器，并使任何脱机的服务器联机。 |

### <a name="volume-health-state-unhealthy"></a>卷运行状况状态：不正常

如果某个卷处于“不正常”运行状况，该卷上的部分或所有数据当前将不可访问。

| 操作状态 | 说明 |
|---|---|
| 无冗余 | 由于过多的驱动器出现故障，该卷已丢失数据。<br> <br>**操作：** 联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |

### <a name="volume-health-state-unknown"></a>卷运行状况状态：未知

如果虚拟磁盘已分离，卷也有可能处于“未知”运行状况。

| 操作状态 | 说明 |
|---|---|
| 分离 | 某个存储设备出现故障，从而可能导致卷不可访问。 某些数据可能已丢失。<br> <br>**采取** <br>1. 检查所有存储设备的物理连接和网络连接。<br>2. 如果所有设备均已正确连接，请联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 恢复失败的连接后，可能需要从备份还原数据。 |

## <a name="drive-states"></a>驱动器状态

使用以下 PowerShell 命令监视驱动器的状态：

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

以下各节描述了驱动器可以处于的运行状况状态：

### <a name="drive-health-state-healthy"></a>驱动器运行状况状态：正常

| 操作状态 | 说明 |
|---|---|
| OK | 卷处于正常状态。 |
| 运行中 | 驱动器正在执行一些内部日常维护操作。 操作完成后，驱动器应会恢复“正常”运行状况。 |

### <a name="drive-health-state-healthy"></a>驱动器运行状况状态：正常

处于“警告”状态的驱动器可以成功读取和写入数据，但存在问题。

| 操作状态 | 说明 |
|---|---|
| 通信断开 | 与驱动器的连接已断开。<br> <br>**操作：** 使所有服务器恢复联机状态。 如果仍未解决问题，请重新连接驱动器。 如果此状态仍然存在，请更换驱动器以确保完全复原。 |
| 预测性故障 | 预测驱动器即将发生故障。<br> <br>**操作：** 尽快更换驱动器以确保完全复原。 |
| IO 错误 | 访问驱动器时发生暂时性错误。<br> <br>**操作：** 如果此状态仍然存在，请更换驱动器以确保完全复原。 |
| 暂时性错误 | 驱动器出现暂时性错误。 此错误通常意味着驱动器无响应，但这也可能意味着无法正确地从驱动器中删除存储空间直通保护分区。 <br> <br>**操作：** 如果此状态仍然存在，请更换驱动器以确保完全复原。 |
| 异常延迟 | 驱动器有时无响应并出现故障迹象。<br> <br>**操作：** 如果此状态仍然存在，请更换驱动器以确保完全复原。 |
| 从池中删除 | Azure Stack 集线器正在从其存储池中删除驱动器。<br> <br>**操作：** 等待 Azure Stack 集线器完成删除驱动器，然后检查状态。<br>如果状态仍然存在，请联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |
| 启动维护模式 | Azure Stack 集线器正在将驱动器置于维护模式。 此状态为临时状态，驱动器应很快处于维护模式状态。<br> <br>**操作：** 等待 Azure Stack 集线器完成此过程，然后检查状态。 |
| 维护中模式 | 驱动器处于维护模式，因此对其的读取和写入操作已停止。 此状态通常意味着 Azure Stack 集线器管理任务，如 PNU 或 FRU 正在运行驱动器。 但管理员也可以将驱动器置于维护模式。<br> <br>**操作：** 等待中心 Azure Stack 集线器完成管理任务，并在以后检查状态。<br>如果状态仍然存在，请联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |
| 停止维护模式 | Azure Stack 集线器正在使驱动器恢复联机。 此状态为临时状态-驱动器应很快处于另一状态，理想情况下正常。<br> <br>**操作：** 等待 Azure Stack 集线器完成此过程，然后检查状态。 |

### <a name="drive-health-state-unhealthy"></a>驱动器运行状况状态：不正常

当前无法写入或访问处于“不正常”状态的驱动器。

| 操作状态 | 说明 |
|---|---|
| 拆分 | 已从池中分离驱动器。<br> <br>**操作：** 将驱动器替换为新磁盘。 如果必须使用此磁盘，请从系统中删除该磁盘，确保磁盘上没有有用的数据，擦除磁盘，然后重新放置磁盘。 |
| 不可用 | 物理磁盘被隔离，因为它不受解决方案供应商的支持。 仅支持批准用于解决方案并且包含正确磁盘固件的磁盘。<br> <br>**操作：** 将驱动器替换为具有解决方案的已批准制造商和型号的磁盘。 |
| 已过时的元数据 | 用于更换的磁盘之前已用过，可能包含未知存储系统中的数据。 该磁盘已被隔离。 <br> <br>**操作：** 将驱动器替换为新磁盘。 如果必须使用此磁盘，请从系统中删除该磁盘，确保磁盘上没有有用的数据，擦除磁盘，然后重新放置磁盘。 |
| 无法识别的元数据 | 在驱动器上找到了无法识别的元数据，这通常表示驱动器包含来自其他池的元数据。<br> <br>**操作：** 将驱动器替换为新磁盘。 如果必须使用此磁盘，请从系统中删除该磁盘，确保磁盘上没有有用的数据，擦除磁盘，然后重新放置磁盘。 |
| 介质故障 | 驱动器出现故障，不再可供存储空间使用。<br> <br>**操作：** 尽快更换驱动器以确保完全复原。 |
| 设备硬件故障 | 此驱动器上出现硬件故障。 <br> <br>**操作：** 尽快更换驱动器以确保完全复原。 |
| 正在更新固件 | Azure Stack 集线器正在更新驱动器上的固件。 此状态为临时状态，通常持续时间不到一分钟，在此期间，池中的其他驱动器将处理所有读取和写入操作。<br> <br>**操作：** 等待 Azure Stack 集线器完成更新并检查该状态。 |
| 正在启动 | 驱动器正在为操作做好准备。 此状态应为 "临时"-完成后，驱动器将转换为其他操作状态。<br> <br>**操作：** 等待 Azure Stack 集线器完成操作并稍后检查状态。 |

## <a name="reasons-a-drive-cant-be-pooled"></a>驱动器无法入池的原因

有些驱动器并未准备好 Azure Stack 集线器存储池中。 通过查看驱动器的`CannotPoolReason`属性，可以查明驱动器不符合池要求的原因。 下表更具体地描述了每种原因。

| 原因 | 说明 |
|---|---|
| 硬件不合规 | 使用运行状况服务指定的已批准存储模型列表中不包括该驱动程序。<br> <br>**操作：** 将驱动器替换为新磁盘。 |
| 固件不合规 | 使用运行状况服务指定的已批准固件修订版列表中不包括该物理驱动器上的固件。<br> <br>**操作：** 将驱动器替换为新磁盘。 |
| 已由群集使用 | 该驱动器当前已由故障转移群集使用。<br> <br>**操作：** 将驱动器替换为新磁盘。 |
| 可移动媒体 | 该驱动器分类为可移动驱动器。 <br> <br>**操作：** 将驱动器替换为新磁盘。 |
| 不正常 | 该驱动器不处于正常状态，可能需要更换。<br> <br>**操作：** 将驱动器替换为新磁盘。 |
| 容量不足 | 某些分区占用了驱动器上的可用空间。<br> <br>**操作：** 将驱动器替换为新磁盘。 如果必须使用此磁盘，请从系统中删除该磁盘，确保磁盘上没有有用的数据，擦除磁盘，然后重新放置磁盘。 |
| 正在验证 | 运行状况服务正在检查是否已批准使用驱动器上的固件。<br> <br>**操作：** 等待 Azure Stack 集线器完成此过程，然后检查状态。 |
| 验证失败 | 运行状况服务无法检查是否已批准使用驱动器上的固件。<br> <br>**操作：** 联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |
| 脱机 | 驱动器已脱机。 <br> <br>**操作：** 联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |

## <a name="next-step"></a>下一步

[管理存储容量](azure-stack-manage-storage-shares.md) 
