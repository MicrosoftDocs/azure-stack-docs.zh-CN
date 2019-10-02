---
title: 为 Azure Stack 创建 VPN 网关 | Microsoft Docs
description: 为 Azure Stack 创建和配置 VPN 网关。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a66057ea2490f4510d28db8b07d03e4ed17ba3ad
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714752"
---
# <a name="create-vpn-gateways-for-azure-stack"></a>为 Azure Stack 创建 VPN 网关

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

必须先为虚拟网络创建虚拟网络（VPN）网关，然后才能在 Azure 虚拟网络与本地站点之间发送网络流量。

VPN 网关是一种虚拟网络网关，可以通过公共连接发送加密的流量。 可以使用 VPN 网关在 Azure Stack 中的虚拟网络和 Azure 中的虚拟网络之间安全地发送流量。 还可以在虚拟网络和连接到 VPN 设备的另一个网络之间安全地发送流量。

创建虚拟网络网关时，需指定要创建的网关类型。 Azure Stack 支持一种类型的虚拟网络网关：**Vpn** 类型。

每个虚拟网络可以使用两个虚拟网络网关，但每种类型的网关只能有一个网关。 根据选择的设置，可与一个 VPN 网关建立多个连接。 此类设置的一个示例是多站点连接配置。

在为 Azure Stack 创建和配置 VPN 网关之前，请查看 [Azure Stack 网络的注意事项](azure-stack-network-differences.md)，以了解 Azure Stack 的配置与 Azure 的不同之处。

>[!NOTE]
>在 Azure 中，所选 VPN 网关 SKU 的带宽吞吐量必须分配给连接到网关的所有连接。 但在 Azure Stack 中，VPN 网关 SKU 的带宽值会应用于连接到网关的每个连接资源。
>
> 例如：
>
> * 在 Azure 中，基本 VPN 网关 SKU 可以容纳大约 100 Mbps 的聚合吞吐量。 如果对该 VPN 网关创建两个连接，而且其中一个连接使用 50 Mbps 的带宽，则 50 Mbps 可供另一个连接使用。
> * 在 Azure Stack 中，与基本 VPN 网关 SKU 的每个连接都分配 100 Mbps 的吞吐量。

## <a name="configuring-a-vpn-gateway"></a>配置 VPN 网关

VPN 网关连接需依赖于多个具有特定设置的资源。 大多数资源可单独进行配置，但在某些情况下，必须按特定的顺序配置这些资源。

### <a name="settings"></a>设置

为每个资源选择的设置对于创建成功的连接至关重要。

有关 VPN 网关的各个资源和设置的信息，请参阅[关于 Azure Stack 的 VPN 网关设置](azure-stack-vpn-gateway-settings.md)。 本文可帮助你了解：

* 网关类型、VPN 类型和连接类型。
* 网关子网、本地网络网关和可能需要考虑的其他资源设置。

### <a name="deployment-tools"></a>部署工具

可以使用一个配置工具（如 Azure 门户）创建和配置资源。 稍后，可以切换到 PowerShell 等其他工具来配置其他资源或修改现有资源（如果适用）。 目前，无法在 Azure 门户中配置每个资源和资源设置。 每个连接拓扑的文章中的说明指定了何时需要特定配置工具。

## <a name="connection-topology-diagrams"></a>连接拓扑图

VPN 网关连接可以使用不同的配置。 确定哪种配置最适合自己的需要。 在以下部分，可以查看有关以下 VPN 网关连接的信息和拓扑图示：

* 可用的部署模型
* 可用的配置工具
* 直接转到某篇文章的链接（如果适用）

以下部分中的图示和说明可帮助你选择符合要求的连接拓扑。 这些图示显示主要基准拓扑，但也可以使用这些图示作为指导来构建更复杂的配置。

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>站点到站点和多站点（IPsec/IKE VPN 隧道）

### <a name="site-to-site"></a>站点到站点

