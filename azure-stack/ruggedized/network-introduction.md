---
title: Azure Stack 中心耐用网络简介。
description: 了解 Azure Stack Hub 耐用设备的网络连接。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: d654f0489c0ec1987eb42197e4bfdeb0f01291ef
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939457"
---
# <a name="azure-stack-hub-ruggedized-network-introduction"></a>Azure Stack 中心耐用网络简介

## <a name="network-design-overview"></a>网络设计概述

### <a name="physical-network-design"></a>物理网络设计

Azure Stack 集线器耐用解决方案需要可复原且高度可用的物理基础结构以支持其操作和服务。 从 ToR 到边界交换机的上行链路限制为 SFP+ 或 SFP28 介质以及 1 GB、10 GB 或 25 GB 的速度。 请咨询原始设备制造商 (OEM) 硬件供应商以了解可用性。

下图显示了 Azure Stack 中心耐用的建议设计。

 ![Azure Stack 中心耐用物理网络](media/network-introduction/physical-network-design-basic.png) 

### <a name="logical-network-design"></a>逻辑网络设计

逻辑网络设计代表物理网络基础结构的抽象。 它们用于组织和简化主机、虚拟机 (VM) 和服务的网络分配。 网络站点在逻辑网络创建过程中创建，用于定义：
- 虚拟局域网 (VLAN)
- IP 子网
- IP 子网/VLAN 对

所有这些都与每个物理位置中的逻辑网络关联。

下表显示了逻辑网络以及必须规划的关联 IPv4 子网范围：

| **逻辑网络**   | **说明**  | **大小** |
|-----------------------|------------------|----------|
| 公共虚拟 IP (VIP)       | Azure Stack 中心耐用使用此网络中的总31个地址。 8个公共 IP 地址适用于一小组 Azure Stack Hub 耐用服务，其余部分由租户 Vm 使用。 如果打算使用应用服务和 SQL 资源提供程序，则还要额外使用 7 个地址。 其余 15 个 IP 保留用于将来的 Azure 服务。 | /26 (62 主机) -<br>/22 (1022 主机) <br><br>建议使用 /24（254 台主机） |
| 交换机基础结构 | 用于路由的专用交换机管理接口的点到点 IP 地址，以及分配给交换机的环回地址。 | /26 |
| 基础结构        | 用于 Azure Stack 集线器耐用内部组件进行通信。 | /24 |
| 专用               | 用于存储网络、专用 VIP、基础结构容器和其他内部功能。 | /20 |
| 基板管理控制器 (BMC) | 用于与物理主机上的基板管理控制器通信。 | /26 |

### <a name="network-infrastructure"></a>网络基础结构

Azure Stack 中心耐用的网络基础结构包含在交换机上配置的多个逻辑网络。 下图显示了这些逻辑网络，及其如何与架顶 (TOR)、基板管理控制器和边界（客户网络）交换机集成。

Azure Stack 中心耐用逻辑网络示意图：

 ![Azure Stack 中心耐用逻辑网络](media/network-introduction/logical-network-design.png) 

#### <a name="bmc-network"></a>BMC 网络

此网络专门用于将所有基板管理控制器（也称为 BMC 或服务处理器）连接到管理网络。 示例包括：iDRAC、iLO、iBMC 等。 仅一个 BMC 帐户用于与任何 BMC 节点通信。 如果硬件生命周期主机 (HLH) 存在，它将位于此网络，并可提供 OEM 特定的软件，用于硬件维护或监视。

HLH 也托管部署 VM (DVM)。 DVM 在 Azure Stack Hub 耐用部署过程中使用，并在部署完成时删除。 此 DVM 在联网部署场景中需要进行 Internet 访问，以便测试、验证和访问多个组件。 这些组件可以在公司网络内，也可以在公司网络外（例如：NTP、DNS 和 Azure）。 有关连接要求的详细信息，请参阅 Azure Stack 集线器耐用 firewall 集成中的 NAT 部分。

#### <a name="private-network"></a>专用网络

/20 (4096 主机 Ip) 网络专用于 Azure Stack 中心耐用区域。 它不会在 Azure Stack Hub 耐用区域的边界交换机设备之外扩展。 此网络划分为多个子网，例如：

