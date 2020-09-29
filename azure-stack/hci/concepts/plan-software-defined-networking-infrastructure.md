---
title: 规划软件定义的网络基础结构
description: 本主题提供有关如何规划软件定义的网络 (SDN) 基础结构部署的信息。
manager: grcusanz
ms.topic: conceptual
ms.assetid: ea7e53c8-11ec-410b-b287-897c7aaafb13
ms.author: anpaul
author: AnirbanPaul
ms.date: 09/11/2020
ms.openlocfilehash: 7d7eeaec5f82e08cf33a307f429389f03e712987
ms.sourcegitcommit: a845ae0d3794b5d845b2ae712baa7e38f3011a7b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2020
ms.locfileid: "90045518"
---
# <a name="plan-a-software-defined-network-infrastructure"></a>规划软件定义的网络基础结构

>适用于： Azure Stack HCI，版本 20H2;Windows server 2019，Windows Server (半年通道) ，Windows Server 2016

了解针对软件定义的网络 (SDN) 基础结构（包括硬件和软件先决条件）的部署规划。 本主题包括物理和逻辑网络配置、路由、网关、网络硬件等的规划要求。 它还包括有关扩展 SDN 基础结构和使用分阶段部署的注意事项。

## <a name="prerequisites"></a>先决条件
SDN 基础结构有几个硬件和软件先决条件，其中包括：
- **安全组和动态 DNS 注册**。 你必须为网络控制器部署准备你的数据中心，这需要 (Vm) 一组虚拟机。 部署网络控制器之前，必须先配置安全组和动态 DNS 注册。

    若要了解有关你的数据中心的网络控制器部署的详细信息，请参阅 [部署网络控制器的要求](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)。

- **物理网络**。 你需要访问物理网络设备，才能 (Vlan) 、路由和边界网关协议 (BGP) 配置虚拟局域网。 本主题提供有关手动交换机配置的说明，以及使用第3层交换机/路由器上的 BGP 对等互连的选项，或者 (RRAS) VM 的路由和远程访问服务器。

