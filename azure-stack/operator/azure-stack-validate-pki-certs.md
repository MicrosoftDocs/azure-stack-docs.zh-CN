---
title: 验证用于 Azure Stack 集成系统部署的 Azure Stack 公钥基础结构证书 | Microsoft Docs
description: 介绍如何验证 Azure Stack 集成系统的 Azure Stack PKI 证书。 介绍如何使用 Azure Stack 证书检查器工具。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 3823aa73d58af48c662690aa0d8e8a21180b4ed6
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283236"
---
# <a name="validate-azure-stack-pki-certificates"></a>验证 Azure Stack PKI 证书

可[从 PowerShell 库](https://aka.ms/AzsReadinessChecker)获取本文所述的 Azure Stack 就绪性检查器工具。 使用该工具可以验证[生成的 PKI 证书](azure-stack-get-pki-certs.md)是否适用于前期部署。 请留出足够的时间来验证证书，以测试证书并在必要时重新颁发证书。

就绪性检查器工具执行以下证书验证：

- **读取 PFX**  
    检查 PFX 文件是否有效且密码正确，以及公开的信息是否受密码保护。 
- **签名算法**  
    检查签名算法是否不是 SHA1。
- **私钥**  
    检查私钥是否存在，并且已连同“本地计算机”属性一起导出。 
- **证书链**  
    检查证书链是否完整，包括自签名证书。
- **DNS 名称**  
    检查 SAN 是否包含每个终结点的相关 DNS 名称，或支持性的通配符是否存在。
- **密钥用法**  
    检查密钥用法是否包含数字签名和密钥加密，增强型密钥用法是否包含服务器身份验证和客户端身份验证。
- **密钥大小**  
    检查密钥大小是否为 2048 或更大。
- **链序**  
    检查其他证书的顺序，验证顺序是否正确。
- **其他证书**  
    确保除了相关叶证书及其链以外，PFX 中未打包其他证书。
- **无配置文件**  
    检查新用户是否无需加载用户配置文件即可加载 PFX 数据，可在证书有效期内模拟 gMSA 帐户的行为。

> [!IMPORTANT]  
> PKI 证书是一个 PFX 文件，其密码应被视为敏感信息。

## <a name="prerequisites"></a>先决条件

在验证用于 Azure Stack 部署的 PKI 证书之前，系统应符合以下先决条件：

- Microsoft Azure Stack 准备情况检查程序
- 遵照[准备说明](azure-stack-prepare-pki-certs.md)导出的 SSL 证书
- DeploymentData.json
- Windows 10 或 Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>执行核心服务证书验证

使用以下步骤来准备和验证用于部署和机密轮换的 Azure Stack PKI 证书：

1. 在 PowerShell 提示符（5.1 或更高版本）下，运行以下 cmdlet 安装 **AzsReadinessChecker**：

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. 创建证书目录结构。 在以下示例中，可将 `<c:\certificates>` 更改为所选的新目录路径。
    ```powershell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > 如果使用 AD FS 作为标识系统，则需要 AD FS 和 Graph。 例如：
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - 将证书放入上一步骤中创建的相应目录。 例如：  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. 在 PowerShell 窗口中，更改 **RegionName** 和 **FQDN** 的值以适用于 Azure Stack 环境，然后运行以下命令：

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. 检查输出和所有证书是否通过所有测试。 例如：

```powershell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
```

### <a name="known-issues"></a>已知问题

**症状**：跳过了测试

**原因**：如果不符合依赖关系，AzsReadinessChecker 会跳过某些测试：

 - 如果证书链出错，则会跳过其他证书。

    ```powershell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**解决方法**：遵循针对每个证书的每组测试下的详细信息部分中的工具指导。

## <a name="perform-platform-as-a-service-certificate-validation"></a>执行平台即服务证书验证

如果计划 SQL/MySQL 或应用程序服务部署，请使用以下步骤准备和验证 Azure Stack PKI 证书以获取平台即服务 (PaaS) 证书。

1.  在 PowerShell 提示符（5.1 或更高版本）下，运行以下 cmdlet 安装 **AzsReadinessChecker**：

    ```powershell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  为每个需要验证的 PaaS 证书创建一个包含路径和密码的嵌套哈希表。 在 PowerShell 窗口中运行：

    ```powershell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  更改 **RegionName** 和 **FQDN** 的值以匹配 Azure Stack 环境来启动验证。 然后运行：

    ```powershell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  检查输出和所有证书是否通过所有测试。

    ```powershell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="certificates"></a>证书

| 目录 | 证书 |
| ---    | ----        |
| acsBlob | wildcard_blob_\<region>_\<externalFQDN> |
| ACSQueue  |  wildcard_queue_\<region>_\<externalFQDN> |
| ACSTable  |  wildcard_table_\<region>_\<externalFQDN> |
| 管理扩展主机  |  wildcard_adminhosting_\<region>_\<externalFQDN> |
| 管理门户  |  adminportal_\<region>_\<externalFQDN> |
| ARM Admin  |  adminmanagement_\<region>_\<externalFQDN> |
| ARM Public  |  management_\<region>_\<externalFQDN> |
| KeyVault  |  wildcard_vault_\<region>_\<externalFQDN> |
| KeyVaultInternal  |  wildcard_adminvault_\<region>_\<externalFQDN> |
| 公共扩展主机  |  wildcard_hosting_\<region>_\<externalFQDN> |
| 公共门户  |  portal_\<region>_\<externalFQDN> |

## <a name="using-validated-certificates"></a>使用已验证的证书

通过 AzsReadinessChecker 验证证书后，可在 Azure Stack 部署中使用这些证书，或者将其用于 Azure Stack 机密轮换。 

 - 对于部署，请根据 [Azure Stack PKI 要求文档](azure-stack-pki-certs.md)中的规定，安全地将证书传送给部署工程师，使他们能够将其复制到部署主机。
 - 对于机密轮换，可以遵循 [Azure Stack 机密轮换文档](azure-stack-rotate-secrets.md)，使用这些证书来更新 Azure Stack 环境公共基础结构终结点的旧证书。
 - 对于 PaaS 服务，可以按照[在 Azure Stack 中提供服务概述](service-plan-offer-subscription-overview.md)文档，使用证书在 Azure Stack 中安装 SQL、MySQL 和应用程序服务资源提供程序。

## <a name="next-steps"></a>后续步骤

[数据中心标识集成](azure-stack-integrate-identity.md)
