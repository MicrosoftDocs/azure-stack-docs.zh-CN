---
title: Azure Stack HCI 中的 RAS 网关概述
description: 使用本主题来了解 Azure Stack HCI 中软件定义的网络的 RAS 网关。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: a7f05f7e07960e83681a13c92f4653b0993668de
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858878"
---
# <a name="what-is-ras-gateway-for-software-defined-networking"></a>软件定义的网络的 RAS 网关是什么？

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

远程访问服务 (RAS) 网关是一种基于软件的边界网关协议 (可为云服务提供商) Csp (和使用 Hyper-v 网络虚拟化) HNV (托管多租户虚拟网络的企业。 可以使用 RAS 网关在虚拟网络与另一个网络之间路由网络流量，无论是本地还是远程。

RAS 网关需要 [网络控制器](network-controller-overview.md)，它执行网关池的部署，在每个网关上配置租户连接，并在网关出现故障时将网络流量切换到备用网关。

  > [!NOTE]
  > 多租户是云基础结构的功能，它支持虚拟机 (VM) 多个租户的工作负荷，但将它们彼此隔离，同时所有工作负荷都在同一基础结构上运行。 单个租户的多个工作负载可以互连和接受远程管理，但这些系统不与其他租户的工作负载互连，其他租户也不能远程管理它们。

## <a name="ras-gateway-features"></a>RAS 网关功能

RAS 网关为虚拟专用网络提供了许多功能 (VPN) 、隧道、转发和动态路由。

### <a name="site-to-site-ipsec-vpn"></a>站点到站点 IPsec VPN

利用这一 RAS 网关功能，你可以使用站点到站点 (S2S) 虚拟专用网络 (VPN) 连接，将两个网络通过 Internet 连接到不同的物理位置。 这是使用 IKEv2 VPN 协议进行加密的连接。

对于在其数据中心托管许多租户的 Csp，RAS 网关提供多租户网关解决方案，使租户能够通过远程站点的站点到站点 VPN 连接访问和管理其资源。 RAS 网关允许网络流量在你的数据中心中的虚拟资源及其物理网络之间流动。

### <a name="site-to-site-gre-tunnels"></a>站点到站点 GRE 隧道

通用路由封装 (基于 GRE) 的隧道可在租户虚拟网络与外部网络之间实现连接。 由于 GRE 协议是轻型协议，因此大多数网络设备上都提供对 GRE 的支持，因此，它是在不需要加密数据的情况下进行隧道的理想选择。

S2S 隧道中的 GRE 支持解决了在租户虚拟网络与使用多租户网关的租户外部网络之间转发的问题。

### <a name="layer-3-forwarding"></a>第 3 层转发

第 3 层 (L3) 转发使数据中心中的物理基础结构和 Hyper-V 网络虚拟化云中虚拟化的基础结构之间能够连接。 使用 L3 转发连接，租户网络 Vm 可以通过 SDN 网关连接到物理网络，该网关已在软件定义的网络 (SDN) 环境中进行配置。 在本例中，SDN 网关充当虚拟网络和物理网络之间的路由器。

### <a name="dynamic-routing-with-bgp"></a>带 BGP 的动态路由

BGP 可减少对在路由器上进行手动路由配置的需要，因为它是一种动态路由协议，可自动了解使用站点到站点 VPN 连接进行连接的站点之间的路由。 如果你的组织有多个使用启用了 BGP 的路由器（如 RAS 网关）连接的站点，则在发生网络中断或故障时，BGP 允许路由器自动计算并使用有效的路由。

RAS 网关附带的 BGP 路由反射器提供了一种替代 BGP 完整网格拓扑的方法，该拓扑是路由器之间路由同步所必需的。 有关详细信息，请参阅 [什么是路由反射器？](route-reflector-overview.md)

## <a name="how-ras-gateway-works"></a>RAS 网关的工作原理

RAS 网关用于在物理网络与 VM 网络资源之间路由网络流量，而不考虑位置。 您可以将网络流量路由到同一物理位置或多个不同的位置。

你可以同时在使用多个功能的高可用性池中部署 RAS 网关。 网关池包含 RAS 网关的多个实例以实现高可用性和故障转移。

可以通过在池中添加或删除网关 Vm 来轻松地向上或向下缩放网关池。 删除或添加网关不会中断池提供的服务。 你还可以添加和删除整个网关池。 有关详细信息，请参阅 [RAS 网关高可用性](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-high-availability)。

每个网关池提供 M + N 冗余。 这意味着，"N" 个备用网关 Vm 数由 "N" 个备用网关 vm。 通过 M + N 冗余，可以更灵活地确定部署 RAS 网关时所需的可靠性级别。

可以将单个公共 IP 地址分配给所有池，也可以分配给部分池。 这样做会大大减少必须使用的公共 IP 地址数，因为可以让所有租户在单个 IP 地址上连接到云。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [RAS 网关部署体系结构](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [网络控制器概述](network-controller-overview.md)
- [规划部署网络控制器](network-controller.md)
- [Azure Stack HCI 中的 SDN](software-defined-networking.md)