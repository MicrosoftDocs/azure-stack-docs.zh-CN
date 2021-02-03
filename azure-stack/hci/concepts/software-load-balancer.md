---
title: 'Azure Stack HCI 和 Windows Server 中 SDN 的软件负载均衡器 (SLB) '
description: 使用本主题来了解 Azure Stack HCI 和 Windows Server 中软件定义的网络的软件负载均衡器。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/02/2021
ms.openlocfilehash: cabd243daa8ea427ca7a1684f1db9be4770c214f
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510765"
---
# <a name="what-is-software-load-balancer-slb-for-sdn"></a>什么是用于 SDN 的软件负载均衡器 \(SLB\)？

> 适用于： Azure Stack HCI，版本 20H2;Windows Server 2019;Windows Server 2016

云服务提供商 (Csp) 以及部署 [软件定义的网络 (SDN ](software-defined-networking.md) 的企业) 可以使用软件负载平衡器 (SLB) 在虚拟网络资源之间均匀分配租户和租户客户网络流量。 SLB 允许多台服务器承载相同的工作负载，具有较高的可用性和可扩展性。

软件负载均衡器提供以下功能：

- 适用于北/南和东/西 TCP/UDP 流量的第 4 层 (L4) 负载均衡服务。

- 公用网络和内部网络流量负载均衡。

- 虚拟局域网 (VLAN) 以及使用 Hyper-V 网络虚拟化创建的虚拟网络上的动态 IP 地址 (DIP) 支持。

- 运行状况探测支持。

- 为云缩放做好准备，包括多路复用器和主机代理的横向扩展功能和纵向扩展功能。

- 通过与 SDN 技术（例如 RAS 网关、数据中心防火墙和路由反射器）无缝集成，实现多租户统一边缘。