- **存储网络**：一个 /25（128 个 IP）网络，用于支持空间直通和服务器消息块 (SMB) 存储流量与 VM 实时迁移的使用。
- **内部虚拟 IP 网络**：一个 /25 网络，专用于软件负载均衡器的仅限内部的 VIP。
- **容器网络**：一个 /23（512 个 IP）网络，专用于在运行基础结构服务的容器之间处理仅限内部使用的流量。

专用网络的大小为/20 (4096) 专用 IP 空间的 ip。 此网络专用于 Azure Stack 集线器耐用系统。 它不会路由到 Azure Stack 集线器耐用系统的边界交换机设备，并可在多个 Azure Stack 集线器耐用系统上重复使用。 尽管网络专用于 Azure Stack 中心耐用，但它不能与数据中心中的其他网络重叠。 有关专用 IP 空间的指导，建议遵循 RFC 1918。

/20 个专用 IP 空间划分为多个网络，以便在将来的版本中将 Azure Stack 中心耐用的系统基础结构运行在容器上。 有关详细信息，请参阅 1910 发行说明。 借助此全新专用 IP 空间，可以在部署之前持续减少所需的可路由 IP 空间。

#### <a name="azure-stack-hub-ruggedized-infrastructure-network"></a>Azure Stack 中心耐用基础结构网络

/24 网络专用于内部 Azure Stack 集线器耐用组件，用于在自身之间进行通信和交换数据。 此子网可在 Azure Stack Hub 耐用解决方案外部路由到你的数据中心。 建议在不要此子网上使用公共的或可从 Internet 路由的 IP 地址。 此网络被播发到边框，但其所有 Ip 都受访问控制列表 (Acl) 保护。 允许进行访问的 IP 在一个较小的范围内（其大小相当于一个 /27 网络）。 Ip 主机服务（如特权终结点） (PEP) 和 Azure Stack Hub 耐用备份。

#### <a name="public-vip-network"></a>公共 VIP 网络

公共 VIP 网络分配给 Azure Stack Hub 耐用中的网络控制器。 它不是交换机上的逻辑网络。 SLB 针对租户工作负荷使用地址池并分配 /32 网络。 在交换机路由表中，这些 /32 IP 通过边界网关协议 (BGP) 播发为可用路由。 此网络包含可从外部访问的公用地址。 Azure Stack 集线器耐用基础结构会保留此公共 VIP 网络中的前31个地址，而剩余的地址由租户 Vm 使用。 此子网中的网络大小范围为最小 /26（64 台主机）到最大 /22（1022 台主机）。 建议计划使用/24 网络。

#### <a name="switch-infrastructure-network"></a>交换机基础结构网络

此 /26 网络是一个子网，其中包含可路由的点到点 IP /30（两个主机 IP）子网和环回。 这些是专用于带内交换机管理和 BGP 路由器 ID 的 /32 子网。 这一范围的 IP 地址必须可在 Azure Stack Hub 耐用解决方案外部路由到你的数据中心。 这些 IP 地址可以是专用的，也可以是公共的。

#### <a name="switch-management-network"></a>交换机管理网络

此 /29（六个主机 IP）网络专用于连接交换机的管理端口。 此网络允许带外访问，以便进行部署、管理和故障排除操作。 它是从上述交换机基础结构网络计算而来的。

## <a name="dns-design-overview"></a>DNS 设计概述

若要从外部 Azure Stack 中心耐用访问 (*门户*、 *adminportal*、 *Management*、 *adminmanagement*) Azure Stack Hub 耐用终结点，必须将 Azure Stack 中心耐用 dns 服务与托管要在 Azure Stack HUB 耐用中使用的 dns 区域的 dns 服务器集成。

### <a name="azure-stack-hub-ruggedized-dns-namespace"></a>Azure Stack 中心耐用 DNS 命名空间

部署 Azure Stack Hub 耐用时，需要提供一些与 DNS 相关的重要信息。

