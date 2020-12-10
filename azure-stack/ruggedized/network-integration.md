---
title: Azure Stack 中心耐用网络集成
description: 了解 Azure Stack Azure Stack 中心耐用设备的网络集成。
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 91f21fd139e2816e764f5d4a1dc3fc7602b8fecb
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939447"
---
# <a name="azure-stack-hub-ruggedized-network-integration"></a>Azure Stack 中心耐用网络集成 

本主题介绍 Azure Stack 网络集成。

## <a name="border-connectivity-uplink"></a>边界连接 (上行) 

网络集成规划是成功进行 Azure Stack 集成系统部署、操作和管理的重要先决条件。 边界连接规划从选择是否要将动态路由与边界网关协议 (BGP) 一起使用开始。 这需要分配 16 位的 BGP 自治系统编号（公共或专用），或者使用静态路由（在这种情况下会将静态默认路由分配给边界设备）。

架顶式 (TOR) 交换机需要在物理接口上配置具有点到点 IP（/30 网络）的第 3 层上行链路。 不支持具有支持 Azure Stack 操作的 TOR 交换机的第 2 层上行链路。 

### <a name="bgp-routing"></a>BGP 路由

使用 BGP 等动态路由协议可以保证系统始终会注意到网络更改和便于管理。 为了增强安全性，可以针对 TOR 和边界之间的 BGP 对等互连设置密码。

如下图所示，将使用前缀列表阻止播发 TOR 交换机上的专用 IP 空间。 前缀列表将拒绝播发专用网络，它会作为路由映射应用于 TOR 与边界之间的连接。

Azure Stack 解决方案内运行的软件负载均衡器 (SLB) 将对等互连到 TOR 设备，以便它可以动态播发 VIP 地址。

若要确保用户流量立即以透明方式从故障中恢复，TOR 设备之间配置的 VPC 或 MLAG 允许对主机和 HSRP 或 VRRP 使用多底盘链接聚合以便为 IP 网络提供网络冗余。




### <a name="static-routing"></a>静态路由

静态路由需要额外配置边界设备。 它需要更多的手动干预和管理，以及在任何更改之前进行彻底的分析。 配置错误导致的问题可能需要更多时间进行回退，具体取决于所做的更改。 不建议使用此路由方法，但支持此方法。

若要使用静态路由将 Azure Stack 集成到网络环境，必须连接边界和 TOR 设备之间的所有四个物理链路。
由于静态路由的工作方式，无法保证高可用性。

必须将边界设备配置为具有指向 TOR 之间的四个 P2P Ip 中的每一个 Ip 的静态路由，并将路由到 Azure Stack 中的任何网络的流量的边框，但操作仅需要 *外部* 或公共 VIP 网络。 初始部署需要到 BMC 网络和外部网络的静态路由。 操作员可以选择在边框中保留静态路由，以访问位于 *BMC* 和 *基础结构* 网络上的管理资源。 添加指向 *交换机基础结构* 和 *交换机管理* 网络的静态路由是可选的。

TOR 设备配置有将所有流量发送到边界设备的静态默认路由。 默认规则的一个流量例外是，对于专用空间，将使用应用于 TOR 到边界连接的访问控制列表阻止该流量。

静态路由仅适用于 TOR 与边界交换机之间的上行链路。
机架内使用的是 BGP 动态路由，因为它对于 SLB 和其他组件来说是基本工具，无法禁用或删除。




\* 在部署后，BMC 网络是可选的。

\*\* 交换机基础结构网络是可选的，因为整个网络可以包含在交换机管理网络中。

\*\*\* 交换机管理网络是必需的，可以与交换机基础结构网络分开添加。

### <a name="transparent-proxy"></a>透明代理

如果数据中心要求所有流量都使用代理，则必须配置“透明代理”以便根据策略处理来自机架的所有流量，并分离网络上不同区域之间的流量。

Azure Stack 解决方案不支持普通 Web 代理 


透明代理（也称为截获、内联或强制代理）将截获网络层的正常通信，而无需任何特殊的客户端配置。 客户端不需要知道代理是否存在。



SSL 流量拦截不受支持，并且在访问终结点时可能会导致服务故障。 与标识所需的终结点进行通信时，支持的最大超时值为 60 秒，并可以进行 3 次重试尝试。

## <a name="dns"></a>DNS


本部分介绍域名系统 (DNS) 配置。

### <a name="configure-conditional-dns-forwarding"></a>配置条件性 DNS 转发

这仅适用于 AD FS 部署。 


若要通过现有的 DNS 基础结构启用名称解析，请配置条件性转发。

若要添加条件性转发器，必须使用特权终结点。

对于此过程，请使用能够与 Azure Stack 中的特权终结点通信的数据中心网络中的计算机。

1.  打开提升了权限的 Windows PowerShell 会话（以管理员身份运行），连接到特权终结点的 IP 地址。 使用进行 CloudAdmin 身份验证的凭据。

    ```powershell
    \$cred=Get-Credential Enter-PSSession -ComputerName \<IP Address of ERCS\> -ConfigurationName PrivilegedEndpoint -Credential \$cred 
    ```

