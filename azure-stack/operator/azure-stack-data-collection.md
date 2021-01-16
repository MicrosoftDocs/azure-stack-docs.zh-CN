---
title: Azure Stack Hub 日志和客户数据处理
description: 了解 Azure Stack Hub 如何收集客户数据和信息。
author: PatAltimore
ms.topic: article
ms.date: 02/24/2020
ms.author: patricka
ms.reviewer: chengwei
ms.lastreviewed: 02/24/2020
ms.openlocfilehash: 60301776ae496a33622005bed3011e773ea321f2
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255973"
---
# <a name="azure-stack-hub-log-and-customer-data-handling"></a>Azure Stack Hub 日志和客户数据处理 

Microsoft 在与 Azure Stack 中心连接时，microsoft 是一个处理器或 subprocessor 的个人数据，Microsoft 向所有2018客户提出了以下承诺：

- [联机服务条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)的“数据保护条款”部分中的“个人数据的处理；GDPR”条款。
- [联机服务条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)附件 4 中的“欧盟一般数据保护条例条款”。

随着 Azure Stack 中心位于客户数据中心，Microsoft 是数据控制器，只是通过 [诊断](./diagnostic-log-collection.md)、 [遥测](azure-stack-telemetry.md)和 [计费](azure-stack-usage-reporting.md)与 Microsoft 共享的数据。  

## <a name="data-access-controls"></a>数据访问控制 
被分配了调查特定支持案例的 Microsoft 员工将获得对加密数据的只读访问权限。 如果需要，Microsoft 员工还可以访问用于删除数据的工具。 对客户数据的所有访问都会受到审核和记录。  

数据访问控制：
- 在结案后，数据最多只保留 90 天。
- 在 90 天期限内，客户随时可以删除数据。
- Microsoft 员工会根据具体情况提供对数据的访问权限，并且仅在需要时才可帮助解决支持问题。
- 如果 Microsoft 必须与 OEM 合作伙伴共享客户数据，必须经得客户的同意。  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>客户可以实施哪些数据主题请求 (DSR) 控制？
Microsoft 支持按客户请求删除按需数据。 客户可以请求我们的支持工程师之一在任何时间删除给定案例的所有日志，然后再将数据永久擦除。  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>删除数据后，Microsoft 是否通知客户？
对于自动化数据删除操作（案例关闭后 90 天），我们不会主动联系客户并通知他们有关删除的信息。

对于按需数据删除操作，Microsoft 支持工程师有权访问该工具，使其可以按需删除数据。 他们可以在完成删除后通过电话向客户提供确认。

## <a name="diagnostic-data"></a>诊断数据
在支持过程中，Azure Stack Hub 操作员可与 Azure Stack Hub 支持和工程团队[共享诊断日志](./diagnostic-log-collection.md)，以方便进行故障排除。

Microsoft 为客户提供了一个工具和脚本来收集和上载请求的诊断日志文件。 收集后，日志文件将通过与 Microsoft 的 HTTPS 受保护的加密连接进行传输。 由于 HTTPS 提供在线加密，因此传输中加密无需密码。 Azure 收到日志后，会加密并存储日志，在关闭支持案例 90 天后自动将其删除。

## <a name="telemetry-data"></a>遥测数据
[Azure Stack 集线器遥测](azure-stack-telemetry.md) 会自动通过连接的用户体验将系统数据上传到 Microsoft。 Azure Stack Hub 操作员可以随时控制自定义遥测功能和隐私设置。

Microsoft 不打算收集敏感数据，如信用卡号、用户名和密码、电子邮件地址等。 如果我们确定敏感信息是无意中收集到的，我们会予以删除。

## <a name="billing-data"></a>账单数据
[Azure Stack 集线器计费](azure-stack-usage-reporting.md) 利用全球 Azure 的计费和使用情况管道，因此与 Microsoft 符合性准则一致。

Azure Stack 中心操作员可以将 Azure Stack 中心配置为将使用情况信息转发到 Azure 进行计费。 对于选择即用即付计费模型的 Azure Stack 集线器集成系统客户，此配置是必需的。 用量报告通过遥测单独进行控制，选择容量模式的集成系统 Azure Stack 客户或 Azure Stack 开发工具包用户无需使用此功能。 对于上述方案，可以使用[注册脚本](azure-stack-usage-reporting.md)来禁用用量报告。


## <a name="next-steps"></a>后续步骤 
[详细了解 Azure Stack Hub 安全性](azure-stack-security-foundations.md)