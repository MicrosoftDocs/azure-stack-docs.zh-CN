---
title: Azure Stack 中心公钥基础结构证书要求
description: 了解 Azure Stack 中心集成系统 Azure Stack 中心 PKI 证书部署要求。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 12/16/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 5ed0a7fa6091ee62a7de5e1938bd0094d7765512
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509698"
---
# <a name="azure-stack-hub-public-key-infrastructure-certificate-requirements"></a>Azure Stack 中心公钥基础结构证书要求

Azure Stack 集线器具有公共基础结构网络，该网络使用分配给一小部分 Azure Stack 中心服务和可能租户 Vm 的外部可访问公共 IP 地址。 Azure Stack 中心部署过程中需要具有这些 Azure Stack 中心公共基础结构终结点的适当 DNS 名称的 PKI 证书。 本文提供了有关以下内容的信息：

- Azure Stack 中心部署所需的证书。
- 获取与这些规范匹配的证书的过程。
- 如何在部署期间准备、验证和使用这些证书。

> [!NOTE]
> 默认情况下，Azure Stack 中心还使用由内部 Active Directory 集成证书颁发机构（CA）颁发的证书，在节点之间进行身份验证。 若要验证证书，所有 Azure Stack 中心基础结构计算机都通过将该证书添加到其本地证书存储区来信任内部 CA 的根证书。 Azure Stack 中心内没有证书固定或允许列表。 根据目标的 FQDN 验证每个服务器证书的 SAN。 同时还会验证整个信任链以及证书的到期日期（没有证书固定的标准 TLS 服务器身份验证）。

## <a name="certificate-requirements"></a>证书要求
以下列表描述了部署 Azure Stack 中心所需的证书要求：

- 证书必须由内部证书颁发机构或公共证书颁发机构颁发。 如果使用公共证书颁发机构，则必须将其作为 Microsoft 受信任的根证书颁发机构程序的一部分包含在基本操作系统映像中。 有关完整列表，请参阅[Microsoft 受信任的根证书程序：参与者](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)。
- Azure Stack 中心基础结构必须对证书中发布的证书颁发机构的证书吊销列表（CRL）位置具有网络访问权限。 此 CRL 必须是 http 终结点。
- 在1903之前的版本中循环使用证书时，必须从用于签署部署时提供的证书或上述任何公共证书颁发机构的相同内部证书颁发机构颁发证书。 对于1903和更高版本，证书可由任何企业证书颁发机构或公共证书颁发机构颁发。
- 不支持使用自签名证书。
- 对于部署和旋转，你可以使用一个证书，涵盖证书的 "使用者名称" 和 "使用者备用名称（SAN）" 字段中的所有命名空间，或者你可以使用 Azure Stack 中心下的每个命名空间的单个证书你计划利用的服务需要。 这两种方法都需要对需要使用通配符（如**KeyVault**和**KeyVaultInternal**）。
- 证书的 PFX 加密应该是3DES。
- 证书签名算法不应为 SHA1。
- 证书格式必须为 PFX，因为 Azure Stack 集线器安装需要公钥和私钥。 私钥必须设置本地计算机密钥属性。
- PFX 加密必须为3DES （从 Windows 10 客户端或 Windows Server 2016 证书存储导出时，此加密是默认值）。
- 证书 pfx 文件的 "密钥用法" 字段中必须包含 "数字签名" 和 "KeyEncipherment" 值。
- 证书 pfx 文件的 "增强型密钥用法" 字段中的值必须为 "服务器身份验证（1.3.6.1.5.5.7.3.1）" 和 "客户端身份验证（1.3.6.1.5.5.7.3.2）"。
- 证书的 "颁发给：" 字段不得与 "颁发者" 字段相同。
- 部署时，所有证书 pfx 文件的密码必须相同。
- 证书 pfx 的密码必须是复杂密码。 请记下此密码，因为你将使用它作为部署参数。 密码必须满足以下密码复杂性要求：
    - 最小长度为8个字符。
    - 至少三个字符中的三个：大写字母、小写字母、0-9 的数字、特殊字符、不大写或小写的字母字符。
- 确保使用者可选名称扩展（x509v3_config）中的 "使用者名称" 和 "使用者可选名称" 匹配。 使用 "使用者可选名称" 字段，你可以指定要由单个 SSL 证书保护的其他主机名（网站、IP 地址、公用名）。

