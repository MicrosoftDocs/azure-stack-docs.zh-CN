---
title: Azure Stack HCI 中的网络控制器概述
description: 使用本主题了解 Azure Stack HCI 中软件定义的网络的网络控制器。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: c207b436571d85d0902cd69a2168144e176919b1
ms.sourcegitcommit: 6a0f7f452998c404a80ca9d788dbf3cdf4d78b38
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893894"
---
# <a name="what-is-network-controller"></a>什么是网络控制器？

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

网络控制器是软件定义的网络 (SDN) 管理的基础。 它是一种高度可缩放的服务器角色，它提供了一种集中的可编程点，可用于管理、配置、监视和故障排除虚拟网络基础结构。

使用网络控制器，你可以自动执行网络基础结构的配置和管理，而不是执行网络设备和服务的手动配置。

## <a name="how-network-controller-works"></a>网络控制器的工作方式

网络控制器提供了一个 (API) 的应用程序编程接口，该接口允许网络控制器与 (Southbound API) 的网络设备、服务和组件通信并进行管理，另外还提供了另一个 API，该 API 允许管理应用程序将所需的网络设置和服务 (Northbound API) 。

借助 Southbound API，网络控制器可以管理网络设备和网络服务，并收集有关网络所需的所有信息。 网络控制器持续监视网络设备和服务的状态，并确保修正任何来自所需状态的配置偏移。

网络控制器 Northbound API 作为 REST 接口而实现。 它可以从管理应用程序管理数据中心网络。 若要进行管理，用户可以直接使用 REST API，或使用在 REST API 上构建的 Windows PowerShell 或使用图形用户界面（如 Windows 管理中心或 System Center Virtual Machine Manager）的管理应用程序。

## <a name="network-controller-features"></a>网络控制器功能

网络控制器允许管理 SDN 功能，如虚拟网络、防火墙、软件负载平衡器和 RAS 网关。 以下是其中的一些功能。

### <a name="virtual-network-management"></a>虚拟网络管理

此网络控制器功能可用于部署和配置 Hyper-v 网络虚拟化、在各个 Vm 上配置虚拟网络适配器以及存储和分发虚拟网络策略。 利用此功能，你可以创建虚拟网络和子网，将 (Vm 的虚拟机) 到这些网络，并在同一虚拟网络中的 Vm 之间实现通信。

网络控制器支持 (VLAN) 网络、网络虚拟化通用路由封装 (NVGRE) 和虚拟可扩展局域网 (VXLAN) 的虚拟局域网。

## <a name="firewall-management"></a>防火墙管理

利用此网络控制器功能，你可以为数据中心中的内部 (东/西部) 和外部 (北/南) 网络流量配置和管理工作负荷 Vm 的 "允许/拒绝" 防火墙访问控制规则。 在工作负荷 Vm 的 vSwitch 端口中查明防火墙规则，因此它们分散在数据中心内的工作负荷中，并与工作负荷一起移动。

使用 Northbound API，可以定义来自工作负荷 Vm 的传入和传出流量的防火墙规则。 还可以配置每条防火墙规则，以记录该规则允许或拒绝的流量。

## <a name="software-load-balancer-management"></a>软件负载平衡器管理

[软件负载均衡器](software-load-balancer.md) 允许多台服务器托管相同的工作负荷，从而提供高可用性和可伸缩性。 使用软件负载平衡器，可以配置和管理负载平衡、入站网络地址转换 (NAT) ，以及连接到传统 VLAN 网络和虚拟网络的工作负荷的 Internet 出站访问。

## <a name="gateway-management"></a>网关管理

[远程访问服务 (RAS) 网关](gateway-overview.md) ，你可以部署、配置和管理作为网关池成员的 vm，从而为客户工作负荷提供外部网络连接。 对于网关，在虚拟网络与远程网络之间支持以下连接类型：

- 站点到站点虚拟专用网络 (VPN) 使用 IPsec 的网关连接
- 使用通用路由封装的站点到站点 VPN 网关连接 (GRE) 
- 第3层转发功能
 
网关连接支持动态路由管理的 (BGP) 边界网关协议。

## <a name="virtual-appliance-chaining"></a>虚拟设备链接

此网络控制器功能允许你将虚拟网络设备连接到虚拟网络。 这些设备可用于高级防火墙、负载平衡、入侵检测和防护以及许多其他网络服务。 可以添加执行用户定义的路由和端口镜像功能的虚拟设备。 使用用户定义的路由时，虚拟设备将用作虚拟网络上虚拟子网之间的路由器。 使用端口镜像时，进入或离开监视的端口的所有网络流量都会复制并发送到虚拟设备进行分析。

若要详细了解用户定义的路由，请参阅 [在虚拟网络上使用网络虚拟设备](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)。

## <a name="network-controller-deployment-considerations"></a>网络控制器部署注意事项

- 不要将网络控制器服务器角色部署到物理主机上。 网络控制器应部署在其自己的专用 Vm 上。

- 你可以在域和非域环境中部署网络控制器。 在域环境中，网络控制器使用 Kerberos 对用户和网络设备进行身份验证;在非域环境中，你必须部署用于身份验证的证书。

- 网络控制器部署非常重要，它可提供高可用性，并使你能够轻松地扩展或缩减你的数据中心需求。 至少使用三个 Vm 为网络控制器应用程序提供高可用性。

- 为了实现高可用性和可伸缩性，网络控制器依赖于 Service Fabric。 Service Fabric 提供了一种分布式系统平台，用于构建可伸缩、可靠且易于管理的应用程序。 [详细了解作为 Service Fabric 应用程序的网络控制器](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability#network-controller-as-a-service-fabric-application)。


## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [规划部署网络控制器](network-controller.md)
- [使用 Windows PowerShell 部署网络控制器](../deploy/network-controller-powershell.md)
- [Azure Stack HCI 中的 SDN](software-defined-networking.md)