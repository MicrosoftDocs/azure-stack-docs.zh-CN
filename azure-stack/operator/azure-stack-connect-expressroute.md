---
title: 使用 ExpressRoute 将 Azure Stack 集线器连接到 Azure |Microsoft Docs
description: 了解如何使用 ExpressRoute 将 Azure Stack 集线器中的虚拟网络连接到 Azure 中的虚拟网络。
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
ms.openlocfilehash: 1e476f29909d3057deaf9331d11b780c1a887e6c
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804973"
---
# <a name="connect-azure-stack-hub-to-azure-using-azure-expressroute"></a>使用 Azure ExpressRoute 将 Azure Stack 中心连接到 Azure

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

本文介绍如何使用[Microsoft Azure ExpressRoute](/azure/expressroute/)的直接连接将 Azure Stack 集线器虚拟网络连接到 Azure 虚拟网络。

可以使用本文作为教程，并使用示例设置相同的测试环境。 或者，您可以使用本文作为指导您设置自己的 ExpressRoute 环境的演练。

## <a name="overview-assumptions-and-prerequisites"></a>概述、假设和先决条件

使用 Azure ExpressRoute 可以通过连接服务提供商提供的专用连接将本地网络扩展到 Microsoft 云。 ExpressRoute 不是通过公共 internet 的 VPN 连接。

有关 Azure ExpressRoute 的详细信息，请参阅[ExpressRoute 概述](/azure/expressroute/expressroute-introduction)。

### <a name="assumptions"></a>假设

本文假定：

* 你有 Azure 的工作知识。
* 你对 Azure Stack 中心有一个基本的了解。
* 你基本了解网络。

### <a name="prerequisites"></a>必备组件

若要使用 ExpressRoute 连接 Azure Stack 集线器和 Azure，必须满足以下要求：

* 通过[连接提供商](/azure/expressroute/expressroute-locations)预配的[ExpressRoute 线路](/azure/expressroute/expressroute-circuit-peerings)。
* 用于在 Azure 中创建 ExpressRoute 线路和 Vnet 的 Azure 订阅。
* 必须执行以下操作的路由器：
  * 支持其 LAN 接口与 Azure Stack 中心多租户网关之间的站点到站点 VPN 连接。
  * 如果 Azure Stack 中心部署中有多个租户，则支持创建多个 Vrf （虚拟路由和转发）。
* 具有以下内容的路由器：
  * 连接到 ExpressRoute 线路的 WAN 端口。
  * 连接到 Azure Stack 中心多租户网关的 LAN 端口。

### <a name="expressroute-network-architecture"></a>ExpressRoute 网络体系结构

下图显示了使用本文中的示例完成 ExpressRoute 设置后，Azure Stack 中心和 Azure 环境：

![ExpressRoute 网络](media/azure-stack-connect-expressroute/Conceptual.png)

下图显示了多个租户如何从 Azure Stack 中心基础结构通过 ExpressRoute 路由器连接到 Azure：

![与 ExpressRoute 建立多租户连接](media/azure-stack-connect-expressroute/Architecture.png)

本文中的示例使用此关系图中所示的相同多租户体系结构，使用 ExpressRoute 专用对等互连将 Azure Stack 集线器连接到 Azure。 使用从 Azure Stack 集线器中的虚拟网络网关到 ExpressRoute 路由器的站点到站点 VPN 连接来完成连接。

本文中的步骤说明如何在两个 Vnet 之间创建从 Azure Stack 集线器中的两个不同租户到 Azure 中的相应 Vnet 的端到端连接。 设置两个租户是可选的;你还可以将这些步骤用于单个租户。

## <a name="configure-azure-stack-hub"></a>配置 Azure Stack 集线器

若要为第一个租户设置 Azure Stack 中心环境，请使用以下步骤作为指导。 如果要设置多个租户，请重复以下步骤：

>[!NOTE]
>以下步骤演示如何使用 Azure Stack 中心门户创建资源，但也可以使用 PowerShell。

