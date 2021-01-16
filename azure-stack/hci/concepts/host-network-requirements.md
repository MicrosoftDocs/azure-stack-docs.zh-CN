---
title: Azure Stack HCI 的主机网络要求
description: 了解 Azure Stack HCI 的主机网络要求
author: v-dasis
ms.topic: how-to
ms.date: 11/25/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 3e31852e554c85ffab18aacaa336a007a97874f2
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255293"
---
# <a name="host-network-requirements-for-azure-stack-hci"></a>Azure Stack HCI 的主机网络要求

> 适用于 Azure Stack HCI 版本 20H2

本主题讨论 Azure Stack HCI 主机网络注意事项和要求。 有关数据中心体系结构和服务器之间的物理连接的信息，请参阅 [物理网络要求](physical-network-requirements.md)。

## <a name="network-traffic-types"></a>网络流量类型

Azure Stack HCI 网络流量可以按其预期目的进行分类：

- **计算流量** -源自或发往虚拟机的流量 (VM) 
- **存储流量** -使用服务器消息块 (SMB 的存储空间直通 (S2D) 流量) 
- **管理流量** -对群集管理的管理员（例如 Active Directory、远程桌面、windows 管理中心和 windows PowerShell）非常重要的流量。

## <a name="selecting-a-network-adapter"></a>选择网络适配器

对于 Azure Stack HCI，我们需要选择一个网络适配器，该适配器已实现 Windows Server Software-Defined 数据中心 (SDDC) 使用标准或高级额外资格 (AQ) 的认证。 这些适配器支持最先进的平台功能，并已通过我们的硬件合作伙伴完成了大部分测试。 通常情况下，这一级别的审查会导致硬件和驱动程序相关的质量问题的降低。

