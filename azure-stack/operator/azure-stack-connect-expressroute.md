---
title: 使用 ExpressRoute 将 Azure Stack 连接到 Azure |Microsoft Docs
description: 了解如何使用 ExpressRoute 将 Azure Stack 中的虚拟网络连接到 Azure 中的虚拟网络。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 4d9331f5167a0ce9d305a76225987d8b1d3f3dcc
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961561"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>使用 Azure ExpressRoute 将 Azure Stack 连接到 Azure

适用于：*Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍如何使用[Microsoft Azure ExpressRoute](/azure/expressroute/)的直接连接将 Azure Stack 的虚拟网络连接到 Azure 虚拟网络。

可以使用本文作为教程，并使用示例设置相同的测试环境。 或者，可以使用本文作为引导性的演练来设置自己的 ExpressRoute 环境。

## <a name="overview-assumptions-and-prerequisites"></a>概述、假设和先决条件

使用 Azure ExpressRoute 可以通过连接服务提供商提供的专用连接将本地网络扩展到 Microsoft 云。 ExpressRoute 不是通过公共 internet 的 VPN 连接。

有关 Azure ExpressRoute 的详细信息，请参阅 [ExpressRoute 概述](/azure/expressroute/expressroute-introduction)。

### <a name="assumptions"></a>假设

本文假设读者：

* 在 Azure 方面有实践经验。
* 对 Azure Stack 有基本的了解。
* 对网络有基本的了解。

### <a name="prerequisites"></a>先决条件

若要使用 ExpressRoute 连接 Azure Stack 和 Azure，必须满足以下要求：

* 通过[连接提供商](/azure/expressroute/expressroute-locations)预配的 [ExpressRoute 线路](/azure/expressroute/expressroute-circuit-peerings)。
* 一个用于在 Azure 中创建 ExpressRoute 线路和 VNet 的 Azure 订阅。
* 满足以下要求的路由器：
  * 支持在其 LAN 接口与 Azure Stack 多租户共享网关之间建立站点到站点 VPN 连接。
  * 如果 Azure Stack 部署中有多个租户，则支持创建多个 Vrf （虚拟路由和转发）。
* 具有以下组件的路由器：
  * 已连接到 ExpressRoute 线路的 WAN 端口。
  * 已连接到 Azure Stack 多租户网关的 LAN 端口。

### <a name="expressroute-network-architecture"></a>ExpressRoute 网络体系结构

下图显示了使用本文中的示例完成设置 ExpressRoute 后的 Azure Stack 和 Azure 环境：

![ExpressRoute 网络](media/azure-stack-connect-expressroute/Conceptual.png)

下图显示多个租户如何从 Azure Stack 基础结构通过 ExpressRoute 路由器连接到位于 Microsoft 边缘的 Azure：

![使用 ExpressRoute 的多租户连接](media/azure-stack-connect-expressroute/Architecture.png)

本文中的示例使用此图中所示的相同多租户体系结构，通过 ExpressRoute 专用对等互连将 Azure Stack 连接到 Azure。 连接方式是使用站点到站点 VPN 连接从 Azure Stack 中的虚拟网络网关连接到 ExpressRoute 路由器。

本文中的步骤说明如何在两个 VNet 之间创建端到端连接：从 Azure Stack 中的不同租户连接到它们在 Azure 中的相应 VNet。 设置两个租户是可选的操作；也可以针对单个租户使用这些步骤。

## <a name="configure-azure-stack"></a>配置 Azure Stack

若要为第一个租户设置 Azure Stack 环境，请使用以下步骤作为指导。 若要设置多个租户，请重复这些步骤：

>[!NOTE]
>以下步骤说明如何使用 Azure Stack 门户创建资源，但也可以使用 PowerShell。

