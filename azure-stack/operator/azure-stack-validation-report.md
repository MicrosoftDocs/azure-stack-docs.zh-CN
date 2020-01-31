---
title: Azure Stack 中心的验证报告
description: 使用 Azure Stack 集线器就绪检查器报表来查看验证结果。
author: ihenkel
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 21c19a368b62a35e3b2daeef2a0e36f84eb4e527
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880568"
---
# <a name="azure-stack-hub-validation-report"></a>Azure Stack 中心验证报告

使用*Azure Stack 集线器就绪检查*程序工具来运行可支持部署和维护 Azure Stack 中心环境的验证。 该工具将结果写入到一个 json 报表文件。 此报表显示有关部署 Azure Stack 集线器的先决条件状态的详细和汇总数据。 该报表还显示现有 Azure Stack 中心部署的机密旋转信息。  

## <a name="where-to-find-the-report"></a>在何处可以找到报表

当该工具运行时，它会将结果记录到**AzsReadinessCheckerReport**中。 该工具还会创建名为**AzsReadinessChecker**的日志。 这些文件的位置与 PowerShell 中的验证结果一起显示：

![运行-验证](./media/azure-stack-validation-report/validation.png)

当在同一台计算机上运行时，两个文件都将保留后续验证检查的结果。 例如，可以运行该工具来验证证书、再次运行以验证 Azure 标识，然后第三次验证注册。 生成的 json 报表中提供了所有三个验证的结果。  

默认情况下，这两个文件都将写入**C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**。  

- 使用命令行末尾的 `-OutputPath <path>` 参数来指定不同的报表位置。
- 使用命令行末尾的 `-CleanReport` 参数从**AzsReadinessCheckerReport**中清除有关先前运行的工具的信息。

## <a name="view-the-report"></a>查看报告

若要在 PowerShell 中查看报表，请将报表的路径提供为 `-ReportPath`的值。 此命令显示报表的内容并标识尚未获得结果的验证。

例如，若要从打开到报表所在位置的 PowerShell 提示符查看报表，请运行以下命令：

```powershell
Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json
```

输出类似于以下示例：

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>查看报表摘要

要查看报表的摘要，你可以将 `-summary` 参数添加到 PowerShell 命令的末尾。 例如：

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

此摘要显示没有结果的验证，并指示已完成的验证是通过还是失败。 输出类似于以下示例：

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Hub Graph Validation Summary ###############

Azure Stack Hub Graph Validation results not available.

############### Azure Stack Hub ADFS Validation Summary ###############

Azure Stack Hub ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>查看筛选的报表

若要查看按单一验证类型筛选的报表，请将 **-ReportSections**参数与以下值之一一起使用：

- 证书
- AzureRegistration
- AzureIdentity
- 图形
- ADFS
- 工作
- 所有  

例如，若要查看仅用于证书的报表摘要，请使用以下 PowerShell 命令行：

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
