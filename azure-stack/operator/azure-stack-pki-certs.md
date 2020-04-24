---
title: Azure Stack Hub 公钥基础结构证书要求
description: 了解 Azure Stack Hub 集成系统的 Azure Stack Hub PKI 证书部署要求。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 3/04/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: c4565ce33faf1f76a4774736d9195c9d7256b6da
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80423832"
---
# <a name="azure-stack-hub-public-key-infrastructure-pki-certificate-requirements"></a>Azure Stack 中心公钥基础结构（PKI）证书要求

Azure Stack Hub 有一个公共基础结构网络，该网络使用分配给少量 Azure Stack Hub 服务，并可能分配给租户 VM 的外部可访问公共 IP 地址。 在部署 Azure Stack Hub 期间，需要使用这些 Azure Stack Hub 公共基础结构终结点的、具有适当 DNS 名称的 PKI 证书。 本文提供以下方面的信息：

- 部署 Azure Stack Hub 时需要哪些证书。
- 获取与这些规范匹配的证书的过程是什么。
- 如何在部署期间准备、验证和使用这些证书。

> [!NOTE]
> 默认情况下，Azure Stack Hub 还使用内部 Active Directory 集成证书颁发机构 (CA) 颁发的证书在节点之间进行身份验证。 为了验证证书，所有 Azure Stack Hub 基础结构计算机都信任内部 CA 的根证书，方法是将该证书添加到其本地证书存储中。 在 Azure Stack Hub 中没有证书固定或将证书加入允许列表的功能。 根据目标的 FQDN 验证每个服务器证书的 SAN。 同时还会验证整个信任链以及证书到期日期（没有锁定证书的标准 TLS 服务器身份验证）。

## <a name="certificate-requirements"></a>证书要求
以下列表描述了部署 Azure Stack Hub 时需要满足的证书要求：

- 证书必须由内部证书颁发机构或公共证书颁发机构颁发。 如果使用公共证书颁发机构，它必须作为 Microsoft 信任根颁发机构计划的一部分包含在基础操作系统映像中。 如需完整列表，请参阅 [Microsoft 受信任根证书计划：参与者](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)中找到完整列表。
- Azure Stack Hub 基础结构必须能够通过网络访问证书中发布的证书颁发机构的证书吊销列表 (CRL) 位置。 此 CRL 必须是一个 http 终结点。
- 在 pre-1903 内部版本中轮换证书时，证书必须由签署部署时提供的证书的同一内部证书颁发机构颁发，或者由上述任何公共证书颁发机构颁发。 就 1903 及更高版本来说，证书可以由任何企业或公共证书颁发机构来颁发。
- 不支持使用自签名证书。
- 对于部署和轮换，可以使用单一证书覆盖证书的“使用者名称”和“使用者可选名称(SAN)”字段中的所有命名空间，也可以为下面你计划使用的 Azure Stack Hub 服务所需的每个命名空间使用单独的证书。 这两种方法都需要将通配符用于需要它们的终结点，例如 **KeyVault** 和 **KeyVaultInternal**。
- 证书的 PFX 加密应当为 3DES。
- 证书签名算法不能为 SHA1。
- 证书格式必须是 PFX，因为安装 Azure Stack Hub 时需要公钥和私钥。 私钥必须设置本地计算机密钥属性。
- PFX 加密必须是 3DES（从 Windows 10 客户端或 Windows Server 2016 证书存储进行导出时，此加密是默认设置）。
- 证书 pfx 文件的“密钥使用”字段中必须包含“数字签名”和“KeyEncipherment”值。
- 证书 pfx 文件的“增强型密钥使用”字段中必须包含“服务器身份验证(1.3.6.1.5.5.7.3.1)”和“客户端身份验证(1.3.6.1.5.5.7.3.2)”值。
- 证书的“颁发给:”字段不能与其“颁发者:”字段相同。
- 部署时，所有证书 pfx 文件的密码都必须相同。
- 证书 pfx 的密码必须是复杂密码。 请记下此密码，因为它将用作部署参数。 此密码必须满足以下密码复杂性要求：
    - 最小长度为 8 个字符。
    - 至少包含以下字符中的三种字符：大写字母、小写字母、0-9 中的数字、特殊字符、不是大写也不是小写的字母字符。
- 确保使用者名称与使用者可选名称扩展 (x509v3_config) 中的使用者可选名称匹配。 “使用者可选名称”字段允许你指定要受单个 SSL 证书保护的其他主机名（网站、IP 地址、公用名称）。

> [!NOTE]  
> 不支持自签名证书。  
> 在断开连接模式下部署 Azure Stack Hub 时，建议使用企业证书颁发机构颁发的证书。 这很重要，因为访问 Azure Stack Hub 终结点的客户端必须能够联系证书吊销列表 (CRL)。

> [!NOTE]  
> 支持在证书的信任链中包含中间证书颁发机构。 

## <a name="mandatory-certificates"></a>必需的证书
本部分中的表格描述部署 Azure AD 和 AD FS Azure Stack Hub 时所需的 Azure Stack Hub 公共终结点 PKI 证书。 证书要求已根据区域以及所用命名空间和每个命名空间所需的证书分组。 此表格还描述了解决方案提供程序将每个公共终结点的不同证书所复制到的文件夹。

需要使用每个 Azure Stack Hub 公共基础结构终结点的、具有适当 DNS 名称的证书。 每个终结点的 DNS 名称使用以下格式表示：&lt;prefix>.&lt;region>.&lt;fqdn>  。

