---
title: Azure Stack 的 VPN 网关设置 | Microsoft Docs
description: 了解在 Azure Stack 中使用的 VPN 网关的设置。
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
ms.date: 12/27/2018
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 35f7c5b15e9dce3b27fd01ab262154e139200f92
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64300270"
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Azure Stack 的 VPN 网关配置设置

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

VPN 网关是一种虚拟网络网关，可在 Azure Stack 中的虚拟网络与远程 VPN 网关之间发送加密流量。 远程 VPN 网关可位于 Azure 中、数据中心的设备中，或另一个站点上的设备中。 如果两个终结点之间有网络连接，可以在这两个网络之间建立安全的站点到站点 (S2S) VPN 连接。

VPN 网关连接依赖于多个资源配置，其中每个资源包含可配置的设置。 本文介绍与资源管理器部署模型中创建的虚拟网络的 VPN 网关相关的资源和设置。 可在[关于 Azure Stack 的 VPN 网关](azure-stack-vpn-gateway-about-vpn-gateways.md)中找到每个连接解决方案的说明和拓扑图。

## <a name="vpn-gateway-settings"></a>VPN 网关设置

### <a name="gateway-types"></a>网关类型

每个 Azure Stack 虚拟网络支持单个虚拟网络网关，其类型必须是 **Vpn**。  此项支持不同于 Azure，后者可支持其他类型。  

创建虚拟网络网关时，必须确保用于配置的网关类型正确。 VPN 网关需要 `-GatewayType Vpn` 标志，例如：

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>网关 SKU

创建虚拟网络网关时，必须指定要使用的网关 SKU。 根据工作负荷、吞吐量、功能和、SLA 的类型，选择满足需求的 SKU。

Azure Stack 提供下表中显示的 VPN 网关 SKU。

|   | VPN 网关吞吐量 |VPN 网关最大 IPsec 隧道数 |
|-------|-------|-------|
|**基本 SKU**  | 100 Mbps  | 20    |
|**标准 SKU**           | 100 Mbps  | 20    |
|**高性能 SKU** | 200 Mbps    | 10    |

### <a name="resizing-gateway-skus"></a>调整网关 SKU 大小

Azure Stack 不支持在所支持的旧式 SKU 之间调整 SKU 大小。

同样，Azure Stack 不支持将大小从支持的旧式 SKU（基本、标准和高性能）调整为 Azure 所支持的新式 SKU（VpnGw1、VpnGw2 和 VpnGw3）。

### <a name="configure-the-gateway-sku"></a>配置网关 SKU

#### <a name="azure-stack-portal"></a>Azure Stack 门户

如果使用 Azure Stack 门户创建资源管理器虚拟网络网关，可以使用下拉列表选择网关 SKU。 这些选项对应于所选的网关类型和 VPN 类型。

#### <a name="powershell"></a>PowerShell

以下 PowerShell 示例将 **-GatewaySku** 指定为 `VpnGw1`：

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>连接类型

在 Resource Manager 部署模型中，每个配置都需要特定的虚拟网络网关连接类型。 **-ConnectionType** 的可用资源管理器 PowerShell 值为：

