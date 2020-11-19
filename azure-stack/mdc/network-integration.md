---
title: 模块化数据中心网络集成
description: 了解 Azure 模块式 Datacenter Azure Stack 网络集成。
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: e2bd1f42cd60aabc3baa153525b2a7aeb03ac30b
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924327"
---
# <a name="network-integration"></a>网络集成

本文介绍了 Azure 模块化数据中心的 Azure Stack 网络集成。

## <a name="border-connectivity-uplink"></a>边界连接 (上行) 

网络集成规划是成功进行 Azure Stack 集成系统部署、操作和管理的重要先决条件。 边界连接计划首先选择是否要将动态路由与边界网关协议 (BGP) 或静态路由一起使用。 动态路由要求你将16位 BGP 自治系统编号（ (public 或 private) 分配）。 静态路由使用分配给边框设备的静态默认路由。

边缘交换机要求第3层上行链路具有点到点 Ip (/30 网络) 在物理接口上进行配置。 不支持其边缘交换机支持 Azure Stack 操作的第 2 层上行链路。

### <a name="bgp-routing"></a>BGP 路由

使用 BGP 等动态路由协议可以保证系统始终会注意到网络更改和便于管理。 为增强安全性，可以在边缘和边框之间设置 BGP 对等互连上的密码。

如下图所示，通过使用前缀列表，将 (TOR) 开关上的专用 IP 空间公布。 前缀列表拒绝专用网络的播发，并将其作为 TOR 与边缘之间的连接的路由映射。

软件负载平衡器 (SLB) 在 Azure Stack 解决方案对等节点内运行，以便能够动态地公布 VIP 地址。

为了确保用户流量能立即且透明地从故障中恢复，虚拟私有云或多底盘链接聚合 (在 TOR 设备之间配置的 MLAG) 允许使用 MLAG 连接到为 IP 网络提供网络冗余的主机和 HSRP 或 VRRP。

### <a name="static-routing"></a>静态路由

静态路由需要额外配置边界设备。 它需要更多的手动干预和管理，以及在任何更改之前进行彻底的分析。 根据所做的更改，可能需要更长时间才能回滚导致配置错误的问题。 不建议采用此路由方法，但支持此方法。

若要通过使用静态路由将 Azure Stack 集成到你的网络环境中，必须连接边框与边缘设备之间的所有四个物理链接。 由于静态路由的工作方式，无法保证高可用性。

必须配置边框设备，使其包含指向每个四个点到点 Ip 集的静态路由，这些 Ip 设置为流向 Azure Stack 内任何网络的流量。 不过，操作只需要 external 或公有 VIP 网络。 初始部署需要到 BMC 和外部网络的静态路由。 操作员可以选择在边框中保留静态路由，以访问位于 BMC 和基础结构网络上的管理资源。 添加指向交换机基础结构和交换机管理网络的静态路由是可选的。

TOR 设备配置有将所有流量发送到边界设备的静态默认路由。 默认规则的一个流量例外是针对专用空间，该空间是使用应用于 TOR 连接的访问控制列表阻止的。

静态路由仅适用于边缘和边框开关之间的上行链路。 机架内使用的是 BGP 动态路由，因为它对于 SLB 和其他组件来说是基本工具，无法禁用或删除。

\* 在部署后，BMC 网络是可选的。

\*\* 交换机基础结构网络是可选的，因为整个网络可以包含在交换机管理网络中。

\*\*\* 交换机管理网络是必需的，可与交换机基础结构网络分开添加。

### <a name="transparent-proxy"></a>透明代理

如果你的数据中心需要所有流量才能使用代理，则必须配置透明代理以处理来自货架的所有流量，使其根据策略进行处理。 你必须在网络上的区域之间分离流量。

Azure Stack 解决方案不支持普通 Web 代理。

透明代理（也称为截获、内联或强制代理）将截获网络层的正常通信，而无需任何特殊的客户端配置。 客户端不需要知道代理是否存在。

SSL 流量截获不受支持，并且可能导致在访问终结点时出现服务故障。 与标识所需的终结点进行通信时，支持的最大超时值为60秒，且具有三次重试。

## <a name="dns"></a>DNS

本部分介绍域名系统 (DNS) 配置。

### <a name="configure-conditional-dns-forwarding"></a>配置条件性 DNS 转发

