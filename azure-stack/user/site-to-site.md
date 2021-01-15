---
title: 在 Azure Stack Hub 中排查站点到站点 VPN 连接问题
description: 配置本地网络与 Azure Stack Hub 虚拟网络之间的站点到站点 VPN 连接后可以执行的故障排除步骤。
author: sethmanheim
ms.author: sethm
ms.date: 11/22/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: a39cbd5a845e79114393710c8ec77d37f30a68a0
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243504"
---
# <a name="troubleshoot-site-to-site-vpn-connections"></a>排查站点到站点 VPN 连接问题

本文介绍了在本地网络和 Azure Stack Hub 虚拟网络之间配置站点到站点 (S2S) VPN 连接后，如果该连接突然停止工作且无法重新连接，你可以采取哪些故障排除步骤。

如果本文未解决你的 Azure Stack Hub 问题，可以访问 [Azure Stack Hub MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)。

还可提交 Azure 支持请求。 请参阅 [Azure Stack Hub 支持](../operator/azure-stack-manage-basics.md#where-to-get-support)。

> [!NOTE]
> 在两个 Azure Stack Hub 部署之间只能创建一个站点到站点 VPN 连接。 这是因为平台中的某个限制仅允许同一 IP 地址具有单个 VPN 连接。 由于 Azure Stack Hub 利用多租户网关，该网关将单一公共 IP 用于 Azure Stack Hub 系统中的所有 VPN 网关，因此两个 Azure Stack Hub 系统之间只能有一个 VPN 连接。 此限制也适用于将多个站点到站点 VPN 连接连接到使用单一 IP 地址的任何 VPN 网关。 Azure Stack Hub 不允许使用同一 IP 地址创建多个本地网络网关资源。

## <a name="initial-troubleshooting-steps"></a>初始故障排除步骤

IPsec/IKEV2 的 Azure Stack Hub 默认参数已更改[（从内部版本 1910 开始）](../user/azure-stack-vpn-gateway-settings.md#ike-phase-1-main-mode-parameters)。有关内部版本的详细信息，请与 Azure Stack Hub 操作员联系。

> [!IMPORTANT]
> 使用 S2S 隧道时，数据包将与其他标头一起进一步封装。 此封装会增加数据包的总大小。 在这些情况下，必须将 TCP **MSS** 固定在 **1350**。 如果 VPN 设备不支持 MSS 钳位，则可以改为在隧道接口上将 MTU 设置为 1400 字节。 有关详细信息，请参阅[虚拟网络 TCPIP 性能优化](/azure/virtual-network/virtual-network-tcpip-performance-tuning)。

- 确认 VPN 配置为基于路由 (IKEv2)。 Azure Stack Hub 不支持基于策略的 (IKEv1) 配置。

- 检查是否使用的是[已验证的 VPN 设备和操作系统版本](/azure/vpn-gateway/vpn-gateway-about-vpn-devices#devicetable)。 如果设备是未经验证的 VPN 设备，可能需要与设备制造商联系，了解是否存在兼容性问题。

- 验证 Azure Stack Hub 虚拟网络与本地网络之间是否没有重叠的 IP 范围。 这可能会导致连接问题。 

- 验证 VPN 对等 IP：

  - Azure Stack Hub 的“本地网关”对象中的 IP 定义应与本地设备 IP 匹配。

  - 在本地设备中设置的 Azure Stack Hub 网关 IP 定义应与 Azure Stack Hub 网关 IP 匹配。

## <a name="status-not-connected---intermittent-disconnects"></a>“未连接”状态 - 间歇性断开连接

### <a name="az-modules"></a>[Az 模块](#tab/az)

- 比较本地 VPN 设备与 AzSH 虚拟网络 VPN 的共享密钥，确保密钥匹配。 若要查看 AzSH VPN 连接的共享密钥，请使用以下方法之一：

  - **Azure Stack Hub 租户门户**：转到创建的 VPN 网关站点到站点连接。 在“设置”部分中，选择“共享密钥”。

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="VPN 连接":::

  - **Azure PowerShell**：使用以下 PowerShell 命令：

```powershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
```

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm)

- 比较本地 VPN 设备与 AzSH 虚拟网络 VPN 的共享密钥，确保密钥匹配。 若要查看 AzSH VPN 连接的共享密钥，请使用以下方法之一：

  - **Azure Stack Hub 租户门户**：转到创建的 VPN 网关站点到站点连接。 在“设置”部分中，选择“共享密钥”。

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="VPN 连接":::

  - **Azure PowerShell**：使用以下 PowerShell 命令：

```powershell
Get-AzurerRMVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
```

---

## <a name="status-connected---traffic-not-flowing"></a>“已连接”状态 - 流量未流动

- 检查并删除网关子网中的用户定义的路由 (UDR) 和网络安全组 (NSG)，然后测试结果。 如果问题得到解决，请验证 NSG 或 UDR 应用的设置。

   网关子网上用户定义的路由可能会限制某些流量，并允许其他流量。 这使得 VPN 连接看起来对于某些流量不可靠，而对于其他流量很可靠。

- 检查本地 VPN 设备的外部接口地址。 

  - 如果 VPN 设备面向 Internet 的 IP 地址包含在 Azure Stack Hub 的“本地网络”定义中，可能会出现偶发的断开连接现象。

  - 设备的外部接口必须直接位于 Internet 上。 在 Internet 和设备之间应该没有网络地址转换或防火墙。

  - 若要将防火墙群集配置为具有虚拟 IP，必须断开群集并直接向可以通过网关与之连接的公共接口公开 VPN 设备。

- 验证子网是否完全匹配。

  - 验证 Azure Stack Hub 虚拟网络和本地定义之间的虚拟网络地址空间是否完全匹配。

  - 验证“本地网络网关”与本地网络本地定义之间的子网是否完全匹配。

## <a name="create-a-support-ticket"></a>创建支持票证

如果前面的步骤都无法解决你的问题，请创建[支持票证](../operator/azure-stack-manage-basics.md#where-to-get-support)并使用[按需日志收集工具](../operator/diagnostic-log-collection.md)来提供日志。