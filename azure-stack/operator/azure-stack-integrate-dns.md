---
title: Azure Stack datacenter DNS 集成 |Microsoft Docs
description: 了解如何将 Azure Stack DNS 与数据中心 DNS 集成。
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/21/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 08/21/2019
keywords: ''
ms.openlocfilehash: 1f84eeffffae3c103c33b366a5c503a907cf6715
ms.sourcegitcommit: 4a2318ad395b2a931833ccba4430d8d04cdd8819
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72780489"
---
# <a name="azure-stack-datacenter-dns-integration"></a>Azure Stack datacenter DNS 集成

若要从外部 Azure Stack 访问**门户**、 **adminportal**、 **management**和**adminmanagement**等 Azure Stack 终结点，需要将 AZURE STACK dns 服务与托管 dns 区域的 dns 服务器集成要在 Azure Stack 中使用。

## <a name="azure-stack-dns-namespace"></a>DNS 命名空间 Azure Stack

部署 Azure Stack 时，需要提供一些与 DNS 相关的重要信息。


|字段  |描述  |示例|
|---------|---------|---------|
|地区|Azure Stack 部署的地理位置。|`east`|
|外部域名|要用于 Azure Stack 部署的区域的名称。|`cloud.fabrikam.com`|
|内部域名|用于 Azure Stack 中的基础结构服务的内部区域的名称。 它与目录服务集成并专用（无法从 Azure Stack 部署外部访问）。|`azurestack.local`|
|DNS 转发器|用于转发 DNS 查询、DNS 区域和在 Azure Stack 外部托管的记录的 DNS 服务器，可在公司 intranet 或公共 internet 上进行。 部署后，可以通过[ **AzSDnsForwarder** Cmdlet](#editing-dns-forwarder-ips)编辑 DNS 转发器的值。 
|命名前缀（可选）|要 Azure Stack 基础结构角色实例计算机名称所使用的命名前缀。  如果未提供，则默认值为 `azs`。|`azs`|

Azure Stack 部署和终结点的完全限定的域名（FQDN）是区域参数和外部域名参数的组合。 使用上表中的示例中的值，此 Azure Stack 部署的 FQDN 将是以下名称：

`east.cloud.fabrikam.com`

同样，此部署的某些终结点的示例类似于以下 Url：

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

若要将此示例 DNS 命名空间用于 Azure Stack 部署，需要满足以下条件：

- 区域 `fabrikam.com` 将注册到域注册机构和/或内部企业 DNS 服务器中，这取决于你的名称解析要求。
- 子域 `cloud.fabrikam.com` 存在于 `fabrikam.com` 区域下。
- 可以从 Azure Stack 部署中访问承载区域 `fabrikam.com` 和 `cloud.fabrikam.com` 的 DNS 服务器。

为了能够解析外部 Azure Stack Azure Stack 终结点和实例的 DNS 名称，需要将托管外部 DNS 区域的 DNS 服务器与托管要使用的父区域的 DNS 服务器 Azure Stack 进行集成。

### <a name="dns-name-labels"></a>DNS 名称标签

Azure Stack 支持将 DNS 名称标签添加到公共 IP 地址，以允许对公共 IP 地址进行名称解析。 使用 DNS 标签，用户可以轻松访问按名称在 Azure Stack 中托管的应用和服务。 DNS 名称标签使用的命名空间与基础结构终结点略有不同。 按照前面的示例命名空间，DNS 名称标签的命名空间如下所示：

`*.east.cloudapp.cloud.fabrikam.com`

因此，如果某个租户在公共 IP 地址资源的 DNS 名称标签字段中指示值**myapp** ，则它会在 AZURE STACK 外部 DNS 服务器上的区域**East.cloudapp.cloud.fabrikam.com**中创建一个**myapp**记录。 生成的完全限定的域名如下所示：

`myapp.east.cloudapp.cloud.fabrikam.com`

如果要利用此功能并使用此命名空间，则必须将托管外部 DNS 区域的 DNS 服务器与托管要使用的父区域的 DNS 服务器集成，以便 Azure Stack。 这是一个与 Azure Stack 服务终结点的命名空间不同的命名空间，因此你必须创建其他委托或条件转发规则。

有关 DNS 名称标签工作方式的详细信息，请参阅[在 Azure Stack 中使用 dns](../user/azure-stack-dns.md)。

## <a name="resolution-and-delegation"></a>解析和委托

有两种类型的 DNS 服务器：

- 权威 DNS 服务器托管 DNS 区域。 它只应答这些区域中的 DNS 记录查询。
- 递归 DNS 服务器不托管 DNS 区域。 它调用权威 DNS 服务器来收集所需的数据，以应答所有 DNS 查询。

Azure Stack 包括权威 DNS 服务器和递归 DNS 服务器。 递归服务器用于解析内部专用区域以外的所有内容的名称和该 Azure Stack 部署的外部公共 DNS 区域。

![Azure Stack DNS 体系结构](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>解析 Azure Stack 中的外部 DNS 名称

若要解析 Azure Stack 之外的终结点的 DNS 名称（例如： www \.bing .com），需要提供 Azure Stack 的 DNS 服务器，该服务器可用于转发 Azure Stack 不具有权威的 DNS 请求。 对于部署，Azure Stack 将请求转发到的 DNS 服务器在部署工作表（在 DNS 转发器字段中）中需要。 请在此字段中至少提供两台服务器，以获得容错能力。 如果没有这些值，Azure Stack 部署将失败。 部署后，可以通过[ **AzSDnsForwarder** Cmdlet](#editing-dns-forwarder-ips)编辑 DNS 转发器的值。 



### <a name="configure-conditional-dns-forwarding"></a>配置条件 DNS 转发

> [!IMPORTANT]
> 这仅适用于 AD FS 部署。

若要启用现有 DNS 基础结构的名称解析，请配置条件转发。

若要添加条件转发器，必须使用特权终结点。

对于此过程，请使用数据中心网络中的计算机，该计算机可以与 Azure Stack 中的特权终结点进行通信。

1. 打开提升的 Windows PowerShell 会话（以管理员身份运行），并连接到特权终结点的 IP 地址。 使用凭据进行 CloudAdmin authentication。

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. 连接到特权终结点后，请运行以下 PowerShell 命令。 将提供的示例值替换为要使用的 DNS 服务器的域名和 IP 地址。

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>解析 Azure Stack 外部 Azure Stack 的 DNS 名称
权威服务器是保存外部 DNS 区域信息以及任何用户创建的区域的服务器。 与这些服务器集成，以启用区域委派或条件性转发，解析 Azure Stack 外部 Azure Stack 的 DNS 名称。

## <a name="get-dns-server-external-endpoint-information"></a>获取 DNS 服务器外部终结点信息

若要将 Azure Stack 部署与 DNS 基础结构集成，需要以下信息：

- DNS 服务器 Fqdn
- DNS 服务器 IP 地址

Azure Stack DNS 服务器的 Fqdn 采用以下格式：

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

使用示例值，DNS 服务器的 Fqdn 为：

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


此信息也会在名为 `AzureStackStampInformation.json` 的文件中的所有 Azure Stack 部署结束时创建。 此文件位于部署虚拟机的 `C:\CloudDeployment\logs` 文件夹中。 如果不确定哪些值用于 Azure Stack 部署，可以从此处获取值。

如果部署虚拟机不再可用或无法访问，可以通过连接到特权终结点并运行 `Get-AzureStackStampInformation` PowerShell cmdlet 来获取这些值。 有关详细信息，请参阅[特权终结点](azure-stack-privileged-endpoint.md)。

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>设置 Azure Stack 的条件性转发

将 Azure Stack 与 DNS 基础结构集成的最简单且最安全的方法是从承载父区域的服务器执行区域的条件性转发。 如果你可以直接控制托管 Azure Stack 外部 DNS 命名空间的父区域的 DNS 服务器，则建议使用此方法。

如果你不熟悉如何使用 DNS 执行条件性转发，请参阅以下 TechNet 文章：为[域名分配条件转发器](https://technet.microsoft.com/library/cc794735)或特定于你的 DNS 解决方案的文档。

如果你指定了外部 Azure Stack DNS 区域，使其看起来像公司域名的子域，则无法使用条件转发。 必须配置 DNS 委派。

示例：

- 公司 DNS 域名： `contoso.com`
- Azure Stack 外部 DNS 域名： `azurestack.contoso.com`

## <a name="editing-dns-forwarder-ips"></a>编辑 DNS 转发器 Ip

DNS 转发器 Ip 是在 Azure Stack 部署过程中设置的。 但是，如果转发器 Ip 需要出于任何原因进行更新，则可以通过连接到特权终结点并运行 `Get-AzSDnsForwarder` 和 `Set-AzSDnsForwarder [[-IPAddress] <IPAddress[]>]` PowerShell cmdlet 来编辑这些值。 有关详细信息，请参阅[特权终结点](azure-stack-privileged-endpoint.md)。

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>将外部 DNS 区域委派给 Azure Stack

若要从 Azure Stack 部署外部解析 DNS 名称，需要设置 DNS 委派。

每个注册机构都有自身的 DNS 管理工具，可以更改域的名称服务器记录。 在注册机构的 DNS 管理页中，编辑 NS 记录，并将区域的 NS 记录替换为 Azure Stack 中的记录。

大多数 DNS 注册机构要求你至少提供两个 DNS 服务器来完成委派。

## <a name="next-steps"></a>后续步骤

[防火墙集成](azure-stack-firewall.md)
