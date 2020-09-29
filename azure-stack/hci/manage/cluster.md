---
title: 管理 Azure Stack HCI 群集-Windows 管理中心
description: 了解如何使用 Windows 管理中心在 Azure Stack HCI 上管理群集。
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 07/21/2020
ms.openlocfilehash: d1b8556908da268bbd99c7aa9341128c9dc5be36
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573779"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>使用 Windows Admin Center 管理 Azure Stack HCI 群集

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

Windows Admin Center 可用于管理 Azure Stack HCI 群集。 具体来说，是使用 Windows Admin Center 中的群集管理器扩展来管理群集。

## <a name="view-the-cluster-dashboard"></a>查看群集仪表板

群集仪表板显示有关群集运行状况和性能的信息。

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="群集仪表板屏幕" lightbox="media/manage-cluster/cluster-dashboard.png":::

若要查看此信息，请在“所有连接”下选择群集名称，然后在左侧的“工具”下选择“仪表板”  。 可以查看以下内容：

- 群集事件警报
- 已加入群集的服务器列表
- 群集上运行的虚拟机列表
- 群集上可用的磁盘驱动器列表
- 群集上可用的卷列表
- 群集的群集 CPU 使用率
- 群集的群集内存总使用率
- 群集的群集存储总使用率
- 群集总输入/输出操作数/秒 (IOPS)
- 群集平均延迟(以毫秒计)

## <a name="change-cluster-storage-settings"></a>更改群集存储设置

有两个可以进行更改、可应用于群集且与存储空间直通相关的设置。

1. 在 Windows Admin Center 中，从顶部下拉箭头中选择“群集管理器”。
1. 在 " **工具**" 下，选择底部的 " **设置** "。
1. 若要配置存储缓存，请选择“存储空间直通”，然后配置以下各项：

   - 对于“永久缓存”，选择“已启用”或“已禁用”  

   - 对于“HDD 缓存模式”，选择“只读”、“只写”或“读取/写入”   

   - 对于“SSD 缓存模式”，选择“只读”、“只写”或“读取/写入”   

        :::image type="content" source="media/manage-cluster/cluster-settings-ssd.png" alt-text="群集存储空间直通屏幕" lightbox="media/manage-cluster/cluster-settings-ssd.png":::

1. 若要使用服务器内存来缓存频繁读取的内容，请选择“内存中缓存”，然后指定每个服务器要使用的最大内存。 另请参阅[了解 Azure Stack HCI 中的缓存](../concepts/cache.md)。

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="群集内存中缓存屏幕" lightbox="media/manage-cluster/cluster-settings-memory.png":::

## <a name="change-cluster-general-settings"></a>更改群集常规设置

可以向群集应用五个常规设置。 可在此处设置和管理访问点、节点关闭行为、流量加密、VM 负载均衡和群集见证。

1. 在 Windows Admin Center 中，从顶部下拉箭头中选择“群集管理器”。
1. 在“工具”下，选择“设置” 。
1. 若要更改群集名称，请选择“访问点”，然后输入新名称。

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="主动/主动延伸群集方案" lightbox="media/manage-cluster/cluster-settings-access.png":::

1. 若要控制节点关闭行为，请选择“节点关闭行为”，然后确保勾选了复选框。 此操作会先将所有虚拟机从节点移出，以允许节点正常关闭。

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="群集节点关闭行为屏幕" lightbox="media/manage-cluster/cluster-settings-shutdown.png":::

1. 若要加密用于在群集节点之间发送数据的 SMB 连接，请选择“群集流量加密”，然后从下拉框中选择“加密”，以实现以下加密目的 ：

   - **核心流量** - 对在端口 3343 上通过 NetFT（群集虚拟适配器）发送的流量进行加密

   - **服务器流量** - 对群集共享卷 (CSV) 和存储总线层 (SBL) 流量进行加密

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="群集群集流量加密屏幕" lightbox="media/manage-cluster/cluster-settings-encryption.png":::

1. 若要自动在整个群集的所有虚拟机中实现负载均衡，请选择“虚拟机负载均衡”，然后执行以下操作：

   - 对于“均衡虚拟机”，选择相应的操作
   - 对于“入侵”，选择相应的行为

     若要了解其工作原理，请参阅 [Virtual Machine Load Balancing overview](/windows-server/failover-clustering/vm-load-balancing-overview)（虚拟机负载均衡概述）。

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="群集虚拟机负载均衡屏幕" lightbox="media/manage-cluster/cluster-settings-vm-load.png":::

1. 若要选择仲裁见证类型，请选择“见证”，然后选择以下选项之一：

   - **云见证** - 使用 Azure 云资源作为见证
   - **磁盘见证** - 使用磁盘资源作为见证（不用于延伸群集）
   - **文件共享见证** - 使用文件共享作为见证

        有关详细信息，请参阅[了解 Azure Stack HCI 上的群集和池仲裁](../concepts/quorum.md)。

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="群集见证屏幕" lightbox="media/manage-cluster/cluster-settings-witness.png":::

## <a name="change-cluster-hyper-v-settings"></a>更改群集 Hyper-V 设置

可以向群集应用五个 Hyper-V 主机设置。

1. 在 Windows Admin Center 中，从顶部下拉箭头中选择“群集管理器”。
1. 在“工具”下，选择“设置” 。
1. 选择“常规”，然后使用以下设置：

   - **虚拟硬盘路径** - 指定用于存储虚拟硬盘文件的默认文件夹。

   - **虚拟机路径** - 指定用于存储虚拟机配置文件的默认文件夹。

   - **虚拟机监控程序计划程序类型** - 选择“核心计划程序”或“经典计划程序” 。 这会决定虚拟机监控程序如何安排虚拟进程在使用“同时多线程”（也称为 SMT 或超线程）的物理处理器上运行。

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="群集 Hyper-V 常规设置屏幕" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. 若要允许从虚拟机重定向本地设备和资源，请选择“增强会话模式”。 请注意，增强会话模式的连接需要使用受支持的来宾操作系统。

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="群集 Hyper-V 增强会话模式屏幕" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. 若要允许虚拟机涵盖物理 NUMA 节点，请选择“NUMA 涵盖”。 非统一内存体系结构 (NUMA) 涵盖可以为虚拟机提供比单个 NUMA 节点上可用内存更多的内存。

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="群集 NUMA 涵盖屏幕" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. 若要指定可以在运行的同时进行迁移（实时迁移）的 VM 的数量，请选择“实时迁移”，选择一个数字，然后指定以下内容：

   - 对于“身份验证协议”，选择 CredSSP 或 Kerberos  。

   - 对于“性能选项”，请选择“压缩”或 SMB  。 压缩数据通过 TCP/IP 连接发送。

   - 勾选“使用任意网络”复选框即可使用节点上的任意可用网络执行迁移

        :::image type="content" source="media/manage-cluster/cluster-settings-liv-migration.png" alt-text="群集实时迁移屏幕" lightbox="media/manage-cluster/cluster-settings-liv-migration.png":::

1. 若要指定可同时执行的存储迁移数，请选择“存储迁移”，然后选择一个数字。

    :::image type="content" source="media/manage-cluster/cluster-settings-sto-migration.png" alt-text="群集存储迁移屏幕" lightbox="media/manage-cluster/cluster-settings-sto-migration.png":::

## <a name="next-steps"></a>后续步骤

- 若要监视群集，请参阅[使用 Azure Monitor 监视 Azure Stack HCI](azure-monitor.md)。
