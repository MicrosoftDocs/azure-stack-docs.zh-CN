---
title: 解决 PKI 证书的常见问题
titleSuffix: Azure Stack Hub
description: 使用 Azure Stack Hub 就绪性检查器解决 Azure Stack Hub PKI 证书的常见问题。
author: BryanLa
ms.topic: how-to
ms.date: 11/10/2020
ms.author: bryanla
ms.reviewer: unknown
ms.lastreviewed: 11/19/2019
ms.openlocfilehash: 070430d438334417f7c6acbd6e8f70798ba3c576
ms.sourcegitcommit: 7b189e5317b8fe5f8ad825565da3607a39a1b899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94493624"
---
# <a name="fix-common-issues-with-azure-stack-hub-pki-certificates"></a>解决 Azure Stack Hub PKI 证书的常见问题

可以通过本文中的信息来了解并解决 Azure Stack Hub PKI 证书的常见问题。 使用 Azure Stack Hub 就绪性检查器工具[验证 Azure Stack Hub PKI 证书](azure-stack-validate-pki-certs.md)时，可以发现问题。 该工具先检查证书是否满足 Azure Stack Hub 部署和 Azure Stack Hub 机密轮换的 PKI 要求，然后将结果记录到 [report.json 文件](azure-stack-validation-report.md)。  

## <a name="pfx-encryption"></a>PFX 加密

**问题** - PFX 加密不是 TripleDES-SHA1。

**修复** - 使用 **TripleDES-SHA1** 加密导出 PFX 文件。 从证书管理单元导出时或使用 `Export-PFXCertificate` 时，这是针对所有 Windows 10 客户端的默认加密。

## <a name="read-pfx"></a>读取 PFX

**警告** - 密码仅保护证书中的私密信息。  

**修复** - 使用与“启用证书隐私”相对应的可选设置来导出 PFX 文件。   

**问题** - PFX 文件无效。  

**修复** - 使用 [准备用于部署的 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤重新导出证书。

## <a name="signature-algorithm"></a>签名算法

**问题** - 签名算法为 SHA1。

**修复** - 执行“生成 Azure Stack Hub 证书签名请求”中的步骤，使用签名算法 SHA256 重新生成证书签名请求 (CSR)。 然后向证书颁发机构重新提交 CSR，要求其重新颁发证书。

## <a name="private-key"></a>私钥

**问题** - 私钥缺失或者不包含本地计算机属性。  

**修复** - 在生成 CSR 的计算机中，使用 [准备用于部署的 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker)中的步骤重新导出证书。 这些步骤包括从本地计算机证书存储进行导出。

## <a name="certificate-chain"></a>证书链

**问题** - 证书链不完整。  

**修复** - 证书应包含完整的证书链。 按照[准备用于部署的 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker)中的步骤重新导出证书，选择“包括证书路径中的所有证书(如果可能)”选项。 

## <a name="dns-names"></a>DNS 名称

**问题** - 证书上的 **DNSNameList** 不包含 Azure Stack Hub 服务终结点名称或有效的通配符匹配项。 通配符匹配项仅适用于 DNS 名称最左侧的命名空间。 例如，`*.region.domain.com` 仅对 `portal.region.domain.com` 有效，而对 `*.table.region.domain.com` 无效。

**修复** - 执行“生成 Azure Stack Hub 证书签名请求”中的步骤，以便使用为 Azure Stack Hub 终结点提供支持所需的正确 DNS 名称重新生成 CSR。 将 CSR 重新提交到证书颁发机构。 然后按照[准备用于部署的 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker)中的步骤，从生成 CSR 的计算机导出证书。  

## <a name="key-usage"></a>密钥使用情况

**问题** - 密钥用法缺少数字签名或密钥加密，或者增强型密钥用法缺少服务器身份验证或客户端身份验证。  

**修复** - 按照 [生成 Azure Stack Hub 证书签名请求](azure-stack-get-pki-certs.md)中的步骤，使用正确的密钥用法属性重新生成 CSR。 将 CSR 重新提交给证书颁发机构，并确认证书模板未覆盖请求中的密钥用法。

## <a name="key-size"></a>密钥大小

**问题** - 密钥大小不到 2048。

**修复** - 按照 [生成 Azure Stack Hub 证书签名请求](azure-stack-get-pki-certs.md)中的步骤，使用正确的密钥长度 (2048) 重新生成 CSR，然后将 CSR 重新提交给证书颁发机构。

## <a name="chain-order"></a>链序

**问题** - 证书链的顺序不正确。  

**修复** - 按照 [准备用于部署的 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker)中的步骤重新导出证书，选择“包括证书路径中的所有证书(如果可能)”选项。  确保仅选择分支证书进行导出。

## <a name="other-certificates"></a>其他证书

**问题** - PFX 包包含的证书不是分支证书，或者不是证书链的一部分。  

**修复** - 按照 [准备用于部署的 Azure Stack Hub PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker)中的步骤重新导出证书，选择“包括证书路径中的所有证书(如果可能)”选项。  确保仅选择分支证书进行导出。

## <a name="fix-common-packaging-issues"></a>修复常见的打包问题

**AzsReadinessChecker** 工具包含名为 **Repair-AzsPfxCertificate** 的帮助程序 cmdlet，它可以通过导入和导出 PFX 文件来修复常见的打包问题，这些问题包括：

- **PFX 加密** 不是 TripleDES-SHA1。
- **私钥** 缺少本地计算机属性。
-  证书链不完整或错误。 如果 PFX 包不包含证书链，则本地计算机必须包含。
- **其他证书**

如果需要生成新的 CSR 并重新颁发证书，则 **Repair-AzsPfxCertificate** 无用。

### <a name="prerequisites"></a>必备条件

在运行此工具的计算机上，必须满足以下先决条件：

- Windows 10 或 Windows Server 2016，具有 Internet 连接。
- PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmdlet，然后查看主要  版本和次要  版本：

   ```powershell
   $PSVersionTable.PSVersion
   ```

- 配置[适用于 Azure Stack Hub 的 PowerShell](azure-stack-powershell-install.md)。
- 下载最新版本的 [Azure Stack Hub 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。

### <a name="import-and-export-an-existing-pfx-file"></a>导入和导出现有的 PFX 文件

1. 在满足先决条件的计算机上，打开一个提升的 PowerShell 提示符，然后运行以下命令来安装 Azure Stack Hub 就绪性检查器：

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. 在 PowerShell 提示符下，运行以下 cmdlet 来设置 PFX 密码。 在出现提示时输入密码：

   ```powershell
   $password = Read-Host -Prompt "Enter password" -AsSecureString
   ```

3. 在 PowerShell 提示符下，运行以下命令来导出新的 PFX 文件：

   - 对于 `-PfxPath`，请指定要处理的 PFX 文件的路径。 在以下示例中，路径为 `.\certificates\ssl.pfx`。
   - 对于 `-ExportPFXPath`，请指定要导出的 PFX 文件的位置和名称。 在以下示例中，路径为 `.\certificates\ssl_new.pfx`：

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. 在工具完成相关操作后，查看成功后的输出：

   ```shell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Hub Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Stack Hub 安全性](azure-stack-rotate-secrets.md)
