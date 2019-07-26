---
title: Azure Stack 日志和数据处理 | Microsoft Docs
description: 了解 Azure Stack 如何收集信息。
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
ms.openlocfilehash: 495b75359cb8c859e532885a1c9fa284691bd90f
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493811"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack 日志和客户数据处理 
适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*  

如果 Microsoft 是与 Azure Stack 建立的个人数据的处理器或 subprocessor, Microsoft 将在 "个人数据处理" (a) 中向所有客户提供一项有效的承诺, 如2018。GDPR "预配" 数据保护条款 "一节, 其中的[联机服务条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和 (b) 一般数据保护条例在[联机服务条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)的附件4中。 

随着 Azure Stack 位于客户数据中心, Microsoft 是数据控制器, 只是通过[诊断](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)、[遥测](azure-stack-telemetry.md)和[计费](azure-stack-usage-reporting.md)与 Microsoft 共享的数据。  

## <a name="data-access-controls"></a>数据访问控制 
被分配了调查特定支持案例的 Microsoft 员工将获得对加密数据的只读访问权限。 如果需要, Microsoft 员工还可以访问用于删除数据的工具。 对客户数据的所有访问都会受到审核和记录。  

数据访问控制：
- 在结案后，数据最多只会保留 90 天。
- 在 90 天期限内，客户随时可以删除数据。
- Microsoft 员工会根据具体情况提供对数据的访问权限, 并且仅在需要时才可帮助解决支持问题。 
- 如果 Microsoft 必须与 OEM 合作伙伴共享客户数据，必须经得客户的同意。  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>客户可以实施哪些数据主题请求 (DSR) 控制？
如前文所述, Microsoft 支持按需数据删除每个客户请求。 客户可能会要求我们的支持工程师在客户选择任何时间删除其所有日志, 然后再将数据永久擦除。  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>删除数据后, Microsoft 是否通知客户？
对于自动数据删除操作 (关闭后90天), 我们不会主动联系客户, 并通知他们有关删除的信息。 

对于按需数据删除操作, Microsoft 支持工程师有权访问工具, 在该工具中, 他们可以按需启动数据删除, 并且在完成后可以在客户电话上提供确认。

## <a name="diagnostic-data"></a>诊断数据
在支持过程中，Azure Stack 操作员可与 Azure Stack 支持和工程团队[共享诊断日志](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)，以方便进行故障排除。

Microsoft 为客户提供了一个工具和脚本来收集和上载请求的诊断日志文件。 收集后, 日志文件将通过与 Microsoft 的 HTTPS 受保护的加密连接进行传输。 由于 HTTPS 提供在线加密，因此传输中加密无需密码。 Azure 收到日志后，会加密并存储日志，在关闭支持案例 90 天后自动将其删除。

## <a name="telemetry-data"></a>遥测数据
[Azure Stack 遥测](azure-stack-telemetry.md)会通过连接的用户体验自动将系统数据上传到 Microsoft。 Azure Stack 操作员随时可以通过相应的控制机制自定义遥测功能和隐私设置。

Microsoft 无意收集敏感数据，例如信用卡号、用户名和密码、电子邮件地址或类似的敏感信息。 如果我们确定敏感信息是无意中收集到的，我们会予以删除。 

## <a name="billing-data"></a>账单数据
[Azure Stack 计费](azure-stack-usage-reporting.md)利用全球 Azure 的计费和使用情况管道, 因此与 Microsoft 符合性准则相一致。

Azure Stack 操作员可以配置 Azure Stack，以将用量信息转发到 Azure 进行计费。 选择即用即付计费模型的多节点 Azure Stack 客户一定要这样做。 用量报告通过遥测单独进行控制，选择容量模式的多节点 Azure Stack 客户或 Azure Stack 开发工具包用户无需使用此功能。 对于上述方案，可以使用[注册脚本](azure-stack-usage-reporting.md)来禁用用量报告。


## <a name="next-steps"></a>后续步骤 
[详细了解 Azure Stack 安全性](azure-stack-security-foundations.md) 
