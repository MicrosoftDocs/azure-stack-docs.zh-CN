---
title: 排查 Azure Stack 集线器中的站点到站点 VPN 连接问题
description: 在本地网络与 Azure Stack 中心虚拟网络之间配置站点到站点 VPN 连接后，可以执行的故障排除步骤。
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 5277e908fa9f3c5d8fbcebf28b4d766045d03187
ms.sourcegitcommit: 999c6cd0ab64cd2d695feb8405a9c720c9ae755b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2020
ms.locfileid: "83342900"
---
# <a name="troubleshoot-site-to-site-vpn-connections"></a>排查站点到站点 VPN 连接问题

本文介绍在本地网络与 Azure Stack 中心虚拟网络之间配置站点到站点（S2S） VPN 连接后可以执行的故障排除步骤，并且该连接突然停止工作且无法重新连接。

如果本文未解决 Azure Stack 中心问题，则可以访问[Azure Stack 中心 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)。

还可提交 Azure 支持请求。 请参阅[Azure Stack 中心支持](../operator/azure-stack-manage-basics.md#where-to-get-support)。

## <a name="initial-troubleshooting-steps"></a>初始故障排除步骤

IPsec/IKEV2 的 Azure Stack 集线器默认参数已[从 1910 build 开始](../user/azure-stack-vpn-gateway-settings.md#ike-phase-1-main-mode-parameters)更改。请联系你的 Azure Stack 中心操作员获取有关生成版本的详细信息。

> [!IMPORTANT]
> 使用 S2S 隧道时，数据包将与其他标头进一步封装。 此封装增加了数据包的总大小。 在这些情况下，必须将 TCP **MSS**固定在**1350**。 如果 VPN 设备不支持 MSS 钳位，则可以改为将隧道接口上的 MTU 设置为**1400**字节。 有关详细信息，请参阅[虚拟网络 TCPIP 性能优化](/azure/virtual-network/virtual-network-tcpip-performance-tuning)。

- 确认 VPN 配置是基于路由的（IKEv2）。 Azure Stack 中心不支持基于策略的（IKEv1）配置。

- 检查是否使用的是[已验证的 VPN 设备和操作系统版本](/azure/vpn-gateway/vpn-gateway-about-vpn-devices#devicetable)。 如果设备是未经验证的 VPN 设备，你可能需要与设备制造商联系，了解是否存在兼容性问题。

- 验证 VPN 对等 Ip：

  - Azure Stack 中心中**本地网络网关**对象的 IP 定义应与本地设备 IP 匹配。

  - 在本地设备上设置 Azure Stack 中心网关 IP 定义应与 Azure Stack 中心网关 IP 匹配。

## <a name="status-not-connected---intermittent-disconnects"></a>状态 "未连接"-间歇断开连接

- 比较本地 VPN 设备与 AzSH 虚拟网络 VPN 的共享密钥，确保密钥匹配。 若要查看 AzSH VPN 连接的共享密钥，请使用以下方法之一：

  - **Azure Stack 中心租户门户**：中转到已创建的 VPN 网关站点到站点连接。 在 "**设置**" 部分中，选择 "**共享密钥**"。

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="VPN 连接":::

  - **Azure PowerShell**：使用以下 PowerShell 命令：

      ```powershell
      Get-AzureRMVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
      ```

## <a name="status-connected--traffic-not-flowing"></a>状态 "已连接" –流量未流动

- 检查并删除网关子网中的用户定义路由（UDR）和网络安全组（Nsg），然后测试结果。 如果问题得到解决，请验证 NSG 或 UDR 应用的设置。

   网关子网上用户定义的路由可能会限制某些流量，并允许其他流量。 这使得 VPN 连接看起来对于某些流量不可靠，而对于其他流量很有用。

- 检查本地 VPN 设备的外部接口地址。 

  - 如果 VPN 设备面向 internet 的 IP 地址包含在 Azure Stack 集线器的 "**本地网络**" 定义中，则可能会遇到偶尔断开连接。

  - 设备的外部接口必须直接连接到 internet。 在 internet 和设备之间不应存在网络地址转换或防火墙。

  - 若要将防火墙群集配置为具有虚拟 IP，必须中断群集并直接向使用网关可以通过其接口的公共接口公开 VPN 设备。

- 验证子网是否完全匹配。

  - 验证虚拟网络地址空间与 Azure Stack 中心虚拟网络和本地定义之间的完全匹配。

  - 验证“本地网络网关”与本地网络本地定义之间的子网是否完全匹配。****

## <a name="create-a-support-ticket"></a>创建支持票证

如果前面的步骤都无法解决你的问题，请创建[支持票证](../operator/azure-stack-manage-basics.md#where-to-get-support)，并使用按[需日志收集工具](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md)来提供日志。
