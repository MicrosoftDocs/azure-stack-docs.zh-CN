---
title: Azure Stack HCI 中的网络控制器概述
description: 使用本主题了解 Azure Stack HCI 中用于软件定义的网络的网络控制器。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: f8c559ff409cb6344f20ffa85a60983724ae5b17
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255038"
---
# <a name="what-is-network-controller"></a>什么是网络控制器？

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

网络控制器是软件定义的网络 (SDN) 管理的基础。 这是一种高度可缩放的服务器角色，提供了一种集中的可编程自动操作点，用于对虚拟网络基础结构进行管理、配置、监视和故障排除。

使用网络控制器，你可以自动执行网络基础结构的配置和管理，而不是执行网络设备和服务的手动配置。

## <a name="how-network-controller-works"></a>网络控制器的工作原理

网络控制器提供了一个应用程序编程接口 (API)，该接口允许网络控制器与网络设备、服务和组件进行通信并对其进行管理 (Southbound API)，还提供了另外一个 API，该 API 使管理应用程序能够将所需的网络设置和服务告知网络控制器 (Northbound API)。

借助 Southbound API，网络控制器可以管理网络设备和网络服务，并收集你需要的关于网络的所有信息。 网络控制器持续监视网络设备和服务的状态，并确保与所需状态偏离的配置得到修正。

网络控制器 Northbound API 作为 REST 接口而实现。 该控制器提供从管理应用程序管理数据中心网络的功能。 为进行管理，用户可以直接使用 REST API，或使用在 REST API 的基础上构建的 Windows PowerShell，或使用具有图形用户界面的管理应用程序，如 Windows Admin Center 或 System Center Virtual Machine Manager。

## <a name="network-controller-features"></a>网络控制器功能

通过网络控制器，你可以管理 SDN 功能，如虚拟网络、防火墙、软件负载均衡器和 RAS 网关。 以下是其众多功能中的一部分。

### <a name="virtual-network-management"></a>虚拟网络管理

通过此网络控制器功能，你可以部署和配置 Hyper-V 网络虚拟化、在各个 VM 上配置虚拟网络适配器以及存储和分发虚拟网络策略。 利用此功能，你可以创建虚拟网络和子网，将虚拟机 (VM) 附加到这些网络，并在同一虚拟网络中的 VM 之间启用通信。

网络控制器支持基于虚拟局域网 (VLAN) 的网络、网络虚拟化通用路由封装 (NVGRE) 和虚拟可扩展局域网 (VXLAN)。

## <a name="firewall-management"></a>防火墙管理

利用此网络控制器功能，针对数据中心内部（东/西）和外部（北/南）网络流量，你可以配置和管理工作负载 VM 的允许/拒绝防火墙访问控制规则。 防火墙规则用于工作负载 VM 的 vSwitch 端口，因此它们分布在数据中心内的工作负载中，并随工作负载一起移动。

使用 Northbound API，你可以为工作负载 VM 的传入和传出流量定义防火墙规则。 还可以配置每条防火墙规则，以记录该规则允许或拒绝的流量。

## <a name="software-load-balancer-management"></a>软件负载均衡器管理

利用[软件负载均衡器](software-load-balancer.md)，你可以使多台服务器托管同一工作负载，从而提供高可用性和可伸缩性。 使用软件负载均衡器，你可以配置和管理负载均衡、入站网络地址转换 (NAT) 以及对连接到传统 VLAN 网络和虚拟网络的工作负载的 Internet 出站访问。

## <a name="gateway-management"></a>网关管理

利用[远程访问服务 (RAS) 网关](gateway-overview.md)，你可以部署、配置和管理属于网关池成员的 VM，从而提供指向客户工作负载的外部网络连接。 对于网关，在虚拟网络与远程网络之间支持以下连接类型：

- 使用 IPsec 的站点到站点虚拟专用网络 (VPN) 网关连接
- 使用通用路由封装 (GRE) 的站点到站点 VPN 网关连接
- 第 3 层转发功能
 
网关连接支持在动态路由管理中使用边界网关协议 (BGP)。

## <a name="virtual-appliance-chaining"></a>虚拟设备链接

利用此网络控制器功能，你可以将虚拟网络设备附加到虚拟网络。 这些设备可用于高级防火墙、负载均衡、入侵检测和防护以及许多其他网络服务。 可以添加执行用户定义的路由和端口镜像功能的虚拟设备。 使用用户定义的路由时，虚拟设备将用作虚拟网络上虚拟子网之间的路由器。 使用端口镜像时，进入或离开受监视端口的所有网络流量都会被复制并发送到虚拟设备进行分析。

若要了解有关用户定义的路由的详细信息，请参阅[在虚拟网络上使用网络虚拟设备](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)。

## <a name="network-controller-deployment-considerations"></a>网络控制器部署注意事项

- 不要将网络控制器服务器角色部署在物理主机上。 网络控制器应部署在其自己的专用 VM 上。

- 你可以在域环境和非域环境中部署网络控制器。 在域环境中，网络控制器使用 Kerberos 对用户和网络设备进行身份验证；在非域环境中，必须部署用于身份验证的证书。

- 网络控制器部署非常重要，它可提供高可用性，并使你能够轻松地扩展或缩减你的数据中心需求。 至少使用三个 VM，以便为网络控制器应用程序提供高可用性。

- 为了实现高可用性和可伸缩性，网络控制器依赖于 Service Fabric。 Service Fabric 提供一种分布式系统平台，用于生成可缩放、可靠且易于管理的应用程序。 [了解有关网络控制器作为 Service Fabric 应用程序的详细信息](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability#network-controller-as-a-service-fabric-application)。


## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [规划部署网络控制器](network-controller.md)
- [使用 Windows PowerShell 部署网络控制器](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts)
- [Azure Stack HCI 中的 SDN](software-defined-networking.md)