对于部署，[region] 和 [externalfqdn] 值必须与针对 Azure Stack Hub 系统选择的区域和外部域名相匹配。 例如，如果区域名称为 *Redmond*，外部域名为 *contoso.com*，则 DNS 名称的格式为 *&lt;prefix>.redmond.contoso.com*。 *&lt;prefix>* 值由 Microsoft 预先指定，描述证书保护的终结点。 此外，外部基础结构终结点的 *&lt;prefix>* 值取决于使用特定终结点的 Azure Stack Hub 服务。

对于生产环境，我们建议为每个终结点生成单独的证书并将其复制到相应的目录中。 对于开发环境，证书可以作为单个通配符证书提供，涵盖将 "使用者" 和 "使用者备用名称（SAN）" 字段中的所有命名空间复制到所有目录中。 使用涵盖所有终结点和服务的单个证书是一种不安全的方式，因此仅用于开发。 请记住，这两个选项都要求对 **acs** 和 Key Vault 等需要通配符证书的终结点使用此类证书。

> [!Note]  
> 在部署期间，必须将证书复制到与要部署的标识提供者（Azure AD 或 AD FS）匹配的部署文件夹中。 如果将单个证书用于所有终结点，必须将该证书文件复制到下表所述的每个部署文件夹。 部署虚拟机中已预先构建了文件夹结构，路径为：C:\CloudDeployment\Setup\Certificates。

| 部署文件夹 | 所需的证书使用者和使用者可选名称 (SAN) | 范围（按区域） | 子域命名空间 |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| 公共门户 | portal.&lt;region>.&lt;fqdn> | 门户 | &lt;region>.&lt;fqdn> |
| 管理门户 | adminportal.&lt;region>.&lt;fqdn> | 门户 | &lt;region>.&lt;fqdn> |
| Azure 资源管理器公共门户 | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Azure 资源管理器管理门户 | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） | Blob 存储 | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） | 表存储 | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） | 队列存储 | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） | 密钥保管库 | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>（通配符 SSL 证书） |  内部 Key Vault |  adminvault.&lt;region>.&lt;fqdn> |
| 管理扩展主机 | *.adminhosting.\<region>.\<fqdn>（通配符 SSL 证书） | 管理扩展主机 | adminhosting.\<region>.\<fqdn> |
| 公共扩展主机 | *.hosting.\<region>.\<fqdn>（通配符 SSL 证书） | 公共扩展主机 | hosting.\<region>.\<fqdn> |

如果使用 Azure AD 部署模式来部署 Azure Stack Hub，只需请求上表中所列的证书。 但是，如果使用 AD FS 部署模式来部署 Azure Stack Hub，则还必须请求下表中所述的证书：

|部署文件夹|所需的证书使用者和使用者可选名称 (SAN)|范围（按区域）|子域命名空间|
|-----|-----|-----|-----|
|ADFS|adfs. *&lt;region>.&lt;fqdn>*<br>（SSL 证书）|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph. *&lt;region>.&lt;fqdn>*<br>（SSL 证书）|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> 本部分所列的所有证书必须使用相同的密码。

## <a name="optional-paas-certificates"></a>可选的 PaaS 证书
如果计划在部署和配置 Azure Stack 中心后部署附加的 Azure Stack 中心 PaaS 服务（例如 SQL、MySQL、应用服务或事件中心），则必须请求其他证书以涵盖 PaaS 服务的终结点。

> [!IMPORTANT]
> 用于资源提供程序的证书必须与用于全局 Azure Stack 中心终结点的证书具有相同的根证书颁发机构。

下表描述了资源提供程序所需的终结点和证书。 无需将这些证书复制到 Azure Stack Hub 部署文件夹。 而是在资源提供程序安装过程中提供这些证书。

|范围（按区域）|证书|所需的证书使用者和使用者可选名称 (SAN)|子域命名空间|
|-----|-----|-----|-----|
|SQL、MySQL|SQL 和 MySQL|&#42;.dbadapter. *&lt;region>.&lt;fqdn>*<br>（通配符 SSL 证书）|dbadapter. *&lt;region>.&lt;fqdn>*|
|应用服务|Web 流量默认 SSL 证书|&#42;.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice. *&lt;region>.&lt;fqdn>*<br>（多域通配符 SSL 证书<sup>1</sup>）|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|应用服务|API|api.appservice. *&lt;region>.&lt;fqdn>*<br>（SSL 证书<sup>2</sup>）|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|应用服务|FTP|ftp.appservice. *&lt;region>.&lt;fqdn>*<br>（SSL 证书<sup>2</sup>）|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|应用服务|SSO|sso.appservice. *&lt;region>.&lt;fqdn>*<br>（SSL 证书<sup>2</sup>）|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|事件中心|事件中心|&#42; eventhub。* &lt;区域>。fqdn &lt;>* （SAN）| eventhub.* &lt;区域>。&lt;fqdn>* |

<sup>1</sup> 需要一个包含多个通配符使用者可选名称的证书。 并非所有公共证书颁发机构都支持在单个证书中包含多个通配符 SAN。

<sup>2</sup> &#42; appservice。* &lt;区域>。fqdn &lt;>* 通配符证书不能用于替代这三个证书（appservice）。*&lt;区域>。fqdn &lt;>*，appservice。* &lt;区域>。fqdn &lt;>* 和 appservice。* &lt;区域>。fqdn &lt;>*。 应用服务明确要求对这些终结点使用不同的证书。

## <a name="learn-more"></a>了解详细信息
了解如何[为 Azure Stack 中心部署生成 PKI 证书](azure-stack-get-pki-certs.md)。

## <a name="next-steps"></a>后续步骤
[将 AD FS 标识与 Azure Stack 中心数据中心集成](azure-stack-integrate-identity.md)。