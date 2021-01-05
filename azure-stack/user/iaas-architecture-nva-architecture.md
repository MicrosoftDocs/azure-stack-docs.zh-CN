---
title: 在 Azure Stack Hub 上部署高度可用的网络虚拟设备
description: 了解如何在 Azure Stack Hub 上部署高度可用的网络虚拟设备。
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 7e928757447f47c9235690f38e62778a4594faf1
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873769"
---
# <a name="deploy-highly-available-network-virtual-appliances-on-azure-stack-hub"></a>在 Azure Stack Hub 上部署高度可用的网络虚拟设备

本文介绍如何在 Azure Stack Hub 中部署一组网络虚拟设备 (NVA) 以实现高可用性。 NVA 通常用来控制从外围网络（也称为 DMZ）到其他网络或子网的网络流量流。 本文包括了仅用于入口、仅用于出口和同时用于入口和出口的示例体系结构。

[Azure Stack Hub 市场](../operator/azure-stack-marketplace-azure-items.md)中提供了不同供应商的 NVA，可以使用其中的一个来获得最佳性能。

该体系结构具有以下组件。

## <a name="networking-and-load-balancing"></a>网络和负载均衡

-   **虚拟网络和子网**。 每个 Azure VM 都会部署到可细分为子网的虚拟网络中。 为每个层创建一个单独的子网。

