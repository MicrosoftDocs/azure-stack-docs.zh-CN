---
title: 验证 AD FS 集成
titleSuffix: Azure Stack Hub
description: 了解如何使用 Azure Stack 集线器就绪检查器来验证 Azure Stack 中心的 AD FS 集成。
services: azure-stack
documentationcenter: ''
author: BryanLa
ms.topic: how-to
ms.date: 10/19/2020
ms.author: bryanla
ms.reviewer: jerskine
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: cdbd069fee0584f78a4a62b7910428f47e8966d1
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840246"
---
# <a name="validate-ad-fs-integration-for-azure-stack-hub"></a>验证 Azure Stack 中心的 AD FS 集成

使用 Azure Stack 集线器就绪检查器工具 (AzsReadinessChecker) 验证你的环境是否已准备好进行 Active Directory 联合身份验证服务 (AD FS) 与 Azure Stack 中心集成。 开始数据中心集成之前或 Azure Stack 中心部署之前，请验证 AD FS 集成。

就绪性检查器会验证下列项：

* 联合元数据包含用于联合身份验证的有效 XML 元素。
* 可以检索 *AD FS SSL 证书* ，并且可以生成信任链。 在戳记上，AD FS 必须信任 SSL 证书链。 证书必须由用于 Azure Stack 中心部署证书的相同 *证书颁发机构* 进行签名，或者由受信任的根证书颁发机构伙伴进行签名。 有关受信任的根颁发机构伙伴的完整列表，请参阅 [参与者列表-Microsoft 受信任的根程序](/security/trusted-root/participants-list)。
* AD FS 签名证书受信任且不会在近期过期。

有关 Azure Stack 中心数据中心集成的详细信息，请参阅 [Azure Stack 中心数据中心集成-标识](azure-stack-integrate-identity.md)。

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

从 [PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 Azure Stack Hub 就绪性检查器工具 (AzsReadinessChecker)。  

## <a name="prerequisites"></a>先决条件

必须满足以下先决条件。

**运行该工具的计算机：**

* 具有域连接的 windows 10 或 Windows Server 2016。
* PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell 命令，并查看 *主要* 版本和 *次要* 版本：  
    ```powershell
    $PSVersionTable.PSVersion
    ```
* 最新版本的 [Microsoft Azure Stack 集线器就绪检查](https://aka.ms/AzsReadinessChecker) 程序工具。

**Active Directory 联合身份验证服务环境：**

至少需要下列其中一种形式的元数据：

- AD FS 联合元数据的 URL。 例如：`https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`。
* 联合元数据 XML 文件。 例如： FederationMetadata.xml。

## <a name="validate-ad-fs-integration"></a>验证 AD FS 集成

1. 在满足先决条件的计算机上，打开一个管理 PowerShell 提示符，然后运行以下命令来安装 AzsReadinessChecker：

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```

1. 在 PowerShell 提示符下，运行以下命令开始验证。 指定 **-CustomADFSFederationMetadataEndpointUri** 的值作为联合元数据的 URI。

     ```powershell
     Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml
     ```

1. 运行该工具后，查看输出。 确认状态是否为 OK（表示符合 AD FS 集成要求）。 验证成功时会显示类似于以下示例的输出：

    ```powershell
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Hub Stamp requires prior to Datacenter Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

在生产环境中，通过操作员工作站测试证书信任链并不完全表明 Azure Stack 中心基础结构中的 PKI 信任状态。 Azure Stack 集线器戳记的公共 VIP 网络需要与 PKI 基础结构的 CRL 建立连接。

## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

验证文件可帮助你在部署 Azure Stack 集线器或调查验证问题之前共享状态。 这两个文件都会持久保留每个后续验证检查的结果。 报告将向部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。

这两个文件默认写入到 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`。

使用：

* `-OutputPath`：用于指定不同报表位置的 "运行" 命令末尾的 *path* 参数。
* `-CleanReport`： "运行" 命令末尾的参数，用于清除对以前报表信息 AzsReadinessCheckerReport.js的操作。 有关详细信息，请参阅 [Azure Stack Hub 验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还会将信息记录到 *AzsReadinessChecker.log* 中。

下面的示例针对常见的验证失败提供了指导。

### <a name="command-not-found"></a>找不到命令

```powershell
Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
```

**原因**： PowerShell Autoload 未能正确加载就绪检查器模块。

**解决方法**：显式导入就绪状态检查器模块。 将以下代码复制并粘贴到 PowerShell，并将 `<version>` 其更新为当前安装的版本。

```powershell
Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force
```

## <a name="next-steps"></a>后续步骤

[查看就绪性报表](azure-stack-validation-report.md)  
[有关 Azure Stack Hub 集成的一般注意事项](azure-stack-datacenter-integration.md)  
