---
title: 使用 Windows 管理中心管理 Azure Stack HCI 群集
description: 了解如何使用 Windows 管理中心在 Azure Stack HCI 上管理群集。
ms.topic: article
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 55ce577cbedde36d271ab6fc13234b009c825d7e
ms.sourcegitcommit: 7c10a45a8de0c5c7649e5329ca5b69a0791e37b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83729277"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>使用 Windows 管理中心管理 Azure Stack HCI 群集

> 适用于：Windows Server 2019

可以使用 Windows 管理中心来管理 Azure Stack HCI 中的群集。 具体而言，你将使用 Windows 管理中心中的群集管理器扩展来管理群集。

## <a name="view-the-cluster-dashboard"></a>查看群集仪表板

群集仪表板显示有关群集运行状况和性能的信息。

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="群集仪表板屏幕":::

若要查看此信息，请在 "**所有连接**" 下选择群集名称，然后在左侧的 "**工具**" 下选择 "**仪表板**"。 可以查看以下信息：

- 群集事件警报
- 已加入群集的服务器列表
- 群集上运行的虚拟机的列表
- 群集上可用的磁盘驱动器列表
- 群集上可用卷的列表
- 群集的群集总 CPU 使用率
- 群集的群集内存使用率总计
- 群集的群集存储用量总计
- 总群集输入/输出操作数/秒（IOPS）
- 平均群集延迟（毫秒）

## <a name="change-cluster-general-settings"></a>更改群集常规设置

可以向群集应用五个常规设置。

1. 在 Windows 管理中心，单击顶部下拉箭头中的 "**群集管理器**"。
1. 在 "**工具**" 下，单击 "**设置**"。
1. 若要更改群集名称，请选择 "**访问点**"，然后输入新名称。

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="群集访问点屏幕":::

1. 若要控制节点关闭行为，请选择 "**节点关闭行为**"，并确保已启用该复选框。 这将首先从节点移动所有虚拟机，以允许正常的节点关闭。

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="群集节点关闭行为屏幕":::

1. 若要对用于在群集节点之间发送数据的 SMB 连接进行加密，请选择 "**群集流量加密**"，然后从下拉框中选择以下各项的 "**加密**"：

   - **核心流量**-加密通过 NetFT （群集虚拟适配器）在端口3343上发送的流量

   - **服务器流量**-加密群集共享卷（CSV）和存储总线层（SBL）流量

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="群集群集流量加密屏幕":::

1. 若要跨群集自动对虚拟机进行负载平衡，请选择 "**虚拟机负载平衡**"，然后执行以下操作：

   - 若要**平衡虚拟机**，请选择相应的操作
   - 对于 "**入侵**"，选择适当的行为

     有关其工作原理的信息，请参阅[虚拟机负载平衡概述](https://docs.microsoft.com/windows-server/failover-clustering/vm-load-balancing-overview)。

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="群集虚拟机负载平衡屏幕":::

1. 若要选择仲裁见证类型，请选择 "**见证**"，然后选择下列各项之一：

   - **云见证**-使用 Azure 云资源作为见证服务器
   - **磁盘见证**-使用磁盘资源作为见证服务器
   - **文件共享见证**-使用文件共享作为见证服务器

        有关详细信息，请参阅[配置和管理仲裁](https://docs.microsoft.com/windows-server/failover-clustering/manage-cluster-quorum)。

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="分类见证屏幕":::

## <a name="change-cluster-hyper-v-settings"></a>更改群集 Hyper-v 设置

可以将五个 Hyper-v 主机设置应用到群集。

1. 在 Windows 管理中心，单击顶部下拉箭头中的 "**群集管理器**"。
1. 在 "**工具**" 下，单击 "**设置**"。
1. 选择 "**常规**"，并使用以下设置：

   - **虚拟硬盘路径**-指定用于存储虚拟硬盘文件的默认文件夹。

   - **虚拟机路径**-指定用于存储虚拟机配置文件的默认文件夹。

   - **虚拟机监控程序计划程序类型**-选择**核心计划**程序或**经典计划程序**。 这会确定虚拟机监控程序如何计划虚拟进程在使用同时进行多线程处理（也称为 SMT 或超线程）的物理处理器上运行。

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="群集 Hyper-v 常规设置屏幕":::

1. 若要允许重定向虚拟机中的本地设备和资源，请选择 "**增强会话模式**"。 请注意，"增强会话模式" 连接需要受支持的来宾操作系统。

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="群集 Hyper-v 增强会话模式屏幕":::

1. 若要允许虚拟机跨物理 NUMA 节点，请选择 " **NUMA 跨越**"。 非统一内存体系结构（NUMA）跨越可以为虚拟机提供比单个 NUMA 节点上的可用内存更多的内存。

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="群集 NUMA 跨越屏幕":::

1. 若要指定运行（实时迁移）时可以同时移动的 Vm 数，请选择 "**实时迁移**"，选择一个数字，然后指定以下各项：，选择 "**实时迁移**"，选择一个数字，然后指定以下各项：

   - 对于**身份验证协议**，请选择**CredSSP**或**Kerberos**。

   - 对于 "**性能" 选项**，请选择 "**压缩**" 或 " **SMB**"。 压缩的数据通过 TCP/IP 连接发送。

   - 启用 "**使用任何网络**" 复选框以使用节点上的任何可用网络来执行迁移

        :::image type="content" source="media/manage-cluster/cluster-settings-liv-migration.png" alt-text="群集实时迁移屏幕":::

1. 若要指定可同时执行的存储迁移的数量，请选择 "**存储迁移**"，然后选择一个数字。

    :::image type="content" source="media/manage-cluster/cluster-settings-sto-migration.png" alt-text="群集存储迁移屏幕":::

## <a name="change-cluster-storage-settings"></a>更改群集存储设置

您可以更改与可以应用于群集的存储空间直通相关的两个设置。

1. 在 Windows 管理中心，单击顶部下拉箭头中的 "**群集管理器**"。
1. 在 "**工具**" 下，单击底部的 "**设置**"。
1. 若要配置存储缓存，请选择 "**存储空间直通**"，然后配置以下各项：

   - 对于**永久性缓存**，请选择 "**已启用**" 或 "**已禁用**"

   - 对于**HDD 的缓存模式**，请选择 "**只读**"、"**仅写入**" 或 "**读/写**"

   - 对于**SSD 的缓存模式**，请选择**只读**、**只写**或**读/写**

        :::image type="content" source="media/manage-cluster/cluster-settings-ssd.png" alt-text="群集存储空间直通屏幕":::

1. 若要使用服务器内存来缓存频繁读取，请选择 **"内存中缓存**"，并指定每个服务器要使用的最大内存。 另请参阅[将存储空间直通与 CSV 内存中读取缓存一起使用](https://docs.microsoft.com/windows-server/storage/storage-spaces/csv-cache)。

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="群集内存中缓存屏幕":::

## <a name="next-steps"></a>后续步骤

- 若要监视群集性能，请参阅[监视群集性能](https://docs.microsoft.com/azure-stack/hci/get-started#monitor-cluster-performance-with-the-windows-admin-center-dashboard)