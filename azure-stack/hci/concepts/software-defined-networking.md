---
title: Azure Stack HCI 中 (SDN) 软件定义的网络
description: 概述适用于 Azure Stack HCI 中的功能的 SDN 主题。
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: 34ddaf17a9c90373d76a9b0ce490e2e60a1e221d
ms.sourcegitcommit: 034e61836038ca75199a0180337257189601cd12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91230472"
---
# <a name="sdn-in-azure-stack-hci"></a>Azure Stack HCI 中的 SDN

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

软件定义的网络 (SDN) 提供一种方法，用于在数据中心中集中配置和管理网络和网络服务，如交换机、路由和负载平衡。 可以使用 SDN 动态创建、保护和连接网络，以满足不断发展的应用需求。 运营全球规模的数据中心网络，适用于 Microsoft Azure 的服务，这些服务每天有效地执行数十个网络更改，只是因为 SDN。

虚拟网络元素（如 [Hyper-v 虚拟交换机](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch)、 [Hyper-v 网络虚拟化](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization)、 [软件负载平衡](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn)和 [RAS 网关](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-for-sdn) ）旨在作为 SDN 基础结构的有机组成部分。 你还可以使用现有的 SDN 兼容设备，以便在虚拟网络中运行的工作负荷与物理网络之间实现更深入的集成。

Azure Stack HCI 上有三个主要 SDN 组件，你可以选择要部署的组件：网络控制器、软件负载平衡器和网关。

## <a name="network-controller"></a>网络控制器

[网络控制器](/windows-server/networking/sdn/technologies/Software-Defined-Networking-Technologies#network-controller)提供集中的可编程点，可用于管理、配置、监视和诊断数据中心的虚拟网络基础结构。 它是高度可缩放的服务器角色，使用 Service Fabric 提供高可用性。 网络控制器必须部署在其自己的专用 Vm 上。

部署网络控制器可实现以下功能：

- 创建和管理虚拟网络和子网。 将 Vm)  (虚拟机连接到虚拟子网。
- 为连接到虚拟网络或基于 VLAN 的传统网络的 Vm 配置和管理微细分。
- 将虚拟设备连接到虚拟网络。
- 为连接到虚拟网络的 Vm 或基于 VLAN 的传统网络配置服务质量 (QoS) 策略。

建议在创建 Azure Stack HCI 群集后 [，使用 PowerShell 部署网络控制器](../deploy/network-controller-powershell.md) 。

## <a name="software-load-balancing"></a>软件负载均衡 (SLB)

[软件负载平衡](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) (SLB) 可用于在多个 vm 之间平均分配客户网络流量。 它使多个服务器可以托管相同的工作负荷，从而提供高可用性和可伸缩性。 SLB 使用 [边界网关协议](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) 向物理网络播发虚拟 IP 地址。

## <a name="gateway"></a>网关

网关用于在虚拟网络和另一个网络之间（本地或远程）路由网络流量。 网关可用于：

- 通过 internet 在 SDN 虚拟网络与外部客户网络之间创建安全的站点到站点 IPsec 连接。
- 在 SDN 虚拟网络与外部网络之间 (GRE) 连接创建通用路由封装。 站点到站点连接和 GRE 连接的不同之处在于后者不是加密的连接。 有关 GRE 连接方案的详细信息，请参阅 [Windows Server 中的 Gre 隧道](/windows-server/remote/remote-access/ras-gateway/gre-tunneling-windows-server)。
- 在 SDN 虚拟网络与外部网络之间创建第3层连接。 在这种情况下，SDN 网关只充当虚拟网络与外部网络之间的路由器。

网关使用 [边界网关协议](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) 播发 GRE 终结点并建立点到点连接。 SDN 部署创建支持所有连接类型的默认网关池。 在此池中，可以指定在活动网关出现故障的情况下保留备用的网关数。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [规划软件定义的网络基础结构](plan-software-defined-networking-infrastructure.md)
- [Windows Server 中的 SDN 概述](/windows-server/networking/sdn/software-defined-networking)
- [使用脚本部署软件定义的网络基础结构](/windows-server/networking/sdn/deploy/deploy-a-software-defined-network-infrastructure-using-scripts)