![Azure Stack 网络设置](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>开始之前

在开始配置 Azure Stack 之前，需要：

* Azure Stack 集成系统部署，或 Azure Stack 开发工具包 (ASDK) 部署。 有关部署 ASDK 的信息，请参阅 [Azure Stack 开发工具包部署快速入门](../asdk/asdk-download.md)。
* Azure Stack 中可供用户订阅的套餐。 有关详细信息，请参阅[计划、套餐和订阅](azure-stack-plan-offer-quota-overview.md)。

### <a name="create-network-resources-in-azure-stack"></a>在 Azure Stack 中创建网络资源

使用以下过程在 Azure Stack 中创建租户所需的网络资源。

#### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 登录到 Azure Stack 用户门户。

2. 在门户中，选择“+ 创建资源”。

3. 在“Azure 市场”下，选择“网络”。

4. 在“特色”下，选择“虚拟网络”。

5. 在“创建虚拟网络”下，将下表中显示的值输入相应字段中：

   |字段  |值  |
   |---------|---------|
   |姓名     |Tenant1VNet1         |
   |地址空间     |10.1.0.0/16|
   |子网名称     |Tenant1-Sub1|
   |子网地址范围     |10.1.1.0/24|

6. 此时会看到此前创建的订阅填充到“订阅”字段中。 对于其余字段：

    * 在“资源组”下，选择“新建”以创建新资源组；如果已有一个资源组，请选择“使用现有项”。
    * 确认默认“位置”。
    * 单击“创建”。
    * （可选）单击“固定到仪表板”。

#### <a name="create-the-gateway-subnet"></a>创建网关子网

1. 在“虚拟网络”下，选择“Tenant1VNet1”。
1. 在“设置”下选择“子网”。
1. 选择“+ 网关子网”，将网关子网添加到虚拟网络。
1. 默认情况下，子网的名称设置为 **GatewaySubnet**。 网关子网很特殊，必须使用此名称才能正常运行。
1. 确认“地址范围”是否为 **10.1.0.0/24**。
1. 单击“确定”创建网关子网。

#### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure Stack 用户门户中，单击“+ 创建资源”。
1. 在“Azure 市场”下，选择“网络”。
1. 从网络资源列表中选择“虚拟网关”。
1. 在“名称”字段中，输入 **GW1**。
1. 选择“虚拟网络”。
1. 从下拉列表中选择“Tenant1VNet1”。
1. 依次选择“公共 IP 地址”、“选择公共 IP 地址”，然后单击“新建”。
1. 在“名称”字段中键入 **GW1-PiP**，然后单击“确定”。
1. 默认情况下，应已选择“基于路由”作为“VPN 类型”。 保留该设置。
1. 验证“订阅”和“位置”是否正确。 单击“创建”。

#### <a name="create-the-local-network-gateway"></a>创建本地网关

本地网络网关资源识别位于 VPN 连接另一端的远程网关。 在本示例中，连接的远程端是 ExpressRoute 路由器的 LAN 子接口。 对于上图中的租户 1，远程地址为 10.60.3.255。

1. 登录到 Azure Stack 用户门户，然后选择 " **+ 创建资源**"。
1. 在“Azure 市场”下，选择“网络”。
1. 从资源列表中选择“本地网关”。
1. 在“名称”字段中，键入 **ER-Router-GW**。
1. 对于“IP 地址”字段，请参阅上图。 对于租户 1，ExpressRoute 路由器的 LAN 子接口 IP 地址是 10.60.3.255。 根据自己的环境，输入路由器对应接口的 IP 地址。
1. 在“地址空间”字段中，输入 Azure 中要连接到的 VNet 的地址空间。 租户 1 的子网如下所示：

   * 192.168.2.0/24 是 Azure 中的中心 VNet。
   * 10.100.0.0/16 是 Azure 中的分支 VNet。

   > [!IMPORTANT]
   > 对于 Azure Stack 网关和 ExpressRoute 路由器之间的站点到站点 VPN 连接，本示例假设使用静态路由。

1. 确认“订阅”、“资源组”和“位置”正确无误。 然后选择“创建”。

#### <a name="create-the-connection"></a>创建连接

1. 在 Azure Stack 用户门户中，选择“+ 创建资源”。
1. 在“Azure 市场”下，选择“网络”。
1. 从资源列表中选择“连接”。
1. 在“基本设置”下，选择“站点到站点(IPSec)”作为“连接类型”。
1. 选择“订阅”、“资源组”和“位置”。 单击 **“确定”** 。
1. 在“设置”下，依次选择“虚拟网络网关”、“GW1”。
1. 依次选择“本地网络网关”、“ER Router GW”。
1. 在“连接名称”字段中，输入 **ConnectToAzure**。
1. 在“共享密钥(PSK)”字段中，输入 **abc123** 并选择“确定”。
1. 在“摘要”下，选择“确定”。

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>获取虚拟网络网关的公共 IP 地址

创建虚拟网络网关后，可以获取网关的公共 IP 地址。 请记下此地址，因为稍后需要在部署中使用。 根据部署，此地址将用作**内部 IP 地址**。

1. 在 Azure Stack 用户门户中，选择“所有资源”。
1. 在“所有资源”下选择虚拟网络网关（在本示例中为 **GW1**）。
1. 在“虚拟网络网关”下，从资源列表中选择“概述”。 或者，可以选择“属性”。
1. 要记下的 IP 地址列在“公共 IP 地址”下。 对于示例配置，此地址为 192.68.102.1。

#### <a name="create-a-virtual-machine-vm"></a>创建虚拟机 (VM)

若要通过 VPN 连接测试数据流量，需要 Vm 在 Azure Stack VNet 中发送和接收数据。 创建 VM，并将其部署到虚拟网络的 VM 子网。

1. 在 Azure Stack 用户门户中，选择“+ 创建资源”。
1. 在“Azure 市场”下，选择“计算”。
1. 在 VM 映像列表中，选择“Windows Server 2016 Datacenter Eval”映像。

   >[!NOTE]
   >如果用于本文的映像不可用，请让 Azure Stack 运营商提供不同的 Windows Server 映像。

1. 在“创建虚拟机”中选择“基本信息”，然后键入 **VM01** 作为**名称**。
1. 输入有效的用户名和密码。 创建 VM 后，将使用此帐户登录到该 VM。
1. 提供“订阅”、“资源组”和“位置”。 选择“确定”。
1. 在 "**选择大小**" 下，为此实例选择 VM 大小，然后选择 "**选择**"。
1. 在“设置”下，确认：

   * 虚拟网络为 **Tenant1VNet1**。
   * 子网已设置为 **10.1.1.0/24**。

   使用默认设置，并单击“确定”。

1. 在“摘要”下检查 VM 配置，然后单击“确定”。

若要添加更多租户，请重复以下部分中执行的步骤：

* [创建虚拟网络和 VM 子网](#create-the-virtual-network-and-vm-subnet)
* [创建网关子网](#create-the-gateway-subnet)
* [创建虚拟网络网关](#create-the-virtual-network-gateway)
* [创建本地网络网关](#create-the-local-network-gateway)
* [创建连接](#create-the-connection)
* [创建虚拟机](#create-a-virtual-machine)

如果使用租户2作为示例，请记住更改 IP 地址，以避免重叠。

### <a name="configure-the-nat-vm-for-gateway-traversal"></a>为网关遍历配置 NAT VM

> [!IMPORTANT]
> 本部分仅适用于 ASDK 部署。 多节点部署无需 NAT。

ASDK 是独立的，并且与部署物理主机的网络隔离。 网关连接到的 VIP 网络不是在外部，而是隐藏在执行网络地址转换 (NAT) 的路由器后面。

路由器是运行路由和远程访问服务（RRAS）角色的 ASDK 主机。 你必须在 ASDK 主机上配置 NAT，以允许站点到站点 VPN 连接两端连接。

#### <a name="configure-the-nat"></a>配置 NAT

1. 用管理员帐户登录到 Azure Stack 主计算机。
1. 在提升的 PowerShell ISE 中运行脚本。 此脚本返回**外部 BGPNAT 地址**。

   ```powershell
   Get-NetNatExternalAddress
   ```

1. 若要配置 NAT，请复制并编辑以下 PowerShell 脚本。 编辑脚本，将 `External BGPNAT address` 和 `Internal IP address` 替换为以下示例值：

   * 对于“外部 BGPNAT 地址”，请使用 10.10.0.62
   * 对于“内部 IP 地址”，请使用 192.168.102.1

   在权限提升的 PowerShell ISE 中运行以下脚本：

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500
   ```

## <a name="configure-azure"></a>配置 Azure

完成 Azure Stack 的配置后，可以部署 Azure 资源。 下图显示了 Azure 中的租户虚拟网络示例。 对于 Azure 中的 VNet，可以使用任何名称和地址方案。 但是，在 Azure 和 Azure Stack 中，VNet 的地址范围必须唯一，不得重叠：

![Azure VNet](media/azure-stack-connect-expressroute/AzureArchitecture.png)

在 Azure 中部署的资源类似于在 Azure Stack 中部署的资源。 部署以下组件：

* 虚拟网络和子网
* 网关子网
* 虚拟网络网关
* 连接
* ExpressRoute 线路

示例 Azure 网络基础结构的配置方式如下：

* 标准的中心 (192.168.2.0/24) 和分支 (10.100.0.0./16) VNet 模型。 有关中心辐射型网络拓扑的详细信息，请参阅[在 Azure 中实现中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。
* 工作负荷部署在分支 VNet 中，ExpressRoute 线路连接到中心 VNet。
* 使用 VNet 对等互连连接这两个 VNet。

### <a name="configure-the-azure-vnets"></a>配置 Azure VNet

1. 使用 Azure 凭据登录到 Azure 门户。
1. 使用 192.168.2.0/24 地址范围创建中心 VNet。
1. 使用 192.168.2.0/25 地址范围创建子网，使用 192.168.2.128/27 地址范围添加网关子网。
1. 使用 10.100.0.0/16 地址范围创建分支 VNet 和子网。

有关在 Azure 中创建虚拟网络的详细信息，请参阅[创建虚拟网络](/azure/virtual-network/manage-virtual-network#create-a-virtual-network)。

### <a name="configure-an-expressroute-circuit"></a>配置 ExpressRoute 线路

1. 查看 [ExpressRoute 先决条件和查检表](/azure/expressroute/expressroute-prerequisites)中的 ExpressRoute 先决条件。

1. 根据[创建和修改 ExpressRoute 线路](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager)中的步骤，使用 Azure 订阅创建 ExpressRoute 线路。

   >[!NOTE]
   >将线路的服务密钥提供给服务提供商，使提供商能够在他们一端设置 ExpressRoute 线路。

1. 根据[创建和修改 ExpressRoute 线路的对等互连](/azure/expressroute/expressroute-howto-routing-portal-resource-manager)中的步骤，在 ExpressRoute 线路上配置专用对等互连。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

根据[使用 PowerShell 配置 ExpressRoute 的虚拟网络网关](/azure/expressroute/expressroute-howto-add-gateway-resource-manager)中的步骤，在中心 VNet 中为 ExpressRoute 创建虚拟网络网关。

### <a name="create-the-connection"></a>创建连接

若要将 ExpressRoute 线路链接到中心 VNet，请遵循[将虚拟网络连接到 ExpressRoute 线路](/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)中的步骤。

### <a name="peer-the-vnets"></a>将 VNet 对等互连

根据[使用 Azure 门户创建虚拟网络对等互连](/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal)中的步骤，在中心与分支 VNet 之间建立对等互连。 配置 VNet 对等互连时，请务必使用以下选项：

* 从中心到分支：“允许网关传输”。
* 从分支到中心：“使用远程网关”。

### <a name="create-a-virtual-machine"></a>创建虚拟机

将工作负荷 Vm 部署到分支 VNet。

针对 Azure 中要通过相应 ExpressRoute 线路连接的其他任何租户 VNet，重复这些步骤。

## <a name="configure-the-router"></a>配置路由器

可以使用以下 ExpressRoute 路由器配置示意图作为指导来配置 ExpressRoute 路由器。 此图显示了两个租户（租户 1 和租户 2）及其各自的 ExpressRoute 线路。 每个租户链接到各自在 ExpressRoute 路由器的 LAN 和 WAN 端的 VRF（虚拟路由和转发）。 此配置可确保在两个租户之间保持端到端隔离。 在学习示例配置的过程中，请记下路由器接口中使用的 IP 地址。

![ExpressRoute 路由器配置](media/azure-stack-connect-expressroute/EndToEnd.png)

可以使用任何支持 IKEv2 VPN 和 BGP 的路由器，来终止 Azure Stack 的站点到站点 VPN 连接。 同一路由器用于通过 ExpressRoute 线路连接到 Azure。

以下 Cisco Site Recovery 1000 序列聚合服务路由器配置示例支持*ExpressRoute 路由器配置*关系图中显示的网络基础结构。

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>测试连接

建立站点到站点连接和 ExpressRoute 线路之后，请测试连接。

执行以下 ping 测试：

* 登录到 Azure VNet 中的某个 Vm，并对在 Azure Stack 中创建的 VM 执行 ping 操作。
* 登录到在 Azure Stack 中创建的其中一个 Vm，并对在 Azure VNet 中创建的 VM 执行 ping 操作。

>[!NOTE]
>若要确保通过站点到站点连接和 ExpressRoute 连接发送流量，必须在两端 ping VM 的专用 IP （DIP）地址，而不是 VM 的 VIP 地址。

### <a name="allow-icmp-in-through-the-firewall"></a>允许 ICMP 通过防火墙

默认情况下，Windows Server 2016 不允许通过防火墙传入 ICMP 数据包。 对于用于 ping 测试的每个 VM，都必须允许传入 ICMP 数据包。 若要为 ICMP 创建防火墙规则，请在权限提升的 PowerShell 窗口中运行以下 cmdlet：

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  -DisplayName "Allow ICMPv4-In" `
  -Protocol ICMPv4
```

### <a name="ping-the-azure-stack-vm"></a>Ping Azure Stack VM

1. 登录到 Azure Stack 用户门户。

1. 找到创建的 VM 并选择它。

1. 选择“连接”。

1. 在权限提升的 Windows 或 PowerShell 命令提示符下，输入 **ipconfig /all**。 记下输出中返回的 IPv4 地址。

1. 从 Azure VNet 中的 VM 对 IPv4 地址执行 Ping 操作。

   在示例环境中，该 IPv4 地址来自 10.1.1.x/24 子网。 在你的环境中，该地址可能不同，但是，该地址应在为租户 VNet 子网创建的子网中。

### <a name="view-data-transfer-statistics"></a>查看数据传输统计信息

若要了解有多少流量通过连接，可以在 Azure Stack 用户门户中查找此信息。 查看数据传输统计信息也是一种很好的方法，可查明 ping 测试数据是否通过 VPN 和 ExpressRoute 连接：

1. 登录 Azure Stack 用户门户并选择“所有资源”。
1. 导航到 VPN 网关的资源组，然后选择“连接”对象类型。
1. 从列表中选择“ConnectToAzure”连接。
1. 在“连接” > “概述”下，可以看到“传入数据”和“传出数据”的统计信息。应会看到一些非零值。

   ![“传入数据”和“传出数据”](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>后续步骤

[将应用部署到 Azure 和 Azure Stack](../user/azure-stack-solution-pipeline.md )
