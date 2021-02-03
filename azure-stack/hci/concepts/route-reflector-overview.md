---
title: Azure Stack HCI 和 Windows Server 中的 BGP 路由反射器概述
description: 使用本主题来了解 Azure Stack HCI 和 Windows Server 中软件定义的网络的 BGP 路由反射器。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/02/2021
ms.openlocfilehash: 836931b0efa6c3629123602c141ec5ea35f8aa40
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510833"
---
# <a name="what-is-route-reflector"></a>什么是路由反射器？

> 适用于： Azure Stack HCI，版本 20H2;Windows Server 2019;Windows Server 2016

[远程访问服务 (RAS) 网关](gateway-overview.md)附带的边界网关协议 (BGP) 路由反射器提供了一种替代在路由器之间同步路由所必需的 BGP 完整网格拓扑的方法。 软件定义的网络部署中的路由反射器是一个逻辑实体，位于 RAS 网关和[网络控制器](network-controller-overview.md)之间的控制平面上。 但它不参与数据平面路由。

## <a name="how-route-reflector-works"></a>路由反射器的工作原理

对于完全网格同步，所有 BGP 路由器必须与路由拓扑中的所有其他路由器连接。 但是，使用路由反射器时，路由反射器是唯一连接到其他所有路由器的路由器（称为 BGP 路由反射器客户端），从而简化路由同步并减少网络流量。 路由反射器了解所有路由、计算最佳路由，并将最佳路由重新分发到其 BGP 客户端。

可以配置单个租户的远程访问隧道，以便在多个 RAS 网关虚拟机 (VM) 上终止。 这为云服务提供商 (CSP) 在一个 RAS 网关 VM 无法满足租户连接的所有带宽需求的情况下提供了更大的灵活性。

但是，此功能增加了路由管理以及在租户远程站点与其在云数据中心中的虚拟资源之间有效同步路由的复杂性。 为租户提供与多个 RAS 网关的连接还会在企业端增加配置的复杂性，因为每个租户站点将有单独的路由邻域。

控制平面中的 BGP 路由反射器可解决这些问题，并使 CSP 内部构造部署对企业租户透明。

- 将新租户添加到数据中心时，网络控制器会自动将第一个租户 RAS 网关配置为路由反射器。

- 每个租户都有相应的路由反射器，并驻留在与该租户关联的其中一个 RAS 网关 VM 中。

- 租户路由反射器充当与租户关联的所有 RAS 网关 VM 的路由反射器。 除 RAS 网关路由反射器外的租户网关是路由反射器客户端。 路由反射器在所有路由反射器客户端之间执行路由同步，以便可以进行实际的数据路径路由。

- 路由反射器不提供配置它的 RAS 网关服务。

- 路由反射器使用与租户的企业站点相对应的企业路由更新网络控制器。 这样，网络控制器可以在租户虚拟网络上配置所需的 Hyper-V 网络虚拟化策略，以实现端到端的数据路径访问。

- 如果企业客户在客户地址空间中使用 BGP 路由，则 RAS 网关路由反射器是对应租户的所有站点的唯一外部 BGP (eBGP) 邻域。 无论企业租户的隧道终止点如何，都是如此。 换句话说，无论 CSP 数据中心中的哪个 RAS 网关 VM 终止租户站点的站点到站点 VPN 隧道，所有租户站点的 eBGP 对等方都是路由反射器。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [RAS 网关概述](gateway-overview.md)
- [RAS 网关部署体系结构](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Internet 工程任务组 (IETF) 征求意见文档主题 RFC 4456 BGP 路由反射：完整网格内部 BGP 的替代方法](https://tools.ietf.org/html/rfc4456)