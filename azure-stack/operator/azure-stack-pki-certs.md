---
title: Azure Stack 集成系统的 Azure Stack 公钥基础结构证书要求 | Microsoft Docs
description: 介绍 Azure Stack 集成系统的 Azure Stack PKI 证书部署要求。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 53d8e3daecba269bcdd21fc726e312758f1f6c6f
ms.sourcegitcommit: 38f21e0bcf7b593242ad615c9d8ef8a1ac19c734
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70902705"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure Stack 公钥基础结构证书要求

Azure Stack 有一个公共基础结构网络，该网络使用分配给少量 Azure Stack 服务，并可能分配给租户 VM 的外部可访问公共 IP 地址。 在部署 Azure Stack 期间，需要使用这些 Azure Stack 公共基础结构终结点的、具有适当 DNS 名称的 PKI 证书。 本文提供以下方面的信息：

- 部署 Azure Stack 时需要哪些证书
- 获取与这些规范匹配的证书的过程是什么
- 如何在部署期间准备、验证和使用这些证书

> [!NOTE]
> 默认情况下，Azure Stack 还使用从内部 Active Directory 集成证书颁发机构（CA）颁发的证书，在节点之间进行身份验证。 若要验证证书，所有 Azure Stack 基础结构计算机都通过将该证书添加到其本地证书存储来信任内部 CA 的根证书。 Azure Stack 中没有证书的固定或允许列表。 根据目标的 FQDN 验证每个服务器证书的 SAN。 同时还会验证整个信任链以及证书的到期日期（没有证书固定的标准 TLS 服务器身份验证）。

## <a name="certificate-requirements"></a>证书要求
以下列表描述了部署 Azure Stack 时需要满足的证书要求： 
- 证书必须由内部证书颁发机构或公共证书颁发机构颁发。 如果使用公共证书颁发机构，它必须作为 Microsoft 信任根颁发机构计划的一部分包含在基础操作系统映像中。 在以下网页中查看完整列表： https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Azure Stack 基础结构必须能够通过网络访问证书中发布的证书颁发机构的证书吊销列表 (CRL) 位置。 此 CRL 必须是 http 终结点
- 在 pre-1903 内部版本中轮换证书时，证书必须由签署部署时提供的证书的同一内部证书颁发机构颁发，或者由上述任何公共证书颁发机构颁发。 就 1903 及更高版本来说，证书可以由任何企业或公共证书颁发机构来颁发。
- 不支持使用自签名证书
- 对于部署和轮换，可以使用单一证书覆盖证书的“使用者名称”和“使用者可选名称(SAN)”字段中的所有命名空间，也可以为下面你计划使用的 Azure Stack 服务所需的每个命名空间使用单独的证书。 这两种方法都需要将通配符用于需要它们的终结点，例如 **KeyVault** 和 **KeyVaultInternal**。 
- 证书的 PFX 加密应当为 3DES。 
- 证书签名算法不能为 SHA1。 
- 证书格式必须是 PFX，因为安装 Azure Stack 时需要公钥和私钥。 私钥必须设置本地计算机密钥属性。
- PFX 加密必须是 3DES（从 Windows 10 客户端或 Windows Server 2016 证书存储进行导出时，这是默认值）。
- 证书 pfx 文件的“密钥使用”字段中必须包含“数字签名”和“KeyEncipherment”值。
- 证书 pfx 文件的“增强型密钥使用”字段中必须包含“服务器身份验证(1.3.6.1.5.5.7.3.1)”和“客户端身份验证(1.3.6.1.5.5.7.3.2)”值。
- 证书的“颁发给:”字段不能与其“颁发者:”字段相同。
- 部署时，所有证书 pfx 文件的密码都必须相同
- 证书 pfx 的密码必须是复杂密码。 创建满足以下密码复杂性要求的密码。 最小长度为 8 个字符。 密码必须至少包含以下项中的三项：大写字母、小写字母、0-9 中的数字、特殊字符、不是大写也不是小写的字母字符。 记下此密码。 需将它用作部署参数。
- 确保使用者名称与使用者可选名称扩展 (x509v3_config) 中的使用者可选名称匹配。 “使用者可选名称”字段允许你指定要受单个 SSL 证书保护的其他主机名（网站、IP 地址、公用名称）。

> [!NOTE]  
> 不支持自签名证书。

> [!NOTE]  
> *支持证书*的信任链中存在中间证书颁发机构。 

## <a name="mandatory-certificates"></a>必需的证书
本部分中的表格描述部署 Azure AD 和 AD FS Azure Stack 时所需的 Azure Stack 公共终结点 PKI 证书。 证书要求已根据区域以及所用命名空间和每个命名空间所需的证书分组。 此表格还描述了解决方案提供程序将每个公共终结点的不同证书所复制到的文件夹。 

需要使用每个 Azure Stack 公共基础结构终结点的、具有适当 DNS 名称的证书。 每个终结点的 DNS 名称使用以下格式表示：&lt;prefix>.&lt;region>.&lt;fqdn>。 

对于部署，[region] 和 [externalfqdn] 值必须与针对 Azure Stack 系统选择的区域和外部域名相匹配。 例如，如果区域名称为 *Redmond*，外部域名为 *contoso.com*，则 DNS 名称的格式为 *&lt;prefix>.redmond.contoso.com*。 *&lt;prefix>* 值由 Microsoft 预先指定，描述证书保护的终结点。 此外，外部基础结构终结点的 *&lt;prefix>* 值取决于使用特定终结点的 Azure Stack 服务。 

