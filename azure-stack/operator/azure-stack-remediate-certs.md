---
title: 解决 PKI 证书的常见问题
titleSuffix: Azure Stack Hub
description: 使用 Azure Stack 中心准备情况检查程序解决 Azure Stack 中心 PKI 证书的常见问题。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: bc0ad732005db5571d81d71536edf2384cd80537
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75808373"
---
# <a name="fix-common-issues-with-azure-stack-hub-pki-certificates"></a>解决 Azure Stack 中心 PKI 证书的常见问题

本文中的信息可帮助您了解和解决 Azure Stack 中心 PKI 证书的常见问题。 使用 Azure Stack 集线器准备情况检查程序工具来[验证 Azure Stack 中心 PKI 证书](azure-stack-validate-pki-certs.md)时，可以发现问题。 此工具将检查证书是否满足 Azure Stack 中心部署的 PKI 要求并 Azure Stack 中心机密旋转，然后将结果记录到一个[报表文件](azure-stack-validation-report.md)中。  

## <a name="pfx-encryption"></a>PFX 加密

**问题**-PFX 加密不 TripleDES。

**修复**-导出具有**TRIPLEDES**加密的 PFX 文件。 这是从证书管理单元导出或使用 `Export-PFXCertificate`时，所有 Windows 10 客户端的默认加密。

## <a name="read-pfx"></a>读取 PFX

**警告**-密码仅保护证书中的私有信息。  

**Fix** -导出 PFX 文件，其中包含用于**启用证书隐私**的可选设置。  

**问题**-PFX 文件无效。  

**修复**-按照[为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤，重新导出证书。

## <a name="signature-algorithm"></a>签名算法

**问题**签名算法为 SHA1。

**修复**-使用 Azure Stack 集线器证书签名请求生成中的步骤，重新生成签名算法为 SHA256 的证书签名请求（CSR）。 然后，将 CSR 提交到证书颁发机构以重新颁发证书。

## <a name="private-key"></a>私钥

**问题**-私钥丢失或不包含本地计算机属性。  

**修复**-从生成 CSR 的计算机上，使用[为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)中的步骤重新导出证书。 这些步骤包括从本地计算机证书存储中导出。

## <a name="certificate-chain"></a>证书链

**问题**-证书链不完整。  

**Fix** -证书应包含完整的证书链。 按照[为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)中的步骤操作，然后选择选项 "**包括证书路径中的所有证书（如果可能**）"。

## <a name="dns-names"></a>DNS 名称

**问题**-证书上的**DNSNameList**不包含 Azure Stack 中心服务终结点名称或有效的通配符匹配项。 通配符匹配仅对 DNS 名称的最左侧命名空间有效。 例如，`*.region.domain.com` 仅对 `portal.region.domain.com`有效，而不是 `*.table.region.domain.com`。

**修复**-使用 Azure Stack 中心证书签名请求生成中的步骤重新生成具有正确 DNS 名称的 CSR，以支持 Azure Stack 中心终结点。 将 CSR 提交到证书颁发机构。 然后，按照[为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)中的步骤从生成 CSR 的计算机中导出证书。  

## <a name="key-usage"></a>密钥使用情况

**问题**-密钥用法缺少数字签名或密钥加密，或增强型密钥用法缺少服务器身份验证或客户端身份验证。  

**修复**-使用[Azure Stack 集线器证书签名请求生成](azure-stack-get-pki-certs.md)中的步骤，重新生成具有正确密钥用法属性的 CSR。 将 CSR 提交到证书颁发机构，并确认证书模板未覆盖请求中的密钥用法。

## <a name="key-size"></a>密钥大小

**问题**-密钥大小小于2048。

**修复**-使用[Azure Stack 集线器证书签名请求生成](azure-stack-get-pki-certs.md)中的步骤，重新生成具有正确密钥长度（2048）的 CSR，然后将 csr 重新提交到证书颁发机构。

## <a name="chain-order"></a>链式顺序

**问题**-证书链的顺序不正确。  

**修复**-按照[为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)中的步骤操作，然后选择选项 "**包括证书路径中的所有证书（如果可能**）"。 确保仅选择 "叶证书" 进行导出。

## <a name="other-certificates"></a>其他证书

**问题**-PFX 包包含不是叶证书或证书链的一部分的证书。  

**修复**-按照[为部署准备 Azure Stack 中心 PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)中的步骤进行操作，然后选择选项 "**包括证书路径中的所有证书（如果可能**）"。 确保仅选择 "叶证书" 进行导出。

## <a name="fix-common-packaging-issues"></a>解决常见的打包问题

**AzsReadinessChecker**工具包含一个名为**AzsPfxCertificate**的帮助器 cmdlet，该 cmdlet 可以导入然后导出 PFX 文件以修复常见的打包问题，其中包括：

- **PFX 加密**不 TripleDES。
- **私钥**缺少本地计算机属性。
- **证书链**不完整或不正确。 如果 PFX 包不是，则本地计算机必须包含证书链。
- **其他证书**

如果需要生成新的 CSR 并重新颁发证书， **AzsPfxCertificate**不会有帮助。

### <a name="prerequisites"></a>必备组件

在运行该工具的计算机上，必须具备以下先决条件：

- Windows 10 或 Windows Server 2016，具有 internet 连接。
- PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmdlet，并查看**主要**和**次要**版本：

   ```powershell
   $PSVersionTable.PSVersion
   ```

- [为 Azure Stack 中心配置 PowerShell](azure-stack-powershell-install.md)。
- 下载最新版本的[Azure Stack 集线器就绪检查器](https://aka.ms/AzsReadinessChecker)工具。

### <a name="import-and-export-an-existing-pfx-file"></a>导入和导出现有的 PFX 文件

1. 在满足先决条件的计算机上，打开提升的 PowerShell 提示符，然后运行以下命令以安装 Azure Stack 集线器就绪检查程序：

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. 在 PowerShell 提示符下，运行以下 cmdlet 以设置 PFX 密码。 将 `PFXpassword` 替换为实际密码：

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. 在 PowerShell 提示符下，运行以下命令以导出新的 PFX 文件：

   - 对于 "`-PfxPath`"，请指定正在使用的 PFX 文件的路径。 在下面的示例中，路径是 `.\certificates\ssl.pfx`的。
   - 对于 "`-ExportPFXPath`"，指定要导出的 PFX 文件的位置和名称。 在下面的示例中，路径是 `.\certificates\ssl_new.pfx`的：

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. 工具完成后，检查输出是否成功：

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

- [详细了解 Azure Stack 中心安全性](azure-stack-rotate-secrets.md)
