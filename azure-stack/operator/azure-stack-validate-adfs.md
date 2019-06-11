---
title: 验证 Azure Stack 的 AD FS 集成
description: 使用 Azure Stack 就绪性检查器来验证 Azure Stack 的 AD FS 集成。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: dcc473d270d0a72b2ebf5f31c67fffa6827c9ecc
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828415"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>验证 Azure Stack 的 AD FS 集成

使用 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 来验证环境是否已准备好将 Azure Stack 与 Active Directory 联合身份验证服务 (AD FS) 相集成。 在开始数据中心集成或 Azure Stack 部署之前，请先验证 AD FS 集成。

就绪性检查器会验证下列项：

* 联合元数据包含用于联合身份验证的有效 XML 元素。 
* 可以检索 AD FS SSL 证书，并可以生成信任链。  堆栈上的 AD FS 必须信任 SSL 证书链。 证书必须由签署 Azure Stack 部署证书的同一证书颁发机构  签名，或者由受信任的根颁发机构合作伙伴签名。 有关受信任根颁发机构合作伙伴的完整列表，请参阅 [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)。
* AD FS 签名证书受信任且不会在近期过期。 

有关 Azure Stack 数据中心集成的详细信息，请参阅 [Azure Stack 数据中心集成 - 标识](azure-stack-integrate-identity.md)。

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

从 [PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 Azure Stack 就绪性检查器工具 (AzsReadinessChecker)。  

## <a name="prerequisites"></a>必备组件

必须满足以下先决条件。

**运行该工具的计算机：**

* 已建立域连接的 Windows 10 或 Windows Server 2016。
* PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell 命令，然后查看主要版本和次要版本：    
   > `$PSVersionTable.PSVersion`
* 最新版本的 [Microsoft Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。

**Active Directory 联合身份验证服务环境：**

至少需要下列其中一种形式的元数据：

* AD FS 联合元数据的 URL。 例如 `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`。
* 联合元数据 XML 文件。 例如 FederationMetadata.xml。

## <a name="validate-ad-fs-integration"></a>验证 AD FS 集成

1. 在满足先决条件的计算机上，打开一个管理 PowerShell 提示符，然后运行以下命令来安装 AzsReadinessChecker：

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. 在 PowerShell 提示符下，运行以下命令开始验证。 指定 **-CustomADFSFederationMetadataEndpointUri** 的值作为联合元数据的 URI。

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. 运行该工具后，查看输出。 确认状态是否为 OK（表示符合 AD FS 集成要求）。 验证成功时会显示类似于以下示例的输出：

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

在生产环境中，从操作员工作站测试证书信任链无法完全指示 Azure Stack 基础结构中的 PKI 信任状态。 Azure Stack 标记的公共 VIP 网络需要与 PKI 基础结构的 CRL 建立连接。

## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

验证文件可以帮助你在部署 Azure Stack 之前共享状态，或者调查验证问题。 这两个文件都会持久保留每个后续验证检查的结果。 报告将向部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。

这两个文件默认写入到 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`。

使用：

* **-OutputPath**：在 run 命令的末尾使用 *path* 参数可以指定不同的报告位置。
* **-CleanReport**：在 run 命令的末尾使用该参数可以清除先前报告信息的 AzsReadinessCheckerReport.json。 有关详细信息，请参阅 [Azure Stack 验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还会将信息记录到 *AzsReadinessChecker.log* 中。

下面的示例针对常见的验证失败提供了指导。

### <a name="command-not-found"></a>找不到命令

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**原因**：PowerShell Autoload 无法正常加载就绪性检查器模块。

**解决方法**：显式导入就绪性检查器模块。 复制以下代码并将其粘贴到 PowerShell 中，然后使用当前安装的版本号更新 \<version\>。

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>后续步骤

[查看就绪性报表](azure-stack-validation-report.md)  
[有关 Azure Stack 集成的一般注意事项](azure-stack-datacenter-integration.md)  
