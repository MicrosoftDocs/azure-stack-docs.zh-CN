---
title: 在数据中心发布 Azure Stack 中心服务
description: 了解如何在数据中心发布 Azure Stack 中心服务。
author: ihenkel
ms.topic: article
ms.date: 12/11/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: 5cd828de0e4123faf3fcb7020703ad5d8682c7e1
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882151"
---
# <a name="publish-azure-stack-hub-services-in-your-datacenter"></a>在数据中心发布 Azure Stack 中心服务 

Azure Stack 中心为其基础结构角色设置虚拟 IP 地址（Vip）。 这些 Vip 是从公共 IP 地址池分配的。 每个 VIP 均使用软件定义的网络层中的访问控制列表（ACL）进行保护。 还可跨物理交换机（T) 和 BMC）使用 Acl，进一步强化解决方案。 将为在部署时指定的外部 DNS 区域中的每个终结点创建一个 DNS 条目。 例如，为用户门户指定了门户的 DNS 主机条目。 *&lt;fqdn >&lt;> 区域*。

以下体系结构图显示了不同的网络层和 Acl：

![显示不同网络层和 Acl 的图表](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

### <a name="ports-and-urls"></a>端口和 Url

若要使 Azure Stack 的中心服务（如门户、Azure 资源管理器、DNS 等）可用于外部网络，必须允许将这些终结点的入站流量用于特定的 Url、端口和协议。
 
如果部署中的透明代理与传统代理服务器或防火墙的上行链路正在保护解决方案，则必须允许特定端口和 Url 进行[入站](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)和[出站](azure-stack-integrate-endpoints.md#ports-and-urls-outbound)通信。 其中包括用于标识的端口和 Url、marketplace、修补程序、更新、注册和使用情况数据。

SSL 流量截获[不受支持](azure-stack-firewall.md#ssl-interception)，并且可能在访问终结点时导致服务失败。 

## <a name="ports-and-protocols-inbound"></a>端口和协议（入站）

将 Azure Stack 集线器终结点发布到外部网络需要一组基础结构 Vip。 "*终结点（VIP）* " 表显示了每个终结点、所需的端口和协议。 有关需要其他资源提供程序（如 SQL 资源提供程序）的终结点，请参阅特定资源提供程序部署文档。

未列出内部基础结构 Vip，因为发布 Azure Stack 集线器不需要它们。 用户 Vip 是动态的，由用户自身定义，不受 Azure Stack 中心运营商控制。

> [!Note]  
> IKEv2 VPN 是一种基于标准的 IPsec VPN 解决方案，它使用 UDP 端口500和4500，以及 TCP 端口50。 防火墙并不总是打开这些端口，因此 IKEv2 VPN 可能无法遍历代理和防火墙。

添加[扩展主机](azure-stack-extension-host-prepare.md)后，不需要12495-30015 范围内的端口。

|终结点（VIP）|DNS 主机 A 记录|协议|端口|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;fqdn >&lt;区域 >*|HTTPS|443|
|门户（管理员）|Adminportal. *&lt;fqdn >&lt;区域 >*|HTTPS|443|
|Adminhosting | *.adminhosting.>\<区域。\<fqdn > | HTTPS | 443 |
|Azure 资源管理器（管理员）|Adminmanagement. *&lt;fqdn >&lt;区域 >*|HTTPS|443|
|门户（用户）|端口. *&lt;fqdn >&lt;区域 >*|HTTPS|443|
|Azure 资源管理器（用户）|层. *&lt;fqdn >&lt;区域 >*|HTTPS|443|
|图形|图形. *&lt;fqdn >&lt;区域 >*|HTTPS|443|
|证书吊销列表|*&lt;区域 >&lt;fqdn >*|HTTP|80|
|DNS|&#42;. *&lt;fqdn >&lt;区域 >*|TCP & UDP|53|
|托管 | *。托管。>\<区域。\<fqdn > | HTTPS | 443 |
|Key Vault （用户）|&#42;保管库. *&lt;fqdn >&lt;区域 >*|HTTPS|443|
|Key Vault （管理员）|&#42;.adminvault. *&lt;fqdn >&lt;区域 >*|HTTPS|443|
|存储队列|&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储表|&#42;数据表. *&lt;fqdn >&lt;区域 >*|HTTP<br>HTTPS|80<br>443|
|存储 Blob|&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 资源提供程序|sqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 资源提供程序|mysqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|应用服务|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80（HTTP）<br>443（HTTPS）<br>8172（Msdeploy.exe）|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443（HTTPS）|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443（HTTPS）<br>44300（Azure 资源管理器）|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP、UDP|21，1021，10001-10100 （FTP）<br>990（FTPS）|
|VPN 网关|     |     |[请参阅 VPN 网关常见问题](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability)。|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>端口和 Url （出站）

Azure Stack 集线器仅支持透明代理服务器。 在具有到传统代理服务器的透明代理上行的部署中，必须允许下表中的端口和 Url 进行出站通信。

SSL 流量截获[不受支持](azure-stack-firewall.md#ssl-interception)，并且可能在访问终结点时导致服务失败。 与标识所需的终结点进行通信时支持的最大超时值为60s。

> [!Note]  
> Azure Stack 中心不支持使用 ExpressRoute 访问下表中列出的 Azure 服务，因为 ExpressRoute 可能无法将流量路由到所有终结点。

|用途|目标 URL|协议|端口|源网络|
|---------|---------|---------|---------|---------|
|身份标识|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https：\//management.core.windows.net<br>ARMUri = https：\//management.azure.com<br>https：\//\*msftauth.net<br>https：\//\*msauth.net<br>https：\//\*msocdn.com<br>**Azure Government**<br>https：\//login.microsoftonline.us/<br>https：\//graph.windows.net/<br>**Azure 中国世纪互联**<br>https：\//login.chinacloudapi.cn/<br>https：\//graph.chinacloudapi.cn/<br>**Azure 德国**<br>https：\//login.microsoftonline.de/<br>https：\//graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|公共 VIP-/27<br>公共基础结构网络|
|Marketplace 联合|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https：\//management.usgovcloudapi.net/<br>https://&#42;. blob.core.usgovcloudapi.net/<br>**Azure 中国世纪互联**<br>https：\//management.chinacloudapi.cn/<br>http://&#42;. blob.core.chinacloudapi.cn|HTTPS|443|公共 VIP-/27|
|修补 & 更新|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|公共 VIP-/27|
|注册|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https：\//management.usgovcloudapi.net/<br>**Azure 中国世纪互联**<br>https：\//management.chinacloudapi.cn|HTTPS|443|公共 VIP-/27|
|使用情况|**Azure**<br>https://&#42;. trafficmanager.net<br>**Azure Government**<br>https://&#42;. usgovtrafficmanager.net<br>**Azure 中国世纪互联**<br>https://&#42;. trafficmanager.cn|HTTPS|443|公共 VIP-/27|
|Windows Defender|&#42;。 wdcp.microsoft.com<br>&#42;。 wdcpalt.microsoft.com<br>&#42;。 wd.microsoft.com<br>&#42;。 update.microsoft.com<br>&#42;。 download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https：\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|公共 VIP-/27<br>公共基础结构网络|
|NTP|（为部署提供的 NTP 服务器 IP）|UDP|123|公共 VIP-/27|
|DNS|（为部署提供的 DNS 服务器 IP）|TCP<br>UDP|53|公共 VIP-/27|
|CRL|（证书的 CRL 分发点下的 URL）|HTTP|80|公共 VIP-/27|
|LDAP|为图形集成提供 Active Directory 林|TCP<br>UDP|389|公共 VIP-/27|
|LDAP SSL|为图形集成提供 Active Directory 林|TCP|636|公共 VIP-/27|
|LDAP GC|为图形集成提供 Active Directory 林|TCP|3268|公共 VIP-/27|
|LDAP GC SSL|为图形集成提供 Active Directory 林|TCP|3269|公共 VIP-/27|
|AD FS|为 AD FS 集成提供 AD FS 元数据终结点|TCP|443|公共 VIP-/27|
|诊断日志收集服务|Azure 存储提供的 Blob SAS URL|HTTPS|443|公共 VIP-/27|
|     |     |     |     |     |

使用 Azure 流量管理器对出站 Url 进行负载平衡，以根据地理位置提供可能的最佳连接。 通过负载均衡的 Url，Microsoft 可以更新和更改后端终结点，而不会影响客户。 Microsoft 不会共享负载平衡的 Url 的 IP 地址列表。 使用支持通过 URL （而不是 IP）进行筛选的设备。

所有时候都需要出站 DNS;什么不同是查询外部 DNS 的源和所选的标识集成类型。 在部署过程中，位于 BMC 网络上的 DVM 需要出站访问权限。 但在部署后，DNS 服务会移动到将通过公共 VIP 发送查询的内部组件。 此时，可以删除通过 BMC 网络进行的出站 DNS 访问，但对该 DNS 服务器的公共 VIP 访问权限必须保留，否则身份验证将失败。

## <a name="next-steps"></a>后续步骤

[Azure Stack 中心 PKI 要求](azure-stack-pki-certs.md)