有关详细信息，请参阅本主题中的[软件负载均衡器功能](#bkmk_features)。

> [!NOTE]
> 网络控制器不支持 VLAN 的多租户。 但是，可以将 VLAN 与 SLB 一起用于服务提供商管理的工作负载，例如数据中心基础结构和高密度 Web 服务器。

使用软件负载均衡器，你可以在用于其他 VM 工作负载的同一个 Hyper-V 计算服务器上使用 SLB 虚拟机 (VM) 横向扩展负载均衡功能。 因此，软件负载均衡支持对 CSP 操作所需的负载均衡终结点进行快速创建和删除。 此外，软件负载均衡支持每个群集数十 GB 的容量，提供简单的预配模型，并且易于横向扩展和缩减。

## <a name="how-software-load-balancer-works"></a>软件负载均衡器的工作原理

软件负载均衡器的工作原理是将虚拟 IP 地址 (VIP) 映射到作为数据中心内云服务资源集一部分的 DIP。

VIP 是单个 IP 地址，提供对负载均衡的 VM 池的公共访问。 例如，VIP 是在 Internet 上公开的 IP 地址，以便租户和租户客户可以连接到云数据中心内的租户资源。

DIP 是 VIP 后面的负载均衡池的成员 VM 的 IP 地址。 在云基础结构中将 DIP 分配给租户资源。

VIP 位于 SLB 多路复用器 (MUX) 中。  MUX 由一个或多个 VM 组成。  网络控制器为每个 MUX 提供各自的 VIP，每个 MUX 反过来使用边界网关协议 (BGP) 将每个 VIP 作为/32 路由播发到物理网络上的路由器。  BGP 允许物理网络路由器执行以下操作：

- 了解即使 MUX 位于第 3 层网络中的不同子网中，每个 MUX 上都有一个 VIP。

- 使用相等成本多路径 (ECMP) 路由将每个 VIP 的负载分散到所有可用的 MUX 上。

- 自动检测 MUX 故障或删除，并停止将流量发送到故障的 MUX。

- 将故障或删除的 MUX 的负载分散到正常的 MUX 上。

当来自 Internet 的公共流量到达时，SLB MUX 会检查流量（其中包含 VIP 作为目标），并映射和重写流量，使其到达单个 DIP。 对于入站网络流量，此事务分两个步骤执行，该过程在 MUX VM 与目标 DIP 所在的 Hyper-V 主机之间进行划分：

1. 负载均衡 - MUX 使用 VIP 来选择 DIP，封装数据包，并将流量转发到 DIP 所在的 Hyper-V 主机。

2. 网络地址转换 (NAT) - Hyper-V 主机从数据包中删除封装，将 VIP 转换为 DIP，重新映射端口，然后将数据包转发到 DIP VM。

由于你使用网络控制器定义负载均衡策略，因此 MUX 知道如何将 VIP 映射到正确的 DIP。 这些规则包括协议、前端端口、后端端口和分发算法（5 个、3个 或 2 个元组）。

当租户 VM 响应出站网络流量并将其发送回 Internet 或远程租户位置时，由于 NAT 由 Hyper-V 主机执行，因此流量将绕过 MUX 并直接从 Hyper-V 主机传到边缘路由器。 此 MUX 绕过进程称为直接服务器返回 (DSR)。

建立初始网络流量流后，入站网络流量会完全绕过 SLB MUX。

在下图中，客户端计算机对公司（在本例中是名为 Contoso 的虚构公司）SharePoint 站点的 IP 地址执行 DNS 查询。 将发生以下过程：

1. DNS 服务器将 VIP 107.105.47.60 返回到客户端。

2. 客户端向 VIP 发送 HTTP 请求。

3. 物理网络有多个路径可用于访问位于任何 MUX 上的 VIP。  在此过程中，每个路由器都使用 ECMP 选择路径的下一段，直到请求到达 MUX。

4. 接收请求的 MUX 检查配置的策略，并发现虚拟网络上有两个 DIP（分别为 10.10.10.5 和 10.10.20.5）可用于处理对 VIP 107.105.47.60 的请求

5. MUX 选择 DIP 10.10.10.5 并使用 VXLAN 封装数据包，以便它可以使用主机的物理网络地址将数据包发送到包含 DIP 的主机。

6. 主机接收封装的数据包并对其进行检查。 它删除封装并重写数据包，将目标从 VIP 变为 DIP 10.10.10.5，然后将流量发送到 DIP VM。

7. 请求到达服务器场 2 中的 Contoso SharePoint 站点。 服务器使用自己的 IP 地址作为源，生成响应并将其发送到客户端。

8. 主机截获虚拟交换机中的传出数据包，该数据包会记住客户端（现在是目标）向 VIP 发送了原始请求。  主机将数据包的源重写为 VIP，使客户端看不到 DIP 地址。

9. 主机会将数据包直接转发到物理网络的默认网关，该网关使用其标准路由表将数据包转发到客户端，客户端最终接收响应。

:::image type="content" source="media/software-load-balancing/slb-process.jpg" alt-text="软件负载均衡过程" border="false":::

### <a name="load-balancing-internal-datacenter-traffic"></a>对内部数据中心流量进行负载均衡

当对数据中心内部的网络流量进行负载均衡时（例如在不同服务器上运行且是同一虚拟网络成员的租户资源之间），VM 连接到的 Hyper-V 虚拟交换机执行 NAT。

利用内部流量负载均衡，第一个请求将发送到 MUX 并由 MUX 进行处理，MUX 将选择适当的 DIP，然后将流量路由到 DIP。 此后，已建立的流量流将绕过 MUX，并直接从 VM 传到 VM。

### <a name="health-probes"></a>运行状况探测

软件负载均衡器包括运行状况探测，用于验证网络基础结构的运行状况，包括以下内容：

- 端口的 TCP 探测

- 端口和 URL 的 HTTP 探测

SLB 探测与传统负载均衡器设备的探测不同：传统设备中探测源自设备，并通过线路传输到 DIP；而 SLB 探测源自 DIP 所在的主机，并直接从 SLB 主机代理传递到 DIP，进一步在主机上分布工作。

## <a name="software-load-balancer-infrastructure"></a><a name="bkmk_infrastructure"></a>软件负载均衡器基础结构
在配置软件负载均衡器之前，必须首先部署网络控制器和一个或多个 SLB MUX VM。

此外，必须使用已启用 SDN 的 Hyper-V 虚拟交换机配置 Azure Stack HCI 主机，并确保 SLB 主机代理正在运行。 服务主机的路由器必须支持 ECMP 路由和边界网关协议 (BGP)，并且必须将其配置为接受来自 SLB MUX 的 BGP 对等互连请求。

下图提供了 SLB 基础结构的概览。

:::image type="content" source="media/software-load-balancing/slb-overview.png" alt-text="软件负载均衡器基础结构" border="false":::

以下各节介绍了有关软件负载均衡器基础结构的这些元素的详细信息。

### <a name="network-controller"></a>网络控制器
网络控制器托管 SLB 管理器，并为软件负载均衡器执行以下操作：

- 处理从 Windows Admin Center、System Center、Windows PowerShell 或其他网络管理应用程序通过 Northbound API 传入的 SLB 命令。

- 计算分发到 Azure Stack HCI 主机和 SLB MUX 的策略。

- 提供软件负载均衡器基础结构的运行状况。

可以使用 Windows Admin Center 或 Windows PowerShell 安装和配置网络控制器和其他 SLB 基础结构。

### <a name="slb-mux"></a>SLB MUX
SLB MUX 处理入站网络流量，并将 VIP 映射到 DIP，然后将流量转发到正确的 DIP。 每个 MUX 还使用 BGP 将 VIP 路由发布到边缘路由器。 当 MUX 发生故障时，BGP Keep Alive 会通知 MUX，这使活动的 MUX 在 MUX 发生故障时重新分发负载。 这实质上是为负载均衡器提供负载均衡。

### <a name="slb-host-agent"></a>SLB 主机代理
部署软件负载均衡器时，必须使用 Windows Admin Center、System Center、Windows PowerShell 或其他管理应用程序在每台主机服务器上部署 SLB 主机代理。

SLB 主机代理从网络控制器侦听 SLB 策略更新。 此外，主机代理将 SLB 的规则编程到本地计算机上配置的已启用 SDN 的 Hyper-V 虚拟交换机中。

### <a name="sdn-enabled-hyper-v-virtual-switch"></a>已启用 SDN 的 Hyper-V 虚拟交换机
要使虚拟交换机与 SLB 兼容，必须在虚拟交换机上启用虚拟筛选平台 (VFP) 扩展。 此操作由 SDN 部署 PowerShell 脚本、Windows Admin Center 部署向导和 System Center Virtual Machine Manager (SCVMM) 部署自动完成。

有关在虚拟交换机上启用 VFP 的信息，请参阅 Windows PowerShell 命令 [Get-VMSystemSwitchExtension](/powershell/module/hyper-v/get-vmsystemswitchextension?view=win10-ps) 和 [Enable-VMSwitchExtension](/powershell/module/hyper-v/enable-vmswitchextension?f=255&MSPPError=-2147217396&view=win10-ps)。

已启用 SDN 的 Hyper-V 虚拟交换机为 SLB 执行以下操作：

- 处理 SLB 的数据路径。

- 接收来自 MUX 的入站网络流量。

- 为出站网络流量绕过 MUX，使用 DSR 将其发送到路由器。

### <a name="bgp-router"></a>BGP 路由器
BGP 路由器为软件负载均衡器执行以下操作：

- 使用 ECMP 将入站流量路由到 MUX。

- 对于出站网络流量，使用主机提供的路由。

- 侦听 SLB MUX 的 VIP 的路由更新。

- 如果 Keep Alive 故障，从 SLB 轮换中删除 SLB MUX。

## <a name="software-load-balancer-features"></a><a name="bkmk_features"></a>软件负载均衡器功能
以下部分介绍软件负载均衡器的一些特性和功能。

### <a name="core-functionality"></a>核心功能

- SLB 提供适用于北/南和东/西 TCP/UDP 流量的第 4 层负载均衡服务。

- 可以在基于 Hyper-V 网络虚拟化的网络上使用 SLB。

- 可以将 SLB 与基于 VLAN 的网络一起用于连接到已启用 SDN 的 Hyper-V 虚拟交换机的 DIP VM。

- 一个 SLB 实例可以处理多个租户。

- SLB 和 DIP 支持由 DSR 实现的可缩放且低延迟的返回路径。

- SLB 在使用交换机嵌入式组合 (SET) 或单根输入/输出虚拟化 (SR-IOV) 时发挥作用。

- SLB 包含 Internet 协议版本 6 (IPv6) 和版本 4 (IPv4) 支持。

- 对于站点到站点网关方案，SLB 提供了 NAT 功能，以使所有站点到站点连接都可以使用单个公共 IP。

### <a name="scale-and-performance"></a>缩放和性能

- 为云缩放做好准备，包括 MUX 和主机代理的横向扩展和纵向扩展功能。

- 一个活动的 SLB 管理器网络控制器模块可以支持 8 个 MUX 实例。

### <a name="high-availability"></a>高可用性

- 在主动配置中，可以将 SLB 部署到两个以上的节点。

- 可以在 MUX 池中添加和删除 MUX，而不会影响 SLB 服务。 这会在修补单个 MUX 时维持 SLB 可用性。

- 单个 MUX 实例的运行时间为 99%。

- 运行状况监视数据可用于管理实体。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [网络控制器概述](network-controller-overview.md)
- [使用 Windows PowerShell 部署网络控制器](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts)
- [Azure Stack HCI 和 Windows Server 中的 SDN](software-defined-networking.md)