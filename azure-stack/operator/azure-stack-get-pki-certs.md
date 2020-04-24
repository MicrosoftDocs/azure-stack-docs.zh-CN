---
title: 为 Azure Stack Hub 生成证书签名请求
description: 了解如何在 Azure Stack Hub 集成系统中为 Azure Stack Hub PKI 证书生成证书签名请求。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/10/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: d197a8b4464af8f331a11af2ba642ad053273bf9
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77699705"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>为 Azure Stack Hub 生成证书签名请求

可以使用 Azure Stack Hub 就绪性检查器工具创建适合于 Azure Stack Hub 部署的证书签名请求 (CSR)。 应该花费足够的时间来请求、生成并验证证书，以便在部署之前进行测试。 可以[从 PowerShell 库](https://aka.ms/AzsReadinessChecker)获取工具。

可以使用 Azure Stack Hub 就绪性检查器工具 (AzsReadinessChecker) 请求以下证书：

- **标准证书请求** 根据[生成证书签名请求](azure-stack-get-pki-certs.md#generate-certificate-signing-requests)。
- **平台即服务**：可以请求 [Azure Stack Hub 公钥基础结构证书要求 - 可选的 PaaS 证书](azure-stack-pki-certs.md#optional-paas-certificates)中指定的证书的平台即服务 (PaaS) 名称。

## <a name="prerequisites"></a>先决条件

在为 Azure Stack Hub 部署生成 PKI 证书的任何 CSR 之前，系统应符合以下先决条件：

- Microsoft Azure Stack 集线器就绪检查程序
- 证书属性：
  - 区域名称
  - 外部完全限定的域名 (FQDN)
  - 使用者
- Windows 10 或 Windows Server 2016 或更高版本

  > [!NOTE]  
  > 从证书颁发机构收回证书时，需要在同一个系统上完成[准备 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤！

## <a name="generate-certificate-signing-requests"></a>生成证书签名请求

使用以下步骤来准备和验证 Azure Stack Hub PKI 证书：

1. 在 PowerShell 提示符（5.1 或更高版本）下，运行以下 cmdlet 安装 AzsReadinessChecker：

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. 声明**使用者**。 例如：

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > 如果提供公用名 (CN)，则会在每个证书请求上配置它。 如果省略了 CN，则会在证书请求上配置 Azure Stack Hub 服务的第一个 DNS 名称。

3. 声明已存在的输出目录。 例如：

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. 声明标识系统。

    Azure Active Directory (Azure AD)：

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Active Directory 联合身份验证服务 (AD FS)：

    ```powershell
    $IdentitySystem = "ADFS"
    ```
    > [!NOTE]  
    > 只有 CertificateType 部署需要此参数。

5. 声明用于 Azure Stack Hub 部署的**区域名称**和**外部 FQDN**。

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > `<regionName>.<externalFQDN>` 构成了 Azure Stack Hub 中所有外部 DNS 名称创建位置的基础。 在此示例中，门户将是 `portal.east.azurestack.contoso.com`。  

6. 若要为部署生成证书签名请求，请执行以下命令：

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    若要为其他 Azure Stack Hub 服务生成证书请求，请更改 `-CertificateType` 的值。 例如：

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

7. 另外，对于开发/测试环境，若要生成具有多个使用者可选名称的单个证书请求，请添加 **-RequestType SingleCSR** 参数和值（**不**建议用于生产环境）：

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

9.  将生成的 **.REQ** 文件提交到 CA（内部或公共 CA）。 **New-AzsCertificateSigningRequest** 的输出目录包含提交到证书颁发机构时所需的 CSR。 此目录还包含一个子目录，其中包含生成证书请求期间使用的 INF 文件，供你参考。 请确保 CA 使用符合 [Azure Stack Hub PKI 要求](azure-stack-pki-certs.md)的生成请求来生成证书。

## <a name="next-steps"></a>后续步骤

[准备 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md)