你可以通过查看适配器的 [Windows Server 目录](https://www.windowsservercatalog.com/) 条目和适用的操作系统版本来标识具有标准或高级 AQ 的适配器。 下面是高级 AQ 的表示法的示例：

:::image type="content" source="media/plan-networking/windows-certified.png" alt-text="Windows 认证" lightbox="media/plan-networking/windows-certified.png":::

## <a name="overview-of-key-network-adapter-capabilities"></a>关键网络适配器功能概述

Azure Stack HCI 利用重要的网络适配器功能包括：

- 动态虚拟机多队列 (动态 VMMQ 或 VMMQ) 
- 远程直接内存访问 (RDMA)
- 来宾 RDMA
- 切换嵌入组合 (集) 

### <a name="dynamic-vmmq"></a>动态 VMMQ

具有高级 AQ 的所有网络适配器都支持动态 VMMQ。 动态 VMMQ 要求使用交换机嵌入组合。

**适用的流量类型**：计算

**要求认证**：高级

动态 VMMQ 是一项智能的接收方技术，它基于虚拟机队列其 (VMQ) 、虚拟接收方缩放 (vRSS) 和 VMMQ 提供以下三项主要改进：

- 使用 CPU 核心优化主机效率
- 自动优化对 CPU 核心的网络流量处理，从而使 Vm 能够满足并维持预期的吞吐量
- 允许 "突发" 工作负荷接收预期的流量量

有关动态 VMMQ 的详细信息，请参阅博客文章 [合成加速](https://techcommunity.microsoft.com/t5/networking-blog/synthetic-accelerations-in-a-nutshell-windows-server-2019/ba-p/653976)。

### <a name="rdma"></a>RDMA

RDMA 是网络适配器的网络堆栈卸载，允许 SMB 存储通信绕过操作系统进行处理。

RDMA 可实现高吞吐量、低延迟的网络，同时使用最小的主机 CPU 资源。 然后，可以使用这些主机 CPU 资源来运行其他 Vm 或容器。

**适用的流量类型**：主机存储

**需要认证**：标准

具有标准或高级 AQ 的所有适配器都支持 RDMA (远程直接内存访问) 。 RDMA 是 Azure Stack HCI 中的存储工作负荷的建议部署选择，可以选择为 (使用适用于 Vm 的 SMB) 的存储工作负荷启用该部署。 请参阅 " **来宾 RDMA** " 一节。

Azure Stack HCI 使用 iWARP 或 RoCE 协议实现支持 RDMA。

> [!IMPORTANT]
> RDMA 适配器仅适用于实现相同 RDMA 协议 (iWARP 或 RoCE) 的其他 RDMA 适配器。

并非供应商提供的所有网络适配器都支持 RDMA。 下表按字母顺序列出了这些供应商 (提供高级认证 RDMA 适配器的) 。 但是，此列表中还不包括还支持 RDMA 的硬件供应商。 若要验证 RDMA 支持，请参阅 [Windows Server 目录](https://www.windowsservercatalog.com/) 。

> [!NOTE]
> Azure Stack HCI 不支持无 (IB) 。

|NIC 供应商|iWARP|RoCE|
|----|----|----|
|Broadcom|否|是|
|Chelsio|是|否|
|Intel|是|是 (一些模型) |
|Marvell (Qlogic/Cavium) |是|是|
|Nvidia (Mellanox) |否|是|

> [!NOTE]
> 并非供应商提供的所有适配器都支持 RDMA。 请验证与特定网卡供应商的 RDMA 支持。

有关部署 RDMA 的详细信息，请下载 [SDN GitHub](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx)存储库中的 Word 文档。

#### <a name="internet-wide-area-rdma-protocol-iwarp"></a>Internet 广域 RDMA 协议 (iWARP) 

iWARP 使用传输控制协议 (TCP) ，可以使用数据中心桥接 (DCB) 基于优先级的流控制 (PFC) 和增强的传输服务 (ETS) 来进行增强。

建议在以下情况中使用 iWARP：

- 你的网络体验很少或没有任何管理网络交换机
- 不控制 ToR 开关
- 部署后将不会管理解决方案
- 已使用 iWARP 进行部署
- 不确定选择哪个选项

#### <a name="rdma-over-converged-ethernet-roce"></a>基于聚合以太网的 RDMA (RoCE) 

RoCE 使用用户数据报协议 (UDP) ，并需要数据中心桥接 PFC 和 ETS 来提供可靠性。

建议在以下情况中使用 RoCE：

- 你的数据中心内已有 RoCE 的部署
- 你了解你的物理网络要求

### <a name="guest-rdma"></a>来宾 RDMA

来宾 RDMA 允许 Vm 的 SMB 工作负荷获得与在主机上使用 RDMA 相同的优势。

**适用的流量类型**：计算

**要求认证**：高级

 使用来宾 RDMA 的主要优点是：

- CPU 卸载到 NIC 进行网络流量处理
- 极低延迟
- 高吞吐量

有关详细信息，包括如何部署来宾 RDMA，请从 [SDN GitHub](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx)存储库下载 Word 文档。

### <a name="switch-embedded-teaming-set"></a>切换嵌入组合 (集) 

交换机嵌入组合 (集) 是一项基于软件的组合技术，它已包含在 Windows server 操作系统中，因为 Windows Server 2016。 集不依赖于所使用的网络适配器类型。

**适用的流量类型**：计算、存储和管理

**需要认证：** 操作系统内置 (无) 

SET 是 Azure Stack HCI 唯一支持的组合技术。 负载平衡/故障转移 (LBFO) 是通常与 Windows Server 一起使用的另一组技术，但 Azure Stack HCI 不支持此技术。 有关 Azure Stack HCI 中的 LBFO 的详细信息，请参阅博客文章 [AZURE STACK hci 中的组合](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642) 。 设置适用于计算、存储和管理流量。

集对 Azure Stack HCI 非常重要，因为这是唯一能实现以下功能的组合技术：

- 如果需要，请 (RDMA 适配器的组合) 
- 来宾 RDMA
- 动态 VMMQ
- 其他密钥 Azure Stack HCI 功能 (参阅 [AZURE STACK HCI 中的组合](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642)) 

集通过 LBFO 提供附加功能，包括质量和性能改进。 为此，设置要求使用对称 (完全相同的) 适配器–不支持非对称适配器的组合。 对称网络适配器具有相同的：

- 使 (供应商) 
- 模型 (版本) 
- 速度 (吞吐量) 
- 配置

确定适配器是否对称的最简单方法是：速度相同，并且接口说明匹配。 它们只能用说明中列出的数字进行描述。 使用 [`Get-NetAdapterAdvancedProperty`](/powershell/module/netadapter/get-netadapteradvancedproperty) cmdlet 确保报告的配置列出相同的属性值。

请参阅下表，了解仅 ( # ) 的接口说明与偏差：

|名称|接口说明|链路速度|
|----|----|----|
|NIC1|网络适配器 #1|25 Gbps|
|NIC2|网络适配器 #2|25 Gbps|
|NIC3|网络适配器 #3|25 Gbps|
|NIC4|网络适配器 #4|25 Gbps|

### <a name="rdma-traffic-considerations"></a>RDMA 流量注意事项

如果实现数据中心桥接 (DCB) ，必须确保在每个网络端口（包括网络交换机）上正确实现 PFC 和 ETS 配置。 IWARP 需要 DCB 和 RoCE。

有关如何部署 RDMA 的详细信息，请下载 [SDN GitHub](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx)存储库中的 Word 文档。

基于 RoCE 的 Azure Stack HCI 实现要求跨构造和所有主机配置三个 PFC 通信类，包括默认流量类：

#### <a name="cluster-traffic-class"></a>群集通信类

此流量类可确保为群集检测信号保留足够的带宽：

- 是否必需：是
- 已启用 PFC：否
- 建议的流量优先级：优先级7
- 建议的带宽预留：
    - 10 GbE 或更低的 RDMA 网络 = 2%
    - 25 GbE 或更高版本 RDMA 网络 = 1%

#### <a name="rdma-traffic-class"></a>RDMA 流量类

此流量类可确保使用 SMB Direct 为无损 RDA 通信保留足够的带宽：

- 是否必需：是
- 已启用 PFC：是
- 建议的流量优先级：优先级3或4
- 建议的带宽预留：50%

#### <a name="default-traffic-class"></a>默认流量类

此流量类携带群集或 RDMA 流量类中未定义的所有其他流量，包括 VM 流量和管理流量：

- 必需：默认情况下 (不需要在主机上进行任何配置) 
- 流控制 (PFC) 已启用：否
- 推荐的流量类：默认情况下 (优先级 0) 
- 建议的带宽预留：默认情况下 (不需要主机配置) 

## <a name="storage-traffic-models"></a>存储流量模型

SMB 提供了许多好处，作为 Azure Stack HCI 的存储协议（包括 SMB 多通道）。 尽管 SMB 多通道超出了本主题的范围，但请务必了解 SMB 多通道可使用的每个可能的链接中的流量是多路复用。

> [!NOTE]
>建议使用多个子网和 Vlan 在 Azure Stack HCI 中分隔存储流量。

请考虑以下四个节点群集的示例。 每个服务器在) 左右两侧都有两个存储端口 (。 由于每个适配器都位于同一子网和 VLAN 上，SMB 多通道将跨所有可用链接传播连接。 因此，第一台服务器 (192.168.1.1) 上的左侧端口将连接到第二个服务器上 (192.168.1.2) 的左侧端口。 第一台服务器 (192.168.1.12) 上的右侧端口将连接到第二台服务器上的右端端口。 为第三个和第四个服务器建立类似的连接。

但是，这会创建不必要的连接，并会导致互连 (多底盘链路聚合组或 MC 延迟) ，这种情况下，会连接机架 (ToR) 交换机的顶部， (标记了 Xs) 。 参阅下图：

:::image type="content" source="media/plan-networking/four-node-cluster-1.png" alt-text="四节点群集同一子网" lightbox="media/plan-networking/four-node-cluster-1.png":::

推荐的方法是为每组适配器使用单独的子网和 Vlan。 在下图中，右侧端口现在使用子网 192.168.2/24 和 VLAN2。 这允许左侧端口上的流量保留在 TOR1 上，右侧端口上的流量保留在 TOR2 上。 参阅下图：

:::image type="content" source="media/plan-networking/four-node-cluster-2.png" alt-text="四节点群集不同的子网" lightbox="media/plan-networking/four-node-cluster-2.png":::

## <a name="traffic-bandwidth-allocation"></a>流量带宽分配

下表显示了在 Azure Stack HCI 中使用常见适配器速度的各种流量类型的带宽分配示例。 请注意，这是聚合解决方案的一个示例，其中，所有流量类型 (计算、存储和管理) 在相同的物理适配器上运行，并使用 SET 进行分组。

由于此用例会产生最多的约束，因此它代表一个很好的基线。 但是，考虑适配器数量和速度的排列，这应视为一个示例，而不是支持要求。

在此示例中进行了以下假设：

- 每个团队有两个适配器
- 存储总线层 (SBL) 、群集共享卷 (CSV) 和 Hyper-v (实时迁移) 流量：

    - 使用相同的物理适配器
    - 使用 SMB
- 使用数据中心桥接为 SMB 提供50% 的带宽分配
    - SBL/CSV 是最高优先级的流量，接收到70% 的 SMB 带宽保留，并：
    - 实时迁移 (LM) 使用 cmdlet 进行限制 `Set-SMBBandwidthLimit` ，并收到剩余带宽的29%
        - 如果实时迁移的可用带宽 >= 5 Gbps 并且网络适配器可以使用，则使用 RDMA。 使用以下 cmdlet 来执行此操作：

            ```Powershell
            Set-VMHost VirtualMachineMigrationPerformanceOption SMB
            ```

        - 如果实时迁移的可用带宽 < 5 Gbps，请使用压缩来减少中断时间。 使用以下 cmdlet 来执行此操作：

            ```Powershell
            Set-VMHost -VirtualMachineMigrationPerformanceOption Compression
            ```

 - 如果将 RDMA 与实时迁移一起使用，请确保实时迁移流量不会使用 SMB 带宽限制来消耗分配给 RDMA 通信类的整个带宽。 请注意，此 cmdlet 以字节为单位每秒输入 (Bps) ，而网络适配器以每秒位数 (Bps) 列出。 使用以下 cmdlet 设置 6 Gbps 的带宽限制，例如：

    ```Powershell
    Set-SMBBandwidthLimit -Category LiveMigration -BytesPerSecond 750MB
    ```

    > [!NOTE]
    >在此示例中，750 MBps 等于 6 Gbps

下面是示例带宽分配表：

|NIC 速度|组合带宽|SMB 带宽保留 * *|SBL/CSV %|SBL/CSV 带宽|实时迁移%|最大实时迁移带宽|跳|检测信号带宽|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10 Gbps|20 Gbps|10 Gbps|70%|7 Gbps|*|*|2%|200 Mbps|
|25 Gbps|50 Gbps|25 Gbps|70%|17.5 Gbps|29%|7.25 Gbps|1%|250 Mbps|
|40 Gbps|80 Gbps|40 Gbps|70%|28 Gbps|29%|11.6 Gbps|1%|400 Mbps|
|50 Gbps|100 Gbps|50 Gbps|70%|35 Gbps|29%|14.5 Gbps|1%|500 Mbps|
|100 Gbps|200 Gbps|100 Gbps|70%|70 Gbps|29%|29 Gbps|1%|1 Gbps|
|200 Gbps|400 Gbps|200 Gbps|70%|140 Gbps|29%|58 Gbps|1%|2 Gbps|

*-应使用压缩而不是 RDMA，因为实时迁移流量的带宽分配是 <5 Gbps

* *-50% 是此示例的示例带宽预留

## <a name="stretched-cluster-considerations"></a>延伸群集注意事项

延伸群集提供跨多个数据中心的灾难恢复。 最简单的形式是拉伸 Azure Stack HCI 群集网络如下所示：

:::image type="content" source="media/plan-networking/stretched-cluster.png" alt-text="延伸群集" lightbox="media/plan-networking/stretched-cluster.png":::

延伸群集具有以下要求和特征：

- RDMA 限制为单一站点，在不同的站点或子网中不受支持。
- 同一站点中的服务器必须位于同一机架和第2层边界内。
- 站点间的通信跨越第3层边界;不支持延伸的第2层拓扑。

- 如果站点为其存储适配器使用了 RDMA，则这些适配器必须位于单独的子网和 VLAN 上，才能在站点之间路由。 这可以防止存储副本跨站点使用 RDMA。
- 用于站点间通信的适配器：

    - 可以是物理或 virtual (主机 vNIC) 。 如果是虚拟的，则必须在其自己的子网中预配一个 vNIC，并为每个物理 NIC 设置 VLAN
    - 必须在其自己的子网和 VLAN 上，才能在站点之间路由。
    - 必须使用 cmdlet 禁用 RDMA `Disable-NetAdapterRDMA` 。 建议你显式要求存储副本使用 cmdlet 的特定接口 `Set-SRNetworkConstraint` 。
    - 必须满足存储副本的任何其他要求。
-   如果故障转移到另一个站点，你必须确保有足够的带宽可用于在另一个站点上运行工作负荷。 安全的选项是在其可用容量的50% 的位置预配站点。 如果在故障转移过程中能够容忍性能降低，这并不是一种硬性要求。

## <a name="next-steps"></a>后续步骤

- 了解网络交换机和物理网络需求。 请参阅 [物理网络要求](physical-network-requirements.md)。
- 温习故障转移群集基础知识。 请参阅[故障转移群集网络基础知识](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- 温习如何使用 SET。 请参阅[远程直接内存访问 (RDMA) 和交换机嵌入式组合 (SET)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming)
- 有关部署，请参阅[使用 Windows Admin Center 创建群集](../deploy/create-cluster.md)
- 有关部署，请参阅[使用 Windows PowerShell 创建群集](../deploy/create-cluster-powershell.md)