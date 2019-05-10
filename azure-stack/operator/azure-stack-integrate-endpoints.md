---
title: Azure Stack 数据中心集成 - 发布终结点 | Microsoft Docs
description: 了解如何在数据中心发布 Azure Stack 终结点
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/02/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 262b1790b102483daca1a77d475815b34aa7a1b2
ms.sourcegitcommit: 61de365c93eb7435891867f9808ec37486882d7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65097112"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack 数据中心集成 - 发布终结点

Azure Stack 为其基础结构角色设置虚拟 IP 地址 (VIP)。 这些 VIP 是从公共 IP 地址池分配的。 每个 VIP 受软件定义的网络层中的访问控制列表 (ACL) 保护。 还可以在物理交换机（TOR 和 BMC）之间使用 ACL 来进一步强化解决方案。 将会根据部署时的指定，针对外部 DNS 区域中的每个终结点创建一个 DNS 条目。


以下体系结构图显示了不同的网络层和 ACL：

![结构化图片](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>端口和协议（入站）

将 Azure Stack 终结点发布到外部网络需要一组基础结构 VIP。 “终结点 (VIP)”表显示了每个终结点、所需的端口和协议。 请参阅特定资源提供程序部署文档，了解需要其他资源提供程序（例如 SQL 资源提供程序）的终结点。

因为它们不需要发布 Azure stack，未列出内部基础结构 Vip。

> [!Note]  
> 用户 VIP 是动态的，由用户自己定义，而不受 Azure Stack 操作员的控制。

> [!Note]
> 自 1811 更新起，由于添加了[扩展主机](azure-stack-extension-host-prepare.md)，因此不再需要打开 12495-30015 范围内的端口。

|终结点 (VIP)|DNS 主机 A 记录|Protocol|端口|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|门户（管理员）|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure 资源管理器（管理员）|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|门户（用户）|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure 资源管理器（用户）|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|图形|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|证书吊销列表|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP 和 UDP|53|
|托管 | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault（用户）|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault（管理员）|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|存储队列|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储表|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储 Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 资源提供程序|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 资源提供程序|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|应用服务|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300（Azure 资源管理器）|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP、UDP|21、1021、10001-10100 (FTP)<br>990 (FTPS)|
|VPN 网关|     |     |[请参阅 VPN 网关常见问题解答](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability)。|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>端口和 URL（出站）

Azure Stack 仅支持透明代理服务器。 如果部署中的透明代理上行链接到传统的代理服务器，则必须允许以下端口和 URL，以便能够进行出站通信：

> [!Note]  
> Azure Stack 不支持使用 ExpressRoute 访问下表中列出的 Azure 服务。

|目的|目标 URL|Protocol|端口|源网络|
|---------|---------|---------|---------|---------|
|标识|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|公共 VIP - /27<br>公共基础结构网络|
|市场联合|https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|公共 VIP - /27|
|修补程序和更新|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|公共 VIP - /27|
|注册|https:\//management.azure.com|HTTPS|443|公共 VIP - /27|
|使用情况|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net |HTTPS|443|公共 VIP - /27|
|Windows Defender|\*.wdcp.microsoft.com<br>\*.wdcpalt.microsoft.com<br>\*.wd.microsoft.com<br>\*.update.microsoft.com<br>\*.download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|公共 VIP - /27<br>公共基础结构网络|
|NTP|（为部署提供的 NTP 服务器的 IP）|UDP|123|公共 VIP - /27|
|DNS|（为部署提供的 DNS 服务器的 IP）|TCP<br>UDP|53|公共 VIP - /27|
|CRL|（证书上的 CRL 分发点下的 URL）|HTTP|80|公共 VIP - /27|
|LDAP|为 Graph 集成提供的 Active Directory 林|TCP<br>UDP|389|公共 VIP - /27|
|LDAP SSL|为 Graph 集成提供的 Active Directory 林|TCP|636|公共 VIP - /27|
|LDAP GC|为 Graph 集成提供的 Active Directory 林|TCP|3268|公共 VIP - /27|
|LDAP GC SSL|为 Graph 集成提供的 Active Directory 林|TCP|3269|公共 VIP - /27|
|AD FS|为 AD FS 集成提供的 AD FS 元数据终结点|TCP|443|公共 VIP - /27|
|     |     |     |     |     |

> [!Note]  
> 使用 Azure 流量管理器对出站 URL 进行负载均衡，以根据地理位置提供尽可能最佳的连接。 使用负载平衡的 Url，Microsoft 可以更新和更改后端终结点而不会影响客户。 Microsoft 不共享 IP 地址的列表，为负载平衡 Url。 应使用支持按 URL 而不是按 IP 筛选的设备。

## <a name="next-steps"></a>后续步骤

[Azure Stack PKI 要求](azure-stack-pki-certs.md)