![Azure Stack 中心网络设置](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>开始之前

开始配置 Azure Stack 集线器之前，需要：

* Azure Stack 集线器集成系统部署或 Azure Stack 开发工具包（ASDK）部署。 有关部署 ASDK 的信息，请参阅[Azure Stack 开发工具包部署快速入门](../asdk/asdk-download.md)。
* 用户可以订阅 Azure Stack 中心提供的产品/服务。 有关详细信息，请参阅 "[服务、计划、产品/服务、订阅概述](service-plan-offer-subscription-overview.md)"。

### <a name="create-network-resources-in-azure-stack-hub"></a>在 Azure Stack 集线器中创建网络资源

使用以下过程在 Azure Stack Hub 中为租户创建所需的网络资源。

#### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 登录到 Azure Stack 集线器用户门户。

2. 在门户中，选择 " **+ 创建资源**"。

3. 在 " **Azure Marketplace**" 下，选择 "**网络**"。

4. 在 "**特色**" 下，选择 "**虚拟网络**"。

5. 在 "**创建虚拟网络**" 下，将下表中显示的值输入到相应字段中：

   |字段  |值  |
   |---------|---------|
   |名称     |Tenant1VNet1         |
   |地址空间     |10.1.0.0/16|
   |子网名称     |Tenant1-Sub1|
   |子网地址范围     |10.1.1.0/24|

6. 你应该会看到之前创建的订阅已在 "**订阅**" 字段中填充。 对于其余字段：

    * 在 "**资源组**" 下，选择 "**新建**" 以创建新的资源组，或选择 "**使用现有**资源组"。
    * 验证默认**位置**。
    * 单击“创建”。
    * 可有可无单击 "**固定到仪表板**"。

#### <a name="create-the-gateway-subnet"></a>创建网关子网

1. 在 "**虚拟网络**" 下，选择**Tenant1VNet1**。
1. 在“设置”下选择“子网”。
1. 选择 " **+ 网关子网**"，将网关子网添加到虚拟网络。
1. 默认情况下，子网的名称设置为 **GatewaySubnet**。 网关子网是一种特殊情况，必须使用此名称才能正常工作。
1. 验证**地址范围**是否为**10.1.0.0/24**。
1. 单击“确定”创建网关子网。

#### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure Stack 集线器用户门户中，单击 " **+ 创建资源**"。
1. 在 " **Azure Marketplace**" 下，选择 "**网络**"。
1. 从网络资源列表中选择“虚拟网关”。
1. 在 "**名称**" 字段中，输入**GW1**。
1. 选择“虚拟网络”。
1. 从下拉列表中选择 " **Tenant1VNet1** "。
1. 选择 "**公共 ip 地址**"，**选择 "公共 ip 地址**"，然后单击 "**新建**"。
1. 在 "**名称**" 字段中，键入**GW1-PiP**，然后单击 **"确定"** 。
1. 默认情况下，应已选择“基于路由”作为“VPN 类型”。 保留该设置。
1. 验证“订阅”和“位置”是否正确。 单击“创建”。

#### <a name="create-the-local-network-gateway"></a>创建本地网关

本地网络网关资源在 VPN 连接的另一端标识远程网关。 在此示例中，连接的远程端是 ExpressRoute 路由器的 LAN 子接口。 对于上图中的租户1，远程地址为10.60.3.255。

1. 登录到 Azure Stack 集线器用户门户，然后选择 " **+ 创建资源**"。
1. 在 " **Azure Marketplace**" 下，选择 "**网络**"。
1. 从资源列表中选择“本地网关”。
1. 在 "**名称**" 字段中，键入**ER-路由器-GW**。
1. 对于 " **IP 地址**" 字段，请参阅上图。 租户1的 ExpressRoute 路由器 LAN 子接口的 IP 地址为10.60.3.255。 对于你自己的环境，输入路由器的相应接口的 IP 地址。
1. 在 "**地址空间**" 字段中，输入要在 Azure 中连接到的 vnet 的地址空间。 租户1的子网如下所示：

   * 192.168.2.0/24 是 Azure 中的集线器 VNet。
   * 10.100.0.0/16 是 Azure 中的分支 VNet。

   > [!IMPORTANT]
   > 此示例假设你使用的是 Azure Stack 中心网关和 ExpressRoute 路由器之间的站点到站点 VPN 连接的静态路由。

1. 验证你的**订阅**、**资源组**和**位置**是否正确。 然后选择“创建”。

#### <a name="create-the-connection"></a>创建连接

1. 在 Azure Stack 集线器用户门户中，选择 " **+ 创建资源**"。
1. 在 " **Azure Marketplace**" 下，选择 "**网络**"。
1. 从资源列表中选择“连接”。
1. 在 "**基本**信息" 下，选择 "**站点到站点（IPSec）** " 作为 "**连接类型**"。
1. 选择**订阅**、**资源组**和**位置**。 单击“确定”。
1. 在 "**设置**" 下，选择 "**虚拟网络网关**"，然后选择 " **GW1**"。
1. 选择 "**本地网络网关**"，然后选择 " **ER 路由器 GW**"。
1. 在 "**连接名称**" 字段中，输入**ConnectToAzure**。
1. 在 "**共享密钥（PSK）** " 字段中，输入**abc123** ，然后选择 **"确定"** 。
1. 在 "**摘要**" 下，选择 **"确定"** 。

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>获取虚拟网络网关公共 IP 地址

创建虚拟网络网关后，可以获取网关的公共 IP 地址。 记下此地址，以备以后用于部署时使用。 根据你的部署，此地址将用作**内部 IP 地址**。

1. 在 Azure Stack 集线器用户门户中，选择 "**所有资源**"。
1. 在 "**所有资源**" 下，选择虚拟网络网关，该网关在示例中为**GW1** 。
1. 在 "**虚拟网络网关**" 下，从资源列表中选择 "**概述**"。 或者，您可以选择 "**属性**"。
1. 要记录的 IP 地址列在 "**公共 ip 地址**" 下。 对于示例配置，此地址为192.68.102.1。

#### <a name="create-a-virtual-machine-vm"></a>创建虚拟机 (VM)

若要通过 VPN 连接测试数据流量，需要 Vm 在 Azure Stack Hub VNet 中发送和接收数据。 创建 VM，并将其部署到虚拟网络的 VM 子网。

1. 在 Azure Stack 集线器用户门户中，选择 " **+ 创建资源**"。
1. 在 " **Azure Marketplace**" 下，选择 "**计算**"。
1. 在 VM 映像列表中，选择 " **Windows Server 2016 Datacenter Eval** " 映像。

   >[!NOTE]
   >如果用于本文的图像不可用，请询问 Azure Stack 中心操作员提供不同的 Windows Server 映像。

1. 在 "**创建虚拟机**" 中，选择 "**基本**"，然后键入**VM01**作为**名称**。
1. 请输入有效的用户名和密码。 创建 VM 后，你将使用此帐户登录到该 VM。
1. 提供**订阅**、**资源组**和**位置**。 选择“确定”。
1. 在 "**选择大小**" 下，为此实例选择 VM 大小，然后选择 "**选择**"。
1. 在 "**设置**" 下，确认：

   * 虚拟网络为**Tenant1VNet1**。
   * 子网已设置为**10.1.1.0/24**。

   使用默认设置，再单击“确定”。

1. 在 "**摘要**" 下查看 VM 配置，然后单击 **"确定"** 。

若要添加更多租户，请重复执行以下部分中所述的步骤：

* [创建虚拟网络和 VM 子网](#create-the-virtual-network-and-vm-subnet)
* [创建网关子网](#create-the-gateway-subnet)
* [创建虚拟网络网关](#create-the-virtual-network-gateway)
* [创建本地网络网关](#create-the-local-network-gateway)
* [创建连接](#create-the-connection)
* [创建虚拟机](#create-a-virtual-machine)

如果使用租户2作为示例，请记住更改 IP 地址，以避免重叠。

### <a name="configure-the-nat-vm-for-gateway-traversal"></a>为网关遍历配置 NAT VM

> [!IMPORTANT]
> 本部分仅适用于 ASDK 部署。 此 NAT 对于多节点部署不是必需的。

ASDK 是独立的，并且与部署物理主机的网络隔离。 连接网关所连接到的 VIP 网络不是外部网络;它隐藏在执行网络地址转换（NAT）的路由器后面。

路由器是运行路由和远程访问服务（RRAS）角色的 ASDK 主机。 你必须在 ASDK 主机上配置 NAT，以允许站点到站点 VPN 连接两端连接。

#### <a name="configure-the-nat"></a>配置 NAT

1. 用管理员帐户登录到 Azure Stack 集线器主机计算机。
1. 在提升的 PowerShell ISE 中运行脚本。 此脚本返回**外部 BGPNAT 地址**。

   ```powershell
   Get-NetNatExternalAddress
   ```

1. 若要配置 NAT，请复制并编辑以下 PowerShell 脚本。 编辑脚本以将 `External BGPNAT address` 和 `Internal IP address` 替换为以下示例值：

   * 对于*EXTERNAL BGPNAT address* ，请使用10.10.0.62
   * 对于*内部 IP 地址*，请使用192.168.102。1

   从提升权限的 PowerShell ISE 中运行以下脚本：

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

Azure Stack 中心配置完成后，可以部署 Azure 资源。 下图显示了 Azure 中的租户虚拟网络的示例。 可以在 Azure 中使用 VNet 的任何名称和寻址方案。 但是，Azure 中 Vnet 的地址范围和 Azure Stack 中心必须唯一且不得重叠：

![Azure Vnet](media/azure-stack-connect-expressroute/AzureArchitecture.png)

在 Azure 中部署的资源类似于在 Azure Stack 中心部署的资源。 部署以下组件：

* 虚拟网络和子网
* 网关子网
* 虚拟网络网关
* 连接
* ExpressRoute 线路

示例 Azure 网络基础结构配置如下：

* 标准集线器（192.168.2.0/24）和分支（10.100.0.0） VNet 模型。 有关中心辐射型网络拓扑的详细信息，请参阅[在 Azure 中实现中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。
* 工作负荷部署在分支 VNet 中，ExpressRoute 线路连接到集线器 VNet。
* 这两个 Vnet 使用 VNet 对等互连进行连接。

### <a name="configure-the-azure-vnets"></a>配置 Azure Vnet

1. 用你的 Azure 凭据登录到 Azure 门户。
1. 使用 192.168.2.0/24 地址范围创建集线器 VNet。
1. 使用 192.168.2.0/25 地址范围创建子网，并使用 192.168.2.128/27 地址范围添加网关子网。
1. 使用 10.100.0.0/16 地址范围创建辐射 VNet 和子网。

有关在 Azure 中创建虚拟网络的详细信息，请参阅[创建虚拟网络](/azure/virtual-network/manage-virtual-network#create-a-virtual-network)。

### <a name="configure-an-expressroute-circuit"></a>配置 ExpressRoute 线路

1. 查看[expressroute 先决条件 & 清单](/azure/expressroute/expressroute-prerequisites)中的 expressroute 必备组件。

1. 按照[创建和修改 ExpressRoute 线路](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager)中的步骤使用 Azure 订阅创建 expressroute 线路。

   >[!NOTE]
   >向服务提供线路服务密钥，以便他们可以在其一端设置 ExpressRoute 线路。

1. 按照为[expressroute 线路创建和修改对等互连](/azure/expressroute/expressroute-howto-routing-portal-resource-manager)中的步骤，在 expressroute 线路上配置专用对等互连。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

按照[使用 PowerShell 配置 expressroute 的虚拟网络网关](/azure/expressroute/expressroute-howto-add-gateway-resource-manager)中的步骤在中心 VNet 中为 expressroute 创建虚拟网络网关。

### <a name="create-the-connection"></a>创建连接

若要将 ExpressRoute 线路链接到中心 VNet，请按照[将虚拟网络连接到 ExpressRoute 线路](/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)中的步骤操作。

### <a name="peer-the-vnets"></a>将 VNet 对等互连

对等中心和辐射 Vnet 使用使用[Azure 门户创建虚拟网络对等互连](/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal)中的步骤。 配置 VNet 对等互连时，请确保使用以下选项：

* 从中心到辐射，**允许网关传输**。
* 从分支到中心，**使用远程网关**。

### <a name="create-a-virtual-machine"></a>创建虚拟机

将工作负荷 Vm 部署到分支 VNet。

对于想要在 Azure 中通过各自的 ExpressRoute 线路连接的任何其他租户 Vnet，请重复这些步骤。

## <a name="configure-the-router"></a>配置路由器

你可以使用以下 ExpressRoute 路由器配置关系图作为配置 ExpressRoute 路由器的指南。 此图显示了两个租户（租户1和租户2）及其各自的 ExpressRoute 线路。 每个租户都链接到 ExpressRoute 路由器的 LAN 和 WAN 端各自的 VRF （虚拟路由和转发）。 此配置可确保两个租户之间的端对端隔离。 按照配置示例操作，记下路由器接口中使用的 IP 地址。

![ExpressRoute 路由器配置](media/azure-stack-connect-expressroute/EndToEnd.png)

你可以使用任何支持 IKEv2 VPN 和 BGP 的路由器，以终止 Azure Stack 集线器中的站点到站点 VPN 连接。 使用 ExpressRoute 线路连接到 Azure 时，使用同一个路由器。

以下 Cisco ASR 1000 序列聚合服务路由器配置示例支持*ExpressRoute 路由器配置*关系图中所示的网络基础结构。

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

建立站点到站点连接和 ExpressRoute 线路后，请测试连接。

执行以下 ping 测试：

* 登录到 Azure VNet 中的某个 Vm，并对在 Azure Stack Hub 中创建的 VM 执行 ping 操作。
* 登录到在 Azure Stack 集线器中创建的其中一个 Vm，并对在 Azure VNet 中创建的 VM 执行 ping 操作。

>[!NOTE]
>若要确保通过站点到站点连接和 ExpressRoute 连接发送流量，必须在两端 ping VM 的专用 IP （DIP）地址，而不是 VM 的 VIP 地址。

### <a name="allow-icmp-in-through-the-firewall"></a>允许 ICMP 进入防火墙

默认情况下，Windows Server 2016 不允许通过防火墙传入 ICMP 数据包。 对于用于 ping 测试的每个 VM，都必须允许传入 ICMP 数据包。 若要为 ICMP 创建防火墙规则，请在提升的 PowerShell 窗口中运行以下 cmdlet：

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  -DisplayName "Allow ICMPv4-In" `
  -Protocol ICMPv4
```

### <a name="ping-the-azure-stack-hub-vm"></a>Ping Azure Stack 中心 VM

1. 登录到 Azure Stack 集线器用户门户。

1. 找到创建的 VM 并选择它。

1. 选择“连接”。

1. 在提升的 Windows 或 PowerShell 命令提示符下，输入**ipconfig/all**。 请注意输出中返回的 IPv4 地址。

1. 从 Azure VNet 中的 VM 对 IPv4 地址执行 Ping 操作。

   在示例环境中，IPv4 地址来自 10.1.1/24 子网。 在您的环境中，该地址可能不同，但它应位于为租户 VNet 子网创建的子网中。

### <a name="view-data-transfer-statistics"></a>查看数据传输统计信息

如果要了解通过连接传递的流量，可以在 Azure Stack 集线器用户门户中找到此信息。 查看数据传输统计信息也是一种很好的方法，可查明 ping 测试数据是否通过 VPN 和 ExpressRoute 连接：

1. 登录到 Azure Stack 集线器用户门户，并选择 "**所有资源**"。
1. 导航到 VPN 网关的资源组，并选择 "**连接**" 对象类型。
1. 从列表中选择 " **ConnectToAzure** " 连接。
1. 在 "**连接** > **概述**" 下，可以查看**中的数据**和**数据输出**的统计信息。应会看到一些非零值。

   ![传入和传出数据](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>后续步骤

[将应用部署到 Azure 和 Azure Stack 中心](../user/azure-stack-solution-pipeline.md)
