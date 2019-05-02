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
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: e0b62c9f9519e1b77211327c4f9b48d4b53cf99a
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985876"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack 日志和客户数据处理 
*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*  

范围内 Microsoft 是处理器或与 Azure Stack 的个人数据的 subprocessor，Microsoft 向所有客户，有效 2018 年 5 月 25 日，承诺会使在 (a) 在"处理的个人数据;"数据保护条款"部分中的 GDPR"预配[在线服务条款](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0)和 (b) 在欧盟一般数据保护条例中的条款的 4 个进行附件[联机服务条款](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0)。 

因为 Azure Stack 驻留在客户数据中心，Microsoft 是单独的与 Microsoft 通过共享的数据的数据控制器[诊断](azure-stack-diagnostics.md)，[遥测](azure-stack-telemetry.md)，和[计费](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>数据访问控制 
Microsoft 员工，分配要调查特定的支持案例，将授予只读访问加密数据。 Microsoft 员工也有权访问可用于删除数据，如果所需的工具。 对客户数据的所有访问都会受到审核和记录。  

数据访问控制：
1.  在结案后，数据最多只会保留 90 天。
2.  在 90 天期限内，客户随时可以删除数据。
3.  Microsoft 员工被授予访问权限的情况中的数据，并仅在需要时以帮助解决支持问题。 
4.  如果 Microsoft 必须与 OEM 合作伙伴共享客户数据，必须经得客户的同意。  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>客户可以实施哪些数据主题请求 (DSR) 控制？
前面曾提到，Microsoft 才支持每个客户请求删除按需数据。 客户可以请求我们支持工程师在客户的选择，永久清除数据之前的任何时间删除其给定用例的所有日志。  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>删除数据时，没有 Microsoft 通知客户？
对于自动化数据删除操作（结案后的 90 天内），我们不会主动联系客户并发出删除通知。 

对于按需数据删除操作，Microsoft 支持工程师有权访问其中他们可以启动按需数据删除和他们可以提供确认在手机上与客户在完成时使用的工具。

## <a name="diagnostic-data"></a>诊断数据
在支持过程中，Azure Stack 操作员可与 Azure Stack 支持和工程团队[共享诊断日志](azure-stack-diagnostics.md)，以方便进行故障排除。

Microsoft 提供了一种工具和客户收集并上传的脚本请求诊断日志文件。 收集后，日志文件会传输通过 HTTPS 保护的加密的连接到 Microsoft。 由于 HTTPS 提供在线加密，因此传输中加密无需密码。 Azure 收到日志后，会加密并存储日志，在关闭支持案例 90 天后自动将其删除。

## <a name="telemetry-data"></a>遥测数据
[Azure Stack 遥测](azure-stack-telemetry.md)自动将系统数据上传到 Microsoft 通过互连用户体验。 Azure Stack 操作员随时可以通过相应的控制机制自定义遥测功能和隐私设置。

Microsoft 无意收集敏感数据，例如信用卡号、用户名和密码、电子邮件地址或类似的敏感信息。 如果我们确定敏感信息是无意中收集到的，我们会予以删除。 

## <a name="billing-data"></a>账单数据
[Azure Stack 计费](azure-stack-usage-reporting.md)利用全球 Azure 计费和使用情况管道，因此符合 Microsoft 的符合性指南。

Azure Stack 操作员可以配置 Azure Stack，以将用量信息转发到 Azure 进行计费。 选择即用即付计费模型的多节点 Azure Stack 客户一定要这样做。 用量报告通过遥测单独进行控制，选择容量模式的多节点 Azure Stack 客户或 Azure Stack 开发工具包用户无需使用此功能。 对于上述方案，可以使用[注册脚本](azure-stack-usage-reporting.md)来禁用用量报告。


## <a name="next-steps"></a>后续步骤 
[详细了解 Azure Stack 安全性](azure-stack-security-foundations.md) 
