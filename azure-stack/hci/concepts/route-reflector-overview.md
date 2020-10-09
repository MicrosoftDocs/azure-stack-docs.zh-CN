---
title: Azure Stack HCI 中的 BGP 路由反射器概述
description: 使用本主题来了解 Azure Stack HCI 中软件定义的网络的 BGP 路由反射器。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: ca130c60e8cf08484001f606c4d0f84d786ca16b
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858876"
---
# <a name="what-is-route-reflector"></a>什么是路由反射器？

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

边界网关协议 (BGP) 路由反射器都包含在 [远程访问服务 (RAS) 网关](gateway-overview.md) 中，并提供对路由器之间的路由同步所需的 bgp 完整网格拓扑的替代方法。 软件定义的网络部署中的路由反射器是位于 RAS 网关和 [网络控制器](network-controller-overview.md)之间的控制平面上的逻辑实体。 但它不参与数据平面路由。

## <a name="how-route-reflector-works"></a>路由反射器的工作原理

对于完全网格同步，所有 BGP 路由器必须与路由拓扑中的所有其他路由器连接。 然而，使用路由反射器时，路由反射器是唯一连接到其他所有路由器的路由器（称为 BGP 路由反射器客户端），因此可以简化路由同步并减少网络流量。 路由反射器学习所有路由，计算最佳路由，并将最佳路由重新分发到其 BGP 客户端。

可以配置单个租户的远程访问隧道，使其在多个 RAS 网关虚拟机 (VM) 上终止。 这为云服务提供商提供了更大的灵活性， (Csp) 在一个 RAS 网关 VM 无法满足租户连接的所有带宽要求的情况下。

然而，此功能引入了路由管理的额外复杂性，并有效地同步了租户远程站点及其虚拟资源在云数据中心。 为租户提供与多个 RAS 网关的连接还会在企业结束时增加配置的复杂性，其中每个租户网站将有单独的路由邻居。

控制平面中的 BGP 路由反射器可解决这些问题，并使 CSP 内部结构部署对企业租户透明。

- 将新租户添加到数据中心时，网络控制器会自动将第一个租户 RAS 网关配置为路由反射器。

- 每个租户都有相应的路由反射器，并驻留在与该租户关联的 RAS 网关 Vm 之一中。

- 租户路由反射器充当与租户关联的所有 RAS 网关 Vm 的路由反射器。 除了 RAS 网关路由反射器外，租户网关是路由反射器客户端。 路由反射器在所有路由反射器客户端之间执行路由同步，以便可以进行实际的数据路径路由。

- 路由反射器不提供配置它的 RAS 网关服务。

- 路由反射器使用与租户的企业站点相对应的企业路由更新网络控制器。 这允许网络控制器在租户虚拟网络上配置所需的 Hyper-v 网络虚拟化策略，以实现端到端的数据路径访问。

- 如果企业客户使用客户地址空间中的 BGP 路由，则 RAS 网关路由反射器是对应租户的所有站点的唯一外部 BGP (eBGP) 邻居。 无论企业租户的隧道终止点如何都是如此。 换句话说，无论 CSP 数据中心的哪个 RAS 网关 VM 终止租户站点的站点到站点 VPN 隧道，所有租户站点的 eBGP 对等方都是路由反射器。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [RAS 网关概述](gateway-overview.md)
- [RAS 网关部署体系结构](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Internet 工程任务组 (IETF) 征求意见主题 RFC 4456 BGP 路由反射：替代完整网格内部 BGP](https://tools.ietf.org/html/rfc4456)