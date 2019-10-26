---
title: Azure Stack 的网络集成规划 |Microsoft Docs
description: 了解如何规划与 Azure Stack 集成系统的数据中心网络集成。
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
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 4894fb7184944095d968d08e2d668912a78119d4
ms.sourcegitcommit: ef7efcde76d1d7875ca1c882afebfd6a27f1c686
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72888039"
---
# <a name="network-integration-planning-for-azure-stack"></a>Azure Stack 的网络集成规划

本文提供 Azure Stack 的网络基础结构信息，以帮助你确定如何将 Azure Stack 最好地集成到现有的网络环境中。 

> [!NOTE]
> 若要从 Azure Stack 解析外部 DNS 名称（例如 www\.bing.com），需要提供 DNS 服务器来转发 DNS 请求。 有关 Azure Stack DNS 要求的详细信息，请参阅[Azure Stack 数据中心集成-DNS](azure-stack-integrate-dns.md)。

## <a name="physical-network-design"></a>物理网络设计

Azure Stack 解决方案需要一个可复原且具有高可用性的物理基础设施来支持其操作和服务。 从 ToR 到 Border 交换机的上行仅限于 SFP + 或 SFP28 介质、1 GB、10 GB 或 25 GB 的速度。 请与原始设备制造商（OEM）硬件供应商联系以获取可用性。 下图显示了我们的建议设计：

![建议 Azure Stack 网络设计](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>逻辑网络

逻辑网络表示基础物理网络基础结构的抽象。 它们用于组织和简化主机、虚拟机（Vm）和服务的网络分配。 作为逻辑网络创建的一部分，将创建网络站点来定义虚拟局域网（Vlan）、IP 子网以及与每个物理位置中的逻辑网络关联的 IP 子网/VLAN 对。

下表显示了你必须为其规划的逻辑网络和关联的 IPv4 子网范围：

| 逻辑网络 | 描述 | 大小 | 
| -------- | ------------- | ------------ | 
| 公共 VIP | Azure Stack 使用此网络中的总计31个地址。 8个公共 IP 地址适用于一小部分 Azure Stack 服务，其他服务由租户 Vm 使用。 如果计划使用应用服务和 SQL 资源提供程序，请使用7个以上的地址。 剩余的15个 Ip 保留用于将来的 Azure 服务。 | /26 （62主机）-/22 （1022主机）<br><br>建议使用 =/24 （254主机） | 
| 交换机基础结构 | 用于路由目的的点到点 IP 地址、专用交换机管理接口和分配给交换机的环回地址。 | /26 | 
| 基础架构 | 用于 Azure Stack 内部组件进行通信。 | /24 |
| Private | 用于存储网络和专用 Vip。 | /24 | 
| BMC | 用于与物理主机上的 Bmc 通信。 | /26 | 
| | | |

## <a name="network-infrastructure"></a>网络基础结构

Azure Stack 的网络基础结构包含在交换机上配置的多个逻辑网络。 下图显示了这些逻辑网络以及它们如何与架顶（TOR）、基板管理控制器（BMC）和边界（客户网络）交换机集成。

![逻辑网络关系图和切换连接](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC 网络

此网络专用于将所有基板管理控制器（也称为 BMC 或服务处理器）连接到管理网络。 示例包括： iDRAC、iLO、iBMC 等。 仅一个 BMC 帐户用于与任何 BMC 节点通信。 如果存在，则硬件生命周期主机（HLH）位于此网络上，可提供 OEM 特定的软件用于硬件维护或监视。

HLH 还承载部署 VM （DVM）。 在 Azure Stack 部署期间使用 DVM，并在部署完成时删除。 DVM 要求连接的部署方案中的 internet 访问，以便测试、验证和访问多个组件。 这些组件可以位于企业网络内部和外部（例如： NTP、DNS 和 Azure）。 有关连接要求的详细信息，请参阅[Azure Stack 防火墙集成中的 NAT 部分](azure-stack-firewall.md#network-address-translation)。

### <a name="private-network"></a>专用网络

此/24 （254主机 Ip）网络专用于 Azure Stack 区域（不会扩展到 Azure Stack 区域的边界交换机设备上），并分为两个子网：

- **存储网络**： A/25 （126主机 ip）网络，用于支持使用空格直通和服务器消息块（SMB）存储流量和 VM 实时迁移。
- **内部虚拟 IP 网络**：一个/25 网络，专用于软件负载平衡器仅限内部的 vip。

### <a name="azure-stack-infrastructure-network"></a>Azure Stack 基础结构网络
此/24 网络专用于内部 Azure Stack 组件，因此它们之间可以互相通信和交换数据。 此子网可以从 Azure Stack 解决方案外部路由到你的数据中心，我们不建议在此子网上使用公用或 Internet 可路由的 IP 地址。 此网络被播发到边框，但其所有 Ip 都受访问控制列表（Acl）保护。 允许访问的 Ip 在大小相当于 a/27 网络和主机服务[（如特权终结点（PEP））](azure-stack-privileged-endpoint.md)和[Azure Stack 备份](azure-stack-backup-reference.md)的小型范围内。

### <a name="public-vip-network"></a>公共 VIP 网络

公共 VIP 网络分配到 Azure Stack 中的网络控制器。 它不是交换机上的逻辑网络。 SLB 使用地址池并为租户工作负荷分配/32 网络。 在交换机路由表中，这些/32 Ip 通过 BGP 播发为可用路由。 此网络包含外部可访问的或公共的 IP 地址。 Azure Stack 基础结构会保留此公共 VIP 网络中的前31个地址，而剩余的地址由租户 Vm 使用。 此子网上的网络大小可以从最小/26 （64主机）到最大/22 个（1022主机）。 建议计划使用/24 网络。

### <a name="switch-infrastructure-network"></a>交换机基础结构网络

此/26 网络是包含可路由的点到点 IP/30 （两个主机 Ip）子网和环回的子网，这些子网是带内交换机管理和 BGP 路由器 ID 的专用/32 子网。 这一范围的 IP 地址必须可在数据中心内路由 Azure Stack 解决方案之外。 它们可以是专用或公共 Ip。

### <a name="switch-management-network"></a>交换机管理网络

此/29 （六个主机 Ip）网络专用于连接交换机的管理端口。 它允许带外访问进行部署、管理和故障排除。 它通过上面提到的交换机基础结构网络进行计算。

## <a name="next-steps"></a>后续步骤

了解网络规划：[边界连接](azure-stack-border-connectivity.md)。
