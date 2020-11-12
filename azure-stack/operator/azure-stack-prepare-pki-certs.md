---
title: 准备用于部署或轮换的 Azure Stack Hub PKI 证书
titleSuffix: Azure Stack Hub
description: 了解如何为 Azure Stack Hub 部署或为轮换机密准备 PKI 证书。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 10/19/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: 86e3a87bf869d6bd9980746742a7ba03d142d5fe
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545017"
---
# <a name="prepare-azure-stack-hub-pki-certificates-for-deployment-or-rotation"></a>准备用于部署或轮换的 Azure Stack Hub PKI 证书

必须使用符合 Azure Stack Hub 的证书要求的属性来导入和导出[从证书颁发机构 (CA) 获取](azure-stack-get-pki-certs.md)的证书文件。

在本文中，你将了解如何导入、打包和验证证书，以便为 Azure Stack Hub 部署或机密轮换做准备。 

## <a name="prerequisites"></a>必备条件

在打包用于 Azure Stack Hub 部署的 PKI 证书之前，系统应符合以下先决条件：

- 从证书颁发机构返回的证书以 .cer 格式（其他可配置的格式，如 .cert、.sst 或 .pfx）存储在一个目录中。
- Windows 10，或者是 Windows Server 2016 或更高版本
- 使用生成证书签名请求的同一系统（除非要针对的是预打包到 PFX 中的证书）。

