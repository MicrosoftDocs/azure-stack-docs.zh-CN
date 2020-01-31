---
title: 配置 IPsec/IKE 站点到站点 VPN 连接
description: 了解并为 Azure Stack 集线器中的站点到站点 VPN 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略。
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 262d0714c8a0d5979a1313dcff48947f676109ce
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883004"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>配置站点到站点 VPN 连接的 IPsec/IKE 策略

本文逐步讲解如何在 Azure Stack Hub 中配置站点到站点（S2S） VPN 连接的 IPsec/IKE 策略。

>[!NOTE]
> 必须运行 Azure Stack 集线器 build **1809**或更高版本才能使用此功能。  如果你当前运行的是1809之前的版本，请将 Azure Stack 中心系统更新到最新版本，然后再继续执行本文中的步骤。

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>VPN 网关的 IPsec 和 IKE 策略参数

IPsec 和 IKE 协议标准支持各种组合的各种加密算法。 若要查看 Azure Stack 中心支持哪些参数，以便满足符合性或安全性要求，请参阅[IPsec/IKE 参数](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)。

本文提供了有关如何创建和配置 IPsec/IKE 策略并将其应用于新连接或现有连接的说明。

## <a name="considerations"></a>注意事项

使用这些策略时，请注意以下重要注意事项：

- IPsec/IKE 策略仅适用于*标准*和*高性能*（基于路由）网关 sku。

- 对于给定的连接，只能指定一个策略组合。

- 必须指定 IKE（主模式）和 IPsec（快速模式）的所有算法和参数。 不允许指定部分策略。

- 请查阅 VPN 设备供应商规范，确保本地 VPN 设备支持该策略。 如果策略不兼容，则无法建立站点到站点连接。

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>第1部分-创建和设置 IPsec/IKE 策略的工作流

本部分概述了在站点到站点 VPN 连接上创建和更新 IPsec/IKE 策略所需的工作流：

1. 创建虚拟网络和 VPN 网关。

2. 为跨界连接创建本地网络网关。

3. 使用选定的算法和参数创建 IPsec/IKE 策略。

4. 使用 IPsec/IKE 策略创建 IPSec 连接。

5. 为现有连接添加/更新/删除 IPsec/IKE 策略。

本文中的说明将帮助你设置和配置 IPsec/IKE 策略，如下图所示：

