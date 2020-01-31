---
title: 验证 Azure Stack 中心的 AD FS 集成
description: 使用 Azure Stack 集线器就绪状态检查器来验证 Azure Stack 中心的 AD FS 集成。
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: a98a5384b8590f494e6e9d6acdeb05e90fce3a20
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880641"
---
# <a name="validate-ad-fs-integration-for-azure-stack-hub"></a>验证 Azure Stack 中心的 AD FS 集成

使用 Azure Stack 集线器就绪检查器工具（AzsReadinessChecker）来验证你的环境是否已准备好与 Azure Stack 中心 Active Directory 联合身份验证服务（AD FS）集成。 开始数据中心集成之前或 Azure Stack 中心部署之前，请验证 AD FS 集成。

就绪检查程序将验证：

* *联合元数据*包含用于联合的有效 XML 元素。
* 可以检索*AD FS SSL 证书*，并且可以生成信任链。 在 "stamp AD FS 必须信任 SSL 证书链。 证书必须由用于 Azure Stack 中心部署证书的相同*证书颁发机构*进行签名，或者由受信任的根证书颁发机构伙伴进行签名。 有关受信任的根颁发机构伙伴的完整列表，请参阅[TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)。
* *AD FS 签名证书*是受信任的，并且未接近过期。

有关 Azure Stack 中心数据中心集成的详细信息，请参阅[Azure Stack 中心数据中心集成-标识](azure-stack-integrate-identity.md)。

## <a name="get-the-readiness-checker-tool"></a>获取就绪状态检查器工具

从[PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 Azure Stack 集线器就绪检查器工具（AzsReadinessChecker）。  

## <a name="prerequisites"></a>必备组件

必须满足以下先决条件。

**运行该工具的计算机：**

* Windows 10 或 Windows Server 2016，具有域连接性。
* PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell 命令，并查看*主要*版本和*次要*版本：  
   > `$PSVersionTable.PSVersion`
* 最新版本的[Microsoft Azure Stack 集线器就绪检查](https://aka.ms/AzsReadinessChecker)程序工具。

**Active Directory 联合身份验证服务环境：**

至少需要以下一种形式的元数据：

* AD FS 联合元数据的 URL。 示例为 `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`。
* 联合元数据 XML 文件。 例如，Federationmetadata.xml。

## <a name="validate-ad-fs-integration"></a>验证 AD FS 集成

1. 在满足先决条件的计算机上，打开管理 PowerShell 提示符，然后运行以下命令以安装 AzsReadinessChecker：

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. 在 PowerShell 提示符下，运行以下命令以启动验证。 将 **-CustomADFSFederationMetadataEndpointUri**的值指定为联合元数据的 URI。

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. 运行该工具后，查看输出。 确认 AD FS 集成要求状态是否为 "正常"。 验证成功类似于以下示例：

    ```
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

每次验证运行时，都会将结果记录到**AzsReadinessChecker**和**AzsReadinessCheckerReport**中。 这些文件的位置在 PowerShell 中显示验证结果。

验证文件可帮助你在部署 Azure Stack 集线器或调查验证问题之前共享状态。 这两个文件都保留每个后续验证检查的结果。 报表向部署团队确认身份配置。 日志文件可帮助您的部署或支持团队调查验证问题。

默认情况下，这两个文件都将写入 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`。

使用：

* **-OutputPath**： run 命令末尾的*path*参数，用于指定不同的报表位置。
* **-CleanReport**： run 命令末尾的参数，用于清除以前报表信息的 AzsReadinessCheckerReport。 有关详细信息，请参阅[Azure Stack 中心验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，将在 PowerShell 窗口中显示有关失败的详细信息。 该工具还会将信息记录到*AzsReadinessChecker*。

下面的示例提供了有关常见验证失败的指导。

### <a name="command-not-found"></a>找不到命令

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**原因**： PowerShell Autoload 未能正确加载就绪检查器模块。

**解决方法**：显式导入就绪状态检查器模块。 将以下代码复制并粘贴到 PowerShell，并更新 \<版本\>，其中包含当前安装的版本的编号。

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>后续步骤

[查看准备情况报表](azure-stack-validation-report.md)  
[一般 Azure Stack 集线器集成注意事项](azure-stack-datacenter-integration.md)  
