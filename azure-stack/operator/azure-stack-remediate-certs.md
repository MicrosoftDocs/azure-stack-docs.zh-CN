---
title: 修复 Azure Stack 的证书问题 | Microsoft Docs
description: 使用 Azure Stack 就绪性检查器查看和修复证书问题。
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
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: ac6e04fbb884b3689cec7e5f435f9265f7e2108e
ms.sourcegitcommit: 593d40bccf1b2957a763017a8a2d7043f8d8315c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152408"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>修复 Azure Stack PKI 证书的常见问题

可以通过本文中的信息来了解并解决 Azure Stack PKI 证书的常见问题。 你可以使用到的 Azure Stack 就绪状态检查器工具时发现问题[验证 Azure Stack PKI 证书](azure-stack-validate-pki-certs.md)。 此工具会检查以确保证书满足 Azure Stack 部署和 Azure Stack 机密轮换的 PKI 要求并将结果记录[report.json 文件](azure-stack-validation-report.md)。  

## <a name="pfx-encryption"></a>PFX 加密

**失败** - PFX 加密不是 TripleDES-SHA1。

**补救** - 使用 **TripleDES-SHA1** 加密导出 PFX 文件。 从证书管理单元导出或使用 `Export-PFXCertificate` 时，这是所有 Windows 10 客户端的默认设置。

## <a name="read-pfx"></a>读取 PFX

**警告** - 密码仅保护证书中的私密信息。  

**补救措施** - 使用可选设置“启用证书隐私”来导出 PFX 文件。   

**失败** - PFX 文件无效。  

**修复** - 使用[准备用于部署的 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤重新导出证书。

## <a name="signature-algorithm"></a>签名算法

**失败** - 签名算法为 SHA1。

**修正**-SHA256 签名算法中使用 Azure Stack 证书签名请求生成以重新生成证书签名请求 (CSR) 中的步骤。 然后重新提交到证书颁发机构以重新颁发证书的 CSR。

## <a name="private-key"></a>私钥

**失败**-私有密钥缺失或不包含本地计算机属性。  

**修复** - 在生成 CSR 的计算机中，使用[准备用于部署的 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)中的步骤重新导出证书。 这些步骤包括从本地计算机证书存储进行导出。

## <a name="certificate-chain"></a>证书链

**失败** - 证书链不完整。  

**修复** - 证书应包含完整的证书链。 重新导出的证书中的步骤[为部署准备 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)，并选择选项**尽可能包括证书路径中的所有证书**。

## <a name="dns-names"></a>DNS 名称

**失败**- **DNSNameList**对证书不包含 Azure Stack 服务终结点名称或有效的通配符匹配。 通配符匹配项仅适用于 DNS 名称最左侧的命名空间。 例如，`*.region.domain.com`时才有效`portal.region.domain.com`，而不`*.table.region.domain.com`。

**修复** - 执行“生成 Azure Stack 证书签名请求”中的步骤，以便使用为 Azure Stack 终结点提供支持所需的正确 DNS 名称重新生成 CSR。 重新提交到证书颁发机构，CSR，然后按照中的步骤[为部署准备 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)生成 CSR 的计算机导出的证书。  

## <a name="key-usage"></a>密钥使用情况

**失败** - 密钥用法缺少数字签名或密钥加密，或者增强型密钥用法缺少服务器身份验证或客户端身份验证。  

**修正**-使用中的步骤[Azure Stack 证书签名请求生成](azure-stack-get-pki-certs.md)重新生成具有正确的密钥用法属性 CSR。 重新提交到证书颁发机构的 CSR 并确认证书模板不覆盖在请求中的密钥用法。

## <a name="key-size"></a>密钥大小

**失败** - 密钥大小小于 2048。

**修正**-使用中的步骤[Azure Stack 证书签名请求生成](azure-stack-get-pki-certs.md)来重新生成 CSR 正确的密钥长度 (2048)，然后重新提交到证书颁发机构的 CSR。

## <a name="chain-order"></a>链序

**失败** - 证书链的顺序不正确。  

**修正**-重新导出的证书中的步骤[为部署准备 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)，并选择选项**尽可能包括证书路径中的所有证书**. 确保仅选择分支证书进行导出。

## <a name="other-certificates"></a>其他证书

**失败** - PFX 包包含的证书不是分支证书，也不是证书链的一部分。  

**修正**-重新导出的证书中的步骤[为部署准备 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)，并选择选项**尽可能包括证书路径中的所有证书**. 确保仅选择分支证书进行导出。

## <a name="fix-common-packaging-issues"></a>修复常见的打包问题

**AzsReadinessChecker**工具包含名为的帮助程序 cmdlet `Repair-AzsPfxCertificate`，可导入，然后导出 PFX 文件来解决常见打包问题，包括：

- **PFX 加密**是否不是 TripleDES SHA1。
- **私钥**缺少本地计算机的属性。
-  证书链不完整或错误。 如果 PFX 包不包含证书链，则本地计算机必须包含。
- **其他证书**

如果需要生成新的 CSR 并重新颁发证书，则 `Repair-AzsPfxCertificate` 无用。

### <a name="prerequisites"></a>必备组件

在运行此工具的计算机上，必须满足以下先决条件：

- Windows 10 或 Windows Server 2016，具有 Internet 连接。
- PowerShell 5.1 或更高版本。 若要检查你的版本，运行以下 PowerShell cmdlet，然后查看*主要** 和**次要**版本：

   ```powershell
   $PSVersionTable.PSVersion
   ```

- 配置[适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
- 下载最新版[Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。

### <a name="import-and-export-an-existing-pfx-file"></a>导入和导出现有的 PFX 文件

1. 满足的先决条件的计算机上, 打开提升的 PowerShell 提示符，并运行以下命令以安装 Azure Stack 就绪性检查器：

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. 在 PowerShell 提示符下，运行以下 cmdlet 来设置 PFX 密码。 请将 `PFXpassword` 替换为实际密码：

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
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
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Stack 安全性](azure-stack-rotate-secrets.md)
