---
title: Configure VPN gateway settings for Azure Stack | Microsoft Docs
description: Learn about and configure VPN gateways settings for Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: e6d7f2d46a578bbbc8527a5e69f441ec12f38b01
ms.sourcegitcommit: ac7d98a2b58442e82798022d69ebfae6616a225f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239311"
---
# <a name="configure-vpn-gateway-settings-for-azure-stack"></a>Configure VPN gateway settings for Azure Stack

*Applies to: Azure Stack integrated systems and Azure Stack Development Kit*

A VPN gateway is a type of virtual network gateway that sends encrypted traffic between your virtual network in Azure Stack and a remote VPN gateway. The remote VPN gateway can be in Azure, a device in your datacenter, or a device on another site. If there is network connectivity between the two endpoints, you can establish a secure Site-to-Site (S2S) VPN connection between the two networks.

VPN 网关连接依赖于多个资源配置，其中每个资源包含可配置的设置。 This article describes the resources and settings that relate to a VPN gateway for a virtual network that you create in the Resource Manager deployment model. You can find descriptions and topology diagrams for each connection solution in [About VPN Gateway for Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>VPN gateway settings

### <a name="gateway-types"></a>网关类型

Each Azure Stack virtual network supports a single virtual network gateway, which must be of the type **Vpn**.  This support is different from Azure, which supports additional types.

When you create a virtual network gateway, you must make sure that the gateway type is correct for your configuration. A VPN gateway requires the `-GatewayType Vpn` flag; for example:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

### <a name="gateway-skus"></a>网关 SKU

When you create a virtual network gateway, you must specify the gateway SKU that you want to use. 根据工作负荷、吞吐量、功能和、SLA 的类型，选择满足需求的 SKU。

Azure Stack offers the VPN gateway SKUs shown in the following table:

| | VPN gateway throughput |VPN gateway maximum IPsec tunnels |
|-------|-------|-------|
|**基本 SKU**  | 100 Mbps  | 20    |
|**标准 SKU**   | 100 Mbps  | 20 |
|**高性能 SKU** | 200 Mbps | 10 |

### <a name="resizing-gateway-skus"></a>Resizing gateway SKUs

Azure Stack does not support a resize of SKUs between the supported legacy SKUs.

Similarly, Azure Stack does not support a resize from a supported legacy SKU (**Basic**, **Standard**, and **HighPerformance**) to a newer SKU supported by Azure (**VpnGw1**, **VpnGw2**, and **VpnGw3**).

### <a name="configure-the-gateway-sku"></a>配置网关 SKU

#### <a name="azure-stack-portal"></a>Azure Stack portal

If you use the Azure Stack portal to create a Resource Manager virtual network gateway, you can select the gateway SKU by using the dropdown list. The options correspond to the gateway type and VPN type that you select.

#### <a name="powershell"></a>PowerShell

The following PowerShell example specifies the `-GatewaySku` parameter as **Standard**:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>连接类型

在 Resource Manager 部署模型中，每个配置都需要特定的虚拟网络网关连接类型。 The available Resource Manager PowerShell values for `-ConnectionType` are **IPsec**.

In the following PowerShell example, a S2S connection is created that requires the IPsec connection type:

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN types

为 VPN 网关配置创建虚拟网络网关时，必须指定 VPN 类型。 选择的 VPN 类型取决于要创建的连接拓扑。 A VPN type can also depend on the hardware that you're using. S2S 配置需要 VPN 设备。 有些 VPN 设备仅支持特定的 VPN 类型。

> [!IMPORTANT]  
> Currently, Azure Stack only supports the route-based VPN type. If your device only supports policy-based VPNs, then connections to those devices from Azure Stack are not supported.  
>
> In addition, Azure Stack does not support using policy-based traffic selectors for route-based gateways at this time, because custom IPSec/IKE policy configurations are not supported.

* **PolicyBased**: Policy-based VPNs encrypt and direct packets through IPsec tunnels based on the IPsec policies that are configured with the combinations of address prefixes between your on-premises network and the Azure Stack VNet. The policy, or traffic selector, is usually an access list in the VPN device configuration.

  >[!NOTE]
  >**PolicyBased** is supported in Azure, but not in Azure Stack.

* **RouteBased**: Route-based VPNs use routes that are configured in the IP forwarding or routing table to direct packets to their corresponding tunnel interfaces. 然后，隧道接口会加密或解密出入隧道的数据包。 The policy, or traffic selector, for **RouteBased** VPNs are configured as any-to-any (or use wild cards). By default, they cannot be changed. The value for a **RouteBased** VPN type is **RouteBased**.

以下 PowerShell 示例将 `-VpnType` 指定为**基于路由**。 When you create a gateway, you must make sure that the `-VpnType` is correct for your configuration.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Gateway requirements

The following table lists the requirements for VPN gateways.

| |Policy-based Basic VPN Gateway | Route-based Basic VPN Gateway | Route-based Standard VPN Gateway | Route-based High Performance VPN Gateway|
|--|--|--|--|--|
| **Site-to-Site connectivity (S2S connectivity)** | 不支持 | Route-based VPN configuration | Route-based VPN configuration | Route-based VPN configuration |
| **身份验证方法**  | 不支持 | Pre-shared key for S2S connectivity  | Pre-shared key for S2S connectivity  | Pre-shared key for S2S connectivity  |
| **S2S 连接的最大数目**  | 不支持 | 20 | 20| 10|
|**活动路由支持 (BGP)** | 不支持 | 不支持 | 受支持 | 受支持 |

### <a name="gateway-subnet"></a>Gateway subnet

在创建 VPN 网关之前，必须创建一个网关子网。 The gateway subnet has the IP addresses that the virtual network gateway VMs and services use. 在创建虚拟网络网关时，将网关 VM 部署到网关子网，并使用所需的 VPN 网关设置进行配置。 Don't deploy anything else (for example, additional VMs) to the gateway subnet.

>[!IMPORTANT]
>网关子网必须命名为 **GatewaySubnet** 才能正常工作。 Azure Stack uses this name to identify the subnet to which to deploy the virtual network gateway VMs and services.

创建网关子网时，请指定子网包含的 IP 地址数。 将网关子网中的 IP 地址分配到网关 VM 和网关服务。 有些配置需要具有比其他配置更多的 IP 地址。 查看要创建的配置的说明，验证想要创建的网关子网是否会满足这些要求。

Additionally, you should make sure your gateway subnet has enough IP addresses to handle additional future configurations. Although you can create a gateway subnet as small as /29, we recommend you create a gateway subnet of /28 or larger (/28, /27, /26, and so on.) That way, if you add functionality in the future, you do not have to tear down your gateway, then delete and recreate the gateway subnet to allow for more IP addresses.

The following Resource Manager PowerShell example shows a gateway subnet named **GatewaySubnet**. 可以看到，CIDR 表示法指定了 /27，这可提供足够的 IP 地址供大多数现有配置使用。

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> 使用网关子网时，避免将网络安全组 (NSG) 与网关子网关联。 Associating a network security group to this subnet can cause your VPN gateway to stop functioning as expected. For more information about network security groups, see [What is a network security group?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>本地网关

When creating a VPN gateway configuration in Azure, the local network gateway often represents your on-premises location. In Azure Stack, it represents any remote VPN device that sits outside Azure Stack. This device could be a VPN device in your datacenter (or a remote datacenter), or a VPN gateway in Azure.

You give the local network gateway a name, the public IP address of the VPN device, and specify the address prefixes that are on the on-premises location. Azure looks at the destination address prefixes for network traffic, consults the configuration that you've specified for your local network gateway, and routes packets accordingly.

This PowerShell example creates a new local network gateway:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Sometimes you need to modify the local network gateway settings; for example, when you add or modify the address range, or if the IP address of the VPN device changes. For more info, see [Modify local network gateway settings using PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>IPsec/IKE parameters

When you set up a VPN connection in Azure Stack, you must configure the connection at both ends. If you're configuring a VPN connection between Azure Stack and a hardware device such as a switch or router that is acting as a VPN gateway, that device might ask you for additional settings.

Unlike Azure, which supports multiple offers as both an initiator and a responder, Azure Stack supports only one offer by default. If you need to use different IPSec/IKE settings to work with your VPN device, there are more settings available to you to configure your connection manually. For more information, see [Configure IPsec/IKE policy for site-to-site VPN connections](azure-stack-vpn-s2s.md).

### <a name="ike-phase-1-main-mode-parameters"></a>IKE 阶段 1（主模式）参数

| properties              | Value|
|-|-|
| SDK 版本           | IKEv2 |
|Diffie-Hellman 组   | ECP384 |
| 身份验证方法 | 预共享密钥 |
|加密和哈希算法 | AES256, SHA384 |
|SA 生存期（时间）     | 28,800 秒|

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 阶段 2（快速模式）参数

| properties| Value|
|-|-|
|SDK 版本 |IKEv2 |
|Encryption & Hashing Algorithms (Encryption)     | GCMAES256|
|Encryption & Hashing Algorithms (Authentication) | GCMAES256|
|SA 生存期（时间）  | 27,000 秒  |
|SA Lifetime (Kilobytes) | 33,553,408     |
|完全向前保密 (PFS) | ECP384 |
|死对等体检测 | 受支持|  

## <a name="next-steps"></a>后续步骤

* [Connect using ExpressRoute](../operator/azure-stack-connect-expressroute.md)
