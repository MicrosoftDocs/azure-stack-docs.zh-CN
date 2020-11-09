---
title: 为 Azure Stack HCI 规划主机网络
description: 了解如何为 Azure Stack HCI 群集规划主机网络
author: v-dasis
ms.topic: how-to
ms.date: 11/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e9a03fa7518c6a450204cdbdb40483b593b1867b
ms.sourcegitcommit: ce864e1d86ad05a03fe896721dea8f0cce92085f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2020
ms.locfileid: "94383461"
---
# <a name="plan-host-networking-for-azure-stack-hci"></a>为 Azure Stack HCI 规划主机网络

> 适用于 Azure Stack HCI，版本20H2

本主题讨论非拉伸和拉伸 Azure Stack HCI 群集环境中的主机网络规划注意事项和要求。

## <a name="traffic-types-supported"></a>支持的流量类型

Azure Stack HCI 使用服务器消息块 (SMB) 。 Azure Stack HCI 上的 SMB 支持以下通信类型：

- 存储总线层 (SBL) -存储空间直通使用;最高优先级流量
- 群集共享卷 (CSV)
- 实时迁移 (LM) 
- 存储副本 (SR) -在延伸群集中使用
- 文件共享 (FS) -FS 传统和 Scale-Out 文件服务器 (SOFS) 
- 群集检测信号 (HB-ACCT-WC) 
- 群集通信（节点加入、群集更新、注册表更新）

SMB 流量可以流经以下协议：

- 传输控制协议 (TCP) -在站点之间使用
- 远程直接内存访问 (RDMA)

## <a name="traffic-bandwidth-allocation"></a>流量带宽分配

下表显示了各种流量类型的带宽分配，其中：

- 所有单位均为 Gbps
- 值适用于延伸和非延伸的群集
- SMB 流量获取总带宽分配的50%
- 存储总线层/群集共享卷 (SBL/CSV) 流量获取剩余50% 分配的70%
- 实时迁移 (LM) 流量获取剩余50% 分配的15%
- 存储副本 (SR) 流量获取剩余50% 分配的14%
- 检测信号 (HB-ACCT-WC) 流量获取剩余50% 分配的1%
- * = 应使用压缩而不是 RDMA，前提是 LM 流量的带宽分配为 <5 Gbps

|NIC 速度|组带宽|SMB 50% 保留|SBL/CSV%|SBL/CSV 带宽|LM|LM 带宽|高层 |SR 带宽|HB-ACCT-WC|HB-ACCT-WC 带宽|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10|20|10|70%|7|14% *|1.4 *|14%|1.4|2%|0.2|
|25|50|25|70%|17.5|15% *|3.75 *|14%|3.5|1%|0.25|
|40|80| 40|70%|28|15%|6|14%|5.6|1%|0.4|
|50|100|50|70%|35|15%|7.5|14%|7|1%|0.5|
|100|200|100|70%|70|15%|15|14%|14|1%|1|
|200|400|200|70%|140|15%|30|14%|28|1%|2|

## <a name="rdma-considerations"></a>RDMA 注意事项

 (RDMA) 的远程直接内存访问可以直接从一台计算机的内存访问，而无需涉及计算机的操作系统。 这将允许高吞吐量、低延迟的网络，同时最大限度地降低 CPU 使用率，这在群集中尤其有用。

所有主机 RDMA 流量都利用 SMB 直通。 SMB 直通是通过 RDMA 发送的 SMB 3.0 流量，在端口445上通过多路复用。 最少两个基于优先级的流控制 (PFC) 启用的流量类 (TCs) 必须用于 RDMA 流量，才能与市场上大多数当前和未来的物理交换机兼容。

Internet 广域 RDMA 协议 (iWARP) 通过 TCP 运行 RDMA，而通过聚合以太网的 RDMA (RoCE) 避免使用 TCP，但需要 Nic 和支持它的物理交换机。 有关 RDMA over RoCE 的聚合网络要求，请参阅 [Windows Server 2016 和 2019 RDMA 部署指南](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx)。

默认情况下，对于同一子网上的站点中的群集节点之间的所有东/西部流量启用 RDMA。 RDMA 处于禁用状态，在不同子网中的站点之间的北/南伸展群集流量不受支持。

下面是 Azure Stack HCI 的 RDMA 要求：