![设置和配置 IPsec/IKE 策略](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>第2部分-支持的加密算法和密钥强度

下表列出了 Azure Stack 中心客户可配置的受支持的加密算法和密钥强度：

| IPsec/IKEv2                                          | 选项                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| IKEv2 加密                                     | AES256、AES192、AES128、DES3、DES                                        |
| IKEv2 完整性                                      | SHA384、SHA256、SHA1、MD5                                                |
| DH 组                                             | ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、None         |
| IPsec 加密                                     | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、无 |
| IPsec 完整性                                      | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                       |
| PFS 组                                            | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、无                         |
| QM SA 生存期                                       | （可选：如果未指定，则使用默认值）<br />                         秒（整数; 最小值为 300/默认值27000秒）<br />                         Kb （integer; 1024/默认值 102400000 Kb） |
| 流量选择器                                     | Azure Stack 集线器不支持基于策略的流量选择器。         |

- 本地 VPN 设备配置必须匹配，或者必须包含可在 Azure IPsec/IKE 策略中指定的以下算法和参数：

  - IKE 加密算法（主模式/阶段1）。
  - IKE 完整性算法（主模式/阶段1）。
  - DH 组（主模式/阶段1）。
  - IPsec 加密算法（快速模式/阶段2）。
  - IPsec 完整性算法（快速模式/阶段2）。
  - PFS 组（快速模式/阶段2）。
  - SA 生存期仅为本地规范，不需要匹配。

- 如果 GCMAES 用作 IPsec 加密算法，则必须为 IPsec 完整性选择相同的 GCMAES 算法和密钥长度。 例如：将 GCMAES128 用于这两者。

- 在上表中：

  - IKEv2 对应于主模式或阶段1。
  - IPsec 对应于快速模式或阶段2。
  - DH 组指定在主模式或阶段1中使用的 Diffie-hellmen 组。
  - PFS 组指定在快速模式或阶段2中使用的 Diffie-hellmen 组。

- IKEv2 主模式 SA 生存期在 Azure Stack 集线器 VPN 网关上28800秒固定。

下表列出了自定义策略支持的相应 Diffie-Hellman 组：

| Diffie-Hellman 组 | DHGroup   | PFSGroup      | 密钥长度    |
|----------------------|-----------|---------------|---------------|
| 第                    | DHGroup1  | PFS1          | 768 位 MODP  |
| 2                    | DHGroup2  | PFS2          | 1024 位 MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048 位 MODP |
| 19                   | ECP256    | ECP256        | 256 位 ECP   |
| 20                   | ECP384    | ECP384        | 384 位 ECP   |
| 24                   | DHGroup24 | PFS24         | 2048 位 MODP |

有关详细信息，请参阅 [RFC3526](https://tools.ietf.org/html/rfc3526) 和 [RFC5114](https://tools.ietf.org/html/rfc5114)。

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>第3部分-使用 IPsec/IKE 策略创建新的站点到站点 VPN 连接

本部分逐步讲解使用 IPsec/IKE 策略创建站点到站点 VPN 连接的步骤。 以下步骤将创建连接，如下图所示：

![站点到站点-策略](media/azure-stack-vpn-s2s/site-to-site.png)

有关创建站点到站点 VPN 连接的详细分步说明，请参阅[创建站点到站点 vpn 连接](/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)。

### <a name="prerequisites"></a>必备组件

在开始之前，请确保满足以下先决条件：

- Azure 订阅。 如果还没有 Azure 订阅，可以激活[MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

- Azure 资源管理器 PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅为[Azure Stack 集线器安装 PowerShell](../operator/azure-stack-powershell-install.md)。

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>步骤 1 - 创建虚拟网络、VPN 网关和本地网关

#### <a name="1-declare-variables"></a>1. 声明变量

对于本练习，请首先声明以下变量。 在为生产配置时，请确保将占位符替换为你自己的值：

```powershell
$Sub1 = "<YourSubscriptionName>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. 连接到订阅并创建新的资源组

确保切换到 PowerShell 模式，以便使用Resource Manager cmdlet。 有关详细信息，请参阅[使用 PowerShell 作为用户连接到 Azure Stack 集线器](azure-stack-powershell-configure-user.md)。

打开 PowerShell 控制台并连接到帐户;例如：

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. 创建虚拟网络、VPN 网关和本地网关

以下示例创建虚拟网络**TestVNet1**以及三个子网和 VPN 网关。 替换值时，请务必明确地命名网关子网**GatewaySubnet**。 如果命名为其他名称，网关创建会失败。

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" `
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 `
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 `
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix `
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>步骤 2-使用 IPsec/IKE 策略创建站点到站点 VPN 连接

#### <a name="1-create-an-ipsecike-policy"></a>1. 创建 IPsec/IKE 策略

此示例脚本使用以下算法和参数创建 IPsec/IKE 策略：

- IKEv2：AES128、SHA1、DHGroup14
- IPsec： AES256、SHA256、none、SA 生存期14400秒和102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

如果将 GCMAES 用于 IPsec，必须为 IPsec 加密和完整性使用相同的 GCMAES 算法和密钥长度。

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. 创建站点到站点 VPN 连接和 IPsec/IKE 策略

创建站点到站点 VPN 连接并应用之前创建的 IPsec/IKE 策略：

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> 在连接上指定 IPsec/IKE 策略后，Azure VPN 网关将仅使用指定的加密算法和该特定连接上的密钥强度来发送或接受具有指定加密算法和密钥强度的 IPsec/IKE 提议。 请确保连接的本地 VPN 设备使用或接受确切的策略组合，否则无法建立站点到站点 VPN 隧道。

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>第4部分-更新连接的 IPsec/IKE 策略

上一部分介绍了如何管理现有站点到站点连接的 IPsec/IKE 策略。 本部分逐步讲解连接的下列操作：

1. 显示连接的 IPsec/IKE 策略。
2. 向连接添加或更新 IPsec/IKE 策略。
3. 从连接中删除 IPsec/IKE 策略。

> [!NOTE]
> IPsec/IKE 策略仅受标准 VPN 网关和基于路由的高性能 VPN 网关支持。 它不适用于*基本*网关 SKU。

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. 显示连接的 IPsec/IKE 策略

以下示例展示如何对连接配置 IPsec/IKE 策略。 脚本还会继续前面的练习：

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最后一个命令列出在连接上配置的当前 IPsec/IKE 策略（如果有）。 下面的示例是连接的示例输出：

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

如果没有配置 IPsec/IKE 策略，则命令 `$connection6.policy` 获取空返回。 这并不意味着不会在连接上配置 IPsec/IKE;这意味着没有自定义 IPsec/IKE 策略。 实际连接使用本地 VPN 设备和 Azure VPN 网关之间协商的默认策略。

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. 为连接添加或更新 IPsec/IKE 策略

为连接添加新策略或更新现有策略的步骤相同：创建新策略，然后将新策略应用于连接：

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

可以再次获取连接，检查策略是否已更新：

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最后一行应会显示输出，如以下示例所示：

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. 从连接中删除 IPsec/IKE 策略

从连接中删除自定义策略后，Azure VPN 网关会还原为[默认的 IPsec/IKE 提议](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)，并与本地 VPN 设备重新协商。

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = "AzS123"
$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

可使用相同脚本来检查是否已删除连接的策略。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 集线器的 VPN 网关配置设置](azure-stack-vpn-gateway-settings.md)