继续进行到相应的[准备证书（Azure Stack 就绪性检查器）](#prepare-certificates-azure-stack-readiness-checker)或[准备证书（手动步骤）](#prepare-certificates-manual-steps)部分。

## <a name="prepare-certificates-azure-stack-readiness-checker"></a>准备证书（Azure Stack 就绪性检查器）

使用 Azure Stack 就绪性检查器 PowerShell cmdlet 将证书打包，步骤如下：

1. 在 PowerShell 提示符（5.1 或更高版本）下，通过运行以下 cmdlet 来安装 Azure Stack 就绪性检查器模块：

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```
2. 指定证书文件的路径。 例如：

    ```powershell  
        $Path = "$env:USERPROFILE\Documents\AzureStack"
    ```

3. 声明 pfxPassword。 例如：

    ```powershell  
        $pfxPassword = Read-Host -AsSecureString -Prompt "PFX Password"
    ```
4. 声明要将生成的 PFX 导出到的 ExportPath。 例如：

    ```powershell  
        $ExportPath = "$env:USERPROFILE\Documents\AzureStack"
    ```

5. 将证书转换为 Azure Stack Hub 证书。 例如：

    ```powershell  
        ConvertTo-AzsPFX -Path $Path -pfxPassword $pfxPassword -ExportPath $ExportPath
    ```
8.  查看输出：

    ```powershell  
    ConvertTo-AzsPFX v1.2005.1286.272 started.

    Stage 1: Scanning Certificates
        Path: C:\Users\[*redacted*]\Documents\AzureStack Filter: CER Certificate count: 11
        adminmanagement_east_azurestack_contoso_com_CertRequest_20200710235648.cer
        adminportal_east_azurestack_contoso_com_CertRequest_20200710235645.cer
        management_east_azurestack_contoso_com_CertRequest_20200710235644.cer
        portal_east_azurestack_contoso_com_CertRequest_20200710235646.cer
        wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20200710235649.cer
        wildcard_adminvault_east_azurestack_contoso_com_CertRequest_20200710235642.cer
        wildcard_blob_east_azurestack_contoso_com_CertRequest_20200710235653.cer
        wildcard_hosting_east_azurestack_contoso_com_CertRequest_20200710235652.cer
        wildcard_queue_east_azurestack_contoso_com_CertRequest_20200710235654.cer
        wildcard_table_east_azurestack_contoso_com_CertRequest_20200710235650.cer
        wildcard_vault_east_azurestack_contoso_com_CertRequest_20200710235647.cer

    Detected ExternalFQDN: east.azurestack.contoso.com

    Stage 2: Exporting Certificates
        east.azurestack.contoso.com\Deployment\ARM Admin\ARMAdmin.pfx
        east.azurestack.contoso.com\Deployment\Admin Portal\AdminPortal.pfx
        east.azurestack.contoso.com\Deployment\ARM Public\ARMPublic.pfx
        east.azurestack.contoso.com\Deployment\Public Portal\PublicPortal.pfx
        east.azurestack.contoso.com\Deployment\Admin Extension Host\AdminExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\KeyVaultInternal\KeyVaultInternal.pfx
        east.azurestack.contoso.com\Deployment\ACSBlob\ACSBlob.pfx
        east.azurestack.contoso.com\Deployment\Public Extension Host\PublicExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\ACSQueue\ACSQueue.pfx
        east.azurestack.contoso.com\Deployment\ACSTable\ACSTable.pfx
        east.azurestack.contoso.com\Deployment\KeyVault\KeyVault.pfx

    Stage 3: Validating Certificates.

    Validating east.azurestack.contoso.com-Deployment-AAD certificates in C:\Users\[*redacted*]\Documents\AzureStack\east.azurestack.contoso.com\Deployment 

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
        ...
    Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    ConvertTo-AzsPFX Completed
    ```
    > [!NOTE]
    > 有关其他用法，请使用 Get-help ConvertTo-AzsPFX -Full 以进一步了解用法，如针对不同的证书格式禁用验证或筛选。

    成功验证之后，可以提供用于部署或轮换的证书，而不需要任何其他步骤。

## <a name="prepare-certificates-manual-steps"></a>准备证书（手动步骤）

对于新的 Azure Stack Hub PKI 证书，请使用以下手动步骤来打包证书。

### <a name="import-the-certificate"></a>导入证书

1. 将[从所选 CA 获取](azure-stack-get-pki-certs.md)的原始证书版本复制到部署主机上的目录。 
   > [!WARNING]
   > 如果已以任何方式导入、导出或更改直接由 CA 提供的文件，请勿复制该文件。

1. 右键单击证书并选择“安装证书”或“安装PFX”，具体取决于从 CA 传送证书的方式。

1. 在 **证书导入向导** 中，选择“本地计算机”作为导入位置。 选择“下一步”  。 在下一个屏幕上，再次选择“下一步”。

    ![证书的本地计算机导入位置](./media/prepare-pki-certs/1.png)

1. 选择“将所有证书放在以下存储中”，然后选择“企业信任”作为位置。 选择“确定”以关闭“证书存储选择”对话框，然后选择“下一步”。

   ![配置用于证书导入的证书存储](./media/prepare-pki-certs/3.png)

   a. 如果要导入 PFX，则会看到其他对话框。 在“私钥保护”页上，输入证书文件的密码，然后启用“将此密钥标记为可导出。”  选项，这样就可在以后备份或传输密钥。 选择“下一步”  。

   ![将密钥标记为可导出](./media/prepare-pki-certs/2.png)

1. 选择“完成”以完成导入。

> [!NOTE]
> 导入 Azure Stack Hub 的证书后，证书的私钥将作为 PKCS 12 文件 (PFX) 存储在群集存储上。

### <a name="export-the-certificate"></a>导出证书

打开证书管理员 MMC 控制台并连接到本地计算机证书存储。

1. 打开 Microsoft 管理控制台。 若要在 Windows 10 中打开控制台，请右键单击“开始”菜单，选择“运行”，然后键入“mmc”并按 Enter 键  。

2. 选择“文件” > “添加/删除管理单元”，然后选择“证书”并选择“添加”。

    ![在 Microsoft 管理控制台中添加证书管理单元](./media/prepare-pki-certs/mmc-2.png)

3. 选择“计算机帐户”，然后选择“下一步”。 选择“本地计算机”，然后选择“完成”。 选择“确定”以关闭“添加/删除管理单元”页。

    ![选择与“在 Microsoft 管理控制台中添加证书管理单元”相对应的帐户](./media/prepare-pki-certs/mmc-3.png)

4. 浏览到“证书” > “企业信任” > “证书位置”。 确认在右侧看到你的证书。

5. 从证书管理员控制台任务栏中，选择“操作” > “所有任务” > “导出”。 选择“下一步”  。

   > [!NOTE]
   > 根据你拥有 Azure Stack Hub 证书的数量，可能需要多次完成此过程。

6. 选择“是，导出私钥”，然后选择“下一步” 。

7. 在“导出文件格式”部分执行以下操作：
    
   - 选择“包括证书路径中的所有证书(如果可能)”。  
   - 选择“导出所有扩展属性”。  
   - 选择“启用证书隐私”。  
   - 选择“ **下一页** ”。  
    
     ![包含选定选项的证书导出向导](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. 选择“密码”并为证书提供密码。 创建满足以下密码复杂性要求的密码：

    * 最小长度为 8 个字符。
    * 至少包含以下字符中的三种字符：大写字母、小写字母、0-9 中的数字、特殊字符、不是大写也不是小写的字母字符。

    记下此密码。 需将它用作部署参数。

9. 选择“下一步”  。

10. 选择要导出的 PFX 文件的文件名和位置。 选择“ **下一步** ”。

11. 选择“完成”。

## <a name="next-steps"></a>后续步骤

[验证 PKI 证书](azure-stack-validate-pki-certs.md)
