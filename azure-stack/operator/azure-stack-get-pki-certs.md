---
title: 为 Azure Stack 中心生成证书签名请求
description: 了解如何在 Azure Stack 集线器集成系统中为 Azure Stack 中心 PKI 证书生成证书签名请求。
author: ihenkel
ms.topic: article
ms.date: 09/10/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: baee1a439129f83013e191db683927cd15cb1d65
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882251"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>为 Azure Stack 中心生成证书签名请求

可以使用 Azure Stack 集线器准备情况检查器工具创建适用于 Azure Stack 中心部署的证书签名请求（Csr）。 在部署之前，应该请求、生成并验证证书的时间足以进行测试。 可以[从 PowerShell 库](https://aka.ms/AzsReadinessChecker)获取该工具。

你可以使用 Azure Stack 集线器就绪检查器工具（AzsReadinessChecker）来请求以下证书：

- 根据[生成证书签名请求](azure-stack-get-pki-certs.md#generate-certificate-signing-requests)的**标准证书请求**。
- **平台即服务**：可以为[Azure Stack 中心公钥基础结构证书要求-可选 PaaS 证书](azure-stack-pki-certs.md#optional-paas-certificates)中指定的证书请求平台即服务（PaaS）名称。

## <a name="prerequisites"></a>必备组件

在为 Azure Stack 中心部署生成 PKI 证书的任何 Csr 之前，系统应满足以下先决条件：

- Microsoft Azure Stack 集线器就绪检查程序
- 证书属性：
  - 区域名称
  - 外部完全限定的域名（FQDN）
  - 主题
- Windows 10 或 Windows Server 2016 或更高版本

  > [!NOTE]  
  > 从证书颁发机构收到证书后，[准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤将需要在同一系统上完成！

## <a name="generate-certificate-signing-requests"></a>生成证书签名请求

使用以下步骤来准备和验证 Azure Stack 中心 PKI 证书：

1. 通过运行以下 cmdlet，从 PowerShell 提示符（5.1 或更高版本）安装 AzsReadinessChecker：

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. 声明**使用者**。 例如：

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > 如果提供一个公用名（CN），则将在每个证书请求上进行配置。 如果省略了 CN，将在证书请求上配置 Azure Stack 中心服务的第一个 DNS 名称。

3. 声明已存在的输出目录。 例如：

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. 声明标识系统。

    Azure Active Directory （Azure AD）：

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Active Directory 联合身份验证服务（AD FS）：

    ```powershell
    $IdentitySystem = "ADFS"
    ```
    > [!NOTE]  
    > 此参数仅对 Certificatetype token-signing 部署是必需的。

5. 声明用于 Azure Stack 中心部署的**区域名称**和**外部 FQDN** 。

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > `<regionName>.<externalFQDN>` 构成了 Azure Stack 中心中所有外部 DNS 名称的创建基础。 在此示例中，将 `portal.east.azurestack.contoso.com`门户。  

6. 生成用于部署的证书签名请求：

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    若要为其他 Azure Stack 中心服务生成证书请求，请更改 `-CertificateType`的值。 例如：

    ```powershell  
    # App Services
    New-AzsCertificateSigningRequest -certificateType AppServices -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsCertificateSigningRequest -certificateType DBAdapter -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsCertificateSigningRequest -certificateType EventHubs -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsCertificateSigningRequest -certificateType IoTHub -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. 另外，对于开发/测试环境，若要生成具有多个使用者可选名称的单个证书请求，请添加 **-RequestType SingleCSR**参数和值（对于生产环境**不**建议使用）：

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  查看输出：

    ```powershell  
    New-AzsCertificateSigningRequest v1.1912.1082.37 started.
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\checker\Documents\AzureStackCSR\wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20191219140359.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9.  提交 **。** 生成到 CA （内部或公共）的请求文件。 **AzsCertificateSigningRequest**的输出目录包含提交给证书颁发机构所需的 CSR。 该目录还包含一个子目录，其中包含一个子目录，其中包含在证书请求生成过程中使用的 INF 文件。 请确保您的 CA 使用您生成的请求来生成证书，该请求符合[Azure Stack 中心 PKI 要求](azure-stack-pki-certs.md)。

## <a name="next-steps"></a>后续步骤

[准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md)
