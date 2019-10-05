---
title: 适用于 Azure Stack 的云解决方案提供商的使用情况和计费注册错误代码 |Microsoft Docs
description: Azure Stack 使用情况和注册错误代码。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: b06892e5e4c9d388756a2197f2054c1bac9f013c
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973997"
---
# <a name="usage-and-billing-registration-error-codes"></a>使用情况和计费注册错误代码

如果你是 CSP，则在[将租户添加](azure-stack-csp-ref-operations.md#add-tenant-to-registration)到注册以针对客户的 Azure 订阅 ID 报告使用情况时，可能会出现以下错误消息。

## <a name="list-of-registration-error-codes"></a>注册错误代码列表

| Error                           | 详细信息                                                                                                                                                                                                                                                                                                                           | 注释                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **RegistrationNotFound**            | 提供的注册未找到。 确保正确提供以下信息：<br>1.订阅标识符（提供的值：**订阅标识符**），<br>2.资源组（提供的值：**资源组**）、<br>3.注册名称（提供的值：**注册名称**）。                             | 当指向初始注册的信息不正确时，通常会发生此错误。 如需验证注册的资源组和名称，可以通过列出所有资源的方式在 Azure 门户中找到它。 如果找到多个注册资源，请查看属性中的 **CloudDeploymentID**，然后选择其 **CloudDeploymentID** 与云的 CloudDeploymentID 匹配的注册。 若要查找 **CloudDeploymentID**，可以在 Azure Stack 上使用以下 PowerShell 命令：<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| **BadCustomerSubscriptionId**       | 提供的**客户订阅标识符**和**注册名称**订阅标识符不属于同一 Microsoft CSP。 检查客户订阅标识符是否正确。 如果问题持续出现，请联系支持人员。 | 如果客户订阅为 CSP 订阅，但它汇总到 CSP 合作伙伴时，该合作伙伴不同于在初始注册中使用的订阅所汇总到的合作伙伴，则会发生此错误。 进行该检查是为了防止出现特定的情况，该情况会导致对与所使用的 Azure Stack 无关的 CSP 合作伙伴收费。                                                                                                                                                                                                                                                                          |
| **InvalidCustomerSubscriptionId**   | **客户订阅标识符**无效。 确保提供有效的 Azure 订阅。                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **CustomerSubscriptionNotFound**    | 在**注册名称**下找不到**客户订阅标识符**。 确保使用有效的 Azure 订阅，并已使用 PUT 操作将订阅 ID 添加到注册。                                                   | 尝试验证是否已向订阅添加租户但却找不到与注册关联的客户订阅时，会发生此错误。 尚未将客户添加到订阅，或者订阅 ID 写入错误。                                                                                                                                                                                                                                                                                                                                |
| **UnauthorizedCspRegistration**     | 提供的注册名称尚未获批使用多租户。 请向 azstCSP@microsoft.com 发送电子邮件，并提供注册名称、资源组以及在注册中使用的订阅标识符。                                                                                    | 注册需要获得 Microsoft 的批准，在允许使用多租户后，才能开始向其添加租户。                                                                                                                                                                                                                                                                                                                                                                                             |
| **CustomerSubscriptionsNotAllowed** | 不支持对断开连接的客户执行客户订阅操作。 若要使用此功能，请使用“即用即付”许可重新注册。                                                                                                                                                                    | 你尝试向其添加租户的注册是一个容量注册，也就是说，在创建注册时使用了参数 `BillingModel Capacity`。 只允许“即用即付”注册添加租户。 必须使用参数 `BillingModel PayAsYouUse` 重新注册。                                                                                                                                                                                                                                                                                          |
| **InvalidCSPSubscription**          | 提供的“客户订阅标识符”不是有效的 CSP 订阅。 确保提供有效的 Azure 订阅。                                                                                                                                                        | 这很有可能是错误地键入了客户订阅。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **MetadataResolverBadGatewayError** | 某个上游服务器返回了异外错误。 请稍后重试。 如果问题持续出现，请联系支持人员。                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="next-steps"></a>后续步骤

- 详细了解[适用于云服务提供商的使用情况报告基础结构](azure-stack-csp-ref-infrastructure.md)。
- 若要详细了解 CSP 计划，请参阅[云解决方案](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
