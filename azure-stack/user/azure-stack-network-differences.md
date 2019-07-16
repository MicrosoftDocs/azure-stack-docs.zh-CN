---
title: Azure Stack 网络差异 |Microsoft Docs
description: 了解 Azure Stack 中网络的差异和用法注意事项。
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 07/10/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/10/2019
ms.openlocfilehash: 7e0f533c10e8dae0566284ffb09cfa7281213002
ms.sourcegitcommit: ca7e6b7b9b27d0d93ee4d5d1eeaf3113bbcea4da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68229438"
---
# <a name="differences-and-considerations-for-azure-stack-networking"></a>差异和 Azure Stack 网络注意事项

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 网络具有许多由 Azure 网络提供的功能。 但是，在部署 Azure Stack 网络之前，你应该了解一些关键区别。

本文概述 Azure Stack 网络及其功能的独特注意事项。 有关 Azure Stack 与 Azure 之间的大致差异的详细信息，请参阅[重要注意事项](azure-stack-considerations.md)一文。

## <a name="cheat-sheet-networking-differences"></a>速查表：网络差异

| 服务 | 功能 | Azure（公有云） | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | 多租户 DNS | 支持 | 尚不支持 |
|  | DNS AAAA 记录 | 支持 | 不支持 |
|  | 每个订阅的 DNS 区域数 | 100（默认值）<br>可以请求增加。 | 100 |
|  | 每个区域的 DNS 记录集数 | 5000（默认值）<br>可以请求增加。 | 5000 |
|  | 用于区域委派的名称服务器 | Azure 为创建的每个用户（租户）区域提供四个名称服务器。 | Azure Stack 为创建的每个用户（租户）区域提供两个名称服务器。 |
| Azure 防火墙 | 网络安全服务 | Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 | 尚不支持。 |
| 虚拟网络 | 虚拟网络对等互连 | 通过 Azure 主干网络连接同一区域中的两个虚拟网络。 | 尚不支持 |
|  | IPv6 地址 | 可以分配 IPv6 地址作为[网络接口配置](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions)的一部分。 | 仅支持 IPv4。 |
|  | DDoS 防护计划 | 支持 | 尚不支持。 |
|  | 规模集 IP 配置 | 支持 | 尚不支持。 |
|  | 专用访问服务（子网） | 支持 | 尚不支持。 |
|  | 服务终结点 | 支持 Azure 服务的内部（非Internet）连接。 | 尚不支持。 |
|  | 服务终结点策略 | 支持 | 尚不支持。 |
|  | 服务隧道 | 支持 | 尚不支持。  |
| 网络安全组 | 扩充式安全规则 | 支持 | 尚不支持。 |
|  | 有效安全规则 | 支持 | 尚不支持。 |
|  | 应用程序安全组 | 支持 | 尚不支持。 |
| 虚拟网络网关 | 点到站点 VPN 网关 | 支持 | 尚不支持。 |
|  | VNet 到 VNet 网关 | 支持 | 尚不支持。 |
|  | 虚拟网络网关类型 | Azure 支持 VPN<br> Express Route <br> 超 Net。 | Azure Stack 目前仅支持 VPN 类型。 |
|  | VPN 网关 SKU | 支持基本、GW1、GW2、GW3、标准高性能、超高性能 SKU。 | 支持基本、标准和高性能 SKU。 |
|  | VPN 类型 | 基于策略的和基于路由的 azure 支持。 | Azure Stack 支持基于路由的仅。 |
|  | BGP 设置 | Azure 支持配置“BGP 对等互连地址”和“对等互连权重”。 | “BGP 对等互连地址”和“对等互连权重”在 Azure Stack 中自动配置。 没有任何方式，供用户使用他们自己的值配置这些设置。 |
|  | 默认网关站点 | Azure 支持为强制隧道配置默认站点。 | 尚不支持。 |
|  | 网关大小调整 | Azure 支持在部署后调整网关大小。 | 调整大小不受支持。 |
|  | 可用性配置 | 主动/主动 | 主动/被动 |
|  | UsePolicyBasedTrafficSelectors | Azure 支持将基于策略的流量选择器与基于路由的网关连接配合使用。 | 尚不支持。 |
| 负载均衡 | SKU | 支持基本和标准负载均衡器。 | 仅支持基本负载均衡器。<br>不支持 SKU 属性。<br>基本 SKU 负载均衡器 /path/ 不能有 5 个以上的前端 IP 配置。  |
|  | 区域 | 支持可用性区域。 | 尚不支持 |
|  | 服务终结点的入站 NAT 规则支持 | Azure 支持为入站 NAT 规则指定服务终结点。 | Azure Stack 尚不支持服务终结点，因此不能指定这些。 |
|  | Protocol | Azure 支持指定 GRE 或 ESP。 | 在 Azure Stack 中不支持协议类。 |
| 公共 IP 地址 | 公共 IP 地址版本 | Azure 支持 IPv6 和 IPv4。 | 仅支持 IPv4。 |
| 网络接口 | 获取有效路由表 | 支持 | 尚不支持。 |
|  | 获取有效 ACL | 支持 | 尚不支持。 |
|  | 启用加速网络 | 支持 | 尚不支持。 |
|  | IP 转发 | 默认已禁用。  可以启用。 | 切换此设置不受支持。  默认已启用。 |
|  | 应用程序安全组 | 支持 | 尚不支持。 |
|  | 内部 DNS 名称标签 | 支持 | 尚不支持。 |
|  | 专用 IP 地址版本 | 支持 IPv6 和 IPv4。 | 仅支持 IPv4。 |
|  | 静态 MAC 地址 | 不支持 | 不支持。 每个 Azure Stack 系统使用相同的 MAC 地址池。 |
| 网络观察程序 | 网络观察程序租户网络监视功能 | 支持 | 尚不支持。 |
| CDN | 内容分发网络配置文件 | 支持 | 尚不支持。 |
| 应用程序网关 | 7 层负载均衡 | 支持 | 尚不支持。 |
| 流量管理器 | 路由传入的流量，以获得最佳应用程序性能和可靠性。 | 支持 | 尚不支持。 |
| Express Route | 设置快速的专用连接到 Microsoft 云服务从你的本地基础结构或共置设施。 | 支持 | 支持将 Azure Stack 连接到 Express Route 线路。 |

## <a name="next-steps"></a>后续步骤

[Azure Stack 中的 DNS](azure-stack-dns.md)
