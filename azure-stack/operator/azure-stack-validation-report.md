---
title: 针对 Azure Stack 的验证报表 | Microsoft Docs
description: 使用 Azure Stack 就绪性检查器报表查看验证结果。
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
ms.date: 04/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: d02173731f8cf7834160a0228c589b036aac7fe6
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984020"
---
# <a name="azure-stack-validation-report"></a>Azure Stack 验证报表

使用*Azure Stack 就绪性检查器*工具运行支持的部署和维护的 Azure Stack 环境的验证。 该工具将结果写入到 .json 报表文件。 该报表显示有关 Azure Stack 部署的先决条件状态的详细数据和汇总数据。 报表还显示有关机密轮换，以便现有的 Azure Stack 部署的信息。  

## <a name="where-to-find-the-report"></a>在何处可以找到该报表

该工具运行时，它会将结果记录到 **AzsReadinessCheckerReport.json** 中。 该工具还会创建一个名为 **AzsReadinessChecker.log** 的日志。 在 PowerShell 中的验证结果一起显示这些文件的位置：

![运行验证](./media/azure-stack-validation-report/validation.png)

当在同一计算机上运行后续验证检查时，这两个文件都会持久保留这些验证检查的结果。 例如，可以运行该工具来验证证书，再次运行它来验证 Azure 标识，第三次运行它来验证注册。 所有三次验证的结果都在生成的 .json 报表中。  

默认情况下，这两个文件都写入到 **C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**。  

- 使用`-OutputPath <path>`要指定不同的报表位置的命令行末尾处的参数。
- 使用`-CleanReport`用于清除之前运行中的工具有关的信息的命令行末尾处的参数**AzsReadinessCheckerReport.json**。

## <a name="view-the-report"></a>查看报告

若要在 PowerShell 中查看报表，提供报表的路径的值作为`-ReportPath`。 此命令显示报表内容，并指明尚没有结果的验证。

例如，若要查看从 PowerShell 提示符下的报表所在的位置打开报表，请运行以下命令：

```shell
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

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>查看报表摘要

若要查看报表的摘要，可以添加`-summary`到末尾的 PowerShell 命令的参数。 例如：

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

摘要显示了不具有结果的验证，并指示通过或失败的验证已完成的。 输出类似于以下示例：

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>查看经筛选的报表

若要查看基于单一验证类型筛选的报表，请将 **-ReportSections** 参数与以下值之一结合使用：

- 证书
- AzureRegistration
- AzureIdentity
- 图形
- ADFS
- 作业
- 全部  

例如，若要仅查看证书的报表摘要，请使用以下 PowerShell 命令行：

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate - Summary
```
