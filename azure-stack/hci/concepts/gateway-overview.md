---
title: Azure Stack HCI 和 Windows Server 中的 RAS 网关概述
description: 使用本主题来了解 Azure Stack HCI 和 Windows Server 中用于软件定义的网络的 RAS 网关。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/02/2021
ms.openlocfilehash: d0658ab5d000f42a2cc9309a1a298eb62048b980
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510850"
---
# <a name="what-is-ras-gateway-for-software-defined-networking"></a>用于软件定义的网络的 RAS 网关是什么？

> 适用于 Azure Stack HCI，版本 20H2;Windows Server 2019;Windows Server 2016

远程访问服务 (RAS) 网关是一种基于软件的支持边界网关协议 (BGP) 的路由器，专为使用 Hyper-V 网络虚拟化 (HNV) 托管多租户虚拟网络的云服务提供商 (CSP) 和企业而设计。 可使用 RAS 网关在虚拟网络与另一个网络（本地或远程网络）之间路由网络流量。

RAS 网关需要[网络控制器](network-controller-overview.md)，它执行网关池的部署，在每个网关上配置租户连接，并在出现网关故障时将网络流量流切换到备用网关。

  > [!NOTE]
  > 多租户是云基础结构的一项功能，可支持多个租户的虚拟机 (VM) 工作负载，同时将它们彼此隔离，而所有工作负载都在同一基础结构上运行。 单个租户的多个工作负载可以互连和接受远程管理，但这些系统不与其他租户的工作负载互连，其他租户也不能远程管理它们。

## <a name="ras-gateway-features"></a>RAS 网关功能

RAS 网关提供了许多针对虚拟专用网 (VPN)、隧道、转发和动态路由的功能。

### <a name="site-to-site-ipsec-vpn"></a>站点到站点 IPsec VPN

利用这项 RAS 网关功能，你可以使用站点到站点 (S2S) 虚拟专用网 (VPN) 连接，通过 Internet 连接不同物理位置的两个网络。 这是一种使用 IKEv2 VPN 协议的加密连接。

对于在其数据中心托管许多租户的 CSP，RAS 网关提供多租户网关解决方案，使租户能够通过来自远程站点的站点到站点 VPN 连接来访问和管理其资源。 RAS 网关允许数据中心的虚拟资源及其物理网络之间的网络流量流。

### <a name="site-to-site-gre-tunnels"></a>站点到站点 GRE 隧道

基于通用路由封装 (GRE) 的隧道可实现租户虚拟网络与外部网络之间的连接。 由于 GRE 协议是轻量级协议，并且大多数网络设备都提供对 GRE 的支持，因此它是不需要加密数据的隧道的理想选择。

S2S 隧道中的 GRE 支持解决了使用多租户网关在租户虚拟网络和租户外部网络之间转发的问题。

### <a name="layer-3-forwarding"></a>第 3 层转发

第 3 层 (L3) 转发使数据中心中的物理基础结构和 Hyper-V 网络虚拟化云中虚拟化的基础结构之间能够连接。 使用 L3 转发连接，租户网络 VM 可通过 SDN 网关（已在软件定义的网络 (SDN) 环境中配置）连接到物理网络。 在本例中，SDN 网关充当虚拟网络和物理网络之间的路由器。

### <a name="dynamic-routing-with-bgp"></a>使用 BGP 的动态路由

BGP 可减少对在路由器上进行手动路由配置的需要，因为它是一种动态路由协议，可自动了解使用站点到站点 VPN 连接进行连接的站点之间的路由。 如果你的组织有多个使用启用了 BGP 的路由器（如 RAS 网关）连接的站点，则在网络中断或发生故障时，BGP 允许路由器自动计算并使用相互之间的有效路由。

RAS 网关附带的 BGP 路由反射器提供了路由器之间路由同步所需的 BGP 全网状拓扑的替代方法。 有关详细信息，请参阅[什么是路由反射器？](route-reflector-overview.md)

## <a name="how-ras-gateway-works"></a>RAS 网关的工作原理

RAS 网关在物理网络和 VM 网络资源（不考虑位置）之间路由网络流量。 你可以路由同一物理位置或多个不同位置的网络流量。

你可以在同时使用多项功能的高可用性池中部署 RAS 网关。 网关池包含多个 RAS 网关实例，以实现高可用性和故障转移。

可通过在池中添加或删除网关 VM 来轻松纵向扩展和缩减网关池。 删除或添加网关不会中断池提供的服务。 你还可以添加和删除整个网关池。 有关详细信息，请参阅 [RAS 网关高可用性](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-high-availability)。

每个网关池都提供 M+N 冗余。 这意味着，“M”个活动的网关 VM 由“N”个备用网关 VM 提供后备支持。 利用 M+N 冗余，可以更灵活地确定部署 RAS 网关时所需的可靠性级别。

可以将一个公共 IP 地址分配给所有池或分配给部分池。 这样做可大大减少必须使用的公共 IP 地址数，因为可以让所有租户在一个 IP 地址上连接到云。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [RAS 网关部署体系结构](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [网络控制器概述](network-controller-overview.md)
- [规划部署网络控制器](network-controller.md)
- [Azure Stack HCI 和 Windows Server 中的 SDN](software-defined-networking.md)