---
title: Publish Azure Stack services in your datacenter | Microsoft Docs
description: Learn how to publish Azure Stack services in your datacenter.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 11/22/2019
ms.author: justinha
ms.reviewer: wamota
ms.lastreviewed: 11/22/2019
ms.openlocfilehash: 4104698b8d615f1078573009a65bd26c361fb0f2
ms.sourcegitcommit: b4de234174864659eb7e08aa5c10de59f5fa6f43
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74478488"
---
# <a name="publish-azure-stack-services-in-your-datacenter"></a>Publish Azure Stack services in your datacenter 

Azure Stack sets up virtual IP addresses (VIPs) for its infrastructure roles. These VIPs are allocated from the public IP address pool. Each VIP is secured with an access control list (ACL) in the software-defined network layer. ACLs are also used across the physical switches (TORs and BMC) to further harden the solution. A DNS entry is created for each endpoint in the external DNS zone that's specified at deployment time. For example, the user portal is assigned the DNS host entry of portal. *&lt;region>.&lt;fqdn>* .

The following architectural diagram shows the different network layers and ACLs:

![Diagram showing different network layers and ACLs](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

### <a name="ports-and-urls"></a>Ports and URLs

To make Azure Stack services (like the portals, Azure Resource Manager, DNS, and so on) available to external networks, you must allow inbound traffic to these endpoints for specific URLs, ports, and protocols.
 
In a deployment where a transparent proxy uplinks to a traditional proxy server or a firewall is protecting the solution, you must allow specific ports and URLs for both [inbound](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) and [outbound](azure-stack-integrate-endpoints.md#ports-and-urls-outbound) communication. These include ports and URLs for identity, the marketplace, patch and update, registration, and usage data.

SSL traffic interception is [not supported](azure-stack-firewall.md#ssl-interception) and can lead to service failures when accessing endpoints. 

## <a name="ports-and-protocols-inbound"></a>Ports and protocols (inbound)

A set of infrastructure VIPs is required for publishing Azure Stack endpoints to external networks. The *Endpoint (VIP)* table shows each endpoint, the required port, and protocol. Refer to the specific resource provider deployment documentation for endpoints that require additional resource providers, like the SQL resource provider.

Internal infrastructure VIPs aren't listed because they're not required for publishing Azure Stack. User VIPs are dynamic and defined by the users themselves, with no control by the Azure Stack operator.

> [!Note]  
> IKEv2 VPN is a standards-based IPsec VPN solution that uses UDP port 500 and 4500 and TCP port 50. Firewalls don't always open these ports, so an IKEv2 VPN might not be able to traverse proxies and firewalls.

With the addition of the [Extension Host](azure-stack-extension-host-prepare.md), ports in the range of 12495-30015 aren't required.

|Endpoint (VIP)|DNS host A record|协议|端口|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrator)|Adminportal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (administrator)|Adminmanagement. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (user)|Portal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (user)|Management. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|图形|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Certificate revocation list|Crl. *&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;. *&lt;region>.&lt;fqdn>*|TCP & UDP|53|
|托管 | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (user)|&#42;.vault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (administrator)|&#42;.adminvault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|存储队列|&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储表|&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储 Blob|&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 资源提供程序|sqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 资源提供程序|mysqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|应用服务|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP、UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|VPN 网关|     |     |[See the VPN gateway FAQ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Ports and URLs (outbound)

Azure Stack supports only transparent proxy servers. In a deployment with a transparent proxy uplink to a traditional proxy server, you must allow the ports and URLs in the following table for outbound communication.

SSL traffic interception is [not supported](azure-stack-firewall.md#ssl-interception) and can lead to service failures when accessing endpoints. The maximum supported timeout to communicate with endpoints required for identity is 60s.

> [!Note]  
> Azure Stack doesn't support using ExpressRoute to reach the Azure services listed in the following table because ExpressRoute may not be able to route traffic to all of the endpoints.

|用途|Destination URL|协议|端口|Source Network|
|---------|---------|---------|---------|---------|
|身份标识|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>https:\//\*.msftauth.net<br>https:\//\*.msauth.net<br>https:\//\*.msocdn.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//graph.windows.net/<br>**Azure 中国世纪互联**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure 德国**<br>https:\//login.microsoftonline.de/<br>https:\//graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Public VIP - /27<br>Public infrastructure Network|
|Marketplace syndication|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>https://&#42;.blob.core.usgovcloudapi.net/<br>**Azure 中国世纪互联**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn|HTTPS|443|Public VIP - /27|
|Patch & Update|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|Public VIP - /27|
|注册|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>**Azure 中国世纪互联**<br>https:\//management.chinacloudapi.cn|HTTPS|443|Public VIP - /27|
|使用情况|**Azure**<br>https://&#42;.trafficmanager.net<br>**Azure Government**<br>https://&#42;.usgovtrafficmanager.net<br>**Azure 中国世纪互联**<br>https://&#42;.trafficmanager.cn|HTTPS|443|Public VIP - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Public VIP - /27<br>Public infrastructure Network|
|NTP|(IP of NTP server provided for deployment)|UDP|123|Public VIP - /27|
|DNS|(IP of DNS server provided for deployment)|TCP<br>UDP|53|Public VIP - /27|
|CRL|(URL under CRL Distribution Points on your certificate)|HTTP|80|Public VIP - /27|
|LDAP|Active Directory Forest provided for Graph integration|TCP<br>UDP|389|Public VIP - /27|
|LDAP SSL|Active Directory Forest provided for Graph integration|TCP|636|Public VIP - /27|
|LDAP GC|Active Directory Forest provided for Graph integration|TCP|3268|Public VIP - /27|
|LDAP GC SSL|Active Directory Forest provided for Graph integration|TCP|3269|Public VIP - /27|
|AD FS|AD FS metadata endpoint provided for AD FS integration|TCP|443|Public VIP - /27|
|Diagnostic Log collection service|Azure Storage provided Blob SAS URL|HTTPS|443|Public VIP - /27|
|     |     |     |     |     |

Outbound URLs are load balanced using Azure traffic manager to provide the best possible connectivity based on geographic location. With load balanced URLs, Microsoft can update and change backend endpoints without affecting customers. Microsoft doesn't share the list of IP addresses for the load balanced URLs. Use a device that supports filtering by URL rather than by IP.

Outbound DNS is required at all times; what varies is the source querying the external DNS and what type of identity integration was chosen. During deployment for a connected scenario, the DVM that sits on the BMC network needs outbound access. But after deployment, the DNS service moves to an internal component that will send queries through a Public VIP. At that time, the outbound DNS access through the BMC network can be removed, but the Public VIP access to that DNS server must remain or else authentication will fail.

## <a name="next-steps"></a>后续步骤

[Azure Stack PKI requirements](azure-stack-pki-certs.md)