对于生产环境，我们建议为每个终结点生成单独的证书并将其复制到相应的目录中。 对于开发环境，可以提供单个通配符证书作为证书，在其中涵盖“使用者”和“使用者可选名称”(SAN) 字段中的所有命名空间，并将该证书复制到所有目录中。 使用涵盖所有终结点和服务的单个证书是一种不安全的方式，因此仅用于开发。 请记住，这两个选项都要求对 **acs** 和 Key Vault 等需要通配符证书的终结点使用此类证书。 

> [!Note]  
> 在部署期间，必须将证书复制到与要部署的标识提供者（Azure AD 或 AD FS）匹配的部署文件夹中。 如果对所有终结点使用单个证书，则必须将该证书文件复制到下表中所述的每个部署文件夹中。 部署虚拟机中已预先构建了文件夹结构，路径为：C:\CloudDeployment\Setup\Certificates。 


| 部署文件夹 | 所需的证书使用者和使用者可选名称 (SAN) | 范围（按区域） | 子域命名空间 |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| 公共门户 | portal.&lt;region>.&lt;fqdn> | 门户 | &lt;region>.&lt;fqdn> |
| 管理门户 | adminportal.&lt;region>.&lt;fqdn> | 门户 | &lt;region>.&lt;fqdn> |
| Azure 资源管理器公共门户 | management.&lt;region>.&lt;fqdn> | Azure 资源管理器 | &lt;region>.&lt;fqdn> |
| Azure 资源管理器管理门户 | adminmanagement.&lt;region>.&lt;fqdn> | Azure 资源管理器 | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） | Blob 存储 | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） | 表存储 | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） | 队列存储 | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） |  内部 Key Vault |  adminvault.&lt;region>.&lt;fqdn> |
| 管理扩展主机 | *.adminhosting.\<region>.\<fqdn>（通配符 SSL 证书） | 管理扩展主机 | adminhosting.\<region>.\<fqdn> |
| 公共扩展主机 | *.hosting.\<region>.\<fqdn>（通配符 SSL 证书） | 公共扩展主机 | hosting.\<region>.\<fqdn> |

如果使用 Azure AD 部署模式来部署 Azure Stack，只需请求上表中所列的证书。 但是，如果使用 AD FS 部署模式来部署 Azure Stack，则还必须请求下表中所述的证书：

|部署文件夹|所需的证书使用者和使用者可选名称 (SAN)|范围（按区域）|子域命名空间|
|-----|-----|-----|-----|
|ADFS|adfs. *&lt;region>.&lt;fqdn>*<br>（SSL 证书）|ADFS|*&lt;region>.&lt;fqdn>*|
|图形|graph. *&lt;region>.&lt;fqdn>*<br>（SSL 证书）|图形|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> 本部分所列的所有证书必须使用相同的密码。 

## <a name="optional-paas-certificates"></a>可选的 PaaS 证书
如果打算在部署和配置 Azure Stack 之后部署其他 Azure Stack PaaS 服务（SQL、MySQL 和 应用服务），则需要请求额外的证书来涵盖 PaaS 服务的终结点。 

> [!IMPORTANT]
> 用于应用服务、SQL 和 MySQL 资源提供程序的证书需要包含与用于全局 Azure Stack 终结点的证书相同的根颁发机构。 

下表描述了 SQL 和 MySQL 适配器以及应用服务所需的终结点与证书。 无需将这些证书复制到 Azure Stack 部署文件夹。 应在安装其他资源提供程序时提供这些证书。 

|范围（按区域）|证书|所需的证书使用者和使用者可选名称 (SAN)|子域命名空间|
|-----|-----|-----|-----|
|SQL、MySQL|SQL 和 MySQL|&#42;.dbadapter. *&lt;region>.&lt;fqdn>*<br>（通配符 SSL 证书）|dbadapter. *&lt;region>.&lt;fqdn>*|
|应用服务|Web 流量默认 SSL 证书|&#42;.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice. *&lt;region>.&lt;fqdn>*<br>（多域通配符 SSL 证书<sup>1</sup>）|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|应用服务|API|api.appservice. *&lt;region>.&lt;fqdn>*<br>（SSL 证书<sup>2</sup>）|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|应用服务|FTP|ftp.appservice. *&lt;region>.&lt;fqdn>*<br>（SSL 证书<sup>2</sup>）|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|应用服务|SSO|sso.appservice. *&lt;region>.&lt;fqdn>*<br>（SSL 证书<sup>2</sup>）|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|

<sup>1</sup> 需要一个包含多个通配符使用者可选名称的证书。 并非所有公共证书颁发机构都支持在单个证书中包含多个通配符 SAN 

<sup>2</sup> 不能使用 &#42;.appservice. *&lt;region>.&lt;fqdn>* 通配符证书来取代这三个证书（api.appservice. *&lt;region>.&lt;fqdn>* 、ftp.appservice. *&lt;region>.&lt;fqdn>* 和 sso.appservice. *&lt;region>.&lt;fqdn>* ）。 应用服务明确要求对这些终结点使用不同的证书。 

## <a name="learn-more"></a>了解详情
了解如何[为 Azure Stack 部署生成 PKI 证书](azure-stack-get-pki-certs.md)。 

## <a name="next-steps"></a>后续步骤
[标识集成](azure-stack-integrate-identity.md)