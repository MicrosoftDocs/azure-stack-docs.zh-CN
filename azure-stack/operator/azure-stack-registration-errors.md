---
title: Azure Stack Hub 中的使用情况和计费注册错误代码
description: 了解 Azure Stack Hub 中的使用情况和计费注册错误代码。
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 0c143a2a997a044e102eef63018dba5a492a3e11
ms.sourcegitcommit: 03aad17afe8519536066c735c59ad1bdfe8de083
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89041598"
---
# <a name="usage-and-billing-registration-error-codes"></a>使用情况和计费注册错误代码

如果你是云解决方案提供商 (CSP)，则在[将租户添加](azure-stack-csp-ref-operations.md#add-tenant-to-registration)到注册以针对客户的 Azure 订阅 ID 报告使用情况时，可能会出现以下错误消息。

## <a name="list-of-registration-error-codes"></a>注册错误代码列表

| 错误   | 详细信息  | 注释  |
|---|---|---|
| RegistrationNotFound | 提供的注册未找到。 确保正确提供以下信息：<br>1.订阅标识符（提供的值：订阅标识符****）；<br>2.资源组（提供的值：资源组****）；<br>3.注册名称（提供的值：注册名称****）。 | 当指向初始注册的信息不正确时，通常会发生此错误。 如需验证注册的资源组和名称，可以通过列出所有资源的方式在 Azure 门户中找到它。 如果找到多个注册资源，请查看属性中的 **CloudDeploymentID**，然后选择其 **CloudDeploymentID** 与云的 CloudDeploymentID 匹配的注册。 若要查找 **CloudDeploymentID**，可以在 Azure Stack Hub 上使用以下 PowerShell 命令：<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId | 提供的客户订阅标识符和注册名称订阅标识符不是由同一 Microsoft CSP 拥有**** ****。 检查客户订阅标识符是否正确。 客户订阅 ID 区分大小写。 如果问题仍然存在，请联系支持部门。 | 如果客户订阅为 CSP 订阅，但它汇总到 CSP 合作伙伴时，该合作伙伴不同于在初始注册中使用的订阅所汇总到的合作伙伴，则会发生此错误。 进行此检查是为了防止会导致向不对所用的 Azure Stack Hub 负责的 CSP 合作伙伴计费的情况。 |
| InvalidCustomerSubscriptionId  | 客户订阅标识符无效****。 确保提供有效的 Azure 订阅。 |   |
| CustomerSubscriptionNotFound  | 在注册名称下找不到客户订阅标识符**** ****。 确保使用有效的 Azure 订阅，并已使用 PUT 操作将订阅 ID 添加到注册。 | 尝试验证是否已向订阅添加租户但却找不到与注册关联的客户订阅时，会发生此错误。 尚未将客户添加到订阅，或者订阅 ID 写入错误。 |
| UnauthorizedCspRegistration | 提供的注册名称尚未获批使用多租户****。 请向 azstCSP@microsoft.com 发送电子邮件，并提供注册名称、资源组以及在注册中使用的订阅标识符。 | 注册需要获得 Microsoft 的批准，在允许使用多租户后，才能开始向其添加租户。 |
| CustomerSubscriptionsNotAllowed | 不支持对断开连接的客户执行客户订阅操作。 若要使用此功能，请使用即用即付许可重新注册。 | 你尝试向其添加租户的注册是一个容量注册。 因此在创建该注册时，使用了参数 `BillingModel Capacity`。 只允许“即用即付”注册添加租户。 必须使用参数 `BillingModel PayAsYouUse` 重新注册。 |
| InvalidCSPSubscription | 提供的客户订阅标识符不是有效的 CSP 订阅。**** 确保提供有效的 Azure 订阅。 | 该错误很有可能是错误地键入了客户订阅。 |
| MetadataResolverBadGatewayError | 某个上游服务器返回了异外错误。 请稍后重试。 如果问题仍然存在，请联系支持部门。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[适用于云解决方案提供商的使用情况报告基础结构](azure-stack-csp-ref-infrastructure.md)。
- 若要详细了解 CSP 计划，请参阅[云解决方案](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要详细了解如何从 Azure Stack Hub 检索资源使用情况信息，请参阅 [Azure Stack Hub 中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
