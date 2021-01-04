---
title: 配置 Azure Stack Hub 的 VPN 网关设置
description: 了解并配置 Azure Stack Hub 的 VPN 网关设置。
author: sethmanheim
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: sethm
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: 178164148e9d7de069c4ab12dc3042899b83d16d
ms.sourcegitcommit: 8790b8a4ecf4421409534df5ff510d537cc000da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97801940"
---
# <a name="configure-vpn-gateway-settings-for-azure-stack-hub"></a>配置 Azure Stack Hub 的 VPN 网关设置

VPN 网关是一种虚拟网络网关，可在 Azure Stack Hub 中的虚拟网络与远程 VPN 网关之间发送加密流量。 远程 VPN 网关可位于 Azure 中、数据中心的设备中，或另一个站点上的设备中。 如果两个终结点之间有网络连接，可以在这两个网络之间建立安全的站点到站点 (S2S) VPN 连接。

VPN 网关连接依赖于多个资源的配置，每个资源都包含可配置的设置。 本文介绍与资源管理器部署模型中创建的虚拟网络的 VPN 网关相关的资源和设置。 可在[关于 Azure Stack Hub 的 VPN 网关](azure-stack-vpn-gateway-about-vpn-gateways.md)中找到每个连接解决方案的说明和拓扑图。

## <a name="vpn-gateway-settings"></a>VPN 网关设置

### <a name="gateway-types"></a>网关类型

每个 Azure Stack Hub 虚拟网络支持单个虚拟网络网关，其类型必须是 **Vpn**。 此项支持不同于 Azure，后者可支持其他类型。

创建虚拟网络网关时，必须确保用于配置的网关类型正确。 VPN 网关需要 `-GatewayType Vpn` 标志，例如：

```powershell
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

### <a name="gateway-skus"></a>网关 SKU

创建虚拟网络网关时，必须指定要使用的网关 SKU。 根据工作负荷、吞吐量、功能和 SLA 的类型，选择满足需求的 SKU。

Azure Stack Hub 提供下表中所示的 VPN 网关 SKU：

| | 隧道吞吐量 |VPN 网关最大 IPsec 隧道数 |
|-------|-------|-------|
|**基本 SKU**  | 100 Mbps    | 20 个    |
|**标准 SKU**   | 100 Mbps  | 20 个 |
|**高性能 SKU** | 200 Mbps | 10 个 |

### <a name="resizing-gateway-skus"></a>调整网关 SKU 大小

Azure Stack Hub 不支持在所支持的旧式 SKU 之间调整 SKU 大小。

同样，Azure Stack Hub 不支持将大小从支持的旧式 SKU（“基本”、“标准”和“高性能”）调整为 Azure 所支持的新式 SKU（“VpnGw1”、“VpnGw2”和“VpnGw3”）     。

### <a name="configure-the-gateway-sku"></a>配置网关 SKU

#### <a name="azure-stack-hub-portal"></a>Azure Stack Hub 门户

如果使用 Azure Stack Hub 门户创建资源管理器虚拟网络网关，可以使用下拉列表选择网关 SKU。 这些选项对应于所选的网关类型和 VPN 类型。

#### <a name="powershell"></a>PowerShell

以下 PowerShell 示例将 `-GatewaySku` 参数指定为“Standard”：

```powershell
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>连接类型

在 Resource Manager 部署模型中，每个配置都需要特定的虚拟网络网关连接类型。 `-ConnectionType` 的可用资源管理器 PowerShell 值为 **IPsec**。

以下 PowerShell 示例创建需要 IPsec 连接类型的 S2S 连接：

```powershell
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN 类型

为 VPN 网关配置创建虚拟网络网关时，必须指定 VPN 类型。 选择的 VPN 类型取决于要创建的连接拓扑。 VPN 类型还取决于使用的硬件。 S2S 配置需要 VPN 设备。 有些 VPN 设备仅支持特定的 VPN 类型。

> [!IMPORTANT]  
> 目前，Azure Stack Hub 仅支持基于路由的 VPN 类型。 如果设备仅支持基于策略的 VPN，则不支持从 Azure Stack Hub 连接到这些设备。  
>
> 此外，由于自定义 IPSec/IKE 策略配置不受支持，因此 Azure Stack Hub 目前不支持对基于路由的网关使用基于策略的流量选择器。

* **PolicyBased**：基于策略的 VPN 会根据使用本地网络和 Azure Stack Hub VNet 之间的地址前缀的各种组合配置的 IPsec 策略，加密数据包并引导其通过 IPsec 隧道。 策略或流量选择器通常是 VPN 设备配置中的访问列表。

  >[!NOTE]
  >**PolicyBased** 在 Azure 中受支持，但在 Azure Stack Hub 中不受支持。

* **RouteBased**：基于路由的 VPN 使用 IP 转发或路由表中配置的路由将数据包定向到相应的隧道接口。 然后，隧道接口会加密或解密出入隧道的数据包。 **RouteBased** VPN 的策略或流量选择器配置为任意到任意（或使用通配符）。 默认情况下，无法更改这些 VPN。 **RouteBased** VPN 类型的值为 **RouteBased**。

以下 PowerShell 示例将 `-VpnType` 指定为 **RouteBased**。 创建网关时，必须确保 `-VpnType` 符合你的配置。

```powershell
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>网关要求

下表列出了 VPN 网关的要求。

| |基于策略的基本 VPN 网关 | 基于路由的基本 VPN 网关 | 基于路由的标准 VPN 网关 | 基于路由的高性能 VPN 网关|
|--|--|--|--|--|
| **站点到站点连接（S2S 连接）** | 不支持 | 基于路由的 VPN 配置 | 基于路由的 VPN 配置 | 基于路由的 VPN 配置 |
| **身份验证方法**  | 不支持 | S2S 连接的预先共享密钥  | S2S 连接的预先共享密钥  | S2S 连接的预先共享密钥  |
| **S2S 连接的最大数目**  | 不支持 | 20 个 | 20 个| 10 个|
|**活动路由支持 (BGP)** | 不支持 | 不支持 | 支持 | 支持 |