本指南仅适用于) 部署 AD FS Active Directory 联合身份验证服务 (。

若要通过现有的 DNS 基础结构启用名称解析，请配置条件性转发。

若要添加条件性转发器，必须使用特权终结点。

对于此过程，请使用能够与 Azure Stack 中的特权终结点通信的数据中心网络中的计算机。

1. 打开提升了权限的 Windows PowerShell 会话 (以管理员身份运行) 。 连接到特权终结点的 IP 地址。 使用进行 CloudAdmin 身份验证的凭据。

    ```powershell
    \$cred=Get-Credential Enter-PSSession -ComputerName \<IP Address of ERCS\> -ConfigurationName PrivilegedEndpoint -Credential \$cred 
    ```

1. 连接到特权终结点后，运行以下 PowerShell 命令。 将提供的示例值替换为要使用的 DNS 服务器的域名和 IP 地址。

    ```powershell
    Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2" 
    ```

### <a name="resolve-azure-stack-dns-names-from-outside-azure-stack"></a>解析 Azure Stack 外部 Azure Stack 的 DNS 名称

权威服务器是保存外部 DNS 区域信息和任何用户创建的区域的服务器。 与这些服务器集成即可启用区域委托或条件性转发，以便从 Azure Stack 外部解析 Azure Stack DNS 名称。

### <a name="get-dns-server-external-endpoint-information"></a>获取 DNS 服务器外部终结点信息

若要将 Azure Stack 部署与 DNS 基础结构集成，需提供以下信息：

- DNS 服务器完全限定的域名 (Fqdn) 
- DNS 服务器 IP 地址

Azure Stack DNS 服务器的 FQDN 具有以下格式：

- \<NAMINGPREFIX\>-ns01.\<REGION\>.\<EXTERNALDOMAINNAME\>
- \<NAMINGPREFIX\>-ns02.\<REGION\>.\<EXTERNALDOMAINNAME\>

使用示例值时，DNS 服务器的 FQDN 如下所示：

- azs-ns01.east.cloud.fabrikam.com
- azs-ns02.east.cloud.fabrikam.com

此信息可在管理门户中获得，但也可以在所有 Azure Stack 部署结束时在名为“AzureStackStampInformation.json”的文件中创建。 此文件位于部署虚拟机的 " *C： \\ CloudDeployment \\ logs* " 文件夹中。 如果不确定对 Azure Stack 部署使用了什么值，可以从该文件中获取这些值。

如果部署虚拟机不再可用或无法访问，可以通过连接到特权终结点并运行 *AzureStackStampInformation* PowerShell cmdlet 来获取这些值。 有关详细信息，请参阅特权终结点。

### <a name="set-up-conditional-forwarding-to-azure-stack"></a>设置 Azure Stack 的条件性转发

若要将 Azure Stack 与 DNS 基础结构集成，最简单也最安全的方式是将区域从托管父区域的服务器进行条件性转发。 如果你可以直接控制托管 Azure Stack 外部 DNS 命名空间的父区域的 DNS 服务器，则建议使用此方法。

如果你不熟悉如何使用 DNS 执行条件性转发，请参阅 TechNet 文章 "为域名分配条件转发器" 或特定于你的 DNS 解决方案的文档。

如果你指定了外部 Azure Stack DNS 区域，使其看起来像公司域名的子域，则无法使用条件转发。 必须配置 DNS 委托。

例如：

- 公司 DNS 域名： *contoso.com*
- Azure Stack 外部 DNS 域名称： *azurestack.contoso.com*

### <a name="edit-dns-forwarder-ips"></a>编辑 DNS 转发器 Ip

DNS 转发器 Ip 是在 Azure Stack 部署过程中设置的。 如果由于任何原因需要更新转发器 Ip，可以通过连接到特权终结点并运行 *AzSDnsForwarder* 和 *AzSDnsForwarder [[-IPAddress] \<IPAddress[]\> ]* PowerShell cmdlet 来编辑这些值。 有关详细信息，请参阅特权终结点。

### <a name="delegate-the-external-dns-zone-to-azure-stack"></a>将外部 DNS 区域委派给 Azure Stack

若要从 Azure Stack 部署外部来解析 DNS 名称，需设置 DNS 委托。

每个注册机构都有自身的 DNS 管理工具，可以更改域的名称服务器记录。 在注册机构的 DNS 管理页中，请编辑 NS 记录并将区域的 NS 记录替换为 Azure Stack 中的相应记录。

大多数 DNS 注册机构要求至少提供两个 DNS 服务器才能完成委托。

### <a name="firewall"></a>防火墙

Azure Stack 为其基础结构角色设置虚拟 IP 地址 (VIP)。 这些 VIP 是从公共 IP 地址池分配的。 每个 VIP 受软件定义的网络层中的访问控制列表 (ACL) 保护。 还可以在物理交换机（TOR 和 BMC）之间使用 ACL 来进一步强化解决方案。 将会根据部署时的指定，针对外部 DNS 区域中的每个终结点创建一个 DNS 条目。 例如，将为用户门户分配 DNS 主机条目 portal. *\<region\>.\<fqdn\>* 。

以下体系结构图显示了不同的网络层和 Acl。

![体系结构图显示了不同的网络层和 Acl。](media/network-deployment/network-architecture.png)

### <a name="ports-and-urls"></a>端口和 URL

若要使 Azure Stack 服务（如门户、Azure 资源管理器和 DNS 可用于外部网络），必须允许这些终结点的入站流量用于特定的 Url、端口和协议。

在到传统代理服务器或防火墙的透明代理上行链路正在保护解决方案的部署中，必须允许特定的端口和 URL，以便进行入站和出站通信。 例如，用于标识、Azure Stack 中心市场、修补和更新、注册和使用情况数据的端口和 Url。

### <a name="outbound-communication"></a>出站通信

Azure Stack 仅支持透明代理服务器。 在具有到传统代理服务器的透明代理上行的部署中，在连接模式下部署时，必须允许下表中的端口和 Url 进行出站通信。

SSL 流量截获不受支持，并且可能导致在访问终结点时出现服务故障。 与标识所需的终结点进行通信时支持的最大超时值为60秒。

>[!NOTE]
>Azure Stack 不支持使用 Azure ExpressRoute 访问下表中列出的 Azure 服务，因为 ExpressRoute 可能无法将流量路由到所有终结点。


|目的|目标 URL|协议|端口|源网络|
|---------|---------|---------|---------|---------|
|标识|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https： \/ /management.core.windows.net<br>ARMUri = https： \/ /management.azure.com<br>https： \/ / \* . msftauth.net<br>https： \/ / \* . msauth.net<br>https： \/ / \* . msocdn.com<br>**Azure Government**<br>https： \/ /login.microsoftonline.us/<br>https： \/ /graph.windows.net/<br>**Azure 中国世纪互联**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure 德国**<br>https： \/ /login.microsoftonline.de/<br>https： \/ /graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|公共 VIP - /27<br>公共基础结构网络|
|Azure Stack 集线器 Marketplace 联合|**Azure**<br>https:\//management.azure.com<br>https://&#42;. blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https： \/ /management.usgovcloudapi.net/<br>https://&#42;. blob.core.usgovcloudapi.net/<br>**Azure 中国世纪互联**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn|HTTPS|443|公共 VIP - /27|
|修补和更新|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|公共 VIP - /27|
|注册|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https： \/ /management.usgovcloudapi.net/<br>**Azure 中国世纪互联**<br>https:\//management.chinacloudapi.cn|HTTPS|443|公共 VIP - /27|
|使用情况|**Azure**<br>https://&#42;. trafficmanager.net<br>**Azure Government**<br>https://&#42;. usgovtrafficmanager.net<br>**Azure 中国世纪互联**<br>https://&#42;.trafficmanager.cn|HTTPS|443|公共 VIP - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|公共 VIP - /27<br>公共基础结构网络|
|NTP|为部署提供的 NTP 服务器的 IP|UDP|123|公共 VIP - /27|
|DNS|为部署提供的 DNS 服务器的 IP|TCP<br>UDP|53|公共 VIP - /27|
|CRL|证书上 CRL 分发点下的 URL|HTTP|80|公共 VIP - /27|
|LDAP|为 Azure Graph 集成提供 Active Directory 林|TCP<br>UDP|389|公共 VIP - /27|
|LDAP SSL|为图形集成提供 Active Directory 林|TCP|636|公共 VIP - /27|
|LDAP GC|为图形集成提供 Active Directory 林|TCP|3268|公共 VIP - /27|
|LDAP GC SSL|为图形集成提供 Active Directory 林|TCP|3269|公共 VIP - /27|
|AD FS|为 AD FS 集成提供的 AD FS 元数据终结点|TCP|443|公共 VIP - /27|
|诊断日志收集服务|Azure Blob 存储-提供的共享访问签名 URL|HTTPS|443|公共 VIP - /27|
|     |     |     |     |     |
                                                                                                                                                                
### <a name="inbound-communication"></a>入站通信

将 Azure Stack 终结点发布到外部网络需要一组基础结构 VIP。 “终结点 (VIP)”表显示了每个终结点、所需的端口和协议。 对于需要其他资源提供程序（如 SQL 资源提供程序）的终结点，请参阅特定资源提供程序部署文档。

此处未列出内部基础结构 VIP，因为发布 Azure Stack 时不需要这些 VIP。 用户 VIP 是动态的，由用户自己定义，而不受 Azure Stack 操作员的控制。

>[!NOTE]
>
>IKEv2 VPN 是一个基于标准的 IPsec VPN 解决方案，它使用 UDP 端口 500 和 4500 以及 TCP 端口 50。 防火墙并非始终打开这些端口，因此，IKEv2 VPN 可能无法穿过代理和防火墙。

|终结点 (VIP)|DNS 主机 A 记录|协议|端口|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure 门户 (管理员) |Adminportal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure 资源管理器（管理员）|Adminmanagement. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure 门户 (用户) |Portal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure 资源管理器（用户）|Management. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Graph|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|证书吊销列表|Crl. *&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;. *&lt;region>.&lt;fqdn>*|TCP 和 UDP|53|
|Hosting | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Key Vault (用户) |&#42;.vault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Key Vault (管理员) |&#42;.adminvault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure 队列存储 |&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Azure 表存储 |&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Azure Blob 存储 |&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 资源提供程序|sqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 资源提供程序|mysqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Azure 应用服务|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300（Azure 资源管理器）|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP、UDP|21、1021、10001-10100 (FTP)<br>990 (FTPS)|
|Azure VPN 网关|     |     |[请参阅 VPN 网关常见问题](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability)|
|     |     |     |     |

