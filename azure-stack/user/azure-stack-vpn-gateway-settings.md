---
title: 为 Azure Stack 中心配置 VPN 网关设置
description: 了解并配置 Azure Stack 集线器的 VPN 网关设置。
author: sethmanheim
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: sethm
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: b8f7be7885bd4565a13983d858c1f10b30df20b3
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294942"
---
# <a name="configure-vpn-gateway-settings-for-azure-stack-hub"></a>为 Azure Stack 中心配置 VPN 网关设置

VPN 网关是一种虚拟网络网关，用于在 Azure Stack 集线器和远程 VPN 网关中的虚拟网络之间发送加密流量。 远程 VPN 网关可以位于 Azure、数据中心的设备或其他站点中的设备上。 如果两个终结点之间存在网络连接，则可以在两个网络之间建立安全的站点到站点（S2S） VPN 连接。

VPN 网关连接依赖于多个资源配置，其中每个资源包含可配置的设置。 本文介绍了与在资源管理器部署模型中创建的虚拟网络的 VPN 网关相关的资源和设置。 可以在[关于 Azure Stack 集线器的 VPN 网关中](azure-stack-vpn-gateway-about-vpn-gateways.md)找到每个连接解决方案的说明和拓扑图。

## <a name="vpn-gateway-settings"></a>VPN 网关设置

### <a name="gateway-types"></a>网关类型

每个 Azure Stack 集线器虚拟网络支持单个虚拟网络网关，该网关必须是**Vpn**类型。 此支持不同于 Azure，后者支持其他类型。

创建虚拟网络网关时，必须确保配置的网关类型正确。 VPN 网关需要 `-GatewayType Vpn` 标志;例如：

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

### <a name="gateway-skus"></a>网关 SKU

创建虚拟网络网关时，必须指定要使用的网关 SKU。 根据工作负荷、吞吐量、功能和、SLA 的类型，选择满足需求的 SKU。

Azure Stack 中心提供下表中所示的 VPN 网关 Sku：

| | VPN 网关吞吐量 |VPN 网关最大 IPsec 隧道 |
|-------|-------|-------|
|**基本 SKU**  | 100 Mbps  | 20    |
|**标准 SKU**   | 100 Mbps  | 20 |
|**高性能 SKU** | 200 Mbps | 10 |

### <a name="resizing-gateway-skus"></a>调整网关 Sku 大小

Azure Stack 中心不支持在支持的旧式 Sku 之间调整 Sku 大小。

同样，Azure Stack 集线器不支持从受支持的旧 SKU （**基本**、**标准**和**高性能**）调整为 Azure 支持的更新 SKU （**VpnGw1**、 **VpnGw2**和**VpnGw3**）。

### <a name="configure-the-gateway-sku"></a>配置网关 SKU

#### <a name="azure-stack-hub-portal"></a>Azure Stack 中心门户

如果使用 Azure Stack 中心门户创建资源管理器的虚拟网络网关，则可以使用下拉列表选择网关 SKU。 选项对应于所选的网关类型和 VPN 类型。

#### <a name="powershell"></a>PowerShell

以下 PowerShell 示例将 `-GatewaySku` 参数指定为**Standard**：

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>连接类型

在 Resource Manager 部署模型中，每个配置都需要特定的虚拟网络网关连接类型。 `-ConnectionType` 的可用资源管理器 PowerShell 值为**IPsec**。

下面的 PowerShell 示例创建需要 IPsec 连接类型的 S2S 连接：

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN 类型

为 VPN 网关配置创建虚拟网络网关时，必须指定 VPN 类型。 选择的 VPN 类型取决于要创建的连接拓扑。 VPN 类型还取决于要使用的硬件。 S2S 配置需要 VPN 设备。 有些 VPN 设备仅支持特定的 VPN 类型。

> [!IMPORTANT]  
> 目前，Azure Stack 集线器仅支持基于路由的 VPN 类型。 如果设备仅支持基于策略的 Vpn，则不支持从 Azure Stack 集线器连接到这些设备。  
>
> 此外，由于不支持自定义 IPSec/IKE 策略配置，Azure Stack 集线器此时不支持对基于路由的网关使用基于策略的流量选择器。

* **PolicyBased**：基于策略的 vpn 会根据使用本地网络与 Azure Stack 中心 VNet 之间的地址前缀的组合配置的 ipsec 策略，加密数据包并将数据包定向到 ipsec 隧道。 策略（或流量选择器）通常是 VPN 设备配置中的访问列表。

  >[!NOTE]
  >**PolicyBased**在 Azure 中受支持，但在 Azure Stack Hub 中不受支持。

* **RouteBased**：基于路由的 VPN 使用 IP 转发或路由表中配置的路由，将数据包定向到相应的隧道接口。 然后，隧道接口会加密或解密出入隧道的数据包。 **RouteBased** vpn 的策略或流量选择器配置为任意对等（或使用通配符）。 默认情况下，不能更改它们。 **RouteBased** VPN 类型的值为**RouteBased**。

以下 PowerShell 示例将 `-VpnType` 指定为**基于路由**。 创建网关时，必须确保配置的 `-VpnType` 正确。

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>网关要求

下表列出了对 VPN 网关的要求。

| |基于策略的基本 VPN 网关 | 基于路由的基本 VPN 网关 | 基于路由的标准 VPN 网关 | 基于路由的高性能 VPN 网关|
|--|--|--|--|--|
| **站点到站点连接（S2S 连接）** | 不支持 | 基于路由的 VPN 配置 | 基于路由的 VPN 配置 | 基于路由的 VPN 配置 |
| **身份验证方法**  | 不支持 | 用于 S2S 连接的预共享密钥  | 用于 S2S 连接的预共享密钥  | 用于 S2S 连接的预共享密钥  |
| **S2S 连接的最大数目**  | 不支持 | 20 | 20| 10|
|**活动路由支持 (BGP)** | 不支持 | 不支持 | 支持 | 支持 |