> [!NOTE]  
> 不支持自签名证书。  
> 在断开连接模式下部署 Azure Stack 集线器时，建议使用企业证书颁发机构颁发的证书。 这一点很重要，因为访问 Azure Stack 中心终结点的客户端必须能够联系证书吊销列表（CRL）。

> [!NOTE]  
> *支持证书*的信任链中存在中间证书颁发机构。

## <a name="mandatory-certificates"></a>必需证书
本部分中的表描述了 Azure AD 和 AD FS Azure Stack 中心部署所需的 Azure Stack 中心公共终结点 PKI 证书。 证书要求按区域以及使用的命名空间和每个命名空间所需的证书进行分组。 该表还介绍了解决方案提供商将不同的证书复制到每个公共终结点的文件夹。

需要为每个 Azure Stack 中心公共基础结构终结点提供适当 DNS 名称的证书。 每个终结点的 DNS 名称的格式为： *&lt;前缀 >.&lt;区域 >。&lt;fqdn >* 。

对于你的部署，[region] 和 [externalfqdn] 值必须与你为 Azure Stack 中心系统选择的区域和外部域名匹配。 例如，如果区域名称为*Redmond* ，外部域名为*CONTOSO.COM*，则 DNS 名称的格式 *&lt;前缀 > .com .com*。 *&lt;前缀 >* 值由 Microsoft predesignated，用于描述证书所保护的终结点。 此外， *&lt;前缀 >* 外部基础结构终结点的值取决于使用特定终结点的 Azure Stack 中心服务。

对于生产环境，我们建议为每个终结点生成单个证书，并将其复制到相应的目录中。 对于开发环境，证书可以作为单个通配符证书提供，涵盖已复制到所有目录中的 "使用者" 和 "使用者备用名称（SAN）" 字段中的所有命名空间。 涵盖所有终结点和服务的单个证书是一种不安全的状况，因此仅用于开发。 请记住，这两个选项都要求将通配符证书用于**acs**和 Key Vault 所需的终结点。

> [!Note]  
> 在部署过程中，您必须将证书复制到与要部署的标识提供者匹配的部署文件夹（Azure AD 或 AD FS）。 如果对所有终结点使用单个证书，则必须将该证书文件复制到下表中所述的每个部署文件夹中。 文件夹结构是在部署虚拟机中预生成的，可在以下位置找到： C:\CloudDeployment\Setup\Certificates。

| 部署文件夹 | 必需的证书使用者和使用者可选名称（SAN） | 范围（每个区域） | 子域命名空间 |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| 公共门户 | 端口.>&lt;区域。&lt;fqdn > | 门户 | > &lt;区域。&lt;fqdn > |
| 管理门户 | adminportal.>&lt;区域。&lt;fqdn > | 门户 | > &lt;区域。&lt;fqdn > |
| Azure 资源管理器公有 | 层.>&lt;区域。&lt;fqdn > | Azure 资源管理器 | > &lt;区域。&lt;fqdn > |
| Azure 资源管理器管理员 | adminmanagement.>&lt;区域。&lt;fqdn > | Azure 资源管理器 | > &lt;区域。&lt;fqdn > |
| ACSBlob | \* blob。>&lt;区域。&lt;fqdn ><br>（通配符 SSL 证书） | Blob 存储 | blob.>&lt;区域。&lt;fqdn > |
| ACSTable | \* 表。>&lt;区域。&lt;fqdn ><br>（通配符 SSL 证书） | 表存储 | 数据表.>&lt;区域。&lt;fqdn > |
| ACSQueue | *. queue。>&lt;区域。&lt;fqdn ><br>（通配符 SSL 证书） | 队列存储 | 使.>&lt;区域。&lt;fqdn > |
| KeyVault | \* 保管库。>&lt;区域。&lt;fqdn ><br>（通配符 SSL 证书） | Key Vault | 保管库.>&lt;区域。&lt;fqdn > |
| KeyVaultInternal | *. .adminvault。>&lt;区域。&lt;fqdn ><br>（通配符 SSL 证书） |  内部 Keyvault |  .adminvault.>&lt;区域。&lt;fqdn > |
| 管理扩展主机 | *.adminhosting.>\<区域。\<fqdn > （通配符 SSL 证书） | 管理扩展主机 | adminhosting.>\<区域。\<fqdn > |
| 公用扩展主机 | *。托管。>\<区域。\<fqdn > （通配符 SSL 证书） | 公用扩展主机 | 提供.>\<区域。\<fqdn > |

