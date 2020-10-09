---
title: 'Azure Stack HCI 中 SDN 的软件负载均衡器 (SLB) '
description: 使用本主题来了解 Azure Stack HCI 中软件定义的网络的软件负载均衡器。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: 79588b25f76540aed1499123f7d39e4d57cf9ecd
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858875"
---
# <a name="what-is-software-load-balancer-slb-for-sdn"></a>什么是 \( 用于 SDN 的软件负载均衡器 SLB \) ？

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

云服务提供商 (Csp) 和企业部署 [软件定义的网络 (SDN) 在 AZURE STACK HCI 中](software-defined-networking.md) ，可以使用软件负载平衡器 (SLB) 在虚拟网络资源之间均匀分配租户和租户客户网络流量。 SLB 允许多台服务器承载相同的工作负载，具有较高的可用性和可扩展性。

软件负载均衡器包括以下功能：

- 第4层 (L4) 用于北/南和东/西 TCP/UDP 流量的负载均衡服务。

- 公共网络和内部网络流量负载平衡。

- 动态 IP 地址 (对虚拟局域网 (Vlan) 以及使用 Hyper-v 网络虚拟化创建的虚拟网络上的) 支持。

- 运行状况探测支持。

- 适用于云规模，包括 multiplexers (和主机代理的向外扩展功能和扩展功能。

- 与 SDN 技术（例如 RAS 网关、数据中心防火墙和路由反射器）无缝集成的多租户统一边缘。

有关详细信息，请参阅本主题中的 [软件负载均衡器功能](#bkmk_features) 。

> [!NOTE]
> 网络控制器不支持 Vlan 的多租户。 但是，可以将 Vlan 与 SLB 用于服务提供商管理的工作负荷，例如数据中心基础结构和高密度 Web 服务器。

使用软件负载平衡器，你可以使用 SLB 虚拟机 (Vm) 在用于其他 VM 工作负载的 Hyper-v 计算服务器上扩展负载平衡功能。 因此，软件负载平衡支持对 CSP 操作所需的负载均衡终结点进行快速创建和删除。 此外，软件负载平衡支持每个群集有数十千兆字节，提供简单的预配模式，并且易于扩展和扩展。

## <a name="how-software-load-balancer-works"></a>软件负载均衡器的工作原理

软件负载均衡器的工作原理是将 (Vip) 的虚拟 IP 地址映射到数据中心内的云服务资源集的 Dip。

Vip 是单个 IP 地址，提供对负载平衡 Vm 池的公共访问。 例如，Vip 是在 internet 上公开的 IP 地址，以便租户和租户客户可以连接到云数据中心内的租户资源。

Dip 是 VIP 后面的负载均衡池的成员 Vm 的 IP 地址。 在云基础结构中将 Dip 分配给租户资源。

Vip 位于 SLB 多路复用器 (MUX) 。  MUX 由一个或多个 Vm 组成。  网络控制器为每个 MUX 提供每个 VIP，而每个 MUX 依次使用边界网关协议 (BGP) 将每个 VIP 作为/32 路由播发到物理网络上的路由器。  BGP 允许物理网络路由器：

- 了解到每个 MUX 上都提供了 VIP，即使 Mux 位于第3层网络中不同的子网中。

- 使用相等开销多路径 (ECMP) 路由将每个 VIP 的负载分散到所有可用的 Mux。

- 自动检测 MUX 故障或删除，并停止将流量发送到失败的 MUX。

- 跨正常 Mux 将负载从失败或删除的 MUX 分散。

当公共流量从 internet 接收时，SLB MUX 会检查流量，其中包含 VIP 作为目标，并映射和重写流量，使其到达单个 DIP。 对于入站网络流量，此事务在两个步骤中执行，该过程在 MUX Vm 与目标 DIP 所在的 Hyper-v 主机之间进行拆分：

1. 负载平衡-MUX 使用 VIP 来选择 DIP，封装数据包，并将流量转发到 DIP 所在的 Hyper-v 主机。

2. 网络地址转换 (NAT) -Hyper-v 主机从包中删除封装，将 VIP 转换为 DIP，重新映射端口，然后将数据包转发到 DIP VM。

MUX 知道如何将 Vip 映射到正确的 Dip，因为使用网络控制器定义的负载均衡策略。 这些规则包括协议、前端端口、后端端口和分发算法 (5、3或2元组) 。

当租户 Vm 响应并将出站网络流量发送回 internet 或远程租户位置时，由于由 Hyper-v 主机执行 NAT，流量将绕开 MUX，并直接从 Hyper-v 主机转到边缘路由器。 此 MUX 绕过进程称为直接服务器返回 (DSR) 。

在建立初始网络流量流后，入站网络流量会完全跳过 SLB MUX。

在下图中，客户端计算机对公司 SharePoint 站点的 IP 地址（在本例中为 Contoso 的虚构公司）执行 DNS 查询。 将发生以下过程：

1. DNS 服务器将 VIP 107.105.47.60 返回到客户端。

2. 客户端向 VIP 发送 HTTP 请求。

3. 物理网络有多个路径可用于连接到任何 MUX 上的 VIP。  在此过程中，每个路由器均使用 ECMP 选取路径的下一段，直到请求到达 MUX。

4. 用于接收请求的 MUX 检查配置的策略，并发现虚拟网络上有两个可用的 Dip，10.10.10.5 和10.10.20.5，用于处理 VIP 107.105.47.60 的请求

5. MUX 选择 DIP 10.10.10.5，并使用 VXLAN 封装数据包，以便它可以使用主机的物理网络地址将数据包发送到包含 DIP 的主机。

6. 主机接收封装的数据包并对其进行检查。 它删除了封装并重写数据包，以便目标现在为 DIP 10.10.10.5，而不是 VIP，然后将流量发送到 DIP VM。

7. 请求到达服务器场2中的 Contoso SharePoint 站点。 服务器生成响应，并使用其自己的 IP 地址作为源来将其发送到客户端。

8. 主机会截获虚拟交换机中的传出数据包，这会记住客户端（现在是目标）已将原始请求发送到 VIP。  主机将数据包的源重写为 VIP，使客户端看不到 DIP 地址。

9. 主机会将数据包直接转发到物理网络的默认网关，该网络使用其标准路由表将数据包转发到客户端，后者最终接收响应。

:::image type="content" source="media/software-load-balancing/slb-process.jpg" alt-text="软件负载平衡过程" border="false":::

### <a name="load-balancing-internal-datacenter-traffic"></a>对内部数据中心流量进行负载平衡

当对数据中心内部的网络流量进行负载平衡时（例如在不同服务器上运行的租户资源之间，并且是同一虚拟网络的成员）时，Vm 连接到的 Hyper-v 虚拟交换机会执行 NAT。

使用内部流量负载平衡时，会将第一个请求发送到 MUX，并处理该请求，这将选择相应的 DIP，然后将流量路由到 DIP。 然后，已建立的流量流会绕开 MUX，并直接从 VM 转到 VM。

### <a name="health-probes"></a>运行状况探测

软件负载均衡器包括运行状况探测，用于验证网络基础结构的运行状况，包括以下各项：

- TCP 探测到端口

- HTTP 探测到端口和 URL

不同于传统的负载平衡器设备，其中探测器在设备上发出并通过线路传输到 DIP，SLB 探测是在 DIP 所在的主机上发出的，并直接从 SLB 主机代理传递到 DIP，从而进一步在主机上分布工作。

## <a name="software-load-balancer-infrastructure"></a><a name="bkmk_infrastructure"></a>软件负载均衡器基础结构
必须先部署网络控制器和一个或多个 SLB MUX Vm，然后才能配置软件负载平衡器。

此外，必须将 Azure Stack HCI 主机与启用了 SDN 的 Hyper-v 虚拟交换机一起配置，并确保 SLB 主机代理正在运行。 服务主机的路由器必须支持 ECMP 路由，并边界网关协议 (BGP) ，并且必须将它们配置为接受来自 SLB Mux 的 BGP 对等互连请求。

下图概述了 SLB 基础结构。

:::image type="content" source="media/software-load-balancing/slb-overview.png" alt-text="软件负载平衡过程" border="false":::

以下各节提供了有关软件负载平衡器基础结构的这些元素的详细信息。

### <a name="network-controller"></a>网络控制器
网络控制器托管 SLB 管理器，并为软件负载平衡器执行以下操作：

- 处理从 Windows 管理中心、System Center、Windows PowerShell 或其他网络管理应用程序通过 Northbound API 传入的 SLB 命令。

- 计算分发到 Azure Stack HCI 主机和 SLB Mux 的策略。

- 提供软件负载平衡器基础结构的运行状况状态。

可以使用 Windows 管理中心或 Windows PowerShell 安装和配置网络控制器和其他 SLB 基础结构。

### <a name="slb-mux"></a>SLB MUX
SLB MUX 处理入站网络流量，并将 Vip 映射到 Dip，然后将流量转发到正确的 DIP。 每个 MUX 还使用 BGP 将 VIP 路由发布到边缘路由器。 当 MUX 发生故障时，BGP Keep Keep-alive 会通知 Mux，这允许活动的 Mux 在发生 MUX 故障时重新发布负载。 这实质上是为负载均衡器提供负载平衡。

### <a name="slb-host-agent"></a>SLB 主机代理
部署软件负载均衡器时，必须使用 Windows 管理中心、System Center、Windows PowerShell 或其他管理应用程序在每台主机服务器上部署 SLB 主机代理。

SLB 主机代理从网络控制器侦听 SLB 策略更新。 此外，主机代理会将 SLB 规则用于在本地计算机上配置的启用了 SDN 的 Hyper-v 虚拟交换机。

### <a name="sdn-enabled-hyper-v-virtual-switch"></a>启用了 SDN 的 Hyper-v 虚拟交换机
要使虚拟交换机与 SLB 兼容，必须在虚拟交换机上启用虚拟筛选平台 (VFP) 扩展。 此操作由 SDN 部署 PowerShell 脚本、Windows 管理中心部署向导和 System Center Virtual Machine Manager (SCVMM) 部署自动完成。

有关在虚拟交换机上启用 VFP 的信息，请参阅 Windows PowerShell 命令 [get-vmsystemswitchextension](/powershell/module/hyper-v/get-vmsystemswitchextension?view=win10-ps) 和 [VMSwitchExtension](/powershell/module/hyper-v/enable-vmswitchextension?f=255&MSPPError=-2147217396&view=win10-ps)。

启用了 SDN 的 Hyper-v 虚拟交换机为 SLB 执行以下操作：

- 处理 SLB 的数据路径。

- 从 MUX 接收入站网络流量。

- 绕过用于出站网络流量的 MUX，使用 DSR 将其发送到路由器。

### <a name="bgp-router"></a>BGP 路由器
BGP 路由器为软件负载平衡器执行以下操作：

- 使用 ECMP 将入站流量路由到 MUX。

- 对于出站网络流量，使用主机提供的路由。

- 侦听来自 SLB MUX 的 Vip 的路由更新。

- 如果保持活动失败，则从 SLB 轮换中删除 SLB Mux。

## <a name="software-load-balancer-features"></a><a name="bkmk_features"></a>软件负载均衡器功能
以下部分介绍软件负载平衡器的一些特性和功能。

### <a name="core-functionality"></a>核心功能

- SLB 为北/南和东/西 TCP/UDP 流量提供第4层负载均衡服务。

- 可以在基于 Hyper-v 网络虚拟化的网络上使用 SLB。

- 对于连接到启用了 SDN 的 Hyper-v 虚拟交换机的 DIP Vm，可以将 SLB 与基于 VLAN 的网络结合使用。

- 一个 SLB 实例可以处理多个租户。

- SLB 和 DIP 支持由 DSR 实现的可缩放且低延迟的返回路径。

- SLB 函数同时使用交换机嵌入组合 ( (SR-IOV) 设置) 或单个根输入/输出虚拟化。

- SLB 包含 Internet 协议版本 6 (IPv6) 和版本 4 (IPv4) 支持。

- 对于站点到站点网关方案，SLB 提供了 NAT 功能，以允许所有站点到站点连接使用单个公共 IP。

### <a name="scale-and-performance"></a>缩放和性能

- 适用于云规模，包括 Mux 和主机代理的向外扩展和扩展功能。

- 一个活动的 SLB 管理器网络控制器模块可以支持8个 MUX 实例。

### <a name="high-availability"></a>高可用性

- 可以将 SLB 部署到主动/主动配置中的两个以上的节点。

- 可以在 MUX 池中添加和删除 Mux，而不会影响 SLB 服务。 这会在修补单个 Mux 时维持 SLB 可用性。

- 各个 MUX 实例的运行时间为99%。

- 运行状况监视数据可用于管理实体。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [网络控制器概述](network-controller-overview.md)
- [使用 Windows PowerShell 部署网络控制器](../deploy/network-controller-powershell.md)
- [Azure Stack HCI 中的 SDN](software-defined-networking.md)