### <a name="gateway-subnet"></a>网关子网

在创建 VPN 网关之前，必须创建一个网关子网。 网关子网包含虚拟网络网关 Vm 和服务使用的 IP 地址。 在创建虚拟网络网关时，将网关 VM 部署到网关子网，并使用所需的 VPN 网关设置进行配置。 不要将任何其他内容（例如，其他 Vm）部署到网关子网。

>[!IMPORTANT]
>网关子网必须命名为 **GatewaySubnet** 才能正常工作。 Azure Stack 中心使用此名称来确定要将虚拟网络网关 Vm 和服务部署到的子网。

创建网关子网时，请指定子网包含的 IP 地址数。 将网关子网中的 IP 地址分配到网关 VM 和网关服务。 有些配置需要具有比其他配置更多的 IP 地址。 查看要创建的配置的说明，验证想要创建的网关子网是否会满足这些要求。

此外，还应确保网关子网包含足够的 IP 地址，以处理其他将来的配置。 尽管可以创建小到/29 的网关子网，但建议创建/28 或更大（/28、/27、/26 等）的网关子网。这样一来，如果以后添加功能，则无需断开网关，删除并重新创建网关子网，就可以获得更多的 IP 地址。

以下资源管理器 PowerShell 示例显示名为**GatewaySubnet**的网关子网。 可以看到，CIDR 表示法指定了 /27，这可提供足够的 IP 地址供大多数现有配置使用。

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> 使用网关子网时，避免将网络安全组 (NSG) 与网关子网关联。 将网络安全组与此子网关联可能会导致 VPN 网关停止按预期方式工作。 有关网络安全组的详细信息，请参阅[什么是网络安全组？](/azure/virtual-network/virtual-networks-nsg)。

### <a name="local-network-gateways"></a>本地网关

在 Azure 中创建 VPN 网关配置时，本地网络网关通常代表本地位置。 在 Azure Stack Hub 中，它表示位于 Azure Stack 中心以外的任何远程 VPN 设备。 此设备可以是数据中心（或远程数据中心）中的 VPN 设备，也可以是 Azure 中的 VPN 网关。

为本地网络网关指定一个名称、VPN 设备的公共 IP 地址，并指定位于本地位置的地址前缀。 Azure 会查看网络流量的目标地址前缀，对你为本地网络网关指定的配置进行参考，并相应地路由数据包。

此 PowerShell 示例创建新的本地网络网关：

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

有时需要修改本地网络网关设置;例如，在添加或修改地址范围时，或 VPN 设备的 IP 地址发生更改时。 有关详细信息，请参阅[使用 PowerShell 修改本地网络网关设置](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway)。

## <a name="ipsecike-parameters"></a>IPsec/IKE 参数

在 Azure Stack Hub 中设置 VPN 连接时，必须在两端配置连接。 如果要在 Azure Stack 集线器和硬件设备（如充当 VPN 网关的交换机或路由器）之间配置 VPN 连接，该设备可能会要求提供其他设置。

与 Azure 不同，它支持将多个产品/服务作为发起方和响应方，默认情况下 Azure Stack 集线器仅支持一个产品/服务。 如果需要使用不同的 IPSec/IKE 设置来与 VPN 设备一起使用，则可以使用更多的设置来手动配置连接。 有关详细信息，请参阅为[站点到站点 VPN 连接配置 IPsec/IKE 策略](azure-stack-vpn-s2s.md)。

> [!IMPORTANT] 
> 使用 S2S 隧道时，数据包会进一步封装，增加了标头，增加了数据包的总大小。 在这些情况下，必须将 TCP **MSS**固定在**1350**。 或者，如果 VPN 设备不支持 MSS 固定，则可以改为在隧道接口上将 **MTU** 设置为 **1400** 字节。 有关详细信息，请参阅 [虚拟网络 TCPIP 性能优化] （virtual-network-tcpip-performance-tuning.md） 
>

### <a name="ike-phase-1-main-mode-parameters"></a>IKE 阶段 1（主模式）参数

| properties              | 值|
|-|-|
| SDK 版本           | IKEv2 |
|Diffie-hellman 组 *   | ECP384 |
| 身份验证方法 | 预共享密钥 |
|加密 & 哈希算法 * | AES256、SHA384 |
|SA 生存期（时间）     | 28,800 秒|

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 阶段 2（快速模式）参数

| properties| 值|
|-|-|
|SDK 版本 |IKEv2 |
|加密 & 哈希算法（加密）     | GCMAES256|
|加密 & 哈希算法（身份验证） | GCMAES256|
|SA 生存期（时间）  | 27,000 秒  |
|SA 生存期（Kb） | 33553408     |
|完全向前保密（PFS） * | ECP384 |
|死对等体检测 | 支持| 

>[!NOTE]
>对于版本1910及更高版本，Diffie-hellman 组、哈希算法和完全向前保密的默认值已更改。 如果 Azure Stack 中心位于1910以下的版本中，请使用以下参数的值：

>| properties| 值|
>|-|-|
>|Diffie-Hellman 组   | DHGroup2 |
>|哈希算法 | SHA256 |
>|完全向前保密 (PFS) | 无 |

\* 新的或更改的参数。

## <a name="next-steps"></a>后续步骤

* [使用 ExpressRoute 进行连接](../operator/azure-stack-connect-expressroute.md)
