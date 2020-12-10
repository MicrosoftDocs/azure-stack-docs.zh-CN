---
title: Azure Stack 中心耐用 Azure Stack 集线器已知问题
description: 了解 Azure Stack 中心 Azure Stack 中心耐用中的已知问题。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: 4b75024a1ff747d39f4f2194d0d86c3882475dee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939398"
---
# <a name="known-issues-in-azure-stack-hub-ruggedized"></a>Azure Stack 中心耐用中的已知问题 

本文列出了 Azure Stack Hub 耐用中的已知问题。 每当发现新的问题，此列表就会更新。

## <a name="update"></a>更新

有关已知的 Azure Stack Hub 更新问题，请参阅[排查 Azure Stack Hub 中的更新问题](../operator/azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates)。

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>更新未能将包 Test-azurestack 安装到 CA VM

- 适用于：此问题适用于所有支持的版本。
- 原因：在更新过程中，进程会锁定需要复制到 CA VM 的新内容。 当更新失败时，将释放锁定。
- 修正：继续更新。
- 发生次数：罕见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。 
- 补救措施：如果有资源在这两个订阅上运行，请在用户订阅中重新创建这些资源。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="network-security-groups"></a>网络安全组

#### <a name="denyalloutbound-rule-cannot-be-created"></a>无法创建 DenyAllOutbound 规则

- 适用于：此问题适用于所有支持的版本。
- 原因：VM 创建期间无法在 NSG 中创建到 Internet 的显式 DenyAllOutbound 规则，因为这会使 VM 部署所需的通信无法完成。 它还将拒绝部署 Vm 所需的两个基本 Ip： DHCP IP：169.254.169.254 和 DNS IP：168.63.129.16。

- 补救措施：VM 创建期间允许出站流量流向 Internet，并在 VM 创建完成后修改 NSG 以阻止所需的流量。
- 发生次数：通用

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关概述页中的文档链接链接到特定于 Azure 的文档，而不是 Azure Stack Hub 文档。 使用以下链接获取 Azure Stack Hub 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack Hub 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](../operator/azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>负载均衡器

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>在特定情况下将流量定向到一个后端 VM 的负载均衡器

- 适用于：此问题适用于所有支持的版本。 
- 原因：在负载均衡器上启用“会话相关性”时，2 元组哈希使用 PA IP（物理地址 IP）而不是分配给 VM 的专用 IP。 如果定向到负载均衡器的流量通过 VPN 到达，或者，如果所有客户端 VM（源 IP）位于同一节点上并且启用了会话相关性，则所有流量都将定向到一个后端 VM。
- 发生次数：通用
