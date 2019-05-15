---
title: Azure Stack 集成系统的边界连接网络集成注意事项 | Microsoft Docs
description: 了解可以执行哪些操作来为多节点 Azure Stack 规划数据中心边界网络连接。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 291329d4e681aaeba416e0873e9f6ddf599526de
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618705"
---
# <a name="border-connectivity"></a>边界连接 
网络集成规划是成功进行 Azure Stack 集成系统部署、操作和管理的重要先决条件。 边界连接规划首先是要选择是否将动态路由与边界网关协议 (BGP) 配合使用。 这需要分配 16 位的 BGP 自治系统编号（公共或专用），或者使用静态路由（在这种情况下会将静态默认路由分配给边界设备）。

> [!IMPORTANT]
> 架顶式 (TOR) 交换机需要在物理接口上配置具有点到点 IP（/30 网络）的第 3 层上行链路。 不支持将第 2 层上行链路用于支持 Azure Stack 操作的 TOR 交换机。 

## <a name="bgp-routing"></a>BGP 路由
使用 BGP 等动态路由协议可以保证系统始终会注意到网络更改和便于管理。 为了增强安全性，可以针对 TOR 和边界之间的 BGP 对等互连设置密码。 

如下图所示，将使用前缀列表阻止播发 TOR 交换机上的专用 IP 空间。 前缀列表将拒绝播发专用网络，它会作为路由映射应用于 TOR 与边界之间的连接。

Azure Stack 解决方案内运行的软件负载均衡器 (SLB) 将对等互连到 TOR 设备，以便它可以动态播发 VIP 地址。

若要确保用户流量立即以透明方式从故障中恢复，TOR 设备之间配置的 VPC 或 MLAG 允许对主机和 HSRP 或 VRRP 使用多底盘链接聚合以便为 IP 网络提供网络冗余。

![BGP 路由](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>静态路由
静态路由需要额外配置边界设备。 它需要更多的手动干预和管理，在进行任何更改之前都需要进行全面的分析，配置错误导致的问题可能需要更多时间进行回退，具体取决于所做的更改。 它不是建议的路由方法，但受支持。

若要将 Azure Stack 集成到使用静态路由的网络环境，必须连接边界与 TOR 设备之间的所有四个物理链路，并且，静态路由的工作方式导致无法保证高可用性。

对于以外部网络、公共 VIP 和基础结构网络为目的地的流量，必须为边界设备配置指向 TOR 设备 P2P 的静态路由。 它将需要到 BMC 和外部网络的静态路由以进行部署。 操作员可以选择在边界中保留静态路由以访问位于 BMC 网络上驻留的管理资源。 添加指向*交换机基础结构*和*交换机管理*网络的静态路由是可选的。

TOR 设备配置有将所有流量发送到边界设备的静态默认路由。 默认规则的一个流量例外是，对于专用空间，将使用应用于 TOR 到边界连接的访问控制列表阻止该流量。

静态路由仅适用于 TOR 与边界交换机之间的上行链路。 BGP 动态路由是机架内使用，因为它是 SLB 和其他组件的必备工具，无法禁用或删除。

![静态路由](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup> 在部署后，BMC 网络是可选的。

<sup>\*\*</sup> 交换机基础结构网络是可选的，因为整个网络可以包含在交换机管理网络中。

<sup>\*\*\*</sup> 交换机管理网络是必需的，可以与交换机基础结构网络分开添加。

## <a name="transparent-proxy"></a>透明代理
如果数据中心要求所有流量都使用代理，则必须配置“透明代理”以便根据策略处理来自机架的所有流量，并分离网络上不同区域之间的流量。

> [!IMPORTANT]
> Azure Stack 解决方案不支持普通 web 代理。  

透明代理（也称为截获、内联或强制代理）将截获网络层的正常通信，而无需任何特殊的客户端配置。 客户端不需要注意代理是否存在。

![透明代理](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>后续步骤
[DNS 集成](azure-stack-integrate-dns.md)