2.  连接到特权终结点后，运行以下 PowerShell 命令。 将提供的示例值替换为要使用的 DNS 服务器的域名和 IP 地址。

    ```powershell
    Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2" 
    ```

### <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>从 Azure Stack 外部解析 Azure Stack DNS 名称

权威服务器是指保存了外部 DNS 区域信息以及任何用户创建的区域的服务器。 与这些服务器集成即可启用区域委托或条件性转发，以便从 Azure Stack 外部解析 Azure Stack DNS 名称。

### <a name="get-dns-server-external-endpoint-information"></a>获取 DNS 服务器外部终结点信息

若要将 Azure Stack 部署与 DNS 基础结构集成，需提供以下信息：

-   DNS 服务器 FQDN

-   DNS 服务器 IP 地址

Azure Stack DNS 服务器的 FQDN 具有以下格式：

\<NAMINGPREFIX\>-ns01.\<REGION\>.\<EXTERNALDOMAINNAME\>

\<NAMINGPREFIX\>-ns02.\<REGION\>.\<EXTERNALDOMAINNAME\>

使用示例值时，DNS 服务器的 FQDN 如下所示：

azs-ns01.east.cloud.fabrikam.com

azs-ns02.east.cloud.fabrikam.com

此信息可在管理门户中获得，但也可以在所有 Azure Stack 部署结束时在名为“AzureStackStampInformation.json”的文件中创建。
该文件位于部署虚拟机的“C:\\CloudDeployment\\logs”文件夹中。 如果不确定对 Azure Stack 部署使用了什么值，可以从该文件中获取这些值。

如果部署虚拟机不再可用或无法访问，你可以连接到特权终结点并运行 Get-AzureStackStampInformation PowerShell cmdlet，以便获取这些值。 有关详细信息，请参阅特权终结点。

### <a name="setting-up-conditional-forwarding-to-azure-stack"></a>设置到 Azure Stack 的条件性转发

若要将 Azure Stack 与 DNS 基础结构集成，最简单也最安全的方式是将区域从托管父区域的服务器进行条件性转发。 如果可以直接控制为 Azure Stack 外部 DNS 命名空间托管父区域的 DNS 服务器，建议使用此方法。

如果你不熟悉如何使用 DNS 进行条件转发，请参阅以下 TechNet 文章：“为域名分配条件转发器”或特定于 DNS 解决方案的文档。

如果已将外部 Azure Stack DNS 区域指定为类似公司域名的子域那样，则无法使用条件性转发。 必须配置 DNS 委托。

例如：

-   公司 DNS 域名：contoso.com

-   Azure Stack 外部 DNS 域名：azurestack.contoso.com

### <a name="editing-dns-forwarder-ips"></a>编辑 DNS 转发器 IP

DNS 转发器 IP 是在 Azure Stack 部署期间设置的。 但是，如果转发器 Ip 需要出于任何原因进行更新，则可以通过连接到特权终结点并运行 Get-AzSDnsForwarder 并 Set-AzSDnsForwarder [[-IPAddress]] PowerShell cmdlet 来编辑这些值 \<IPAddress[]\> 。 有关详细信息，请参阅特权终结点。

### <a name="delegating-the-external-dns-zone-to-azure-stack"></a>将外部 DNS 区域委托到 Azure Stack

若要从 Azure Stack 部署外部来解析 DNS 名称，需设置 DNS 委托。

每个注册机构都有自身的 DNS 管理工具，可以更改域的名称服务器记录。 在注册机构的 DNS 管理页中，请编辑 NS 记录并将区域的 NS 记录替换为 Azure Stack 中的相应记录。

大多数 DNS 注册机构要求至少提供两个 DNS 服务器才能完成委托。

## <a name="firewall"></a>防火墙

Azure Stack 为其基础结构角色设置虚拟 IP 地址 (VIP)。
这些 VIP 是从公共 IP 地址池分配的。 每个 VIP 受软件定义的网络层中的访问控制列表 (ACL) 保护。 还可以在物理交换机（TOR 和 BMC）之间使用 ACL 来进一步强化解决方案。 将会根据部署时的指定，针对外部 DNS 区域中的每个终结点创建一个 DNS 条目。 例如，将为用户门户分配 DNS 主机条目 portal. *\<region\>.\<fqdn\>* 。

以下体系结构图显示了不同的网络层和 ACL：

![体系结构图显示了不同的网络层和 ACL](media/network-deployment/network-architecture.png) 


### <a name="ports-and-urls"></a>端口和 URL

要使 Azure Stack 服务（例如门户、Azure 资源管理器、DNS 等）可供外部网络使用，必须允许特定 URL、端口和协议的入站流量发往这些终结点。

在到传统代理服务器或防火墙的透明代理上行链路正在保护解决方案的部署中，必须允许特定的端口和 URL，以便进行入站和出站通信。 这包括用于标识、市场、修补和更新、注册和使用情况数据的端口与 URL。