| 字段 | **说明** | **示例** |
|-----------|-----------------|-------------|
| 区域                   | Azure Stack 中心耐用部署的地理位置。 | 东               |
| 外部域名     | 要用于 Azure Stack 中心耐用部署的区域的名称。 | cloud.fabrikam.com |
| 内部域名     | 用于 Azure Stack Hub 耐用中的基础结构服务的内部区域的名称。 不能从 Azure Stack 中心耐用部署) 外部访问目录服务集成和专用 (。 | azurestack.local   |
| DNS 转发器           | 用于在公司 intranet 或公共 Internet 上转发在 Azure Stack Hub 耐用外托管的 DNS 查询、DNS 区域和记录的 DNS 服务器。 部署后，可以使用 **Set-AzSDnsForwarder** cmdlet 编辑 DNS 转发器值。 |                    |
| 命名前缀（可选） | 需要 Azure Stack 中心耐用基础结构角色实例计算机名称的命名前缀。 如果未提供，则默认值为 "azs"。 | azs                |

Azure Stack 中心耐用部署和终结点的完全限定域名 (FQDN) ，是 Region 参数和外部域名参数的组合。 使用上表中的示例中的值，此 Azure Stack Hub 耐用部署的 FQDN 为： *east.cloud.fabrikam.com*

同样，此部署的部分终结点的示例将如以下 URL 所示：

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

若要为 Azure Stack 中心耐用部署使用此示例 DNS 命名空间，需要满足以下条件：

- 区域 fabrikam.com 已注册到域注册机构和/或公司内部 DNS 服务器。 注册取决于名称解析要求。
- 子域 cloud.fabrikam.com 存在于区域 fabrikam.com 下。
- 可以从 Azure Stack 中心耐用部署中访问承载区域 fabrikam.com 和 cloud.fabrikam.com 的 DNS 服务器。

若要解析 Azure Stack 集线器的 DNS 名称和 Azure Stack 中心耐用的实例，你必须集成 DNS 服务器。 包括承载 Azure Stack Hub 耐用外部 DNS 区域的服务器，以及托管要使用的父区域的 DNS 服务器。

#### <a name="dns-name-labels"></a>DNS 名称标签

Azure Stack 集线器耐用支持将 DNS 名称标签添加到公共 IP 地址，以允许对公共 IP 地址进行名称解析。 使用 DNS 标签，用户可以轻松访问按名称在 Azure Stack Hub 中托管的应用和服务。 DNS 名称标签使用的命名空间与基础结构终结点略有不同。 按照前面的示例命名空间的格式，DNS 名称标签的命名空间将是 \*.east.cloudapp.cloud.fabrikam.com。

如果租户在公共 IP 地址资源的 DNS 名称字段中指定 **myapp** ，则它会在 Azure Stack Hub 耐用外部 DNS 服务器上的区域 **East.cloudapp.cloud.fabrikam.com** 中创建一个 **myapp** 记录。 生成的完全限定域名将为 myapp.east.cloudapp.cloud.fabrikam.com。

如果要利用此功能并使用该命名空间，则必须集成 DNS 服务器， 包括承载 Azure Stack Hub 耐用外部 DNS 区域的服务器，以及托管要使用的父区域的 DNS 服务器。 此命名空间不同于用于 Azure Stack 中心耐用服务终结点的命名空间，因此你必须创建一个附加委托或条件转发规则。

有关 DNS 名称标签的工作方式的详细信息，请参阅 Azure Stack Hub 耐用中的 "使用 DNS"。

### <a name="resolution-and-delegation"></a>解析和委托

有两种类型的 DNS 服务器：

- 权威 DNS 服务器托管 DNS 区域。 它只应答这些区域中的 DNS 记录查询。
- 递归 DNS 服务器不托管 DNS 区域。 它调用权威 DNS 服务器来收集所需的数据，以应答所有 DNS 查询。

Azure Stack 集线器耐用包括权威 DNS 服务器和递归 DNS 服务器。 递归服务器用于解析除内部专用区域之外的所有内容的名称，以及 Azure Stack 中心耐用部署的外部公共 DNS 区域。

## <a name="resolving-external-dns-names-from-azure-stack-hub-ruggedized"></a>解析 Azure Stack 集线器中的外部 DNS 名称耐用

若要解析 Azure Stack Hub 耐用外部的终结点的 DNS 名称 (例如： www.bing.com) ，必须为 Azure Stack 中心耐用提供 DNS 服务器，以便转发 Azure Stack 中心耐用不具有权威的 DNS 请求。 Azure Stack 中心耐用的 DNS 服务器将请求转发到 (在 DNS 转发器字段) 的部署工作表中。 请在此字段中提供至少两个服务器，目的是容错。 如果没有这些值，Azure Stack 集线器耐用部署将失败。 部署后，可以使用 **Set-AzSDnsForwarder** cmdlet 编辑 DNS 转发器值。

## <a name="firewall-design-overview"></a>防火墙设计概述

建议使用防火墙设备来帮助保护 Azure Stack 中心耐用的安全。 防火墙有助于防止分布式拒绝服务 (DDOS) 攻击之类的攻击，以及执行入侵检测和内容检查。 但是，它们也可能成为 Azure 存储服务（例如 Blob、表和队列）的吞吐量瓶颈。

如果使用断开连接部署模式，则必须发布 AD FS 终结点。 有关详细信息，请参阅数据中心集成标识一文。

Azure 资源管理器（管理员）、管理员门户和 Key Vault（管理员）终结点不一定需要外部发布。 例如，作为服务提供商，你可以通过仅从网络内部（而不是从 Internet）管理 Azure Stack 集线器耐用来限制攻击面。

对于企业组织，外部网络可能是现有的公司网络。 在这种情况下，你必须发布终结点才能 Azure Stack 企业网络中的中心耐用操作。

### <a name="network-address-translation"></a>网络地址转换

网络地址转换 (NAT) 是建议采用的方法，它允许部署虚拟机 (DVM) 在部署期间访问外部资源。 另外还允许在注册和故障排除期间访问紧急恢复控制台 (ERCS) VM 或特权终结点 (PEP)。 

还可以使用 NAT 作为外部网络上的公共 IP 地址或公共 VIP 的替代方法。 但是，不建议这样做，因为它限制了租户用户体验并增加了复杂性。 一个选项是一对一 NAT，仍然要求池中的每个用户 IP 有一个公共 IP。 另一个选项是多对一 NAT，需要针对用户可能使用的所有端口按用户 VIP 采用 NAT 规则。

下面是对公共 VIP 使用 NAT 的一些缺点：
- 管理防火墙规则时的开销，因为用户在软件定义的网络 (SDN) 堆栈中控制其自己的终结点和发布规则。 用户必须与 Azure Stack Hub 耐用操作员联系以发布其 Vip，并更新端口列表。 
- 虽然使用 NAT 会限制用户体验，但它使得操作员能够完全控制发布请求。
- 对于采用 Azure 的混合云方案，请注意 Azure 不支持使用 NAT 设置到终结点的 VPN 隧道。

### <a name="ssl-interception"></a>SSL 拦截

目前建议禁用任何 SSL 拦截 (例如，解密卸载) 所有 Azure Stack 中心耐用的流量。 如果在将来的更新中受支持，将提供有关如何为 Azure Stack 中心耐用启用 SSL 拦截的指导。

###  <a name="edge-deployment-firewall-scenario"></a>边缘部署防火墙方案

在边缘部署中，Azure Stack 集线器耐用直接部署在边缘路由器或防火墙后面。 在这些方案中，支持将防火墙放置在边界上方（例如方案 1，在这种情况下它支持主动-主动和主动-被动防火墙配置）， 或让防火墙充当边界设备（例如方案 2，在这种情况下它仅支持主动-主动防火墙配置）。 方案 2 依赖于等成本多路径 (ECMP)，使用 BGP 或静态路由进行故障转移。

在部署时会为外部网络中的公共 VIP 池指定公共的可路由 IP 地址。 出于安全考虑，在边缘方案中，建议不要在任何其他网络上使用公共的可路由 IP。 与在 Azure 之类的公有云中一样，此方案使得用户能够获得完全的自控云体验。

 ![Azure Stack 中心耐用 edge 防火墙方案](media/network-introduction/edge-firewall-scenario-60.png) 

### <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>企业 Intranet 或外围网络防火墙方案

在企业 intranet 或外围部署中，Azure Stack 集线器耐用部署在多分区防火墙上，或部署在边缘防火墙和内部企业网络防火墙之间。 然后，其流量将分布在安全的外围网络（或 DMZ）与不安全的区域之间，如下所述：

- **安全区域**：使用内部或公司可路由 IP 地址的内部网络。 安全网络可以进行划分， 可以通过防火墙 NAT 进行 Internet 出站访问。 通常可以通过内部网络从你的数据中心内对其进行访问。 所有 Azure Stack 集线器耐用网络应驻留在安全区域中，外部网络的公共 VIP 池除外。
- **外围区域**。 外围网络是指通常会在外部或面向 Internet 的应用程序（如 Web 服务器）上部署。 通常由防火墙对其进行监视，以避免 DDoS 和入侵（黑客进攻）之类的攻击，同时仍允许来自 Internet 的指定入站流量。 只有 Azure Stack 集线器耐用的外部网络公共 VIP 池才能驻留在 DMZ 区域中。
- **不安全区域**。 外部网络和 Internet。 不建议在 **不** 安全区域部署 Azure Stack 中心耐用。

 ![外围网络防火墙方案](media/network-introduction/perimeter-network-firewall-scenario-50.png) 

## <a name="vpn-design-overview"></a>VPN 设计概述

虽然 VPN 是用户概念，但解决方案所有者和操作员仍需了解一些重要的注意事项。

必须先为虚拟网络创建虚拟网络 (VPN) 网关，然后才能发送 Azure 虚拟网络和本地站点之间的网络流量。

VPN 网关是一种虚拟网络网关，可以通过公共连接发送加密的流量。 可以使用 VPN 网关在 Azure Stack Hub 耐用中的虚拟网络与 Azure 中的虚拟网络之间安全地发送流量。 还可以在虚拟网络和连接到 VPN 设备的另一个网络之间安全地发送流量。

创建虚拟网络网关时，需指定要创建的网关类型。 Azure Stack 集线器耐用支持一种类型的虚拟网络网关： **Vpn** 类型。

每个虚拟网络可以使用两个虚拟网络网关，但每种类型的网关只能有一个网关。 根据选择的设置，可与一个 VPN 网关建立多个连接。 此类设置的一个示例是多站点连接配置。

在为 Azure Stack Hub 耐用创建和配置 VPN 网关之前，请查看 Azure Stack 中心耐用网络的注意事项。 了解 Azure Stack 集线器耐用的配置与 Azure 的不同之处。

在 Azure 中，所选 VPN 网关 SKU 的带宽吞吐量必须分配给连接到网关的所有连接。 但是，在 Azure Stack Hub 耐用中，VPN 网关 SKU 的带宽值适用于连接到网关的每个连接资源。 例如： 

- 在 Azure 中，基本 VPN 网关 SKU 可以容纳大约 100 Mbps 的聚合吞吐量。 如果对该 VPN 网关创建两个连接，而且其中一个连接使用 50 Mbps 的带宽，则 50 Mbps 可供另一个连接使用。 
- 在 Azure Stack Hub 耐用中，与基本 VPN 网关 SKU 的每个连接都分配 100 Mbps 的吞吐量。

### <a name="vpn-types"></a>VPN 类型

为 VPN 网关配置创建虚拟网络网关时，必须指定 VPN 类型。 选择的 VPN 类型取决于要创建的连接拓扑。 VPN 类型还取决于使用的硬件。 S2S 配置需要 VPN 设备。 有些 VPN 设备仅支持特定的 VPN 类型。 

>[!IMPORTANT]
> 目前，Azure Stack 集线器耐用仅支持基于路由的 VPN 类型。 如果设备仅支持基于策略的 Vpn，则不支持从 Azure Stack 集线器耐用连接到这些设备。 此外，Azure Stack 集线器耐用目前不支持对基于路由的网关使用基于策略的流量选择器，因为不支持自定义 IPSec/IKE 策略配置。

- **PolicyBased**：基于策略的 VPN 会根据 IPsec 策略加密数据包并引导其通过 IPsec 隧道。 策略是通过本地网络与 Azure Stack 中心耐用 VNet 之间的地址前缀的组合进行配置的。 策略或流量选择器通常是 VPN 设备配置中的访问列表。  **PolicyBased** 在 Azure 中受支持，但在 Azure Stack Hub 耐用中不受支持。 
- **RouteBased**：基于路由的 VPN 使用 IP 转发或路由表中配置的路由。 这些路由将数据包定向到相应的隧道接口。 然后，隧道接口会加密或解密出入隧道的数据包。 **RouteBased** VPN 的策略或流量选择器配置为任意到任意（或使用通配符）。 默认情况下，无法更改这些 VPN。 **RouteBased** VPN 类型的值为 **RouteBased**。

### <a name="configuring-a-vpn-gateway"></a>配置 VPN 网关

VPN 网关连接需依赖于多个具有特定设置的资源。 大多数资源可单独进行配置，但在某些情况下，必须按特定的顺序配置这些资源。

#### <a name="settings"></a>设置

为每个资源选择的设置对于创建成功的连接至关重要。

本文可帮助你了解：

- 网关类型、VPN 类型和连接类型。
- 网关子网、本地网络网关和可能需要考虑的其他资源设置。

#### <a name="connection-topology-diagrams"></a>连接拓扑图

VPN 网关连接可以使用不同的配置。 确定哪种配置最适合自己的需要。 在以下部分，可以查看有关以下 VPN 网关连接的信息和拓扑图示：

- 可用的部署模型
- 可用的配置工具
- 直接转到某篇文章的链接（如果有）

以下部分中的图示和说明可帮助你选择符合要求的连接拓扑。 这些图示显示主要基准拓扑，但也可以使用这些图示作为指导来构建更复杂的配置。

#### <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>站点到站点和多站点（IPsec/IKE VPN 隧道）

##### <a name="site-to-site"></a>站点到站点

*站点到站点* (S2S) vpn 网关连接是通过 IPSEC/IKE (IKEV2) VPN 隧道建立的连接。 此类连接需要一个位于本地的 VPN 设备，并需要为此类连接分配公共 IP 地址。 此设备不能位于 NAT 的后面。 S2S 连接可用于跨界和混合配置。

##### <a name="multi-site"></a>多站点

*多站点* 连接是站点到站点连接的变体。 从虚拟网络网关创建多个 VPN 连接，通常情况下连接到多个本地站点。 使用多个连接时，必须使用基于路由的 VPN 类型（使用经典 VNet 时称为动态网关）。 由于每个虚拟网络只能有一个 VPN 网关，因此通过该网关的所有连接都共享可用带宽。

### <a name="gateway-skus"></a>网关 SKU

为 Azure Stack 中心耐用创建虚拟网络网关时，请指定要使用的网关 SKU。 支持以下 VPN 网关 SKU：

- 基本
- Standard
- 高性能

选择较高的网关 SKU 时，会将更多 CPU 和网络带宽分配给网关。 如此一来，网关可以对虚拟网络支持更高的网络吞吐量。

Azure Stack 集线器耐用不支持超高性能网关 SKU，专用于 Express Route。

选择 SKU 时请考虑以下事项：

- Azure Stack 集线器耐用不支持基于策略的网关。
- 基本 SKU 不支持 BGP。
- Azure Stack 中心耐用不支持 ExpressRoute 网关共存配置。

#### <a name="gateway-availability"></a>网关可用性

只能在 **高性能网关** 连接 SKU 上配置高可用性方案。 与 Azure 不同，Azure 通过主动/主动和主动/被动配置提供可用性，Azure Stack 集线器耐用只支持主动/被动配置。

#### <a name="failover"></a>故障转移

Azure Stack Hub 耐用中有三个多租户网关基础结构 Vm。 其中两个 VM 处于活动模式，第三个 VM 处于冗余模式。 活动 VM 支持在其上创建 VPN 连接，而冗余 VM 只在发生故障转移时才接受 VPN 连接。 如果活动网关 VM 变得不可用，VPN 连接在短时间（几秒）的连接丢失之后就会故障转移到冗余 VM。

### <a name="estimated-aggregate-throughput-by-sku"></a>按 SKU 列出的估计聚合吞吐量

下表显示网关类型和预计的网关 SKU 聚合吞吐量：

|                          | **VPN 网关吞吐量 (1)** | **VPN 网关最大 IPsec 隧道数 (2)** |
|--------------------------|--------------------------------|---------------------------------------|
| **基本 SKU** **(3)**    | 100 Mbps                       | 20                                    |
| **标准 SKU**         | 100 Mbps                       | 20                                    |
| **高性能 SKU** | 200 Mbps                       | 10                                    |

表格注释

**(1)** -VPN 吞吐量不是通过 Internet 跨界连接保证的吞吐量。 这是可能的最大吞吐量度量。  
**(2)** -最大隧道是所有订阅的每个 Azure Stack 中心耐用部署总数。  
**(3)** -基本 SKU 不支持 BGP 路由。  

>[!IMPORTANT]
> 在两个 Azure Stack 中心耐用部署之间，只能创建一个站点到站点 VPN 连接。 这是因为平台中的某个限制仅允许同一 IP 地址具有单个 VPN 连接。 由于 Azure Stack 集线器耐用利用多租户网关，该网关对 Azure Stack 中心耐用系统中的所有 VPN 网关使用单个公共 IP，因此在两个 Azure Stack Hub 耐用系统之间只能有一个 VPN 连接。 
>
> 此限制也适用于将多个站点到站点 VPN 连接连接到使用单一 IP 地址的任何 VPN 网关。 Azure Stack 集线器耐用不允许使用同一个 IP 地址创建多个本地网络网关资源。 * * 


### <a name="ipsecike-parameters"></a>IPsec/IKE 参数

在 Azure Stack Hub 耐用设置 VPN 连接时，必须在两端配置连接。 如果要在 Azure Stack Hub 耐用和硬件设备之间配置 VPN 连接，该设备可能会要求提供其他设置。 例如，用作 VPN 网关的交换机或路由器。

与支持多个产品/服务作为发起方和响应方的 Azure 不同，Azure Stack 集线器耐用默认情况下仅支持一个产品/服务。 如需使用适合 VPN 设备的不同 IPSec/IKE 设置，则可通过其他设置来手动配置连接。

#### <a name="ike-phase-1-main-mode-parameters"></a>IKE 阶段 1（主模式）参数

| **属性**                    | **值**      |
|---------------------------------|----------------|
| SDK 版本                     | IKEv2          |
| Diffie-Hellman 组            | ECP384         |
| 身份验证方法           | 预共享密钥 |
| 加密和哈希算法 | AES256、SHA384 |
| SA 生存期（时间）              | 28,800 秒 |

#### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 阶段 2（快速模式）参数

| **属性**                                     | **值**      |
|--------------------------------------------------|----------------|
| SDK 版本                                      | IKEv2          |
| 加密和哈希算法（加密）     | GCMAES256      |
| 加密和哈希算法（身份验证） | GCMAES256      |
| SA 生存期（时间）                               | 27,000 秒 |
| SA 生存期（千字节）                          | 33,553,408     |
| 完全向前保密 (PFS)                    | ECP384         |
| 死对等体检测                              | 支持      |

### <a name="configure-custom-ipsecike-connection-policies"></a>配置自定义 IPsec/IKE 连接策略

IPsec 和 IKE 协议标准支持采用各种组合的各种加密算法。 若要查看 Azure Stack Hub 耐用支持哪些参数以满足符合性或安全性要求，请参阅 IPsec/IKE 参数。

本文说明如何创建和配置 IPsec/IKE 策略，并将其应用到新的或现有的连接。

#### <a name="considerations"></a>注意事项

使用这些策略时，请注意以下重要事项：

- IPsec/IKE 策略仅适用于“标准”和“高性能”（基于路由）网关 SKU。 
- 一个给定的连接只能指定一个策略组合。
- 必须指定 IKE（主模式）和 IPsec（快速模式）的所有算法和参数。 不允许指定部分策略。
- 请查阅 VPN 设备供应商规范，确保本地 VPN 设备支持该策略。 如果策略不兼容，则无法建立站点到站点连接。

#### <a name="workflow-to-create-and-set-ipsecike-policy"></a>用于创建和设置 IPsec/IKE 策略的工作流

本部分概述了针对站点到站点 VPN 连接创建和更新 IPsec/IKE 策略所要运行的工作流：

1.  创建虚拟网络和 VPN 网关。
2.  为跨界连接创建本地网络网关。
3.  使用选定的算法和参数创建 IPsec/IKE 策略。
4.  创建采用 IPsec/IKE 策略的 IPsec 连接。
5.  为现有连接添加/更新/删除 IPsec/IKE 策略。

#### <a name="supported-cryptographic-algorithms-and-key-strengths"></a>支持的加密算法和密钥强度

下表列出了 Azure Stack Hub 耐用客户可配置的受支持的加密算法和密钥强度：

| **IPsec/IKEv2**  | **选项** |
|------------------|-------------|
| IKEv2 加密 | AES256、AES192、AES128、DES3、DES                                                                                                                         |
| IKEv2 完整性  | SHA384、SHA256、SHA1、MD5                                                                                                                                 |
| DH 组         | ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、无                                                                                          |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、无                                                                                  |
| IPsec 完整性  | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                                                                                                        |
| PFS 组        | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、无                                                                                                          |
| QM SA 生存期   | （可选：如果未指定，则使用默认值）                                                                                                      |
|                  | 秒（整数；至少为 300 秒/默认为 27,000 秒）                                                                                                         |
|                  | KB（整数；至少为 1024 KB/默认为 102,400,000 KB）                                                                                                      |
| 流量选择器 | Azure Stack 集线器耐用不支持基于策略的流量选择器。                                                                                           |

本地 VPN 设备配置必须匹配，或者必须包含可在 Azure IPsec/IKE 策略中指定的以下算法和参数：

 - IKE 加密算法（主模式/阶段 1）。
 - IKE 完整性算法（主模式/阶段 1）。
 - DH 组（主模式/阶段 1）。
 - IPsec 加密算法（快速模式/阶段 2）。
 - IPsec 完整性算法（快速模式/阶段 2）。
 - PFS 组（快速模式/阶段 2）。
 - SA 生存期仅为本地规范，不需要遵守。

如果将 GCMAES 用作 IPsec 加密算法，则必须为 IPsec 完整性选择相同的 GCMAES 算法和密钥长度。 例如：将 GCMAES128 用于这两者。

在上表中：

- IKEv2 对应于主模式或阶段 1。
- IPsec 对应于快速模式或阶段 2。
- DH 组指定在主模式或阶段 1 中使用的 Diffie-Hellmen 组。
- PFS 组指定在快速模式或阶段 2 中使用的 Diffie-Hellmen 组。
- IKEv2 主模式 SA 生存期在 Azure Stack Hub 耐用 VPN 网关上28800秒固定。

下表列出了自定义策略支持的相应 Diffie-Hellman 组：

| **Diffie-Hellman 组** | **DHGroup**          | **PFSGroup** | **密钥长度** |
|--------------------------|----------------------|--------------|----------------|
| 1                        | DHGroup1             | PFS1         | 768 位 MODP   |
| 2                        | DHGroup2             | PFS2         | 1024 位 MODP  |
| 14                       | DHGroup14            | PFS2048      | 2048 位 MODP  |
|                          | DHGroup2048          |              |                |
| 19                       | ECP256               | ECP256       | 256 位 ECP    |
| 20                       | ECP384               | ECP384       | 384 位 ECP    |
| 24                       | DHGroup24            | PFS24        | 2048 位 MODP  |

### <a name="connect-azure-stack-hub-ruggedized-to-azure-using-azure-expressroute"></a>使用 Azure ExpressRoute 将 Azure Stack 集线器耐用连接到 Azure

#### <a name="overview-assumptions-and-prerequisites"></a>概述、假设和先决条件

Azure ExpressRoute 可让你将本地网络扩展到 Microsoft 云。 你使用通过连接服务提供商提供的专用连接。 ExpressRoute 不是基于公共 Internet 的 VPN 连接。

有关 Azure ExpressRoute 的详细信息，请参阅 ExpressRoute 概述。

#### <a name="assumptions"></a>假设

本文假设读者：

- 在 Azure 方面有实践经验。
- 你基本了解 Azure Stack 中心耐用。
- 对网络有基本的了解。

#### <a name="prerequisites"></a>先决条件

若要使用 ExpressRoute 连接 Azure Stack 集线器耐用和 Azure，必须满足以下要求：

- 通过连接提供商预配的 ExpressRoute 线路。
- 一个用于在 Azure 中创建 ExpressRoute 线路和 VNet 的 Azure 订阅。
- 支持以下操作的路由器：
  - 站点到站点 VPN 连接的 LAN 接口与 Azure Stack 中心耐用多租户网关之间的连接。
  - 如果 Azure Stack 中心耐用部署中有多个租户，则 (虚拟路由和转发) 创建多个 Vrf。
- 具有以下组件的路由器：
  - 已连接到 ExpressRoute 线路的 WAN 端口。
  - 连接到 Azure Stack 集线器耐用多租户网关的 LAN 端口。

#### <a name="expressroute-network-architecture"></a>ExpressRoute 网络体系结构

下图显示了使用本文中的示例完成 ExpressRoute 设置后，Azure Stack 中心耐用和 Azure 环境：

 ![ExpressRoute 网络体系结构](media/network-introduction/expressroute-network-architecture-60.png) 

下图显示了多个租户如何从 Azure Stack 集线器耐用基础结构通过 ExpressRoute 路由器连接到 Azure：

 ![ExpressRoute 网络体系结构多租户](media/network-introduction/expressroute-network-architecture-multi-tenant-60.png) 

