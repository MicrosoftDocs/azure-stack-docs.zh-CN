---
title: 验证 Azure Stack Hub PKI 证书
titleSuffix: Azure Stack Hub
description: 了解如何使用 Azure Stack Hub 就绪性检查器工具验证 Azure Stack Hub 集成系统的 PKI 证书。
services: azure-stack
documentationcenter: ''
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: c0a077d8278361370a1781260c3f9c2bb2b11f55
ms.sourcegitcommit: c1f48c19c8a9c438fd22298bc570c12a9b19bb45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2020
ms.locfileid: "86410600"
---
# <a name="validate-azure-stack-hub-pki-certificates"></a>验证 Azure Stack Hub PKI 证书

可[从 PowerShell 库](https://aka.ms/AzsReadinessChecker)获取本文所述的 Azure Stack Hub 就绪性检查器工具。 使用该工具可以验证[生成的公钥基础结构 (PKI) 证书](azure-stack-get-pki-certs.md)是否适用于前期部署。 请留出足够的时间来验证证书，以测试证书并在必要时重新颁发证书。

就绪性检查器工具执行以下证书验证：

- **分析 PFX**  
    检查 PFX 文件是否有效且密码正确，以及公开的信息是否受密码保护。
- **到期日期**  
    检查最短有效期是否为 7 天。
- **签名算法**  
    检查签名算法是否不是 SHA1。
- **私钥**  
    检查私钥是否存在，并且已连同“本地计算机”属性一起导出。 
- **证书链**  
    检查证书链是否完整，包括自签名证书。
- **DNS 名称**  
    检查 SAN 是否包含每个终结点的相关 DNS 名称，或支持性的通配符是否存在。
- **密钥用法**  
    检查密钥用法是否包含数字签名和密钥加密，并检查增强型密钥用法是否包含服务器身份验证和客户端身份验证。
- **密钥大小**  
    检查密钥大小是否为 2048 或更大。
- **链序**  
    检查其他证书的顺序，验证顺序是否正确。
- **其他证书**  
    确保除了相关叶证书及其链以外，PFX 中未打包其他证书。

> [!IMPORTANT]  
> PKI 证书是一个 PFX 文件，其密码应被视为敏感信息。

## <a name="prerequisites"></a>先决条件

在验证用于 Azure Stack Hub 部署的 PKI 证书之前，系统应符合以下先决条件：

- Microsoft Azure Stack 中心就绪检查程序。
- 按照[准备说明](azure-stack-prepare-pki-certs.md)导出的 SSL 证书。
- DeploymentData.json。
- Windows 10 或 Windows Server 2016。

## <a name="perform-core-services-certificate-validation"></a>执行核心服务证书验证

使用以下步骤来验证用于部署和机密轮换的 Azure Stack Hub PKI 证书：

1. 在 PowerShell 提示符（5.1 或更高版本）下，运行以下 cmdlet 安装 **AzsReadinessChecker**：

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. 创建证书目录结构。 在以下示例中，可将 `<C:\Certificates\Deployment>` 更改为所选的新目录路径。
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > 如果使用 AD FS 作为标识系统，则需要 AD FS 和 Graph。 例如：
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - 将证书放入上一步骤中创建的相应目录。 例如：  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. 在 PowerShell 窗口中，更改 `RegionName`、`FQDN` 和 `IdentitySystem` 的值使其适合 Azure Stack Hub 环境，然后运行以下 cmdlet：

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsHubDeploymentCertificateValidation -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. 检查输出，确保所有证书通过所有测试。 例如：

    ```powershell
    Invoke-AzsHubDeploymentCertificateValidation v1.2005.1286.272 started.
    Testing: KeyVaultInternal\KeyVaultInternal.pfx
    Thumbprint: E86699****************************4617D6
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ARM Public\ARMPublic.pfx
    Thumbprint: 8DC4D9****************************69DBAA
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: Admin Portal\AdminPortal.pfx
    Thumbprint: 6F9055****************************4AC0EA
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: Public Portal\PublicPortal.pfx


    Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsHubDeploymentCertificateValidation Completed

    ```

    若要验证其他 Azure Stack Hub 服务的证书，请更改 ```-CertificateType``` 的值。 例如：

    ```powershell  
    # App Services
    Invoke-AzsHubAppServicesCertificateValidation -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsHubDBAdapterCertificateValidation -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHubs
    Invoke-AzsHubEventHubsCertificateValidation -CertificatePath C:\Certificates\EventHubs -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsHubIoTHubCertificateValidation -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
    每个文件夹应包含一个与证书类型对应的 PFX 文件。 如果某个证书类型具有多证书要求，则预期每个证书都有一个嵌套的文件夹，这些文件夹的名称区分大小写。 以下代码显示了所有证书类型的示例文件夹/证书结构，以及 ```-CertificateType``` 和 ```-CertificatePath``` 的相应值。
    
    ```powershell  
    C:\>tree c:\SecretStore /A /F
        Folder PATH listing
        Volume serial number is 85AE-DF2E
        C:\SECRETSTORE
        \---AzureStack
            +---CertificateRequests
            \---Certificates
                +---AppServices         # Invoke-AzsCertificateValidation `
                |   +---API             #     -CertificateType AppServices `
                |   |       api.pfx     #     -CertificatePath C:\Certificates\AppServices
                |   |
                |   +---DefaultDomain
                |   |       wappsvc.pfx
                |   |
                |   +---Identity
                |   |       sso.pfx
                |   |
                |   \---Publishing
                |           ftp.pfx
                |
                +---DBAdapter           # Invoke-AzsCertificateValidation `
                |       dbadapter.pfx   #   -CertificateType DBAdapter `
                |                       #   -CertificatePath C:\Certificates\DBAdapter
                |
                +---Deployment          # Invoke-AzsCertificateValidation `
                |   +---ACSBlob         #   -CertificateType Deployment `
                |   |       acsblob.pfx #   -CertificatePath C:\Certificates\Deployment
                |   |
                |   +---ACSQueue
                |   |       acsqueue.pfx
               ./. ./. ./. ./. ./. ./. ./.    <- Deployment certificate tree trimmed.
                |   \---Public Portal
                |           portal.pfx
                |
                +---EventHubs           # Invoke-AzsCertificateValidation `
                |       eventhubs.pfx   #   -CertificateType EventHubs `
                |                       #   -CertificatePath C:\Certificates\EventHubs
                |
                \---IoTHub              # Invoke-AzsCertificateValidation `
                        iothub.pfx      #   -CertificateType IoTHub `
                                        #   -CertificatePath C:\Certificates\IoTHub
    ```

### <a name="known-issues"></a>已知问题

**症状**：跳过了测试

**原因：** 如果不符合依赖关系，AzsReadinessChecker 会跳过某些测试：

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

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**解决方法**：遵循针对每个证书的每组测试下的详细信息部分中的工具指导。

## <a name="certificates"></a>证书

| Directory | 证书 |
| ---    | ----        |
| ACSBlob | `wildcard_blob_<region>_<externalFQDN>` |
| ACSQueue  |  `wildcard_queue_<region>_<externalFQDN>` |
| ACSTable  |  `wildcard_table_<region>_<externalFQDN>` |
| 管理扩展主机  |  `wildcard_adminhosting_<region>_<externalFQDN>` |
| 管理门户  |  `adminportal_<region>_<externalFQDN>` |
| ARM Admin  |  `adminmanagement_<region>_<externalFQDN>` |
| ARM Public  |  `management_<region>_<externalFQDN>` |
| KeyVault  |  `wildcard_vault_<region>_<externalFQDN>` |
| KeyVaultInternal  |  `wildcard_adminvault_<region>_<externalFQDN>` |
| 公共扩展主机  |  `wildcard_hosting_<region>_<externalFQDN>` |
| 公共门户  |  `portal_<region>_<externalFQDN>` |

## <a name="using-validated-certificates"></a>使用已验证的证书

AzsReadinessChecker 验证证书后，便可在 Azure Stack Hub 部署中使用这些证书，或者将其用于 Azure Stack Hub 机密轮换。

 - 对于部署，请根据 [Azure Stack Hub PKI 要求文档](azure-stack-pki-certs.md)中的规定，安全地将证书传送给部署工程师，使他们能够将其复制到部署主机。
 - 对于机密轮换，可以遵循 [Azure Stack Hub 机密轮换文档](azure-stack-rotate-secrets.md)，使用这些证书来更新 Azure Stack Hub 环境公共基础结构终结点的旧证书。
 - 对于 PaaS 服务，可以按照[在 Azure Stack Hub 中提供服务概述](service-plan-offer-subscription-overview.md)文档，使用证书在 Azure Stack Hub 中安装 SQL、MySQL 和应用程序服务资源提供程序。

## <a name="next-steps"></a>后续步骤

[数据中心标识集成](azure-stack-integrate-identity.md)
