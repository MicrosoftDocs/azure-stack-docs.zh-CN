---
title: Azure Stack 中心集成系统 Azure Stack 集线器防火墙集成 |Microsoft Docs
description: 了解 Azure Stack 中心集成系统 Azure Stack 集线器防火墙集成。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 5b480b92bced0af040a75f97cc8094a17de90ffe
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818292"
---
# <a name="azure-stack-hub-firewall-integration"></a>Azure Stack 集线器防火墙集成
建议使用防火墙设备来帮助保护 Azure Stack 中心。 防火墙可以帮助防御分布式拒绝服务（DDOS）攻击、入侵检测和内容检查等内容。 但是，它们也可能成为 Azure 存储服务（如 blob、表和队列）的吞吐量瓶颈。

 如果使用了断开连接的部署模式，则必须发布 AD FS 终结点。 有关详细信息，请参阅[数据中心集成标识一文](azure-stack-integrate-identity.md)。

Azure 资源管理器（管理员）、管理员门户和 Key Vault （管理员）终结点不一定需要外部发布。 例如，作为服务提供商，你可以通过仅从网络内部管理 Azure Stack 集线器（而不是从 internet）来限制攻击面。

对于企业组织，外部网络可以是现有公司网络。 在这种情况下，必须发布终结点才能从企业网络 Azure Stack 集线器。

### <a name="network-address-translation"></a>网络地址转换
建议使用网络地址转换（NAT）方法，以便在部署期间允许部署虚拟机（DVM）访问外部资源和 internet，以及在期间使用紧急恢复控制台（ERCS） Vm 或特权终结点（PEP）注册和故障排除。

NAT 也可以替代外部网络上的公共 IP 地址或公共 Vip。 但是，不建议这样做，因为它会限制租户用户体验并增加复杂性。 一个选项是一个 NAT，在池中每个用户 IP 仍需要一个公共 IP。 另一个选项是 "多对一个 NAT"，它需要用户可以使用的所有端口的每个用户 VIP 的 NAT 规则。

使用用于公共 VIP 的 NAT 的部分缺点是：
- NAT 在管理防火墙规则时增加了开销，因为用户在软件定义的网络（SDN）堆栈中控制自己的终结点及其自己的发布规则。 用户必须与 Azure Stack 中心操作员联系，才能发布其 Vip 并更新端口列表。
- 尽管 NAT 使用限制了用户体验，但它为操作员提供了对发布请求的完全控制权。
- 对于使用 Azure 的混合云方案，请考虑 Azure 不支持使用 NAT 设置到终结点的 VPN 隧道。

### <a name="ssl-interception"></a>SSL 拦截
目前建议在所有 Azure Stack 集线器流量上禁用任何 SSL 拦截（例如解密卸载）。 如果在将来的更新中受支持，将提供有关如何为 Azure Stack 中心启用 SSL 拦截的指南。

## <a name="edge-firewall-scenario"></a>边缘防火墙方案
在边缘部署中，Azure Stack 集线器直接部署在边缘路由器或防火墙后面。 在这些情况下，防火墙支持在边框的上方（方案1），其中支持主动-主动和主动-被动防火墙配置，或者充当边框设备（方案2），其中仅支持主动-主动防火墙配置依赖于相等成本多路径（ECMP）进行故障转移的 BGP 或静态路由。

公共可路由 IP 地址是在部署时为外部网络中的公共 VIP 池指定的。 在边缘方案中，出于安全考虑，不建议使用任何其他网络上的公共可路由 Ip。 此方案使用户能够体验到 Azure 等公有云中的完全自控制云体验。  

![Azure Stack 集线器边缘防火墙示例](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>企业 intranet 或外围网络防火墙方案
在企业 intranet 或外围部署中，Azure Stack 集线器部署在多分区防火墙上，或部署在边缘防火墙与内部企业网络防火墙之间。 然后，在安全区域、外围网络（或 DMZ）与不安全区域之间分配其流量，如下所述：

- **安全区域**：这是使用内部或公司可路由 IP 地址的内部网络。 可以通过防火墙上的 NAT 对安全网络进行分割，使其具有 internet 出站访问权限，并且通常可以通过内部网络从数据中心内的任何位置进行访问。 所有 Azure Stack 集线器网络应驻留在安全区域中，外部网络的公共 VIP 池除外。
- **外围区域**。 外围网络是指通常会在外部或面向 internet 的应用程序（如 Web 服务器）上部署。 它通常由防火墙进行监视，以避免在允许来自 internet 的指定入站流量的情况下出现 DDoS 和入侵（黑客）的攻击。 只有 Azure Stack 集线器的外部网络公共 VIP 池应驻留在 DMZ 区域中。
- 不**安全区域**。 这是外部网络，即 internet。 不建议在**不**安全区域部署 Azure Stack 中心。

![Azure Stack 集线器外围网络示例](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>了解更多
了解有关[Azure Stack 中心终结点使用的端口和协议的](azure-stack-integrate-endpoints.md)详细信息。

## <a name="next-steps"></a>后续步骤
[Azure Stack 中心 PKI 要求](azure-stack-pki-certs.md)

