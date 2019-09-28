---
title: Azure Stack 日志和客户数据处理 |Microsoft Docs
description: 了解 Azure Stack 如何收集客户数据和信息。
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
ms.reviewer: chengwei
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 3e46007c07856df9ecc6b4edca4c595525b3b8ba
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342778"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack 日志和客户数据处理 
适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*  

Microsoft 的 subprocessor 是与 Azure Stack 建立的个人数据的一个处理器或，Microsoft 向所有2018客户提供了以下承诺：

- 个人数据的处理;GDPR "数据保护条款"[中的 "](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)预配" 部分。
- 欧盟一般数据保护条例[在线服务条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)的附件4中的条款。

随着 Azure Stack 位于客户数据中心，Microsoft 是数据控制器，只是通过[诊断](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)、[遥测](azure-stack-telemetry.md)和[计费](azure-stack-usage-reporting.md)与 Microsoft 共享的数据。  

## <a name="data-access-controls"></a>数据访问控制 
被分配了调查特定支持案例的 Microsoft 员工将获得对加密数据的只读访问权限。 如果需要，Microsoft 员工还可以访问用于删除数据的工具。 对客户数据的所有访问都会受到审核和记录。  

数据访问控制：
- 数据仅在关闭后最多保留90天。
- 在 90 天期限内，客户随时可以删除数据。
- Microsoft 员工会根据具体情况提供对数据的访问权限，并且仅在需要时才可帮助解决支持问题。
- 如果 Microsoft 必须与 OEM 合作伙伴共享客户数据，必须经得客户的同意。  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>客户可以实施哪些数据主题请求 (DSR) 控制？
Microsoft 支持按客户请求删除按需数据。 客户可以随时请求我们的一位支持工程师删除给定案例的所有日志，然后再将数据永久删除。  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>删除数据后，Microsoft 是否通知客户？
对于自动数据删除操作（关闭后90天），我们不会主动联系客户，并通知他们有关删除的信息。

对于按需数据删除操作，Microsoft 支持工程师有权访问该工具，使其可以按需删除数据。 完成后，他们可以向客户提供电话确认。

## <a name="diagnostic-data"></a>诊断数据
作为支持过程的一部分，Azure Stack 操作员可以与 Azure Stack 支持和工程团队[共享诊断日志](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)，以帮助进行故障排除。

Microsoft 为客户提供了一个工具和脚本来收集和上载请求的诊断日志文件。 收集后，日志文件将通过与 Microsoft 的 HTTPS 受保护的加密连接进行传输。 由于 HTTPS 通过网络提供加密，因此传输中的加密不需要密码。 收到日志后，将对其进行加密和存储，直到在支持案例关闭90天后将其自动删除。

## <a name="telemetry-data"></a>遥测数据
[Azure Stack 遥测](azure-stack-telemetry.md)会通过连接的用户体验自动将系统数据上传到 Microsoft。 Azure Stack 操作员随时可以通过相应的控制机制自定义遥测功能和隐私设置。

Microsoft 不打算收集敏感数据，如信用卡号、用户名和密码、电子邮件地址等。 如果我们确定敏感信息是无意中收集到的，我们会予以删除。

## <a name="billing-data"></a>账单数据
[Azure Stack 计费](azure-stack-usage-reporting.md)利用全球 Azure 的计费和使用情况管道，因此与 Microsoft 符合性准则相一致。

Azure Stack 操作员可以配置 Azure Stack，以将用量信息转发到 Azure 进行计费。 对于选择即用即付计费模式的 Azure Stack 集成系统客户，此配置是必需的。 使用情况报告独立于遥测，对于选择容量模型或 Azure Stack 开发工具包用户的集成系统客户来说不是必需的。 对于上述方案，可以使用[注册脚本](azure-stack-usage-reporting.md)来禁用用量报告。


## <a name="next-steps"></a>后续步骤 
[详细了解 Azure Stack 安全性](azure-stack-security-foundations.md) 
