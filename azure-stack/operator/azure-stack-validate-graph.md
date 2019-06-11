---
title: 验证 Azure Stack 的 Azure Graph 集成
description: 使用 Azure Stack 就绪性检查器来验证 Azure Stack 的 Graph 集成。
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
ms.openlocfilehash: 75943b3259db736a3dfcd4b30b76f434eaf19111
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828443"
---
# <a name="validate-graph-integration-for-azure-stack"></a>验证 Azure Stack 的 Graph 集成

使用 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 来验证环境是否已准备好将 Azure Stack 与 Graph 相集成。 在开始数据中心集成或 Azure Stack 部署之前，请先验证 Graph 集成。

就绪性检查器会验证下列项：

* 为 Graph 集成创建的服务帐户的凭据具有相应的权限，可以查询 Active Directory。
* 全局目录可以解析并可访问。 
* KDC 可以解析并可访问。
* 已建立必要的网络连接。

有关 Azure Stack 数据中心集成的详细信息，请参阅 [Azure Stack 数据中心集成 - 标识](azure-stack-integrate-identity.md)。

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

从 [PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 Azure Stack 就绪性检查器工具 (AzsReadinessChecker)。

## <a name="prerequisites"></a>必备组件

必须满足以下先决条件。

**运行该工具的计算机：**

* 已建立域连接的 Windows 10 或 Windows Server 2016。
* PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell 命令，然后查看主要版本和次要版本：    
   > `$PSVersionTable.PSVersion`
* Active Directory PowerShell 模块。
* 最新版本的 [Microsoft Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。

**Active Directory 环境：**

* 确定现有 Active Directory 实例中 Graph 服务帐户的用户名和密码。
* 确定 Active Directory 林根 FQDN。

## <a name="validate-the-graph-service"></a>验证 Graph 服务

1. 在满足先决条件的计算机上，打开一个管理 PowerShell 提示符，然后运行以下命令来安装 AzsReadinessChecker：

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. 在 PowerShell 提示符下，运行以下命令以将 *$graphCredential* 变量设置为 Graph 帐户。 请将 `contoso\graphservice` 替换为你的帐户（使用 `domain\username` 格式）。

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. 在 PowerShell 提示符下，运行以下命令开始验证 Graph 服务。 指定 **-ForestFQDN** 的值作为林根的 FQDN。

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. 运行该工具后，查看输出。 确认状态是否为 OK（表示符合 Graph 集成要求）。 验证成功时会显示类似于以下示例的输出：

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

在生产环境中，从操作员工作站测试网络连接无法完全指示 Azure Stack 可用的连接。 Azure Stack 标记的公共 VIP 网络需要 LDAP 流量的连接才能执行标识集成。

## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

验证文件可以帮助你在部署 Azure Stack 之前共享状态，或者调查验证问题。 这两个文件都会持久保留每个后续验证检查的结果。 报告将向部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。

这两个文件默认写入到 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`。

使用：

* **-OutputPath**：在 run 命令的末尾使用 *path* 参数可以指定不同的报告位置。
* **-CleanReport**：在 run 命令的末尾使用该参数可以清除先前报告信息的 *AzsReadinessCheckerReport.json*。 有关详细信息，请参阅 [Azure Stack 验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还会将信息记录到 *AzsGraphIntegration.log* 中。

## <a name="next-steps"></a>后续步骤

[查看就绪性报表](azure-stack-validation-report.md)  
[有关 Azure Stack 集成的一般注意事项](azure-stack-datacenter-integration.md)  
