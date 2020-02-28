---
title: 在 Azure Stack 集线器上部署高度可用的网络虚拟设备
description: 了解如何在 Azure Stack 集线器上部署高度可用的网络虚拟设备。
author: mattbriggs
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 69516e7d50cb0635854d2b9168cb6bc308b229d8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77705026"
---
# <a name="deploy-highly-available-network-virtual-appliances-on-azure-stack-hub"></a>在 Azure Stack 集线器上部署高度可用的网络虚拟设备

本文介绍如何在 Azure Stack Hub 中部署一组网络虚拟设备（Nva）以实现高可用性。 NVA 通常用来控制从外围网络（也称为 DMZ）到其他网络或子网的网络流量流。 本文包括了仅用于入口、仅用于出口和同时用于入口和出口的示例体系结构。

[Azure Stack 中心市场](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items)上提供不同供应商提供的 nva，请使用其中一个来获得最佳性能。

该体系结构具有以下组件。

## <a name="networking-and-load-balancing"></a>网络和负载均衡

-   **虚拟网络和子网**。 每个 Azure VM 都部署到一个可细分为子网的虚拟网络中。 为每个层创建一个单独的子网。

-   **第7层负载均衡器。** 由于应用程序网关在 Azure Stack 集线器上尚不可用，因此[Azure Stack 中心市场位置](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items)有一些替代项，例如： [KEMP LoadMaster 负载均衡器 ADC 内容交换机](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [f5 大 IP 虚拟版](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best)或[A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **负载均衡器**。 使用[Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)将网络流量从 web 层分发到业务层，并从业务层分发到 SQL Server。

-   **网络安全组**（nsg）。 使用 Nsg 限制虚拟网络中的网络流量。 例如，在此处所示的三层体系结构中，数据库层不接受来自 web 前端的流量，而只接受来自业务层和管理子网的流量。

-   **Udr.** 使用[*用户定义的路由*](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview/)（udr）将流量路由到特定的负载均衡器。

本文假定你基本了解 Azure Stack 集线器网络。

## <a name="architecture-diagrams"></a>体系结构关系图

可以在许多不同的体系结构中将 NVA 部署到外围网络。 例如，下图说明了如何使用单个 NVA 进行入口。

![自动生成社交媒体公告说明的屏幕截图](./media/iaas-architecture-nva-architecture/image1.png)

在此体系结构中，NVA 会检查所有入站和出站网络流量并且仅会放行符合网络安全规则的流量，从而提供一个安全的网络边界。 所有网络流量都必须通过 NVA 这一事实意味着 NVA 是网络中的单点故障。 如果 NVA 发生故障，则网络流量没有其他路径可用，并且所有后端子网都不可用。

若要使 NVA 高度可用，请将多个 NVA 部署到可用性集中。

下面的体系结构描述了实现高度可用的 NVA 所需的资源和配置：

| 解决方案 | 优点 | 注意事项 |
| --- | --- | --- |
| 具有第 7 层 NVA 的入口 | 所有 NVA 节点都处于活动状态。 | 需要一个可以终止连接和使用 SNAT 的 NVA。<br>需要为来自企业网络/Internet 和 Azure Stack 集线器的流量提供一组单独的 Nva。<br>仅可用于 Azure Stack 中心以外的流量。  |
| 具有第 7 层 NVA 的出口 | 所有 NVA 节点都处于活动状态。 | 需要一个可以终止连接并实现源网络地址转换（SNAT）的 NVA。 |
| 带有第7层 Nva 的入口-出口 | 所有节点都处于活动状态。<br>能够处理 Azure Stack 集线器发出的流量。 | 需要一个可以终止连接和使用 SNAT 的 NVA。<br>需要为来自企业网络/Internet 和 Azure Stack 集线器的流量提供一组单独的 Nva。 |

## <a name="ingress-with-layer-7-nvas"></a>具有第 7 层 NVA 的入口

下图显示了一个高可用性体系结构，它在面向 internet 的负载均衡器后面实现入口外围网络。 此体系结构旨在为第7层流量（例如 HTTP 或 HTTPS）提供与 Azure Stack 集线器工作负载的连接：

![自动生成的地图说明的屏幕截图](./media/iaas-architecture-nva-architecture/image2.png)

此体系结构的好处是所有 NVA 都是主动的，并且如果其中一个发生故障，则负载均衡器会将网络流量定向到另一个 NVA。 两个 NVA 都将流量路由到内部负载均衡器，因此，只要有一个 NVA 是主动的，流量便可继续流动。 这些 NVA 是终止用于 Web 层 VM 的 SSL 流量所必需的。 无法扩展这些 Nva 来处理企业网络流量，因为企业网络流量需要另一组专用于其自己的网络路由的 Nva。

## <a name="egress-with-layer-7-nvas"></a>具有第 7 层 NVA 的出口

可以扩展具有第7层 Nva 体系结构的入口，为源自 Azure Stack 集线器工作负载的请求提供出口外围网络。 以下体系结构旨在为第7层流量（例如 HTTP 或 HTTPS）提供外围网络中 Nva 的高可用性：

![自动生成的手机说明的屏幕截图](./media/iaas-architecture-nva-architecture/image3.png)

在此体系结构中，源自 Azure Stack 集线器的所有流量都将路由到内部负载均衡器。 该负载均衡器将传出请求分布到一组 NVA 中。 这些 NVA 使用其各自的公共 IP 地址将流量定向到 Internet。

## <a name="ingress-egress-with-layer-7--nvas"></a>带有第7层 Nva 的入口-出口

在两个入口和出口体系结构中，有一个用于入口和出口的单独外围网络。 以下体系结构演示了如何创建可用于第7层流量（例如 HTTP 或 HTTPS）的入口和出口的外围网络：

![自动生成社交媒体公告说明的屏幕截图](./media/iaas-architecture-nva-architecture/image4.png)

在带有第7层 Nva 体系结构的入口出口中，Nva 处理来自第7层负载均衡器的传入请求。 NVA 还处理负载均衡器的后端池中的工作负荷 VM 发出的传出请求。 因为传入流量是使用第7层负载均衡器进行路由的，传出流量是使用 SLB （Azure Stack 中心基本负载均衡器）路由的，Nva 负责维护会话相关性。 也就是说，第7层负载均衡器维护入站和出站请求的映射，因此可以将正确的响应转发给原始请求者。 但是，内部负载均衡器无权访问第7层负载均衡器映射，并使用其自己的逻辑将响应发送到 Nva。 负载均衡器可以将响应发送到 NVA，该响应最初未收到第7层负载均衡器发出的请求。 在这种情况下，Nva 必须传达并传输它们之间的响应，以便正确的 NVA 可以将响应转发到第7层负载均衡器。

> [!Note]  
> 你还可以通过确保 NVA 执行入站源网络地址转换 (SNAT) 来解决非对称路由问题。 这会将请求者的原始源 IP 替换为入站流上使用的 NVA 的 IP 地址之一。 这确保可以一次使用多个 NVA，同时保持路由对称性。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure Stack 集线器 vm，请参阅[Azure Stack 中心 vm 功能](azure-stack-vm-considerations.md)。  
- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
