---
title: New-AksHciNetworkSetting
author: jessicaguan
description: New-AksHciNetworkSetting PowerShell 命令将为新虚拟网络创建对象。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 5babbe99254b23d642696b7ce7c5b865105bd906
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873577"
---
# <a name="new-akshcinetworksetting"></a>New-AksHciNetworkSetting

## <a name="synopsis"></a>摘要
为新的虚拟网络创建对象。

## <a name="syntax"></a>语法
```powershell
New-AksHciNetworkSetting -vnetName <String>
                         -gateway <String>
                         -dnsServers <String[]>
                         -ipAddressPrefix <String>
                         -vipPoolStart <IP address>
                         -vipPoolEnd <IP address>
                         -k8sNodeIpPoolStart <IP address>
                         -k8sNodeIpPoolEnd <IP address>
                        [-vlanID <int>]
                    
```

## <a name="description"></a>说明
创建虚拟网络，以便为所有 Kubernetes 群集中节点的控制平面、负载均衡器、代理终结点和静态 IP 范围设置 DHCP 或静态 IP 地址。 此 cmdlet 将返回 VirtualNetwork 对象，该对象可在稍后的配置步骤中使用。

## <a name="examples"></a>示例

### <a name="deploy-with-a-static-ip-environment"></a>使用静态 IP 环境进行部署

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -k8sNodeIpPoolStart "172.16.10.0" -k8sNodeIpPoolEnd "172.16.10.255" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" -ipAddressPrefix "172.16.0.0/16" -gateway "172.16.0.1" -dnsServers "172.16.0.1" 
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16"
```

> [!NOTE]
> 需要为你的环境自定义此示例命令中给定的值。

### <a name="deploy-with-a-dhcp-environment"></a>使用 DHCP 环境进行部署

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" 
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData"
```

## <a name="parameters"></a>参数

### <a name="-vnetname"></a>-vnetName
外部交换机的名称。 若要获取可用交换机的名称列表，请运行命令 `Get-VMSwitch` 。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-gateway"></a>-网关
子网的默认网关的 IP 地址。

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-dnsservers"></a>-dnsServers
创建具有静态 IP 的网络时是必需的。 指向要用于子网的 DNS 服务器的 IP 地址的数组。 最少可提供1个和3个服务器。 例如 "8.8.8.8"、"192.168.1.1"。

```yaml
Type: System.String[]
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-ipaddressprefix"></a>-ipAddressPrefix
用于静态 IP 分配的地址前缀。

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: True
Position: Named
Default value: external
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vippoolstart"></a>-vipPoolStart
VIP 池的起始 IP 地址。 该地址必须在 DHCP 服务器提供的范围内，或者在子网 CIDR 中提供的范围内。 VIP 池中的 IP 地址将用于 API 服务器和 Kubernetes 服务。 如果你使用的是 DHCP，请确保你的虚拟 IP 地址属于 DHCP IP 预留。 如果使用的是静态 IP，请确保虚拟 ip 来自同一子网。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vippoolend"></a>-vipPoolEnd
VIP 池的结束 IP 地址。 该地址必须在 DHCP 服务器提供的范围内，或者在子网 CIDR 中提供的范围内。 VIP 池中的 IP 地址将用于 API 服务器和 Kubernetes 服务。 如果你使用的是 DHCP，请确保你的虚拟 IP 地址属于 DHCP IP 预留。 如果使用的是静态 IP，请确保虚拟 ip 来自同一子网。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-k8snodeippoolstart"></a>-k8sNodeIpPoolStart
VM 池的起始 IP 地址。 该地址必须在子网范围内。 这对于静态 IP 部署是必需的。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-k8snodeippoolend"></a>-k8sNodeIpPoolEnd
VM 池的结束 IP 地址。 该地址必须在子网范围内。 这对于静态 IP 部署是必需的。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vlanid"></a>-vlanID
指定的网络的 vLAN ID。 如果省略，则不会标记网络。

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

<!--- ### -macPoolName
The name of the MAC address pool that you wish to use for the Azure Kubernetes Service host VM. The pool will be created with the New-AksHciMacPoolSetting command.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
--->
