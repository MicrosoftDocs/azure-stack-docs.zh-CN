---
title: 管理 Azure Stack Hub 的存储基础结构
titleSuffix: Azure Stack
description: 了解如何管理 Azure Stack Hub 的存储基础结构。 请参阅如何监视卷和驱动器。 查看有关将驱动器添加到池的故障排除提示。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 10/19/2020
ms.author: inhenkel
ms.lastreviewed: 5/4/2020
ms.reviewer: jiaha
ms.openlocfilehash: 7141d52e100ce465a20637da2a40cbb75b417939
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92179452"
---
# <a name="manage-storage-infrastructure-for-azure-stack-hub"></a>管理 Azure Stack Hub 的存储基础结构
本文介绍 Azure Stack Hub 存储基础结构资源的运行状况和工作状态。 这些资源包括存储驱动器和卷。 本主题中的信息可帮助你排查各种问题，例如，无法将驱动器添加到池的问题。

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
| ObjStore_1 | 未知 | 已分离 |
| ObjStore_2 | 警告 | {已降级，不完整} |

以下部分列出了运行状况和工作状态：

### <a name="volume-health-state-healthy"></a>卷运行状况：正常

| 操作状态 | 说明 |
|---|---|
| OK | 卷处于正常状态。 |
| 欠佳 | 数据未均匀写入到各个驱动器。<br> <br>**操作：** 请联系支持人员优化存储池中的驱动器用法。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 恢复失败的连接后，可能需要从备份还原数据。 |

### <a name="volume-health-state-warning"></a>卷运行状况：警告

如果卷处于“警告”运行状况状态，则表示数据的一个或多个副本不可用，但 Azure Stack Hub 仍可读取至少一个数据副本。

| 操作状态 | 说明 |
|---|---|
| 运行中 | Azure Stack Hub 正在修复卷（例如，在添加或删除驱动器之后）。 修复完成后，卷应该会恢复“正常”运行状况。<br> <br>**操作：** 等待 Azure Stack Hub 完成修复卷，然后检查状态。 |
| 不完整 | 由于一个或多个驱动器出现故障或缺失，卷的复原能力下降。 但是，缺失的驱动器包含数据的最新副本。<br> <br>**操作：** 重新连接所有缺失的驱动器，更换所有出现故障的驱动器，并使脱机的所有服务器联机。 |
| 已降级 | 由于一个或多个驱动器出现故障或缺失，并且驱动器上的数据副本已过时，因此卷的复原能力下降。<br> <br>**操作：** 重新连接所有缺失的驱动器，更换所有出现故障的驱动器，并使脱机的所有服务器联机。 |

### <a name="volume-health-state-unhealthy"></a>卷运行状况：不正常

如果某个卷处于“不正常”运行状况，该卷上的部分或所有数据当前将不可访问。

| 操作状态 | 说明 |
|---|---|
| 无冗余 | 由于过多的驱动器出现故障，该卷已丢失数据。<br> <br>**操作：** 请联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |

### <a name="volume-health-state-unknown"></a>卷运行状况：未知

如果虚拟磁盘已分离，卷也有可能处于“未知”运行状况。

| 操作状态 | 说明 |
|---|---|
| 已分离 | 某个存储设备出现故障，从而可能导致卷不可访问。 某些数据可能已丢失。<br> <br>**操作：** <br>1.检查所有存储设备的物理连接和网络连接。<br>2.如果所有设备连接正确，请联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 恢复失败的连接后，可能需要从备份还原数据。 |

## <a name="drive-states"></a>驱动器状态

使用以下 PowerShell 命令监视驱动器的状态：

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

以下部分描述了驱动器可能处于的运行状况：

### <a name="drive-health-state-healthy"></a>驱动器运行状况：正常

| 操作状态 | 说明 |
|---|---|
| OK | 卷处于正常状态。 |
| 运行中 | 驱动器正在执行某些内部保养操作。 操作完成后，驱动器应会恢复“正常”运行状况。 |

### <a name="drive-health-state-warning"></a>驱动器运行状况状态：警告

处于“警告”状态的驱动器可以成功读取和写入数据，但存在问题。

| 操作状态 | 说明 |
|---|---|
| 通信断开 | 与驱动器的连接已断开。<br> <br>**操作：** 使所有服务器恢复联机。 如果仍未解决问题，请重新连接驱动器。 如果此状态持续出现，请更换驱动器，以确保能够全面复原。 |
| 预测性故障 | 预测驱动器即将发生故障。<br> <br>**操作：** 请尽快更换驱动器，以确保能够全面复原。 |
| IO 错误 | 访问驱动器时发生暂时性错误。<br> <br>**操作：** 如果此状态持续出现，请更换驱动器，以确保能够全面复原。 |
| 暂时性错误 | 驱动器出现暂时性错误。 此错误通常表示驱动器无响应，但也可能表示不恰当地从驱动器中删除了存储空间直通的保护分区。 <br> <br>**操作：** 如果此状态持续出现，请更换驱动器，以确保能够全面复原。 |
| 异常延迟 | 驱动器有时无响应并出现故障迹象。<br> <br>**操作：** 如果此状态持续出现，请更换驱动器，以确保能够全面复原。 |
| 从池中删除 | Azure Stack Hub 正在从其存储池中删除驱动器。<br> <br>**操作：** 等待 Azure Stack Hub 完成删除驱动器，然后检查状态。<br>如果仍旧出现此状态，请联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |
| 启动维护模式 | Azure Stack Hub 正在将驱动器置于维护模式。 此状态为临时状态，驱动器应很快处于维护模式状态。<br> <br>**操作：** 等待 Azure Stack Hub 完成该过程，然后检查状态。 |
| 维护中模式 | 驱动器处于维护模式，因此对其的读取和写入操作已停止。 此状态通常表示正在执行 Azure Stack Hub 管理任务，例如，PNU 或 FRU 正在操作驱动器。 但是，管理员也可将驱动器置于维护模式。<br> <br>**操作：** 等待 Azure Stack Hub 完成管理任务，然后检查状态。<br>如果仍旧出现此状态，请联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |
| 停止维护模式 | Azure Stack Hub 正在将驱动器恢复联机。 此状态是暂时性的 - 驱动器应该很快就会处于另一种状态，最好是“正常运行”状态。<br> <br>**操作：** 等待 Azure Stack Hub 完成该过程，然后检查状态。 |

### <a name="drive-health-state-unhealthy"></a>驱动器运行状况：不正常

当前无法写入或访问处于“不正常”状态的驱动器。

| 操作状态 | 说明 |
|---|---|
| 拆分 | 已从池中分离驱动器。<br> <br>**操作：** 使用新磁盘替换该驱动器。 如果必须使用此磁盘，请从系统中删除该磁盘，确保该磁盘上没有任何有用的数据，擦除该磁盘，然后重新安装磁盘。 |
| 不可用 | 物理磁盘已被隔离，因为它不受解决方案供应商的支持。 仅支持批准用于解决方案并且包含正确磁盘固件的磁盘。<br> <br>**操作：** 将该驱动器替换为由解决方案批准的制造商提供的且带有认可型号的磁盘。 |
| 已过时的元数据 | 用于更换的磁盘之前已用过，可能包含未知存储系统中的数据。 该磁盘已被隔离。 <br> <br>**操作：** 使用新磁盘替换该驱动器。 如果必须使用此磁盘，请从系统中删除该磁盘，确保该磁盘上没有任何有用的数据，擦除该磁盘，然后重新安装磁盘。 |
| 无法识别的元数据 | 在驱动器上找到了无法识别的元数据，这通常表示驱动器包含来自其他池的元数据。<br> <br>**操作：** 使用新磁盘替换该驱动器。 如果必须使用此磁盘，请从系统中删除该磁盘，确保该磁盘上没有任何有用的数据，擦除该磁盘，然后重新安装磁盘。 |
| 介质故障 | 驱动器出现故障，不再可供存储空间使用。<br> <br>**操作：** 请尽快更换驱动器，以确保能够全面复原。 |
| 设备硬件故障 | 此驱动器上出现硬件故障。 <br> <br>**操作：** 请尽快更换驱动器，以确保能够全面复原。 |
| 正在更新固件 | Azure Stack Hub 正在更新驱动器上的固件。 此状态是暂时性的，其持续时间通常小于一分钟，在此期间，池中的其他驱动器会处理所有读取和写入操作。<br> <br>**操作：** 等待 Azure Stack Hub 完成更新，然后检查状态。 |
| 正在启动 | 驱动器正在为操作做好准备。 此状态应为 "临时"-完成后，驱动器将转换为其他操作状态。<br> <br>**操作：** 等待 Azure Stack Hub 完成操作，然后检查状态。 |

## <a name="reasons-a-drive-cant-be-pooled"></a>驱动器无法入池的原因

某些驱动器尚未做好加入 Azure Stack Hub 存储池的准备。 通过查看驱动器的 `CannotPoolReason` 属性，可以确定驱动器为何不符合入池条件的原因。 下表更具体地描述了每种原因。

| Reason | 说明 |
|---|---|
| 硬件不合规 | 使用运行状况服务指定的已批准存储模型列表中不包括该驱动程序。<br> <br>**操作：** 使用新磁盘替换该驱动器。 |
| 固件不合规 | 使用运行状况服务指定的已批准固件修订版列表中不包括该物理驱动器上的固件。<br> <br>**操作：** 使用新磁盘替换该驱动器。 |
| 已由群集使用 | 该驱动器当前已由故障转移群集使用。<br> <br>**操作：** 使用新磁盘替换该驱动器。 |
| 可移动媒体 | 该驱动器分类为可移动驱动器。 <br> <br>**操作：** 使用新磁盘替换该驱动器。 |
| 不正常 | 该驱动器不处于正常状态，可能需要更换。<br> <br>**操作：** 使用新磁盘替换该驱动器。 |
| 容量不足 | 某些分区占用了驱动器上的可用空间。<br> <br>**操作：** 使用新磁盘替换该驱动器。 如果必须使用此磁盘，请从系统中删除该磁盘，确保该磁盘上没有任何有用的数据，擦除该磁盘，然后重新安装磁盘。 |
| 正在验证 | 运行状况服务正在检查是否已批准使用驱动器上的固件。<br> <br>**操作：** 等待 Azure Stack Hub 完成该过程，然后检查状态。 |
| 验证失败 | 运行状况服务无法检查是否已批准使用驱动器上的固件。<br> <br>**操作：** 请联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |
| Offline | 驱动器已脱机。 <br> <br>**操作：** 请联系支持人员。 在此之前，请参考 https://aka.ms/azurestacklogfiles 中的指导启动日志文件收集过程。 |
