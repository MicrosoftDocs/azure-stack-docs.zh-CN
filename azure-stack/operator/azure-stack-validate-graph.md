---
title: 为 Azure Stack 中心验证 Azure Graph 集成
description: 使用 Azure Stack 集线器就绪状态检查器来验证 Azure Stack 中心的图形集成。
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 29cc035e66039d09e761410808098d57f0b1927f
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882630"
---
# <a name="validate-graph-integration-for-azure-stack-hub"></a>验证 Azure Stack 集线器的图形集成

使用 Azure Stack 集线器就绪检查器工具（AzsReadinessChecker）来验证你的环境是否已准备好与 Azure Stack 中心的图形集成。 开始数据中心集成之前或 Azure Stack 中心部署之前，请验证图形集成。

就绪检查程序将验证：

* 为图形集成创建的服务帐户的凭据具有适当的查询 Active Directory 权限。
* *全局编录*可以解析并且是 contactable 的。
* KDC 可以解析，并 contactable。
* 需要进行网络连接。

有关 Azure Stack 中心数据中心集成的详细信息，请参阅[Azure Stack 中心数据中心集成-标识](azure-stack-integrate-identity.md)。

## <a name="get-the-readiness-checker-tool"></a>获取就绪状态检查器工具

从[PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 Azure Stack 集线器就绪检查器工具（AzsReadinessChecker）。

## <a name="prerequisites"></a>必备组件

必须满足以下先决条件。

**运行该工具的计算机：**

* Windows 10 或 Windows Server 2016，具有域连接性。
* PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell 命令，并查看*主要*版本和*次要*版本：  
   > `$PSVersionTable.PSVersion`
* Active Directory PowerShell 模块。
* 最新版本的[Microsoft Azure Stack 集线器就绪检查](https://aka.ms/AzsReadinessChecker)程序工具。

**Active Directory 环境：**

* 确定现有 Active Directory 实例中图形服务的帐户的用户名和密码。
* 确定 Active Directory 林根 FQDN。

## <a name="validate-the-graph-service"></a>验证 graph 服务

1. 在满足先决条件的计算机上，打开管理 PowerShell 提示符，然后运行以下命令以安装 AzsReadinessChecker：

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. 在 PowerShell 提示符下，运行以下命令，将 *$graphCredential*变量设置为图形帐户。 使用 `domain\username` 格式将 `contoso\graphservice` 替换为你的帐户。

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. 在 PowerShell 提示符下，运行以下命令以启动对 graph 服务的验证。 将 **-ForestFQDN**的值指定为林根的 FQDN。

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. 运行该工具后，查看输出。 确认 "图形集成要求" 的状态为 "正常"。 验证成功类似于以下示例：

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Hub Stamp requires prior to Datacenter Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

在生产环境中，通过操作员工作站测试网络连接并不完全表明 Azure Stack 集线器可以使用连接。 Azure Stack 集线器戳记的公共 VIP 网络需要用于 LDAP 通信的连接才能执行标识集成。

## <a name="report-and-log-file"></a>报表和日志文件

每次验证运行时，都会将结果记录到**AzsReadinessChecker**和**AzsReadinessCheckerReport**中。 这些文件的位置在 PowerShell 中显示验证结果。

验证文件可帮助你在部署 Azure Stack 集线器或调查验证问题之前共享状态。 这两个文件都保留每个后续验证检查的结果。 报表向部署团队确认身份配置。 日志文件可帮助您的部署或支持团队调查验证问题。

默认情况下，这两个文件都将写入 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`。

使用：

* **-OutputPath**： run 命令末尾的*path*参数，用于指定不同的报表位置。
* **-CleanReport**： run 命令末尾的参数，用于清除以前报表信息的*AzsReadinessCheckerReport* 。 有关详细信息，请参阅[Azure Stack 中心验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，将在 PowerShell 窗口中显示有关失败的详细信息。 该工具还会将信息记录到*AzsGraphIntegration*。

## <a name="next-steps"></a>后续步骤

[查看准备情况报表](azure-stack-validation-report.md)  
[一般 Azure Stack 集线器集成注意事项](azure-stack-datacenter-integration.md)  
