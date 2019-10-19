---
title: Azure Stack 网络差异 |Microsoft Docs
description: 了解在 Azure Stack 中使用网络时的差异和注意事项。
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 10/10/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/10/2019
ms.openlocfilehash: 09e75656fc6a00181ffb31087e19e80b92760ed2
ms.sourcegitcommit: b72729305234e13c65de3771cb08678d46ba1348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72543675"
---
# <a name="differences-and-considerations-for-azure-stack-networking"></a>Azure Stack 网络的差异和注意事项

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 网络具有 Azure 网络提供的许多功能。 但是，在部署 Azure Stack 网络之前，你应该了解一些重要的差异。

本文概述了 Azure Stack 网络及其功能的独特注意事项。 若要了解 Azure Stack 与 Azure 之间的高级差异，请参阅[关键注意事项](azure-stack-considerations.md)一文。

## <a name="cheat-sheet-networking-differences"></a>备忘单：网络差异

| 服务 | Feature | Azure （全局） | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | 多租户 DNS | 受支持 | 尚不支持 |
|  | DNS AAAA 记录 | 受支持 | 不支持 |
|  | 每个订阅的 DNS 区域 | 100（默认值）<br>可以按请求增加。 | 100 |
|  | 每个区域的 DNS 记录集 | 5000（默认值）<br>可以按请求增加。 | 5000 |
|  | 区域委派的名称服务器 | Azure 为创建的每个用户（租户）区域提供4个名称服务器。 | Azure Stack 为创建的每个用户（租户）区域提供两个名称服务器。 |
| Azure 防火墙 | 网络安全服务 | Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 | 尚不支持。 |
| 虚拟网络 | 虚拟网络对等互连 | 通过 Azure 主干网络连接同一区域中的两个虚拟网络。 | 尚不支持 |
|  | IPv6 地址 | 你可以将 IPv6 地址分配为[网络接口配置](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)的一部分。 | 仅支持 IPv4。 |
|  | DDoS 保护计划 | 受支持 | 尚不支持。 |
|  | 规模集 IP 配置 | 受支持 | 尚不支持。 |
|  | 专用访问服务（子网） | 受支持 | 尚不支持。 |
|  | 服务终结点 | 与 Azure 服务的内部（非 Internet）连接支持。 | 尚不支持。 |
|  | 服务终结点策略 | 受支持 | 尚不支持。 |
|  | 服务隧道 | 受支持 | 尚不支持。  |
| 网络安全组 | 扩充的安全规则 | 受支持 | 尚不支持。 |
|  | 有效的安全规则 | 受支持 | 尚不支持。 |
|  | 应用程序安全组 | 受支持 | 尚不支持。 |
| 虚拟网络网关 | 点到站点 VPN 网关 | 受支持 | 尚不支持。 |
|  | Vnet 到 Vnet 网关 | 受支持 | 尚不支持。 |
|  | 虚拟网络网关类型 | Azure 支持 VPN<br> 快速路由 <br> 超级网络。 | Azure Stack 当前仅支持 VPN 类型。 |
|  | VPN 网关 Sku | 支持基本、GW1、GW2、GW3、标准高性能和超高性能。 | 支持基本、标准和高性能 Sku。 |
|  | VPN 类型 | Azure 支持基于策略和基于路由的。 | Azure Stack 仅支持路由。 |
|  | BGP 设置 | Azure 支持配置 BGP 对等互连地址和对等互连。 | BGP 对等互连地址和对等权重在 Azure Stack 中自动配置。 用户无法将这些设置配置为自己的值。 |
|  | 默认网关站点 | Azure 支持为强制隧道配置默认网站。 | 尚不支持。 |
|  | 网关调整大小 | Azure 支持在部署后调整网关大小。 | 不支持调整大小。 |
|  | 可用性配置 | 主动/主动 | 主动/被动 |
|  | UsePolicyBasedTrafficSelectors | Azure 支持使用基于策略的流量选择器和基于路由的网关连接。 | 尚不支持。 |
| 负载均衡 | SKU | 支持基本和标准负载均衡器 | 仅支持基本负载均衡器。<br>不支持 SKU 属性。<br>基本 SKU 负载均衡器/path/的前端 IP 配置不能超过5个。  |
|  | 区域 | 支持可用性区域。 | 尚不支持 |
|  | 针对服务终结点的入站 NAT 规则支持 | Azure 支持为入站 NAT 规则指定服务终结点。 | Azure Stack 尚不支持服务终结点，因此无法指定这些终结点。 |
|  | 协议 | Azure 支持指定 GRE 或 ESP。 | Azure Stack 中不支持协议类。 |
| 公共 IP 地址 | 公共 IP 地址版本 | Azure 同时支持 IPv6 和 IPv4。 | 仅支持 IPv4。 |
| 网络接口 | 获取有效的路由表 | 受支持 | 尚不支持。 |
|  | 获取有效的 Acl | 受支持 | 尚不支持。 |
|  | 启用加速网络 | 受支持 | 尚不支持。 |
|  | IP 转发 | 默认情况下禁用。  可以启用。 | 不支持切换此设置。  默认情况下启用。 |
|  | 应用程序安全组 | 受支持 | 尚不支持。 |
|  | 内部 DNS 名称标签 | 受支持 | 尚不支持。 |
|  | 专用 IP 地址版本 | IPv6 和 IPv4 都受支持。 | 仅支持 IPv4。 |
|  | 静态 MAC 地址 | 不支持 | 不支持。 每个 Azure Stack 系统使用同一个 MAC 地址池。 |
| 网络观察程序 | 网络观察程序租户网络监视功能 | 受支持 | 尚不支持。 |
| CDN | 内容交付网络配置文件 | 受支持 | 尚不支持。 |
| 应用程序网关 | 第7层负载均衡 | 受支持 | 尚不支持。 |
| 流量管理器 | 路由传入流量，以获得最佳应用程序性能和可靠性。 | 受支持 | 尚不支持。 |
| 快速路由 | 通过本地基础结构或归置设施建立与 Microsoft 云服务的快速专用连接。 | 受支持 | 支持将 Azure Stack 连接到 Express Route 线路。 |

## <a name="api-versions"></a>API 版本 

Azure Stack 网络支持以下 API 版本： 

- 2018-11-01
- 2018-10-01
- 2018-08-01
- 2018-07-01
- 2018-06-01
- 2018-05-01
- 2018-04-01
- 2018-03-01
- 2018-02-01
- 2018-01-01
- 2017-11-01
- 2017-10-01

## <a name="next-steps"></a>后续步骤

[Azure Stack 中的 DNS](azure-stack-dns.md)