### <a name="outbound-communication"></a>出站通信

Azure Stack 仅支持透明代理服务器。 以连接模式部署时，在使用到传统代理服务器的透明代理上行链路的部署中，必须允许下表中的端口和 URL，以便进行出站通信。

SSL 流量拦截不受支持，并且在访问终结点时可能会导致服务故障。 与标识所需的终结点进行通信时，支持的最大超时值为 60 秒。

>[!NOTE] 
>Azure Stack 不支持使用 ExpressRoute 访问下表中列出的 Azure 服务，因为 ExpressRoute 可能无法将流量路由到所有终结点。 


|目的|目标 URL|协议|端口|源网络|
|---------|---------|---------|---------|---------|
|标识|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https： \/ /management.core.windows.net<br>ARMUri = https： \/ /management.azure.com<br>https： \/ / \* . msftauth.net<br>https： \/ / \* . msauth.net<br>https： \/ / \* . msocdn.com<br>**Azure Government**<br>https： \/ /login.microsoftonline.us/<br>https： \/ /graph.windows.net/<br>**Azure 中国世纪互联**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure 德国**<br>https： \/ /login.microsoftonline.de/<br>https： \/ /graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|公共 VIP - /27<br>公共基础结构网络|
|市场联合|**Azure**<br>https:\//management.azure.com<br>https://&#42;. blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https： \/ /management.usgovcloudapi.net/<br>https://&#42;. blob.core.usgovcloudapi.net/<br>**Azure 中国世纪互联**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn|HTTPS|443|公共 VIP - /27|
|修补程序和更新|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|公共 VIP - /27|
|注册|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https： \/ /management.usgovcloudapi.net/<br>**Azure 中国世纪互联**<br>https:\//management.chinacloudapi.cn|HTTPS|443|公共 VIP - /27|
|使用情况|**Azure**<br>https://&#42;. trafficmanager.net<br>**Azure Government**<br>https://&#42;. usgovtrafficmanager.net<br>**Azure 中国世纪互联**<br>https://&#42;.trafficmanager.cn|HTTPS|443|公共 VIP - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|公共 VIP - /27<br>公共基础结构网络|
|NTP|（为部署提供的 NTP 服务器的 IP）|UDP|123|公共 VIP - /27|
|DNS|（为部署提供的 DNS 服务器的 IP）|TCP<br>UDP|53|公共 VIP - /27|
|CRL|（证书上的 CRL 分发点下的 URL）|HTTP|80|公共 VIP - /27|
|LDAP|为 Graph 集成提供的 Active Directory 林|TCP<br>UDP|389|公共 VIP - /27|
|LDAP SSL|为 Graph 集成提供的 Active Directory 林|TCP|636|公共 VIP - /27|
|LDAP GC|为 Graph 集成提供的 Active Directory 林|TCP|3268|公共 VIP - /27|
|LDAP GC SSL|为 Graph 集成提供的 Active Directory 林|TCP|3269|公共 VIP - /27|
|AD FS|为 AD FS 集成提供的 AD FS 元数据终结点|TCP|443|公共 VIP - /27|
|诊断日志收集服务|Azure 存储提供的 Blob SAS URL|HTTPS|443|公共 VIP - /27|
|     |     |     |     |     |
                                  
                                  
### <a name="inbound-communication"></a>入站通信

将 Azure Stack 终结点发布到外部网络需要一组基础结构 VIP。 “终结点 (VIP)”表显示了每个终结点、所需的端口和协议。 请参阅特定资源提供程序部署文档，了解需要其他资源提供程序（例如 SQL 资源提供程序）的终结点。

此处未列出内部基础结构 VIP，因为发布 Azure Stack 时不需要这些 VIP。 用户 VIP 是动态的，由用户自己定义，而不受 Azure Stack 操作员的控制


>[!NOTE] 
>IKEv2 VPN 是一个基于标准的 IPsec VPN 解决方案，它使用 UDP 端口 500 和 4500 以及 TCP 端口 50。 防火墙并不总是打开这些端口，因此 IKEv2 VPN 可能无法遍历代理和防火墙。 


|终结点 (VIP)|DNS 主机 A 记录|协议|端口|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|门户（管理员）|Adminportal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure 资源管理器（管理员）|Adminmanagement. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|门户（用户）|Portal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure 资源管理器（用户）|Management. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Graph|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|证书吊销列表|Crl. *&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;. *&lt;region>.&lt;fqdn>*|TCP 和 UDP|53|
|Hosting | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault（用户）|&#42;.vault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault（管理员）|&#42;.adminvault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|存储队列|&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储表|&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|存储 Blob|&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 资源提供程序|sqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 资源提供程序|mysqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|应用服务|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300（Azure 资源管理器）|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP、UDP|21、1021、10001-10100 (FTP)<br>990 (FTPS)|
|VPN 网关|     |     |[请参阅 VPN 网关常见问题解答](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability)。|
|     |     |     |     |