- 子网之间以及站点之间的所有流量 (延伸群集) 必须使用 WinSock TCP。 任何中间网络跃点都超出 Azure Stack HCI 的视图和控制。
- 不支持子网之间以及站点之间的 RDMA (延伸群集) 。 使用上行和多个网络设备是指多个故障点，在这种情况下，可能会变得不稳定和似乎。
- 对于延伸群集，存储副本流量不需要额外的虚拟 Nic。 但出于故障排除目的，将跨站点流量和跨子网流量与东-西 RDMA 流量分开可能会很有用。 如果 SMB 直通无法以本机方式禁用跨站点或跨子网，请执行以下操作：
    - 应为存储副本设置一个或多个其他 Vnic
    - 存储副本 Vnic 必须 [使用 PowerShell 禁用](https://docs.microsoft.com/powershell/module/netadapter/disable-netadapterrdma) 了 RDMA，因为它是按定义跨站点和跨子网
    - 为了满足上述站点/子网要求，本地 RDMA 适配器需要一个 vSwitch 和 Vnic 来支持存储副本
    - 站点内 RDMA 带宽要求需要了解每种流量类型的带宽百分比，如 " **流量带宽分配** " 部分所述。 这将确保可将适当的带宽预留和限制应用于 " (节点到节点) 流量
- 实时迁移和存储副本流量必须是 SMB 带宽有限的，否则它们可能会占用所有带宽，从而使高优先级的存储流量。 有关详细信息，请参阅 [SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) 和 [SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint) PowerShell cmdlet。

> [!NOTE]
> 使用 cmdlet 时，需要将位转换为字节 `Set-SmbBandwidthLimit` 。

## <a name="node-interconnect-requirements"></a>节点互连要求

本部分讨论了站点中服务器节点之间的特定网络要求，即互连。 可以使用和支持有交换机或无交换机节点互连：

- **有交换机：** 最常用的方案是，服务器节点通过那些使用网络交换机的以太网彼此连接。 必须正确配置交换机以处理带宽和网络类型。 如果使用的 RDMA 实现了 RoCE 协议，则网络设备和交换机配置很重要。
- **无交换机：** 服务器节点还可以使用直接以太网连接进行互连，不使用交换机。 在这种情况下，每个服务器节点必须与同一站点中的所有其他群集节点直接连接。

### <a name="interconnects-for-2-3-node-clusters"></a>具有 2-3 个节点的群集的互连

下面是具有两个或三个节点的单站点群集的最低互连要求。 这些要求适用于每个服务器节点：

- 一个或多个用于管理功能的 1 Gb 网络适配器卡
- 一个或多个 10 Gb（或更快）的网络接口卡，用于存储和工作负荷流量
- 建议在各个节点之间使用两个或更多个网络连接以确保冗余和性能

### <a name="interconnects-for-4-node-and-greater-clusters"></a>具有 4 个节点的群集和更大群集的互连

下面是具有四个或更多个节点的群集和高性能群集的最低互连要求。 这些要求适用于每个服务器节点：

- 一个或多个用于管理功能的 1 Gb 网络适配器卡。
- 一个或多个 25 Gb（或更快）的网络接口卡，用于存储和工作负荷流量。 建议使用两个或更多个网络连接以确保冗余和性能。
- 支持远程直接内存访问 (RDMA) 的网卡：iWARP（推荐）或 RoCE。

### <a name="site-to-site-requirements-stretched-cluster"></a>站点到站点要求（延伸群集）

在延伸群集的站点之间连接时，每个站点中的互连要求仍适用，此外还有其他必须考虑的存储副本和 Hyper-V 实时迁移流量要求：

- 在进行同步复制的站点之间至少有一个 1 Gb RDMA 或以太网/TCP 连接。 首选 25 Gb 连接。
- 对于同步复制，站点之间的网络需要有足够的带宽来包含 I/O 写入工作负荷，往返延迟平均为 5 毫秒或更低。 异步复制没有延迟建议。
- 如果在站点之间使用单个连接，请使用 PowerShell 设置存储副本的 SMB 带宽限制。 有关详细信息，请参阅 [Set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit)。
- 如果在站点之间使用多个连接，请在连接之间分隔流量。 例如，将存储副本流量与使用 PowerShell 的 Hyper-V 实时迁移流量分别置于不同的网络上。 有关详细信息，请参阅 [Set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint)。

## <a name="network-port-requirements"></a>网络端口要求

请确保站点内和站点间的所有服务器节点之间的相应网络端口处于打开状态（对于延伸群集）。 你需要适当的防火墙和路由器规则，以允许在群集中的所有服务器之间进行 ICMP、SMB（端口 445，以及适用于 SMB Direct 的端口 5445）和 WS-MAN（端口 5985）双向通信。

使用 Windows Admin Center 中的群集创建向导来创建群集时，向导会针对故障转移群集、Hyper-V 和存储副本自动打开群集中每台服务器上的相应防火墙端口。 如果在每个服务器上使用不同的防火墙，请打开以下端口：

### <a name="failover-clustering-ports"></a>故障转移群集端口

- ICMPv4 和 ICMPv6
- TCP 端口 445
- RPC 动态端口
- TCP 端口 135
- TCP 端口 137
- TCP 端口 3343
- UDP 端口 3343

### <a name="hyper-v-ports"></a>Hyper-V 端口

- TCP 端口 135
- TCP 端口 80（HTTP 连接）
- TCP 端口 443（HTTPS 连接）
- TCP 端口 6600
- TCP 端口 2179
- RPC 动态端口
- RPC 终结点映射程序
- TCP 端口 445

### <a name="storage-replica-ports-stretched-cluster"></a>存储副本端口（延伸群集）

- TCP 端口 445
- TCP 5445（如果使用 iWarp RDMA）
- TCP 端口 5985
- 如果使用 PowerShell cmdlet，则 ICMPv4 和 ICMPv6 (`Test-SRTopology`) 

可能需要其他端口，但上面未列出。 这些是用于基本的 Azure Stack HCI 功能的端口。

## <a name="network-switch-requirements"></a>网络交换机要求

本部分定义与 Azure Stack HCI 一起使用的物理交换机的要求。 这些要求列出了所有 Azure Stack HCI 部署所必需的行业规范、组织标准和协议。 除非另有说明，否则不需要为标准的最新活动 (非取代) 版本。

这些要求有助于确保 Azure Stack HCI 群集部署中的节点之间的可靠通信。 节点之间的可靠通信至关重要。 若要为 Azure Stack HCI 提供所需的可靠性级别，需要交换机：

- 遵守适用的行业规范、标准和协议
- 提供交换机支持的规范、标准和协议的可见性
- 提供有关启用了哪些功能的信息

如果交换机支持以下各项，请务必询问交换机供应商：

#### <a name="standard-ieee-8021q"></a>标准： IEEE 802.1 Q

以太网交换机必须符合定义 Vlan 的 IEEE 802.1 Q 规范。 Vlan 是 Azure Stack HCI 的几个方面所必需的，并且在所有方案中都是必需的。

#### <a name="standard-ieee-8021qbb"></a>标准： IEEE 802.1 Q b b

以太网交换机必须符合定义 (PFC) 优先级流控制的 IEEE 802.1 Q b b 规范。 使用数据中心桥接 (DCB) 时，需要 PFC。 由于 DCB 可以在 RoCE 和 iWARP RDMA 方案中使用，因此在所有方案中都需要 802.1 Q b b。 至少需要三类服务 (CoS) 优先级，而不会降级交换机功能或端口速度。

#### <a name="standard-ieee-8021qaz"></a>标准： IEEE 802.1 Qaz

以太网交换机必须符合定义增强传输的 IEEE 802.1 Qaz 规范 (ETS) 。 使用 DCB 时需要 ETS。 由于 DCB 可以在 RoCE 和 iWARP RDMA 方案中使用，因此在所有方案中都需要 802.1 Qaz。 至少需要三个 CoS 优先级，而不会降级交换机功能或端口速度。

#### <a name="standard-ieee-8021ab"></a>标准： IEEE 802.1 AB

以太网交换机必须符合定义 (LLDP) 的链接层发现协议的 IEEE 802.1 AB 规范。 Windows 需要 LLDP 才能发现交换机配置。 必须动态启用 LLDP 类型-长度-值 (TLVs) 的配置。 这些开关不能需要其他配置。

例如，启用802.1 子类型3应自动播发交换机端口上的所有可用 Vlan。

#### <a name="tlv-requirements"></a>TLV 要求

LLDP 允许组织定义并编码自己的自定义 TLVs。 这些称为组织特定的 TLVs。 所有组织特定的 TLVs 从 LLDP TLV 类型值127开始。 下表显示了需要 (TLV 类型 127) 子类型的特定于组织的自定义 TLV，它们是可选的：

|条件|组织|TLV 子类型|
|-|-|-|
|必需|IEEE 802。1|VLAN 名称 (子类型 = 3) |
|必需|IEEE 802。3|最大帧大小 (子类型 = 4) |
|可选|IEEE 802。1|端口 VLAN ID (子类型 = 1) |
|可选|IEEE 802。1|端口和协议 VLAN ID (子类型 = 2) |
|可选|IEEE 802。1|链接聚合 (子类型 = 7) |
|可选|IEEE 802。1|拥塞通知 (子类型 = 8) |
|可选|IEEE 802。1|ETS 配置 (子类型 = 9) |
|可选|IEEE 802。1|ETS 建议 (子类型 =) |
|可选|IEEE 802。1|PFC 配置 (子类型 = B) |
|可选|IEEE 802。1|EVB (子类型 = D) |
|可选|IEEE 802。3|链接聚合 (子类型 = 3) |

> [!NOTE]
> 将来可能需要列出一些可选功能。

## <a name="example-cluster-network-design"></a>群集网络设计示例

下图显示了在同一子网和相同站点上有两个群集的标准 (非拉伸) 群集配置。 服务器节点使用连接到双顶级 (TOR) 交换机的冗余网络适配器在同一群集中相互通信。 群集到群集的通信会经历双重网络书脊设备。

:::image type="content" source="media/plan-host-networking/rack-topology-non-stretched-cluster.png" alt-text="非延伸群集" lightbox="media/plan-host-networking/rack-topology-non-stretched-cluster.png":::

## <a name="example-stretched-cluster-network-design"></a>扩展群集网络设计示例

以下关系图显示了使用单个群集的扩展群集配置，其中的服务器节点位于不同的站点，子网 (每个站点) 四个节点。 服务器节点使用连接到双连接 TOR 交换机的冗余网络适配器在同一群集中相互通信。 使用存储副本进行故障转移时，站点到站点通信通过双重路由器进行。

:::image type="content" source="media/plan-host-networking/rack-topology-stretched-cluster.png" alt-text="延伸群集" lightbox="media/plan-host-networking/rack-topology-stretched-cluster.png":::

### <a name="stretched-cluster-node-networking-option-1"></a>延伸群集节点网络选项1

下图显示了使用交换机嵌入组合 (将) 设置为同一 vNIC 上站点之间的流管理、实时迁移和存储副本流量的扩展群集。 将 [SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) 和 [SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint) PowerShell cmdlet 分别用于带宽限制实时迁移和存储副本流量。 

请记住，当 RDMA 用于站点内实时迁移存储流量时，会将 TCP 用于站点间的流量。

:::image type="content" source="media/plan-host-networking/stretched-cluster-option-1.png" alt-text="延伸群集节点网络选项1" lightbox="media/plan-host-networking/stretched-cluster-option-1.png":::

### <a name="stretched-cluster-node-networking-option-2"></a>延伸群集节点网络选项2

下图显示了针对站点之间的存储副本流量使用 SMB 多 [通道](https://docs.microsoft.com/azure-stack/hci/manage/manage-smb-multichannel) 的扩展群集的更高级配置，以及用于群集管理流量的专用适配器的配置。 将 [SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) 和 [SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint) PowerShell cmdlet 分别用于带宽限制实时迁移和存储副本流量。

请记住，当 RDMA 用于站点内存储流量时，TCP 用于站点间的流量。

:::image type="content" source="media/plan-host-networking/stretched-cluster-option-2.png" alt-text="延伸群集节点网络选项2" lightbox="media/plan-host-networking/stretched-cluster-option-2.png":::


## <a name="next-steps"></a>后续步骤

- 在故障转移群集基础上进行画笔。 请参阅 [故障转移群集网络基础知识](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- 使用 SET 进行上画笔。 请参阅 [ (RDMA 的远程直接内存访问) 和交换机嵌入组合 (集) ](https://docs.microsoft.com/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming)
- 有关部署，请参阅 [使用 Windows 管理中心创建群集](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster)
- 有关部署，请参阅 [使用 Windows PowerShell 创建群集](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell)