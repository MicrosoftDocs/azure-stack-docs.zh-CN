---
title: Azure Stack 数据中心集成 - DNS
description: 了解如何将 Azure Stack DNS 与数据中心 DNS 集成
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/09/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 05/09/2019
keywords: ''
ms.openlocfilehash: bf1aed6c8140f0c0753f49195082dfd71737868a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618671"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Azure Stack 数据中心集成 - DNS

若要能够从 Azure Stack 外部访问 Azure Stack 终结点（**门户**、**adminportal**、**管理**、**adminmanagement** 等），需将 Azure Stack DNS 服务与托管 DNS 区域（要在 Azure Stack 中使用）的 DNS 服务器集成。

## <a name="azure-stack-dns-namespace"></a>Azure Stack DNS 命名空间

部署 Azure Stack 时，必须提供与 DNS 相关的一些重要信息。


|字段  |描述  |示例|
|---------|---------|---------|
|区域|Azure Stack 部署的地理位置。|`east`|
|外部域名|需要用于 Azure Stack 部署的区域的名称。|`cloud.fabrikam.com`|
|内部域名|在 Azure Stack 中用于基础结构服务的内部区域的名称。  它是进行了目录服务集成的，并且是专用的（无法从 Azure Stack 部署外部访问）。|`azurestack.local`|
|DNS 转发器|一种 DNS 服务器，用于转发托管在 Azure Stack 外部的 DNS 查询、DNS 区域和记录，不管是在公司 Intranet 上还是公共 Internet 上。|`10.57.175.34`<br>`8.8.8.8`|
|命名前缀（可选）|需要在 Azure Stack 基础结构角色实例计算机名称中使用的命名前缀。  如果不提供，则默认值为 `azs`。|`azs`|

Azure Stack 部署和终结点的完全限定域名 (FQDN) 是区域参数和外部域名参数的组合。 使用上表中示例的值时，此 Azure Stack 部署的 FQDN 将是以下名称：

`east.cloud.fabrikam.com`

同样，此部署的部分终结点的示例将如以下 URL 所示：

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

若要使用 Azure Stack 部署的这个示例 DNS 命名空间，需要满足以下条件：

- 区域 `fabrikam.com` 已注册到域注册机构和/或公司内部 DNS 服务器，具体取决于名称解析要求。
- 子域 `cloud.fabrikam.com` 存在于区域 `fabrikam.com` 下。
- 托管区域 `fabrikam.com` 和 `cloud.fabrikam.com` 的 DNS 服务器可以从 Azure Stack 部署访问。

若要从 Azure Stack 外部解析 Azure Stack 终结点和实例的 DNS 名称，需将为 Azure Stack 托管外部 DNS 区域的 DNS 服务器与托管需要使用的父区域的 DNS 服务器集成。

### <a name="dns-name-labels"></a>DNS 名称标签

Azure Stack 支持添加 DNS 名称标签到公共 IP 地址以允许公共 IP 地址的名称解析。 这可以是用户来访问应用程序和服务托管在 Azure Stack 中按名称的简便方法。 DNS 名称标签使用基础结构终结点比略有不同的命名空间。 遵循上一示例中命名空间，DNS 名称标签的命名空间如下所示：

`*.east.cloudapp.cloud.fabrikam.com`

因此，如果租户表示值，该值**Myapp**中的公共 IP 地址资源的 DNS 名称标签字段，它创建 A 记录， **myapp**区域中**east.cloudapp.cloud.fabrikam.com** Azure Stack 外部 DNS 服务器上。 生成完全限定的域的名称出现，如下所示：

`myapp.east.cloudapp.cloud.fabrikam.com`

如果你想要利用此功能并使用此命名空间，必须集成使用托管想要使用以及父区域的 DNS 服务器为 Azure Stack 托管外部 DNS 区域的 DNS 服务器。 这是比 Azure Stack 服务终结点的命名空间不同的命名空间，因此你必须创建其他委派或条件性转发规则。

有关 DNS 名称标签的工作原理的详细信息，请参阅[Azure Stack 中使用的 DNS](../user/azure-stack-dns.md)。

## <a name="resolution-and-delegation"></a>解析和委托

有两种类型的 DNS 服务器：

- 权威 DNS 服务器托管 DNS 区域。 它只应答这些区域中的 DNS 记录查询。
- 递归 DNS 服务器不托管 DNS 区域。 它调用权威 DNS 服务器来收集所需的数据，以应答所有 DNS 查询。

Azure Stack 包括权威 DNS 服务器和递归 DNS 服务器。 递归服务器用于解析所有项的名称，该 Azure Stack 部署的内部专用区域和外部公用 DNS 区域除外。 

![Azure Stack DNS 体系结构](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>通过 Azure Stack 解析外部 DNS 名称

若要解析 Azure Stack 外部的终结点（例如：www.bing.com）的 DNS 名称，需提供可供 Azure Stack 用来转发 DNS 请求的 DNS 服务器（Azure Stack 对这些请求来说并不权威）。 进行部署时，DNS 服务器（Azure Stack 向其转发请求）在部署工作表（位于“DNS 转发器”字段中）中是必需的。 请在此字段中提供至少两个服务器，目的是容错。 没有这些值，Azure Stack 部署会失败。

### <a name="configure-conditional-dns-forwarding"></a>配置条件性 DNS 转发

> [!IMPORTANT]
> 这仅适用于 AD FS 部署。

若要通过现有的 DNS 基础结构启用名称解析，请配置条件性转发。

若要添加条件性转发器，必须使用特权终结点。

对于此过程，请使用能够与 Azure Stack 中的特权终结点通信的数据中心网络中的计算机。

1. 打开提升了权限的 Windows PowerShell 会话（以管理员身份运行），连接到特权终结点的 IP 地址。 使用进行 CloudAdmin 身份验证的凭据。

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. 连接到特权终结点后，运行以下 PowerShell 命令。 将提供的示例值替换为要使用的 DNS 服务器的域名和 IP 地址。

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>从 Azure Stack 外部解析 Azure Stack DNS 名称
权威服务器是指保存了外部 DNS 区域信息以及任何用户创建的区域的服务器。 与这些服务器集成即可启用区域委托或条件性转发，以便从 Azure Stack 外部解析 Azure Stack DNS 名称。

## <a name="get-dns-server-external-endpoint-information"></a>获取 DNS 服务器外部终结点信息

若要将 Azure Stack 部署与 DNS 基础结构集成，需提供以下信息：

- DNS 服务器 FQDN
- DNS 服务器 IP 地址

Azure Stack DNS 服务器的 FQDN 具有以下格式：

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

使用示例值时，DNS 服务器的 FQDN 如下所示：

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


在名为 `AzureStackStampInformation.json` 的文件中，此信息也会在所有 Azure Stack 部署的末尾创建。 该文件位于部署虚拟机的 `C:\CloudDeployment\logs` 文件夹中。 如果不确定为你的 Azure Stack 部署使用了什么值，可以从此处获取这些值。

如果部署虚拟机不再可用或无法访问，则可连接到特权终结点并运行 `Get-AzureStackStampInformation` PowerShell cmdlet，以便获取这些值。 有关详细信息，请参阅[特权终结点](azure-stack-privileged-endpoint.md)。

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>设置到 Azure Stack 的条件性转发

若要将 Azure Stack 与 DNS 基础结构集成，最简单也最安全的方式是将区域从托管父区域的服务器进行条件性转发。 如果可以直接控制为 Azure Stack 外部 DNS 命名空间托管父区域的 DNS 服务器，建议使用此方法。

如果您不熟悉如何执行操作条件使用 DNS 转发，请参阅以下 TechNet 文章：[为域名分配条件转发器](https://technet.microsoft.com/library/cc794735)，或特定于 DNS 解决方案的文档。

如果已将外部 Azure Stack DNS 区域指定为类似公司域名的子域那样，则无法使用条件性转发。 必须配置 DNS 委托。

示例：

- 公司 DNS 域名：`contoso.com`
- Azure Stack 外部 DNS 域名：`azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>将外部 DNS 区域委托到 Azure Stack

若要从 Azure Stack 部署外部来解析 DNS 名称，需设置 DNS 委托。

每个注册机构都有自身的 DNS 管理工具，可以更改域的名称服务器记录。 在注册机构的 DNS 管理页中，请编辑 NS 记录并将区域的 NS 记录替换为 Azure Stack 中的相应记录。

大多数 DNS 注册机构要求至少提供两个 DNS 服务器才能完成委托。

## <a name="next-steps"></a>后续步骤

[防火墙集成](azure-stack-firewall.md)
