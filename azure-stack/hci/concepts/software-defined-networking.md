---
title: Azure Stack HCI 中的软件定义的网络 (SDN)
description: 软件定义的网络 (SDN) 提供了一种方法，用于在数据中心中集中配置和管理网络和网络服务，如交换、路由和负载均衡。
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/16/2020
ms.openlocfilehash: 3aaee084c30dd8d6b3f6514d18951fd604bd75aa
ms.sourcegitcommit: 301e571626f8e85556d9eabee3f385d0b81fdef4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2020
ms.locfileid: "92157659"
---
# <a name="software-defined-networking-sdn-in-azure-stack-hci"></a>Azure Stack HCI 中 (SDN) 软件定义的网络

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

软件定义的网络 (SDN) 提供了一种方法，用于在数据中心中集中配置和管理网络和网络服务，如交换、路由和负载均衡。 可以使用 SDN 动态创建、保护和连接网络，以满足不断演变的应用需求。 运营全球规模的数据中心网络，适用于 Microsoft Azure 的服务，这些服务每天有效地执行数十个网络更改，只是因为 SDN。

虚拟网络元素（例如 [Hyper-V 虚拟交换机](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch)、[Hyper-V 网络虚拟化](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization)、[软件负载均衡](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn)和 [RAS 网关](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-for-sdn)）的作用是充当 SDN 基础结构的构成部分。 还可以使用现有 SDN 兼容设备，在虚拟网络中运行的工作负载与物理网络之间实现更深入的集成。

Azure Stack HCI 上有三个主要 SDN 组件，你可以选择要部署的组件：网络控制器、软件负载均衡器和网关。

   > [!NOTE]
   > 延伸 (多站点) 群集不支持 SDN。

## <a name="network-controller"></a>网络控制器

[网络控制器](/windows-server/networking/sdn/technologies/Software-Defined-Networking-Technologies#network-controller)提供一种集中的可编程自动操作点，用于对数据中心的虚拟网络基础结构进行管理、配置、监视和故障排除。 它是高度可缩放的服务器角色，使用 Service Fabric 提供高可用性。 网络控制器必须部署在其自己的专用 VM 上。

部署网络控制器可实现以下功能：

- 创建和管理虚拟网络和子网。 将虚拟机 (VM) 连接到虚拟子网。
- 为连接到虚拟网络或基于 VLAN 的传统网络的 VM 配置和管理微分段。
- 将虚拟设备连接到虚拟网络。
- 为连接到虚拟网络或基于 VLAN 的传统网络的 VM 配置服务质量 (QoS) 策略。

建议 [在群集创建过程中部署网络控制器](../deploy/create-cluster.md#step-5-sdn-optional)。 或者，你可以在创建 Azure Stack HCI 群集后 [使用 PowerShell 部署网络控制器](../deploy/network-controller-powershell.md) 。

## <a name="software-load-balancing"></a>软件负载均衡 (SLB)

[软件负载均衡](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) (SLB) 可用于在多个 VM 之间均匀分布客户网络流量。 它使多台服务器可以托管相同的工作负载，从而提供高可用性和可伸缩性。 SLB 使用[边界网关协议](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp)向物理网络播发虚拟 IP 地址。

## <a name="gateway"></a>网关

网关用于在虚拟网络与另一个网络（本地或远程）之间路由网络流量。 网关可用于：

- 通过 Internet 在 SDN 虚拟网络与外部客户网络之间创建安全的站点到站点 IPsec 连接。
- 在 SDN 虚拟网络与外部网络之间创建通用路由封装 (GRE) 连接。 站点到站点连接与 GRE 连接的不同之处在于后者不是加密连接。 有关 GRE 连接方案的详细信息，请参阅 [Windows Server 中的 GRE 隧道](/windows-server/remote/remote-access/ras-gateway/gre-tunneling-windows-server)。
- 在 SDN 虚拟网络与外部网络之间创建第 3 层连接。 在这种情况下，SDN 网关只充当虚拟网络与外部网络之间的路由器。

网关使用[边界网关协议](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp)播发 GRE 终结点，并建立点到点连接。 SDN 部署会创建支持所有连接类型的默认网关池。 在此池中，可以指定保留为备用以防活动网关出现故障的网关数。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [规划软件定义的网络基础结构](plan-software-defined-networking-infrastructure.md)
- [Windows Server 中的 SDN 概述](/windows-server/networking/sdn/software-defined-networking)
- [使用脚本部署软件定义的网络基础结构](/windows-server/networking/sdn/deploy/deploy-a-software-defined-network-infrastructure-using-scripts)