如果使用 Azure AD 部署模式部署 Azure Stack 中心，只需请求上表中列出的证书。 但是，如果使用 AD FS 部署模式部署 Azure Stack 集线器，则还必须请求下表中所述的证书：

|部署文件夹|必需的证书使用者和使用者可选名称（SAN）|范围（每个区域）|子域命名空间|
|-----|-----|-----|-----|
|ADFS|adfs. *&lt;fqdn >&lt;区域 >*<br>（SSL 证书）|ADFS|*> &lt;区域。&lt;fqdn >*|
|图形|图形. *&lt;fqdn >&lt;区域 >*<br>（SSL 证书）|图形|*> &lt;区域。&lt;fqdn >*|
|

> [!IMPORTANT]
> 本节中列出的所有证书都必须具有相同的密码。

## <a name="optional-paas-certificates"></a>可选 PaaS 证书
如果你计划在部署和配置 Azure Stack 中心后部署其他 Azure Stack 中心 PaaS 服务（SQL、MySQL 和应用服务），则需要请求其他证书以涵盖 PaaS 服务的终结点。

> [!IMPORTANT]
> 用于应用服务、SQL 和 MySQL 资源提供程序的证书需要与用于全局 Azure Stack 中心终结点的证书具有相同的根证书颁发机构。

下表描述了 SQL 和 MySQL 适配器以及应用服务所需的终结点和证书。 不需要将这些证书复制到 Azure Stack 中心部署文件夹中。 相反，在安装其他资源提供程序时提供这些证书。

|范围（每个区域）|证书|必需的证书使用者和使用者可选名称（San）|子域命名空间|
|-----|-----|-----|-----|
|SQL、MySQL|SQL 和 MySQL|&#42;.dbadapter. *&lt;fqdn >&lt;区域 >*<br>（通配符 SSL 证书）|.dbadapter. *&lt;fqdn >&lt;区域 >*|
|应用服务|Web 流量默认 SSL 证书|&#42;appservice. *&lt;fqdn >&lt;区域 >*<br>&#42;appservice。 *&lt;fqdn >&lt;区域 >*<br>&#42;appservice。 *&lt;fqdn >&lt;区域 >*<br>（多域通配符 SSL 证书<sup>1</sup>）|appservice. *&lt;fqdn >&lt;区域 >*<br>appservice。 *&lt;fqdn >&lt;区域 >*|
|应用服务|API|appservice。 *&lt;fqdn >&lt;区域 >*<br>（SSL 证书<sup>2</sup>）|appservice. *&lt;fqdn >&lt;区域 >*<br>appservice。 *&lt;fqdn >&lt;区域 >*|
|应用服务|FTP|appservice。 *&lt;fqdn >&lt;区域 >*<br>（SSL 证书<sup>2</sup>）|appservice. *&lt;fqdn >&lt;区域 >*<br>appservice。 *&lt;fqdn >&lt;区域 >*|
|应用服务|SSO|appservice。 *&lt;fqdn >&lt;区域 >*<br>（SSL 证书<sup>2</sup>）|appservice. *&lt;fqdn >&lt;区域 >*<br>appservice。 *&lt;fqdn >&lt;区域 >*|

<sup>1</sup>需要一个具有多个通配符使用者可选名称的证书。 并非所有公共证书颁发机构都支持在单个证书上使用多个通配符 San。

&#42; <sup>2</sup> appservice。 *&lt;区域 >。&lt;fqdn >* 无法将通配符证书用于替代这三个证书（ *&lt;>&lt;* >&lt;> *&lt;>&lt;* > *&lt;>* 。 Appservice 显式要求对这些终结点使用单独的证书。

## <a name="learn-more"></a>了解详细信息
了解如何[为 Azure Stack 中心部署生成 PKI 证书](azure-stack-get-pki-certs.md)。

## <a name="next-steps"></a>后续步骤
[将 AD FS 标识与 Azure Stack 中心数据中心集成](azure-stack-integrate-identity.md)。