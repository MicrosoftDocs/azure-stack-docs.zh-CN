---
title: 解决 PKI 证书的常见问题
titleSuffix: Azure Stack Hub
description: 使用 Azure Stack Hub 就绪性检查器解决 Azure Stack Hub PKI 证书的常见问题。
author: BryanLa
ms.topic: how-to
ms.date: 11/10/2020
ms.author: bryanla
ms.reviewer: unknown
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: 824463ccf48d6855fd2851e9c6f9116d61b8b818
ms.sourcegitcommit: b50dd116d6d1f89d42bd35ad0f85bb25c5192921
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96152805"
---
# <a name="fix-common-issues-with-azure-stack-hub-pki-certificates"></a>解决 Azure Stack Hub PKI 证书的常见问题

可以通过本文中的信息来了解并解决 Azure Stack Hub PKI 证书的常见问题。 使用 Azure Stack Hub 就绪性检查器工具[验证 Azure Stack Hub PKI 证书](azure-stack-validate-pki-certs.md)时，可以发现问题。 该工具先检查证书是否满足 Azure Stack Hub 部署和 Azure Stack Hub 机密轮换的 PKI 要求，然后将结果记录到 [report.json 文件](azure-stack-validation-report.md)。  

## <a name="http-crl---warning"></a>HTTP CRL-警告

**问题** -证书不包含 CDP 扩展中的 HTTP CRL。

**修复** -这是一个非阻止性问题。 Azure Stack 要求 HTTP CRL 根据 [Azure Stack 中心公钥基础结构 (PKI) 证书要求](https://aka.ms/azspki)进行吊销检查。  未在证书上检测到 HTTP CRL。  若要确保证书吊销检查有效，证书颁发机构应颁发包含 CDP 扩展中的 HTTP CRL 的证书。

## <a name="http-crl---fail"></a>HTTP CRL-失败

**问题** -无法连接到 CDP 扩展中的 HTTP CRL。

**修复** -这是一个阻碍性问题。 Azure Stack 需要连接到 HTTP CRL，以便在 [Azure Stack 集线器端口和 url (出站) ](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound)时进行吊销检查。

## <a name="pfx-encryption"></a>PFX 加密

**问题** - PFX 加密不是 TripleDES-SHA1。

**修复** - 使用 **TripleDES-SHA1** 加密导出 PFX 文件。 从证书管理单元导出时或使用 `Export-PFXCertificate` 时，这是针对所有 Windows 10 客户端的默认加密。

## <a name="read-pfx"></a>读取 PFX

**警告** - 密码仅保护证书中的私密信息。  

**修复** - 使用与“启用证书隐私”相对应的可选设置来导出 PFX 文件。  

**问题** - PFX 文件无效。  

**修复** -按照 [为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤，重新导出证书。

## <a name="signature-algorithm"></a>签名算法

**问题** - 签名算法为 SHA1。

**修复** -使用 Azure Stack 集线器证书签名请求生成中的步骤，重新生成证书签名请求 (CSR) 与 SHA256 的签名算法。 然后向证书颁发机构重新提交 CSR，要求其重新颁发证书。

## <a name="private-key"></a>私钥

**问题** - 私钥缺失或者不包含本地计算机属性。  

**修复** -从生成 CSR 的计算机上，使用 [为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker)中的步骤重新导出证书。 这些步骤包括从本地计算机证书存储进行导出。

## <a name="certificate-chain"></a>证书链

**问题** - 证书链不完整。  

**修复** - 证书应包含完整的证书链。 按照 [为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker) 中的步骤操作，然后选择选项 " **包括证书路径中的所有证书（如果可能**）"。

## <a name="dns-names"></a>DNS 名称

**问题** -证书上的 **DNSNameList** 不包含 Azure Stack 中心服务终结点名称或有效的通配符匹配项。 通配符匹配项仅适用于 DNS 名称最左侧的命名空间。 例如，`*.region.domain.com` 仅对 `portal.region.domain.com` 有效，而对 `*.table.region.domain.com` 无效。

**修复** -使用 Azure Stack 中心证书签名请求生成中的步骤重新生成具有正确 DNS 名称的 CSR，以支持 Azure Stack 中心终结点。 将 CSR 重新提交到证书颁发机构。 然后，按照 [为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker) 中的步骤从生成 CSR 的计算机中导出证书。  

## <a name="key-usage"></a>密钥使用情况

**问题** - 密钥用法缺少数字签名或密钥加密，或者增强型密钥用法缺少服务器身份验证或客户端身份验证。  

**修复** -使用 [Azure Stack 集线器证书签名请求生成](azure-stack-get-pki-certs.md) 中的步骤，重新生成具有正确密钥用法属性的 CSR。 将 CSR 重新提交给证书颁发机构，并确认证书模板未覆盖请求中的密钥用法。

## <a name="key-size"></a>密钥大小

**问题** - 密钥大小不到 2048。

**修复** -使用 [Azure Stack 集线器证书签名请求生成](azure-stack-get-pki-certs.md) 中的步骤，重新生成具有正确密钥长度的 csr (2048) ，然后将 csr 重新提交到证书颁发机构。

## <a name="chain-order"></a>链序

**问题** - 证书链的顺序不正确。  

**修复** -按照 [为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker) 中的步骤操作，然后选择选项 " **包括证书路径中的所有证书（如果可能**）"。 确保仅选择分支证书进行导出。

## <a name="other-certificates"></a>其他证书

**问题** - PFX 包包含的证书不是分支证书，或者不是证书链的一部分。  

**修复** -按照 [为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker)中的步骤进行操作，然后选择选项 " **包括证书路径中的所有证书（如果可能**）"。 确保仅选择分支证书进行导出。

## <a name="fix-common-packaging-issues"></a>修复常见的打包问题

**AzsReadinessChecker** 工具包含名为 **Repair-AzsPfxCertificate** 的帮助程序 cmdlet，它可以通过导入和导出 PFX 文件来修复常见的打包问题，这些问题包括：

- **PFX 加密** 不是 TripleDES-SHA1。
- **私钥** 缺少本地计算机属性。
- 证书链不完整或错误。 如果 PFX 包不包含证书链，则本地计算机必须包含。
- **其他证书**

如果需要生成新的 CSR 并重新颁发证书，则 **Repair-AzsPfxCertificate** 无用。

### <a name="prerequisites"></a>先决条件

在运行此工具的计算机上，必须满足以下先决条件：

- Windows 10 或 Windows Server 2016，具有 Internet 连接。
- PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmdlet，然后查看主要版本和次要版本：

   ```powershell
   $PSVersionTable.PSVersion
   ```

- [为 Azure Stack 中心配置 PowerShell](powershell-install-az-module.md)。
- 下载最新版本的 [Azure Stack 集线器就绪检查器](https://aka.ms/AzsReadinessChecker) 工具。

### <a name="import-and-export-an-existing-pfx-file"></a>导入和导出现有的 PFX 文件

1. 在满足先决条件的计算机上，打开提升的 PowerShell 提示符，然后运行以下命令以安装 Azure Stack 集线器就绪检查程序：

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
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
