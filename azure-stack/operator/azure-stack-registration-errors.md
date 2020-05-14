---
title: Azure Stack 集线器中的使用情况和计费注册错误代码
description: 了解 Azure Stack 集线器中的使用情况和计费注册错误代码。
author: sethmanheim
ms.topic: article
ms.date: 05/01/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 4b193ada5c9a188b725ea88dc2d5f54905a5e537
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2020
ms.locfileid: "83375070"
---
# <a name="usage-and-billing-registration-error-codes"></a>使用情况和计费注册错误代码

如果你是云解决方案提供商（CSP），则[将租户添加](azure-stack-csp-ref-operations.md#add-tenant-to-registration)到注册以便针对客户的 AZURE 订阅 ID 进行报告使用时，可能会出现以下错误消息。

## <a name="list-of-registration-error-codes"></a>注册错误代码列表

| 错误   | 详细信息  | 注释  |
|---|---|---|
| RegistrationNotFound | 找不到提供的注册。 确保正确提供以下信息：<br>1.订阅标识符（提供的值：订阅标识符  ）；<br>2.资源组（提供的值：资源组  ）；<br>3.注册名称（提供的值：注册名称  ）。 | 当指向初始注册的信息不正确时，通常会发生此错误。 如需验证注册的资源组和名称，可以通过列出所有资源的方式在 Azure 门户中找到它。 如果找到多个注册资源，请查看属性中的 **CloudDeploymentID**，然后选择其 **CloudDeploymentID** 与云的 CloudDeploymentID 匹配的注册。 若要查找 **CloudDeploymentID**，可以在 Azure Stack Hub 上使用以下 PowerShell 命令：<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId | 提供的**客户订阅标识符**和**注册名称**订阅标识符不属于同一 Microsoft CSP。 检查客户订阅标识符是否正确。 如果问题仍然存在，请联系支持部门。 | 如果客户订阅是 CSP 订阅，则会发生此错误，但会将其汇总到与初始注册中使用的订阅不同的 CSP 合作伙伴。 进行此项检查是为了防止发生这种情况，这会导致为 CSP 伙伴计费，而不负责使用 Azure Stack 中心。 |
| InvalidCustomerSubscriptionId  | **客户订阅标识符**无效。 确保提供有效的 Azure 订阅。 |   |
| CustomerSubscriptionNotFound  | 在 "**注册名称**" 下找不到**客户订阅标识符**。 确保使用有效的 Azure 订阅，并已使用 PUT 操作将订阅 ID 添加到注册。 | 当尝试 v 已将某个租户添加到订阅，但找不到与该注册关联的客户订阅时，将发生此错误。 尚未将客户添加到注册，或订阅 ID 的编写不正确。 |
| UnauthorizedCspRegistration | 提供的**注册名称**未获得批准，无法使用多租户。 请向 azstCSP@microsoft.com 发送电子邮件，并提供注册名称、资源组以及在注册中使用的订阅标识符。 | 注册需要获得 Microsoft 的批准，在允许使用多租户后，才能开始向其添加租户。 |
| CustomerSubscriptionsNotAllowed | 对于断开连接的客户，客户订阅操作不受支持。 若要使用此功能，请使用即用即付许可重新注册。 | 尝试向其添加租户的注册是一个容量注册。 因此，在创建注册时，将 `BillingModel Capacity` 使用参数。 只允许“即用即付”注册添加租户。 必须使用参数 `BillingModel PayAsYouUse` 重新注册。 |
| InvalidCSPSubscription | 提供的**客户订阅标识符**不是有效的 CSP 订阅。 确保提供有效的 Azure 订阅。 | 此错误很可能是由于客户订阅键入错误引起的。 |
| MetadataResolverBadGatewayError | 某个上游服务器返回了异外错误。 请稍后再试。 如果问题仍然存在，请联系支持部门。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[适用于云解决方案提供商的使用情况报告基础结构](azure-stack-csp-ref-infrastructure.md)。
- 若要详细了解 CSP 计划，请参阅[云解决方案](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要了解有关如何从 Azure Stack 中心检索资源使用情况信息的详细信息，请参阅[Azure Stack 中心中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