-   **第 7 层负载均衡器**。 由于应用程序网关在 Azure Stack 集线器上尚不可用，因此[Azure Stack 中心市场位置](../operator/azure-stack-marketplace-azure-items.md)有一些替代项，例如： [KEMP LoadMaster 负载均衡器 ADC 内容开关](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) /  [f5 大 IP 虚拟版](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best)或[A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **负载均衡器**。 使用 [Azure 负载均衡器](/azure/load-balancer/load-balancer-overview)可将网络流量从 Web 层分配到业务层，以及从业务层分配到 SQL Server。

-   **网络安全组** (NSG)。 使用 NSG 限制虚拟网络中的网络流量。 例如，在此处显示的三层体系结构中，数据库层不接受来自 Web 前端的流量，仅接受来自业务层和管理子网的流量。

-   **UDR。** 使用 [*用户定义的路由*](/azure/virtual-network/virtual-networks-udr-overview/) (UDR) 将流量路由到特定的负载均衡器。

本文假设读者基本了解 Azure Stack Hub 网络。

## <a name="architecture-diagrams"></a>体系结构关系图

NVA 可以采用许多不同的体系结构部署到外围网络中。 例如，下图演示了用于入口的单个 NVA 的使用。

![此屏幕截图显示了用于入口的单个 NVA 的使用。](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image1.svg)

在此体系结构中，NVA 会检查所有入站和出站网络流量并且仅会放行符合网络安全规则的流量，从而提供一个安全的网络边界。 因为所有网络流量都必须通过 NVA，这意味着 NVA 是网络中的单一故障点。 如果 NVA 发生故障，则网络流量没有其他路径可用，并且所有后端子网都不可用。

若要使 NVA 高度可用，请将多个 NVA 部署到可用性集中。

下面的体系结构描述了实现高度可用的 NVA 所需的资源和配置：

| 解决方案 | 优点 | 注意事项 |
| --- | --- | --- |
| 具有第 7 层 NVA 的入口 | 所有 NVA 节点都是主动的。 | 需要一个可以终止连接的 NVA 并使用 SNAT。<br>对于来自企业网络/Internet 和来自 Azure Stack Hub 的流量，需要单独的一组 NVA。<br>只能用于在 Azure Stack Hub 外部产生的流量。  |
| 具有第 7 层 NVA 的出口 | 所有 NVA 节点都是主动的。 | 需要一个可以终止连接的 NVA 并实现源网络地址转换 (SNAT)。 |
| 具有第 7 层 NVA 的入口-出口 | 所有节点都是主动的。<br>能够处理源自 Azure Stack Hub 的流量。 | 需要一个可以终止连接的 NVA 并使用 SNAT。<br>对于来自企业网络/Internet 和来自 Azure Stack Hub 的流量，需要单独的一组 NVA。 |

## <a name="ingress-with-layer-7-nvas"></a>具有第 7 层 NVA 的入口

下图显示了一个高可用性体系结构，它在面向 Internet 的负载均衡器后实现了一个入口外围网络。 此体系结构设计用于提供到 Azure Stack Hub 工作负载的连接以用于第 7 层流量，例如 HTTP 或 HTTPS：

![自动生成的映射说明的屏幕截图](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image2.svg)

此体系结构的好处是所有 NVA 都是主动的，并且如果其中一个发生故障，则负载均衡器会将网络流量定向到另一个 NVA。 两个 NVA 都将流量路由到内部负载均衡器，因此，只要有一个 NVA 是主动的，流量便可继续流动。 这些 NVA 是终止用于 Web 层 VM 的 SSL 流量所必需的。 无法扩展这些 NVA 来处理企业网络流量，因为企业网络流量需要另一组具有自身网络路由的专用 NVA。

## <a name="egress-with-layer-7-nvas"></a>具有第 7 层 NVA 的出口

可以扩展采用第 7 层 NVA 体系结构的入口，以针对源自 Azure Stack Hub 工作负载的请求提供出口外围网络。 以下体系结构设计用于在外围网络中提供具有高可用性的 NVA 以用于第 7 层流量，例如 HTTP 或 HTTPS：

![自动生成的手机说明的屏幕截图](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image4.svg)

在此体系结构中，源自 Azure Stack Hub 的所有流量将路由到一个外部负载均衡器。 该负载均衡器将传出请求分布到一组 NVA 中。 这些 NVA 使用其各自的公共 IP 地址将流量定向到 Internet。

## <a name="ingress-egress-with-layer-7--nvas"></a>具有第 7 层 NVA 的入口-出口

在这两个入口与出口体系结构中，入口与出口有单独的外围网络。 以下体系结构演示了如何创建可以同时用于入口和出口的外围网络以用于第 7 层流量，例如 HTTP 或 HTTPS：

![自动生成的社交媒体文章说明的屏幕截图](./media/iaas-architecture-nva-architecture/iaas-architecture-nva-architecture-image4.svg)

在采用第 7 层 NVA 体系结构的入口-出口中，NVA 处理来自第 7 层负载均衡器的传入请求。 NVA 还处理负载均衡器的后端池中的工作负荷 VM 发出的传出请求。 由于传入流量是使用第 7 层负载均衡器路由的，而传出流量是通过 SLB（Azure Stack Hub 基本负载均衡器）路由的，因此，NVA 负责维护会话相关性。 也就是说，第 7 层负载均衡器维护入站和出站请求的映射，因此，它可以将正确的响应转发到原始请求者。 但是，内部负载均衡器无权访问第 7 层负载均衡器映射，它使用其自身的逻辑将响应发送到 NVA。 负载均衡器可能会将响应发送到起初没有从第 7 层负载均衡器收到请求的 NVA。 在这种情况下，各个 NVA 必须进行通信并在它们之间传输响应，以便正确的 NVA 可以将响应转发到第 7 层负载均衡器。

> [!NOTE]  
> 你还可以通过确保 NVA 执行入站源网络地址转换 (SNAT) 来解决非对称路由问题。 这会将请求者的原始源 IP 替换为入站流上使用的 NVA 的 IP 地址之一。 这确保可以一次使用多个 NVA，同时保持路由对称性。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure Stack Hub VM，请参阅 [Azure Stack Hub VM 功能](azure-stack-vm-considerations.md)。  
- 若要详细了解 Azure 云模式，请参阅[云设计模式](/azure/architecture/patterns)。
