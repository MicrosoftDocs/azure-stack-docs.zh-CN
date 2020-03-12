---
title: 验证 Azure Stack 中心 PKI 证书
titleSuffix: Azure Stack Hub
description: 了解如何使用 Azure Stack 中心就绪检查程序工具验证 Azure Stack 集线器集成系统的 PKI 证书。
services: azure-stack
documentationcenter: ''
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: e8114d060e596f581cd23ec80b0b5f455567dc1f
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025249"
---
# <a name="validate-azure-stack-hub-pki-certificates"></a>验证 Azure Stack 中心 PKI 证书

本文中介绍的 Azure Stack 集线器准备情况检查器工具可[从 PowerShell 库中](https://aka.ms/AzsReadinessChecker)获取。 使用工具验证[生成的公钥基础结构（PKI）证书](azure-stack-get-pki-certs.md)是否适用于预先部署。 如果需要，请保留足够的时间来测试和重新颁发证书，以验证证书。

就绪检查程序工具执行以下证书验证：

- **分析 PFX**  
    检查是否有有效的 PFX 文件、正确的密码以及公共信息是否受密码保护。
- **到期日期**  
    检查七天的最小有效性。
- **签名算法**  
    检查签名算法是否不是 SHA1。
- **私钥**  
    检查私钥是否存在，并连同本地计算机属性一起导出。 
- **证书链**  
    检查证书链是否完整，包括检查自签名证书。
- **DNS 名称**  
    检查 SAN 是否包含每个终结点的相关 DNS 名称，或者是否存在支持通配符。
- **密钥用法**  
    检查密钥用法是否包含数字签名和密钥加密，并检查增强型密钥用法是否包含服务器身份验证和客户端身份验证。
- **密钥大小**  
    检查密钥大小是否为2048或更大。
- **链式顺序**  
    检查其他证书的顺序，验证顺序是否正确。
- **其他证书**  
    确保没有其他证书打包在 PFX 中，而不是相关的叶证书及其链。

> [!IMPORTANT]  
> PKI 证书是 PFX 文件，应将密码视为敏感信息。

## <a name="prerequisites"></a>先决条件

在验证 Azure Stack 中心部署的 PKI 证书之前，系统应符合以下先决条件：

- Microsoft Azure Stack 中心就绪检查程序。
- 按照[准备说明](azure-stack-prepare-pki-certs.md)导出的 SSL 证书。
- DeploymentData。
- Windows 10 或 Windows Server 2016。

## <a name="perform-core-services-certificate-validation"></a>执行 core services 证书验证

使用以下步骤准备并验证 Azure Stack 中心 PKI 证书以进行部署和机密旋转：

1. 通过运行以下 cmdlet，从 PowerShell 提示符（5.1 或更高版本）安装**AzsReadinessChecker** ：

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. 创建证书目录结构。 在下面的示例中，可以将 `<C:\Certificates\Deployment>` 更改为所选的新目录路径。
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > 如果你使用 AD FS 作为标识系统，则需要 AD FS 和图形。 例如：
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - 将证书放入上一步骤中创建的相应目录。 例如：  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. 在 PowerShell 窗口中，更改 `RegionName`的值、`FQDN` 并 `IdentitySystem` 适用于 Azure Stack 中心环境的，并运行以下 cmdlet：

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsCertificateValidation -CertificateType Deployment -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. 检查输出并确保所有证书都通过所有测试。 例如：

    ```powershell
    Invoke-AzsCertificateValidation v1.1912.1082.37 started.
    Testing: KeyVaultInternal\adminvault.pfx
    Thumbprint: B1CB76****************************565B99
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
    Testing: ARM Public\management.pfx
    Thumbprint: 44A35E****************************36052A
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
    Testing: Admin Portal\adminportal.pfx
    Thumbprint: 3F5E81****************************9EBF9A
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
    Testing: Public Portal\portal.pfx

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

    若要验证其他 Azure Stack 中心服务的证书，请更改 ```-CertificateType```的值。 例如：

    ```powershell  
    # App Services
    Invoke-AzsCertificateValidation -CertificateType AppServices -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsCertificateValidation -CertificateType DBAdapter -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHubs
    Invoke-AzsCertificateValidation -CertificateType EventHubs -CertificatePath C:\Certificates\EventHubs -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsCertificateValidation -CertificateType IoTHub -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
    每个文件夹应该包含一个用于证书类型的 PFX 文件。 如果证书类型有多证书要求，则每个证书的嵌套文件夹都应与名称敏感。 以下代码显示了所有证书类型的示例文件夹/证书结构，以及 ```-CertificateType``` 和 ```-CertificatePath```的相应值。
    
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

**症状**：已跳过测试

**原因**：如果不符合依赖关系，AzsReadinessChecker 将跳过某些测试：

 - 如果证书链失败，则会跳过其他证书。

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

**解决方法**：按照每个证书的每个测试集下的详细信息部分中的工具指导进行操作。

## <a name="certificates"></a>证书

| Directory | 证书 |
| ---    | ----        |
| ACSBlob | `wildcard_blob_<region>_<externalFQDN>` |
| ACSQueue  |  `wildcard_queue_<region>_<externalFQDN>` |
| ACSTable  |  `wildcard_table_<region>_<externalFQDN>` |
| 管理扩展主机  |  `wildcard_adminhosting_<region>_<externalFQDN>` |
| 管理门户  |  `adminportal_<region>_<externalFQDN>` |
| ARM 管理员  |  `adminmanagement_<region>_<externalFQDN>` |
| ARM 公共  |  `management_<region>_<externalFQDN>` |
| KeyVault  |  `wildcard_vault_<region>_<externalFQDN>` |
| KeyVaultInternal  |  `wildcard_adminvault_<region>_<externalFQDN>` |
| 公用扩展主机  |  `wildcard_hosting_<region>_<externalFQDN>` |
| 公共门户  |  `portal_<region>_<externalFQDN>` |

## <a name="using-validated-certificates"></a>使用验证的证书

证书通过 AzsReadinessChecker 验证后，便可以在 Azure Stack 中心部署中使用这些证书，也可以 Azure Stack 中心机密旋转。

 - 对于部署，安全地将证书传输到你的部署工程师，以便他们可以将其复制到[Azure Stack 中心 PKI 要求文档](azure-stack-pki-certs.md)中指定的部署主机上。
 - 对于秘密旋转，可以根据[Azure Stack 中心机密旋转文档](azure-stack-rotate-secrets.md)，使用证书来更新 Azure Stack 中心环境的公共基础结构终结点的旧证书。
 - 对于 PaaS 服务，可以按照在[Azure Stack 中心文档中提供服务的概述](service-plan-offer-subscription-overview.md)，使用证书在 Azure Stack 中心安装 SQL、MySQL 和应用服务资源提供程序。

## <a name="next-steps"></a>后续步骤

[数据中心标识集成](azure-stack-integrate-identity.md)
