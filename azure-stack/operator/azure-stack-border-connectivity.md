---
title: Azure Stack 集线器集成系统的边框连接和网络集成
description: 了解如何规划 Azure Stack 集线器集成系统中的数据中心边框网络连接。
author: ihenkel
ms.topic: article
ms.date: 11/15/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 15440c32974aebfed3f3faf86885dd6f0af71ddd
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76878507"
---
# <a name="border-connectivity"></a>边界连接 
网络集成规划是成功 Azure Stack 中心集成系统部署、操作和管理的一个重要必备组件。 边界连接计划首先选择是否要将动态路由与边界网关协议（BGP）配合使用。 这需要分配一个16位 BGP 自治系统编号（公用或专用）或使用静态路由，并将静态默认路由分配给边框设备。

> [!IMPORTANT]
> 架顶（TOR）交换机需要在物理接口上配置了点到点 Ip （/30 网络）的第3层上行链路。 不支持支持 Azure Stack 集线器操作的带 TOR 交换机的第2层上行链路。

## <a name="bgp-routing"></a>BGP 路由
使用 BGP 等动态路由协议可保证系统始终了解网络更改，并简化管理。 为增强安全性，可以在 TOR 与边框之间的 BGP 对等互连上设置密码。

如下图所示，使用前缀列表阻止了 TOR 交换机上专用 IP 空间的播发。 前缀列表拒绝专用网络的播发，并将其作为 TOR 和边框之间连接的路由映射。

在 Azure Stack 集线器解决方案对等节点内运行的软件负载平衡器（SLB）可以动态地公布 VIP 地址。

为了确保用户流量能立即且透明地从故障中恢复，在 TOR 设备之间配置的 VPC 或 MLAG 允许使用多底盘链接聚合到为 IP 网络提供网络冗余的主机和 HSRP 或 VRRP。

![BGP 路由](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>静态路由
静态路由需要对边框设备进行其他配置。 在进行任何更改之前，它需要进行更多手动干预和管理，并进行彻底的分析。 根据所做的更改，根据配置错误导致的问题可能需要更长的时间来回滚。 不建议使用此路由方法，但支持此方法。

若要使用静态路由将 Azure Stack 集线器集成到网络环境，必须连接边框与 TOR 设备之间的四个物理链接。 由于静态路由的工作方式，无法保证高可用性。

必须配置边框设备，使其具有指向 TOR 之间的四个 P2P IP 集的每个 IP 集的静态路由，以及 Azure Stack 集线器内的任何网络的流量的边框，但操作仅需要*外部*或公共 VIP 网络。 初始部署需要到*BMC*和*外部*网络的静态路由。 操作员可以选择在边框中保留静态路由，以访问位于*BMC*和*基础结构*网络上的管理资源。 将静态路由添加到*交换机基础结构*和*交换机管理*网络是可选的。

TOR 设备配置为将所有流量发送到边界设备的静态默认路由。 默认规则的一个流量例外是针对专用空间，该空间使用应用于 TOR to border 连接的访问控制列表来阻止。

静态路由仅适用于 TOR 和 border 交换机之间的上行链路。 BGP 动态路由在机架内使用，因为它是适用于 SLB 和其他组件的基本工具，无法禁用或删除。

![静态路由](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup>在部署后，BMC 网络是可选的。

<sup>\*\*</sup>交换机基础结构网络是可选的，因为整个网络可以包含在交换机管理网络中。

<sup>\*\*\*</sup>交换机管理网络是必需的，可与交换机基础结构网络分开添加。

## <a name="transparent-proxy"></a>透明代理
如果你的数据中心需要所有流量来使用代理，则必须配置*透明代理*以处理来自货架的所有流量，以便根据策略处理该数据，并将网络上的区域之间的流量隔开。

> [!IMPORTANT]
> Azure Stack 集线器解决方案不支持普通 web 代理。  

透明代理（也称为拦截、内联或强制代理）在网络层上拦截正常通信，无需任何特殊的客户端配置。 客户端无需知道是否存在代理。

![透明代理](media/azure-stack-border-connectivity/transparent-proxy.png)

SSL 流量截获[不受支持](azure-stack-firewall.md#ssl-interception)，并且可能在访问终结点时导致服务失败。 与标识所需的终结点进行通信时，支持的最大超时值为 60s 3 次重试。

## <a name="next-steps"></a>后续步骤
[DNS 集成](azure-stack-integrate-dns.md)