- **物理计算主机**。 这些主机运行 Hyper-v，并需要承载 SDN 基础结构和租户 Vm。 为了获得最佳性能，在这些主机中需要特定网络硬件，如 [网络硬件](#network-hardware) 部分所述。

## <a name="physical-and-logical-network-configuration"></a>物理和逻辑网络配置
每个物理计算主机都需要通过一个或多个连接到物理交换机端口的网络适配器建立网络连接。 第2层 [VLAN](https://en.wikipedia.org/wiki/Virtual_LAN) 支持划分为多个逻辑网段的网络。

>[!TIP]
>在访问模式或未标记的情况下，将 VLAN 0 用于逻辑网络。

>[!IMPORTANT]
>Windows Server 2016 软件定义的网络支持是和覆盖的 IPv4 寻址。 不支持 IPv6。 Windows Server 2019 支持 IPv4 和 IPv6 寻址。

### <a name="logical-networks"></a>Logical networks
本部分介绍管理逻辑网络和 Hyper-v 网络虚拟化的 SDN 基础结构规划要求 (HNV) 提供程序逻辑网络。 其中详细介绍了如何设置其他逻辑网络以使用网关和软件负载平衡器 (SLB) ，以及示例网络拓扑。

#### <a name="management-and-hnv-provider"></a>管理和 HNV 提供程序
所有物理计算主机都必须访问管理逻辑网络和 HNV 提供程序逻辑网络。 出于 IP 地址规划目的，每个物理计算主机都必须至少有一个从管理逻辑网络分配的 IP 地址。 网络控制器需要此网络的保留 IP 地址作为具象状态传输 (REST) IP 地址。

HNV 提供商网络充当东/West (内部内部) 租户流量、北/南部 (外部内部) 租户流量和与物理网络交换 BGP 对等互连信息的基础物理网络。

DHCP 服务器可以为管理网络自动分配 IP 地址，也可以手动分配静态 IP 地址。 SDN 堆栈自动为来自 IP 地址池的单个 Hyper-v 主机的 HNV 提供程序逻辑网络分配 IP 地址。 网络控制器指定并管理 IP 地址池。

>[!NOTE]
>仅在网络控制器主机代理收到特定租户 VM 的网络策略后，网络控制器才将 HNV 提供程序的 IP 地址分配给物理计算主机。

| 如果...                                                    | 则...                                               |
| :------------------------------------------------------- | :---------------------------------------------------- |
| 逻辑网络使用 Vlan，                          | 物理计算主机必须连接到有权访问 Vlan 的中继交换机端口。 请务必注意，计算机主机上的物理网络适配器不能激活任何 VLAN 筛选。|
| 你使用的是交换式嵌入组合 (设置) 并且具有多个网络接口卡 (NIC) 团队成员（例如网络适配器）| 必须将该特定主机的所有 NIC 组成员连接到相同的第2层广播域。|
| 物理计算主机正在运行其他基础结构 Vm，如网络控制器、SLB/多路复用器 (MUX) 或网关） | 确保管理逻辑网络为每个托管 VM 提供了足够的 IP 地址。 另外，请确保 HNV 提供程序逻辑网络有足够的 IP 地址分配给每个 SLB/MUX 和网关基础结构 VM。 尽管 IP 保留由网络控制器管理，但由于不可用而无法保留新的 IP 地址可能会导致网络上存在重复的 IP 地址。|

有关可用于在 Microsoft SDN 部署中虚拟化网络的 Hyper-v 网络虚拟化 (HNV) 的信息，请参阅 [Hyper-v 网络虚拟化](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization)。

#### <a name="gateways-and-the-software-load-balancer-slb"></a>网关和软件负载均衡器 (SLB) 
需要创建和预配更多的逻辑网络，以使用网关和 SLB。 请确保为这些网络获取正确的 IP 前缀、VLAN Id 和网关 IP 地址。

|                                |                     |
| :----------------------------- | :------------------ |
| **公共 VIP 逻辑网络** | 公共虚拟 IP (VIP) 逻辑网络必须使用可在云环境以外路由的 IP 子网前缀 (通常是 internet 可路由) 。 外部客户端使用这些地址来访问虚拟网络中的资源，包括站点到站点网关的前端 VIP。 不需要为此网络分配 VLAN。 |
| **专用 VIP 逻辑网络** | 不需要专用 VIP 逻辑网络即可在云外部进行路由。 这是因为仅可以从内部云客户端访问的 Vip 使用它，如专用服务。 不需要为此网络分配 VLAN。 |
| **GRE VIP 逻辑网络** | 泛型路由封装 (GRE) VIP 网络是一个仅存在于定义 Vip 的子网。 Vip 分配给在 SDN 构造上运行的网关 Vm，用于站点到站点 (S2S) GRE 连接类型。 不需要在物理交换机或路由器中预配置此网络，或为其分配 VLAN。 |

#### <a name="sample-network-topology"></a>示例网络拓扑
更改环境的示例 IP 子网前缀和 VLAN Id。

| 网络名称 | 子网 | Mask | Trunk 上的 VLAN ID | 网关 | 预订 (示例)  |
| :----------------------- | :------------ | :------- | :---------------------------- | :-------------- | :------------------------------------------- |
| 管理              | 10.184.108.0 |    24   |          7                   | 10.184.108.1   | 10.184.108.1-路由器<br> 10.184.108.4-网络控制器<br> 10.184.108.10-计算主机1<br> 10.184.108.11-计算主机2<br> 10.184.108-计算主机 X |
| HNV 提供程序             |  10.10.56.0  |    23    |          11                |  10.10.56.1    | 10.10.56.1-路由器<br> 10.10.56.2/MUX1<br> 10.10.56.5 - Gateway1 |
| 公共 VIP               |  41.40.40.0  |    27    |          NA                |  41.40.40.1    | 41.40.40.1-路由器<br> 41.40.40.3-IPSec S2S VPN VIP |
| 专用 VIP              |  20.20.20.0  |    27    |          NA                |  20.20.20.1    | 20.20.20.1-默认 GW (路由器)  |
| GRE VIP                  |  31.30.30.0  |    24    |          NA                |  31.30.30.1    | 31.30.30.1-默认 GW |

## <a name="routing-infrastructure"></a>路由基础结构
\(用于 VIP 子网的下一跃点的路由信息 \) 由 SLB/MUX 和远程访问服务器 (RAS) 网关播发到使用内部 BGP 对等互连的物理网络。 不会为 VIP 逻辑网络分配 VLAN，也不会在第2层交换机中对其进行预配置， (例如) 机箱顶部交换机。

需要在你的 SDN 基础结构用于接收由 SLB/Mux 和 RAS 网关播发的 VIP 逻辑网络路由的路由器上创建一个 BGP 对等节点。 BGP 对等互连只需发生一种方式 (从 SLB/MUX 或 RAS 网关到外部 BGP 对等) 。 在路由的第一层上，你可以使用静态路由或其他动态路由协议，例如开放式最短路径优先 (OSPF) 。 然而，如前文所述，VIP 逻辑网络的 IP 子网前缀确实需要从物理网络路由到外部 BGP 对等节点。

BGP 对等互连通常在作为网络基础结构一部分的托管交换机或路由器中进行配置。 也可以在 "仅路由" 模式下安装有 RAS 角色的 Windows Server 上配置 BGP 对等机。 必须将网络基础结构中的 BGP 路由器对等节点配置为使用其自己的自治系统编号 (ASN) ，并允许从分配给 SDN 组件 \( SLB/MUX 和 RAS 网关的 ASN 进行对等互连 \) 。

您必须从您的物理路由器或该路由器控制的网络管理员处获取以下信息：
- 路由器 ASN
- 路由器 IP 地址

>[!NOTE]
>SLB/MUX 不支持4字节的 Asn。 必须向 SLB/MUX 及其连接到的路由器分配两个字节的 Asn。 你可以在你的环境中的其他地方使用4字节的 Asn。

你或网络管理员必须配置 BGP 路由器对等方，以接受来自你的 RAS 网关和 SLB Mux 使用的 HNV 提供程序逻辑网络的 ASN 和 IP 地址或子网地址的连接。

有关详细信息，请参阅 [ (BGP) 边界网关协议 ](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp)。

## <a name="default-gateways"></a>默认网关
配置为连接到多个网络（例如物理主机、SLB/MUX 和网关 Vm）的计算机必须配置有一个默认网关。 使用以下主机和基础结构 Vm 的默认网关：
- 对于 Hyper-v 主机，请使用管理网络作为默认网关。
- 对于网络控制器 Vm，请使用管理网络作为默认网关。
- 对于 SLB/MUX Vm，请使用管理网络作为默认网关。
- 对于网关 Vm，请使用 HNV 提供程序网络作为默认网关。 应在网关 Vm 的前端 NIC 上设置此项。

## <a name="network-hardware"></a>网络硬件
本部分提供 Nic 和物理交换机的网络硬件部署要求。

### <a name="network-interface-cards-nics"></a>网络接口卡 (NIC)
在 Hyper-v 主机和存储主机中使用的 Nic 需要特定功能才能获得最佳性能。

 (RDMA) 的远程直接内存访问是一种内核旁路技术，它可以在不使用主机 CPU 的情况下传输大量数据，从而释放 CPU 以执行其他工作。 交换机嵌入组合 (集) 是一种备用 NIC 组合解决方案，可在包括 Hyper-v 和 SDN 堆栈的环境中使用。 将一些 NIC 组合功能集成到 Hyper-v 虚拟交换机。

有关详细信息，请参阅 [ (RDMA 的远程直接内存访问) 和交换机嵌入组合 (设置) ](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming)。

若要考虑由 VXLAN 或 NVGRE 封装标头导致的租户虚拟网络流量中的开销， (交换机和主机的第2层 fabric 网络的最大传输单元 (MTU) ，) 必须设置为大于或等于1674字节， \( 包括第2层以太网标头 \) 。

支持新的 *EncapOverhead* advanced adapter 关键字的 Nic 通过网络控制器主机代理自动设置 MTU。 不支持 new *EncapOverhead* 关键字的 nic 需要使用 *JumboPacket* \( 或等效关键字在每个物理主机上手动设置 MTU 大小 \) 。

### <a name="switches"></a>交换机
为环境选择物理交换机和路由器时，请确保它支持以下功能集：
- 需要交换机间 MTU 设置 \(\)
- MTU 设置为 >= 1674 字节， \( 包括 L2 以太网标头\)
- 需要 L3 协议 \(\)
- ) 路由 (ECMP 的相等成本多路径
- \(基于 BGP IETF RFC 4271 的 \) \- ECMP

实现应支持以下 IETF 标准中的必需语句：
- RFC 2545： [用于 IPv6 域间路由的 BGP-4 多协议扩展](https://tools.ietf.org/html/rfc2545)
- RFC 4760： [BGP 的多协议扩展-4](https://tools.ietf.org/html/rfc4760)
- RFC 4893： [BGP 支持四个八进制数作为数字空间](https://tools.ietf.org/html/rfc4893)
- RFC 4456： [BGP 路由反射：完全网格内部 BGP (的替代方法) ](https://tools.ietf.org/html/rfc4456)
- RFC 4724： [BGP 的正常重新启动机制](https://tools.ietf.org/html/rfc4724)

需要以下标记协议：
- 各种类型的通信的 VLAN 隔离
- 802.1 q 干线

以下各项提供了链接控件：
- \( \) \( 仅当使用 RoCE 时，才需要服务质量 QoS PFC\)
- 增强了流量选择 \( 802.1 qaz\)
- 基于优先级的流控制 (PFC) \( 802.1 p/Q 和 802.1 q b b\)

以下各项提供了可用性和冗余性：
- 需要交换机可用性 () 
- 高可用性路由器需要执行网关功能。 可以通过使用多机箱 switch\router 或技术（例如虚拟路由器冗余协议） (VRRP) 来提供此项。

### <a name="switch-configuration-examples"></a>交换机配置示例
为帮助配置物理交换机或路由器， [MICROSOFT SDN GitHub 存储库](https://github.com/microsoft/SDN/tree/master/SwitchConfigExamples)中提供了适用于各种交换机型号和供应商的一组示例配置文件。 提供了详细的自述文件和经过测试的命令行界面 (CLI) 为特定交换机提供命令。

## <a name="compute"></a>计算
所有 Hyper-v 主机都必须安装适当的操作系统，并为 Hyper-v 启用该操作系统，并使用至少一个连接到管理逻辑网络的物理适配器的外部 Hyper-v 虚拟交换机。 主机必须可通过分配给管理主机 vNIC 的管理 IP 地址来访问。

你可以使用与 Hyper-v、共享或本地兼容的任何存储类型。

> [!TIP]
> 可以方便地对所有虚拟交换机使用相同的名称，但这并不是必需的。 如果计划使用脚本进行部署，请参阅与 `vSwitchName` config.psd1 文件中的变量关联的注释。

### <a name="host-compute-requirements"></a>主机计算要求
下面显示了示例部署中使用的四个物理主机的最低硬件和软件要求。

主机|硬件要求|软件要求|
--------|-------------------------|-------------------------
|物理 Hyper-v 主机|四核 2.66 GHz CPU<br> 32 GB RAM<br> 300 GB 磁盘空间<br> 1 Gb/s (或更快) 物理网络适配器|操作系统：按照中的定义<br> 本主题开头的 "适用于"。<br> Hyper-v 角色已安装|

### <a name="sdn-infrastructure-vm-role-requirements"></a>SDN 基础结构 VM 角色要求
下面显示了 VM 角色的要求。

角色|vCPU 要求|内存需求|磁盘要求|
--------|-----------------------------|-----------------------|--------------------------
|网络控制器 (三个节点) |4个 vcpu|最少 4 GB<br> 建议 (8 GB) |75 GB （适用于操作系统驱动器）
|SLB/MUX (三个节点) |8 个 vCPU|建议 8 GB|75 GB （适用于操作系统驱动器）
|RDS 网关<br>  (三个节点的单个池<br> 网关，两个活动，一个被动) |8 个 vCPU|建议 8 GB|75 GB （适用于操作系统驱动器）
|RAS 网关 BGP 路由器<br> 用于 SLB/MUX 对等互连<br>  (或者使用 ToR 开关<br> 作为 BGP 路由器) |2个 vcpu|2 GB|75 GB （适用于操作系统驱动器）|

如果使用 System Center Virtual Machine Manager (VMM) 进行部署，则 VMM 和其他非 SDN 基础结构需要额外的基础结构 VM 资源。 若要了解详细信息，请参阅 [System Center Virtual Machine Manager 的系统要求](https://docs.microsoft.com/system-center/vmm/system-requirements?view=sc-vmm-2019&preserve-view=true)。

## <a name="extending-your-infrastructure"></a>扩展基础结构
你的基础结构的大小和资源要求取决于你计划承载的租户工作负载 Vm。 基础结构 Vm 的 CPU、内存和磁盘要求 (例如：网络控制器、SLB、网关等) 在上表中定义。 可以添加更多的基础结构 Vm 以根据需要进行缩放。 但是，Hyper-v 主机上运行的任何租户都有自己的 CPU、内存和磁盘要求，你必须考虑这些要求。

当租户工作负荷 Vm 开始在物理 Hyper-v 主机上消耗过多资源时，可以通过添加更多的物理主机来扩展基础结构。 可以使用 Windows 管理中心、VMM 或 PowerShell 脚本通过网络控制器创建新的服务器资源。 使用哪种方法取决于最初部署基础结构的方式。 如果需要为 HNV 提供商网络添加其他 IP 地址，可以使用主机可以使用的相应 IP 池) 创建新的逻辑子网 (。

## <a name="phased-deployment"></a>分阶段部署
根据你的要求，你可能需要部署 SDN 基础结构的一个子集。 例如，如果你只想在你的数据中心托管客户工作负荷，并且不需要外部通信，则可以部署网络控制器，并跳过部署 SLB/MUX 和网关 Vm。 下面介绍了 SDN 基础结构的分阶段部署的网络功能基础结构要求。

Feature|部署要求|网络要求|
--------|-------------------------|-------------------------
|逻辑网络管理<br> 访问控制列表 (基于 VLAN 的网络)  (的 Acl) <br> 基于 VLAN 的网络的服务质量 (QoS)  () <br>|网络控制器|无|
|虚拟网络<br> 用户定义的路由<br> 虚拟网络) 的 Acl (<br> 加密的子网<br> 虚拟网络) 的 QoS (<br> 虚拟网络对等互连|网络控制器|HNV PA VLAN，子网，路由器|
|入站/出站 NAT<br> 负载平衡|网络控制器<br> SLB/MUX|HNV PA 网络上的 BGP<br> 专用和公共 VIP 子网|
|GRE 网关连接|网络控制器<br> 网关|HNV PA 网络上的 BGP<br> GRE VIP 子网|
|IPSec 网关连接|网络控制器<br> SLB/MUX<br> 网关|HNV PA 网络上的 BGP<br> 公共 VIP 子网|
|L3 网关连接|网络控制器<br> 网关|租户 VLAN，子网，路由器<br> 租户 VLAN 上的 BGP 可选。|

## <a name="next-steps"></a>后续步骤
如需相关信息，另请参阅：
- [部署网络控制器的要求](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)
- [Azure Stack HCI 中的 SDN](https://docs.microsoft.com/azure-stack/hci/concepts/software-defined-networking)