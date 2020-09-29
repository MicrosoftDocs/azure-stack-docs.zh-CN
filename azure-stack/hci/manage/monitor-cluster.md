---
title: 监视 Azure Stack HCI 群集
description: 如何使用 Windows Admin Center 和 PowerShell 监视 Azure Stack HCI 群集、服务器、虚拟机、驱动器和卷。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/12/2020
ms.openlocfilehash: 9ce1dc258243e9e17458c1f70e5a852a0b56996a
ms.sourcegitcommit: a3e042c782a38ecf3baf7a64b1d492a655972f9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201991"
---
# <a name="monitor-azure-stack-hci-clusters"></a>监视 Azure Stack HCI 群集

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

有三种方法可以监视 Azure Stack HCI 群集及其基础组件：Windows Admin Center、[Azure Monitor](azure-monitor.md) 和 PowerShell。

## <a name="monitor-using-windows-admin-center-dashboard"></a>使用 Windows Admin Center 仪表板进行监视

在管理电脑或服务器上[安装 Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install)，然后将其添加并连接到要监视的 Azure Stack HCI 群集。 登录后，Windows Admin Center 仪表板顶部会立即突出显示关键警报。 例如，以下屏幕截图表明需要安装更新且群集有一个严重的驱动器错误：

:::image type="content" source="media/monitor-cluster/dashboard-alert.png" alt-text="Windows Admin Center 仪表板警报示例":::

## <a name="monitor-virtual-machines"></a>监视虚拟机

了解运行应用程序和数据库的虚拟机 (VM) 的运行状况非常重要。 如果没有为 VM 上运行的工作负载分配足够的 CPU 或内存，性能可能会降低，或者应用程序可能会变得不可用。 如果 VM 在 5 分钟或更长时间内响应的检测信号少于三个，则可能存在问题。

若要在 Windows Admin Center 中监视 VM，请单击左侧“工具”菜单中的“虚拟机” 。 若要查看在群集上运行的 VM 的完整清单，请单击页面顶部的“清单”。 你将看到一个表，其中包含每个 VM 的相关信息，包括：

- **名称：** VM 的名称。
- **状态：** 指示 VM 是在运行还是已停止。
- **主机服务器：** 指示 VM 在群集中的哪个服务器上运行。
- **CPU 使用率：** VM 使用的资源占群集总 CPU 资源量的百分比。
- **内存压力：** VM 使用的资源占可用内存资源的百分比。
- **内存需求：** VM 使用的分配的内存量（GB 或 MB）。
- **分配的内存：** 分配给 VM 的内存总量。
- **运行时间：** VM 已运行多长时间（以“天:小时:分钟:秒”表示）。
- **检测信号：** 指示群集是否可以与 VM 通信。
- **灾难恢复状态：** 显示 VM 是否已登录 Azure Site Recovery。

## <a name="monitor-servers"></a>监视服务器

可以直接通过 Windows Admin Center 监视构成 Azure Stack HCI 群集的主机服务器。 如果没有为主机服务器配置足够的 CPU 或内存来提供 VM 所需的资源，它们可能会导致性能瓶颈。 

若要在 Windows Admin Center 中监视服务器，请单击左侧“工具”菜单中的“服务器” 。 若要查看群集中服务器的完整清单，请单击页面顶部的“清单”。 你将看到一个表，其中包含每个服务器的相关信息，包括：

- **名称：** 群集中的主机服务器的名称。
- **状态：** 指示服务器是启动还是关闭状态。
- **运行时间：** 服务器已启动多长时间。
- **制造商：** 服务器的硬件制造商。
- **型号：** 服务器的型号。
- **序列号：** 服务器的序列号。
- **CPU 使用率：** 正在使用的主机服务器 CPU 占比。 群集中任何服务器使用超过 85% 的 CPU 的时间都不应长于 10 分钟。 
- **内存使用率：** 正在使用的主机服务器内存占比。 如果服务器可用内存小于 100MB 的时间达 10 分钟或更长时间，请考虑增加内存。

## <a name="monitor-volumes"></a>监视卷

存储卷的填充速度可能会很快，因此应定期监视它们以避免对应用程序造成任何影响，这非常重要。 若要在 Windows Admin Center 中监视卷，请单击左侧“工具”菜单中的“卷” 。 若要查看群集上存储卷的完整清单，请单击页面顶部的“清单”。 你将看到一个表，其中包含每个卷的相关信息，包括：

- **名称：** 卷的名称。
- **状态：** “OK”表示卷正常；否则，将显示警告或错误。
- **文件系统：** 卷（ReFS 和 CSVFS）上的文件系统。
- **复原能力：** 指示卷是双向镜像、三向镜像还是镜像加速奇偶校验。
- **Size：** 卷的大小 (TB/GB)
- **存储池：** 卷所属的存储池。
- **存储使用率：** 已使用的卷存储容量占比。
- **IOPS：** 每秒输入/输出操作数。

## <a name="monitor-drives"></a>监视驱动器

Azure Stack HCI 的存储虚拟化方式使得丢失一个单独的驱动器不会对集群造成重大影响。 但是，[需要替换](replace-drives.md)故障驱动器，并且驱动器有可能因填充或引入延迟而影响性能。 如果操作系统无法与驱动器通信，则驱动器可能会变得松散或断开连接，其连接器可能出现故障或驱动器本身可能出现故障。 在通信中断 15 分钟后，Windows 将自动停用驱动器。 

若要在 Windows Admin Center 中监视驱动器，请单击左侧“工具”菜单中的“驱动器” 。 若要查看群集上驱动器的完整清单，请单击页面顶部的“清单”。 你将看到一个表，其中包含每个驱动器的相关信息，包括：

- **序列号：** 驱动器的序列号。
- **状态：** “OK”表示驱动器正常；否则，将显示警告或错误。
- **型号：** 驱动器的型号。
- **Size：** 驱动器的总容量 (TB/GB)。
- **类型：** 驱动器类型（SSD、HDD）。
- **用途：** 指示驱动器是用于缓存还是容量。
- **位置：** 驱动器连接到的存储适配器和端口。
- **服务器：** 驱动器连接到的服务器的名称。
- **存储池：** 驱动器所属的存储池。
- **存储使用率：** 已使用的驱动器存储容量占比。

## <a name="add-performance-counters"></a>添加性能计数器

使用 Windows Admin Center 中的性能监视器工具可实时查看和比较 Windows、应用或设备的性能计数器。

1. 从左侧的“工具”菜单中选择“性能监视器”。
1. 单击“空白工作区”以启动新工作区，或者单击“还原以前的工作区”以还原以前的工作区。
1. 如果要创建新的工作区，请单击“添加计数器”按钮，然后选择要监视的一个或多个源服务器，或选择整个群集。
1. 选择要监视的对象和实例以及计数器和图形类型，以查看动态性能信息。
1. 选择顶部菜单中的“保存”>“另存为”，保存工作区。
 
例如，下面的屏幕截图显示了名为“内存使用率”的性能计数器，它显示了跨二节点群集的内存的相关信息。

:::image type="content" source="media/monitor-cluster/performance-monitor.png" alt-text="Windows Admin Center 中的实时性能计数器示例":::

## <a name="query-and-process-performance-history-with-powershell"></a>通过 PowerShell 查询和处理性能历史记录

还可以使用 PowerShell cmdlet 监视 Azure Stack HCI 群集，这些 cmdlet 返回有关群集及其组件的信息。 请参阅[存储空间直通的性能历史记录](/windows-server/storage/storage-spaces/performance-history)。

## <a name="use-the-health-service-feature"></a>使用运行状况服务功能

应调查群集上的任何运行状况服务错误。 请参阅 [Windows Server 中的运行状况服务](/windows-server/failover-clustering/health-service-overview)，了解如何运行报告和确定故障。

## <a name="troubleshoot-health-and-operational-states"></a>对运行状况和操作状态进行故障排除

若要了解存储池、虚拟磁盘和驱动器的运行状况和操作状态，请参阅[对存储空间和存储空间直通运行状况和操作状态进行故障排除](/windows-server/storage/storage-spaces/storage-spaces-states)。

## <a name="monitor-performance-using-storage-qos"></a>使用存储 QoS 监视性能

存储服务质量 (QoS) 提供了一种集中监视和管理 VM 的存储 I/O 的方法，以减少邻近干扰问题并提供一致的性能。 请参阅[存储服务质量](/windows-server/storage/storage-qos/storage-qos-overview)。

## <a name="set-up-alerts-in-azure-monitor"></a>在 Azure Monitor 中设置警报

Azure Stack HCI 与 Azure Monitor 集成，使用户能够[设置警报](azure-monitor.md#setting-up-alerts-using-windows-admin-center)，并在超过 CPU、磁盘容量和内存使用率阈值、未返回 VM 检测信号或者出现系统严重错误或运行状况服务错误时收到通知。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [Azure Stack HCI 和 Windows Admin Center 入门](../get-started.md)
- [使用 Azure Monitor 监视 Azure Stack HCI](azure-monitor.md)