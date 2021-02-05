---
title: Azure Stack Hub 集成系统的边界连接和网络集成
description: 了解如何在 Azure Stack Hub 集成系统中规划数据中心边界网络连接。
author: PatAltimore
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: patricka
ms.reviewer: wamota
ms.lastreviewed: 01/14/2021
ms.openlocfilehash: 091b6e5d1ac4c097c39e425cb6b15da4db96a7a1
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570644"
---
# <a name="border-connectivity"></a>边界连接 
网络集成规划是成功进行 Azure Stack Hub 集成系统部署、操作和管理的重要先决条件。 边界连接规划从选择是否要将动态路由与边界网关协议 (BGP) 一起使用开始。 这需要分配一个 16 位自治系统编号 (ASN)（公共或专用），或使用静态路由。

> [!IMPORTANT]
> 架顶式 (TOR) 交换机需要在物理接口上配置具有点到点 IP（/30 网络）的第 3 层上行链路。 不支持具有支持 Azure Stack Hub 操作的 TOR 交换机的第 2 层上行链路。 边界设备可支持 32 位 BGP 自治系统编号 (ASN)。

## <a name="bgp-routing"></a>BGP 路由
使用 BGP 等动态路由协议可以保证系统始终会注意到网络更改和便于管理。 为了增强安全性，可以针对 TOR 和边界之间的 BGP 对等互连设置密码。

如下图所示，将使用前缀列表阻止播发 TOR 交换机上的专用 IP 空间。 前缀列表将拒绝播发专用网络，它会作为路由映射应用于 TOR 与边界之间的连接。

Azure Stack Hub 解决方案内运行的软件负载均衡器 (SLB) 将对等互连到 TOR 设备，以便它可以动态播发 VIP 地址。

若要确保用户流量立即以透明方式从故障中恢复，TOR 设备之间配置的 VPC 或 MLAG 允许对主机和 HSRP 或 VRRP 使用多底盘链接聚合以便为 IP 网络提供网络冗余。

![BGP 路由](media/azure-stack-border-connectivity/bgp-routing.svg)

## <a name="static-routing"></a>静态路由
静态路由需要额外配置边界设备。 它需要更多的手动干预和管理，以及在任何更改之前进行彻底的分析。 配置错误导致的问题可能需要更多时间进行回退，具体取决于所做的更改。 不建议使用此路由方法，但支持此方法。

若要使用静态路由将 Azure Stack Hub 集成到网络环境，必须连接边界和 TOR 设备之间的所有四个物理链路。 由于静态路由的工作方式，无法保证高可用性。

对于发往 Azure Stack Hub 内任何网络的流量，边界设备必须配置有指向 TOR 和边界之间设置的四个 P2P IP 中的每一个的静态路由，但仅需要外部或公共 VIP 网络就能运行。 初始部署需要到 BMC 网络和外部网络的静态路由。 操作员可以选择在边界中保留静态路由以访问位于 BMC 和基础结构网络上的管理资源。 添加指向 *交换机基础结构* 和 *交换机管理* 网络的静态路由是可选的。

TOR 设备配置有将所有流量发送到边界设备的静态默认路由。 默认规则的一个流量例外是，对于专用空间，将使用应用于 TOR 到边界连接的访问控制列表阻止该流量。

静态路由仅适用于 TOR 与边界交换机之间的上行链路。 机架内使用的是 BGP 动态路由，因为它对于 SLB 和其他组件来说是基本工具，无法禁用或删除。

![静态路由](media/azure-stack-border-connectivity/static-routing.svg)

<sup>\*</sup> 在部署后，BMC 网络是可选的。

<sup>\*\*</sup> 交换机基础结构网络是可选的，因为整个网络可以包含在交换机管理网络中。

<sup>\*\*\*</sup> 交换机管理网络是必需的，可以与交换机基础结构网络分开添加。

## <a name="next-steps"></a>后续步骤

- [DNS 集成](azure-stack-integrate-dns.md)
- [Azure Stack 中心的透明代理](azure-stack-transparent-proxy.md)