* IPsec

   以下 PowerShell 示例创建需要 IPsec 连接类型的 S2S 连接：

   ```powershell
   New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
   -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

### <a name="vpn-types"></a>VPN 类型

为 VPN 网关配置创建虚拟网络网关时，必须指定 VPN 类型。 选择的 VPN 类型取决于要创建的连接拓扑。 VPN 类型还取决于使用的硬件。 S2S 配置需要 VPN 设备。 有些 VPN 设备仅支持特定的 VPN 类型。

> [!IMPORTANT]  
> 目前，Azure Stack 仅支持基于路由的 VPN 类型。 如果设备仅支持基于策略的 VPN，则不支持从 Azure Stack 连接到这些设备。  
>
> 此外，由于自定义 IPSec/IKE 策略配置尚不受支持，因此 Azure Stack 目前不支持对基于路由的网关使用基于策略的流量选择器。

* **PolicyBased**：基于策略的 VPN 会根据使用本地网络和 Azure Stack VNet 之间的地址前缀的各种组合配置的 IPsec 策略，加密数据包并引导其通过 IPsec 隧道。 策略或流量选择器通常是 VPN 设备配置中的访问列表。

  >[!NOTE]
  >**PolicyBased** 在 Azure 中受支持，但在 Azure Stack 中不受支持。

* **RouteBased**：RouteBased VPN 使用 IP 转发或路由表中配置的路由将数据包定向到相应的隧道接口。 然后，隧道接口会加密或解密出入隧道的数据包。 **RouteBased** VPN 的策略或流量选择器配置为任意到任意（或使用通配符）。默认情况下，无法更改这些 VPN。 **RouteBased** VPN 类型的值为 **RouteBased**。

以下 PowerShell 示例将 **-VpnType** 指定为 **RouteBased**。 创建网关时，必须确保用于配置的 **-VpnType** 正确。

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>网关要求

下表列出了 VPN 网关的要求。

| |基于策略的基本 VPN 网关 | 基于路由的基本 VPN 网关 | 基于路由的标准 VPN 网关 | 基于路由的高性能 VPN 网关|
|--|--|--|--|--|
| **站点到站点连接（S2S 连接）** | 不支持 | 基于路由的 VPN 配置 | 基于路由的 VPN 配置 | 基于路由的 VPN 配置 |
| **身份验证方法**  | 不支持 | S2S 连接的预先共享密钥  | S2S 连接的预先共享密钥  | S2S 连接的预先共享密钥  |   
| **S2S 连接的最大数目**  | 不支持 | 20 | 20| 10|
|**活动路由支持 (BGP)** | 不支持 | 不支持 | 支持 | 支持 |

### <a name="gateway-subnet"></a>网关子网

在创建 VPN 网关之前，必须创建一个网关子网。 网关子网包含虚拟网络网关 VM 和服务使用的 IP 地址。 在创建虚拟网络网关时，将网关 VM 部署到网关子网，并使用所需的 VPN 网关设置进行配置。 不要将任何其他设备（例如，其他 VM）部署到网关子网。

>[!IMPORTANT]
>网关子网必须命名为 **GatewaySubnet** 才能正常工作。 Azure Stack 使用此名称来识别要将虚拟网络网关 VM 和服务部署到的子网。

创建网关子网时，请指定子网包含的 IP 地址数。 将网关子网中的 IP 地址分配到网关 VM 和网关服务。 有些配置需要具有比其他配置更多的 IP 地址。 查看要创建的配置的说明，验证想要创建的网关子网是否会满足这些要求。

此外，应确保网关子网具有足够的 IP 地址，以便处理将来可能会添加的配置。 尽管网关子网最小可创建为 /29，但我们建议创建创建 /28 或更大（/28、/27 和 /26 等）的网关子网。这样一来，如果以后添加功能，就无需断开网关，删除并重新创建网关子网以容纳更多 IP 地址。

以下资源管理器 PowerShell 示例显示名为 **GatewaySubnet** 的网关子网。 可以看到，CIDR 表示法指定了 /27，这可提供足够的 IP 地址供大多数现有配置使用。

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> 使用网关子网时，避免将网络安全组 (NSG) 与网关子网关联。 将网络安全组与此子网关联可能会导致 VPN 网关停止按预期方式工作。 有关网络安全组的详细信息，请参阅[什么是网络安全组？](/azure/virtual-network/virtual-networks-nsg)

### <a name="local-network-gateways"></a>本地网关

在 Azure 中创建 VPN 网关配置时，本地网络网关通常代表本地位置。 在 Azure Stack 中，它代表位于 Azure Stack 外部的任何远程 VPN 设备。 这可能是数据中心（或远程数据中心）内的 VPN 设备，或 Azure 中的 VPN 网关。

指定本地网络网关的名称、VPN 设备的公共 IP 地址，并指定位于本地位置的地址前缀。 Azure 将查看网络流量的目标地址前缀、查阅针对本地网络网关指定的配置，并相应地路由数据包。

以下 PowerShell 示例创建新的本地网络网关：

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

有时，我们需要修改本地网络网关设置；例如，在添加或修改地址范围时，或 VPN 设备的 IP 地址发生变化时。 请参阅[使用 PowerShell 修改本地网络网关设置](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway)。

## <a name="ipsecike-parameters"></a>IPsec/IKE 参数

在 Azure Stack 中设置 VPN 连接时，必须在两端配置连接。 若要配置 Azure Stack 与硬件设备（例如用作 VPN 网关的交换机或路由器）之间的 VPN 连接，可能需要在该设备上进行其他设置。

与 Azure 不同，它支持多个产品/服务为发起方和响应程序，Azure Stack 支持默认情况下只有一个产品/服务。  如果您需要使用不同的 IPSec/IKE 设置以使用你的 VPN 设备，有更多的设置可用于手动配置你的连接。  有关更多详细信息，请参阅[站点到站点 VPN 连接配置 IPsec/IKE 策略](azure-stack-vpn-s2s.md)。

### <a name="ike-phase-1-main-mode-parameters"></a>IKE 阶段 1（主模式）参数

| 属性              | 值|
|-|-|
| SDK 版本           | IKEv2 |
|Diffie-Hellman 组   | 组 2（1024 位） |
| 身份验证方法 | 预共享密钥 |
|加密和哈希算法 | AES256、SHA256 |
|SA 生存期（时间）     | 28,800 秒|

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 阶段 2（快速模式）参数

| 属性| 值|
|-|-|
|SDK 版本 |IKEv2 |
|加密和哈希算法（加密）     | GCMAES256|
|加密和哈希算法（身份验证） | GCMAES256|
|SA 生存期（时间）  | 27,000 秒  |
|SA 生存期（千字节） | 33,553,408     |
|完全向前保密 (PFS) |无<sup>请参阅注释 1</sup> |
|死对等体检测 | 支持|  

* *注释 1：* 在版本 1807 以前，Azure Stack 使用值 PFS2048 作为“完全向前保密(PFS)”值。

## <a name="next-steps"></a>后续步骤

* [使用 ExpressRoute 进行连接](../operator/azure-stack-connect-expressroute.md)