### <a name="gateway-subnet"></a>网关子网

在创建 VPN 网关之前，必须创建一个网关子网。 网关子网包含虚拟网络网关 VM 和服务使用的 IP 地址。 创建虚拟网络网关时，会将网关 VM 部署到网关子网，并使用所需的 VPN 网关设置进行配置。 不要将任何其他设备（例如，其他 VM）部署到网关子网。

>[!IMPORTANT]
>网关子网必须命名为 **GatewaySubnet** 才能正常工作。 Azure Stack Hub 使用此名称来识别要将虚拟网络网关 VM 和服务部署到的子网。

创建网关子网时，需指定子网包含的 IP 地址数。 将网关子网中的 IP 地址分配到网关 VM 和网关服务。 有些配置需要具有比其他配置更多的 IP 地址。 查看要创建的配置的说明，验证要创建的网关子网是否会满足这些要求。

此外，应确保网关子网具有足够的 IP 地址，以便处理将来可能会添加的配置。 尽管网关子网最小可创建为 /29，但我们建议创建创建 /28 或更大（/28、/27 和 /26 等）的网关子网。这样一来，如果以后添加功能，就无需断开网关，删除并重新创建网关子网以容纳更多 IP 地址。

以下资源管理器 PowerShell 示例显示名为 **GatewaySubnet** 的网关子网。 可以看到，CIDR 表示法指定了 /27，这可提供足够的 IP 地址供大多数现有配置使用。

```powershell
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> 处理网关子网时，请避免将网络安全组 (NSG) 关联到网关子网。 将网络安全组与此子网关联可能会导致 VPN 网关停止按预期方式工作。 有关网络安全组的详细信息，请参阅[什么是网络安全组？](/azure/virtual-network/virtual-networks-nsg)

### <a name="local-network-gateways"></a>本地网关

在 Azure 中创建 VPN 网关配置时，本地网络网关通常代表本地位置。 在 Azure Stack Hub 中，它代表位于 Azure Stack Hub 外部的任何远程 VPN 设备。 此设备可以是数据中心（或远程数据中心）内的 VPN 设备，或 Azure 中的 VPN 网关。

指定本地网络网关的名称、VPN 设备的公共 IP 地址，并指定位于本地位置的地址前缀。 Azure 查看网络流量的目标地址前缀、参考针对本地网络网关指定的配置，并相应地路由数据包。

此 PowerShell 示例创建新的本地网络网关：

```powershell
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

有时，我们需要修改本地网络网关设置；例如，在添加或修改地址范围时，或 VPN 设备的 IP 地址发生变化时。 有关详细信息，请参阅[使用 PowerShell 修改本地网络网关设置](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway)。

## <a name="ipsecike-parameters"></a>IPsec/IKE 参数

在 Azure Stack Hub 中设置 VPN 连接时，必须在两端配置连接。 若要配置 Azure Stack Hub 与硬件设备（例如用作 VPN 网关的交换机或路由器）之间的 VPN 连接，该设备可能会要求你提供其他设置。

Azure Stack Hub 默认情况下仅支持一个套餐，这与 Azure 不同，后者支持将多个套餐用作发起程序和响应程序。 如需使用适合 VPN 设备的不同 IPSec/IKE 设置，则可通过其他设置来手动配置连接。 有关详细信息，请参阅[为站点到站点 VPN 连接配置 IPsec/IKE 策略](azure-stack-vpn-s2s.md)。

> [!IMPORTANT] 
> 使用 S2S 隧道时，数据包会通过附加的标头进一步封装，从而增加了数据包的总大小。 在这些情况下，必须将 TCP **MSS** 固定在 **1350**。 或者，如果 VPN 设备不支持 MSS 钳位，则可以改为在隧道接口上将 MTU 设置为 1400 字节 。 有关详细信息，请参阅[虚拟网络 TCPIP 性能优化](/azure/virtual-network/virtual-network-tcpip-performance-tuning)。
>

### <a name="ike-phase-1-main-mode-parameters"></a>IKE 阶段 1（主模式）参数

| 属性              | Value|
|-|-|
| SDK 版本           | IKEv2 |
|Diffie-Hellman 组*   | ECP384 |
| 身份验证方法 | 预共享密钥 |
|加密和哈希算法* | AES256、SHA384 |
|SA 生存期（时间）     | 28,800 秒|

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 阶段 2（快速模式）参数

| 属性| Value|
|-|-|
|SDK 版本 |IKEv2 |
|加密和哈希算法（加密）     | GCMAES256|
|加密和哈希算法（身份验证） | GCMAES256|
|SA 生存期（时间）  | 27,000 秒  |
|SA 生存期（千字节） | 33,553,408     |
|完全向前保密 (PFS)* | ECP384 |
|死对等体检测 | 支持| 

>[!NOTE]
>“Diffie-hellman 组”、“哈希算法”和“完全向前保密”的默认值在内部版本 1910 和更高版本中已更改。 如果 Azure Stack Hub 的内部版本低于 1910，请对上述参数使用以下值：

>| 属性| Value|
>|-|-|
>|Diffie-Hellman 组   | DHGroup2 |
>|哈希算法 | SHA256 |
>|完全向前保密 (PFS) | 无 |

\* 新的或已更改的参数。

## <a name="next-steps"></a>后续步骤

* [使用 ExpressRoute 进行连接](../operator/azure-stack-connect-expressroute.md)