站点到站点 (S2S) VPN 网关连接是通过 IPsec/IKE (IKEv2) VPN 隧道建立的连接。 此类连接需要一个位于本地的 VPN 设备，并需要为此类连接分配公共 IP 地址。 此设备不能位于 NAT 之后。 S2S 连接可以用于跨界和混合配置。

![站点到站点 VPN 连接配置示例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>多站点

多站点连接是站点到站点连接的变体。 可从虚拟网络网关创建多个 VPN 连接（通常是连接到多个本地站点）。 使用多个连接时，必须使用基于路由的 VPN 类型（使用经典 VNet 时称为动态网关）。 由于每个虚拟网络只能有一个 VPN 网关，因此通过网关的所有连接都共享可用带宽。

![Azure VPN 网关多站点连接示例](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>网关 SKU

为 Azure Stack 创建虚拟网络网关时，需要指定要使用的网关 SKU。 支持以下 VPN 网关 SKU：

* 基本
* 标准
* 高性能

当选择较高的网关 SKU 时（例如，“标准”高于“基本”，“高性能”高于“标准”或“基本”），会将更多 CPU 和网络带宽分配给网关。 如此一来，网关可以对虚拟网络支持更高的网络吞吐量。

Azure Stack 不支持专门搭配 Express Route 使用的超性能网关 SKU。

选择 SKU 时请考虑以下事项：

* Azure Stack 不支持基于策略的网关。
* 基本 SKU 不支持边界网关协议 (BGP)。
* Azure Stack 不支持 ExpressRoute-VPN 网关共存配置。

## <a name="gateway-availability"></a>网关可用性

高可用性方案只能在**高性能网关**连接 SKU 上配置。 与同时通过主动/主动和主动/被动配置提供可用性的 Azure 不同，Azure Stack 仅支持主动/被动配置。

### <a name="failover"></a>故障转移

Azure Stack 中有三个多租户网关基础结构 VM。 其中两个 VM 处于活动模式，第三个 VM 处于冗余模式。 活动 VM 支持在其上创建 VPN 连接，而冗余 VM 只在发生故障转移时才接受 VPN 连接。 如果活动网关 VM 变得不可用，VPN 连接在短时间（几秒）的连接丢失之后就会故障转移到冗余 VM。

## <a name="estimated-aggregate-throughput-by-sku"></a>按 SKU 列出的估计聚合吞吐量

下表显示网关类型和预计的网关 SKU 聚合吞吐量：

|| VPN 网关吞吐量（1） | VPN 网关最大 IPsec 隧道数（2） |
|-------|-------|-------|
|**基本 SKU** **(3)** | 100 Mbps | 20 |
|**标准 SKU** | 100 Mbps | 20 |
|**高性能 SKU** | 200 Mbps | 10 |

### <a name="table-notes"></a>表说明

**（1）** -对于跨 internet 的跨界连接，VPN 吞吐量不是有保证的吞吐量。 它是可能的最大吞吐量。  
**（2）** -最大隧道是针对所有订阅的每个 Azure Stack 部署的总量。  
**（3）** -基本 SKU 不支持 BGP 路由。

>[!NOTE]
>在两个 Azure Stack 部署之间只能创建一个站点到站点 VPN 连接。 这是因为平台中的某个限制仅允许同一 IP 地址具有单个 VPN 连接。 由于 Azure Stack 利用多租户网关，该网关将单一公共 IP 用于 Azure Stack 系统中的所有 VPN 网关，因此两个 Azure Stack 系统之间只能有一个 VPN 连接。 此限制也适用于将多个站点到站点 VPN 连接连接到使用单一 IP 地址的任何 VPN 网关。 Azure Stack 不允许使用同一 IP 地址创建多个本地网络网关资源。

## <a name="next-steps"></a>后续步骤

* [Azure Stack 的 VPN 网关配置设置](azure-stack-vpn-gateway-settings.md)
