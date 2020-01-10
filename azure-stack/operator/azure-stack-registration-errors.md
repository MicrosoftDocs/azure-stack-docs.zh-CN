---
title: Azure Stack 中心的云解决方案提供商的使用情况和计费注册错误代码 |Microsoft Docs
description: Azure Stack 集线器使用情况和注册错误代码。
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
ms.openlocfilehash: 508a35d0e52f88cba3aa93db6cc965cba7b05de6
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809723"
---
# <a name="usage-and-billing-registration-error-codes"></a>使用情况和计费注册错误代码

如果你是 CSP，则[将租户添加](azure-stack-csp-ref-operations.md#add-tenant-to-registration)到注册以便针对客户的 AZURE 订阅 ID 进行报告使用时，可能会出现以下错误消息。

## <a name="list-of-registration-error-codes"></a>注册错误代码列表

| 错误                           | 详细信息                                                                                                                                                                                                                                                                                                                           | 注释                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **RegistrationNotFound**            | 找不到提供的注册。 请确保正确提供以下信息：<br>1. 订阅标识符（提供的值：**订阅标识符**），<br>2. 资源组（提供的值：**资源组**），<br>3. 注册名称（提供的值：**注册名称**）。                             | 当指向初始注册的信息不正确时，通常会发生此错误。 如果需要验证注册的资源组和名称，可以通过列出所有资源在 Azure 门户中找到该资源组和名称。 如果找到多个注册资源，请查看 "属性" 中的**CloudDeploymentID** ，并选择其**CloudDeploymentID**与云的匹配项。 若要查找**CloudDeploymentID**，可以在 Azure Stack 集线器上使用此 PowerShell 命令：<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| **BadCustomerSubscriptionId**       | 提供的**客户订阅标识符**和**注册名称**订阅标识符不属于同一 Microsoft CSP。 检查客户订阅标识符是否正确。 如果问题持续出现，请联系支持人员。 | 当客户订阅是 CSP 订阅，但它汇总到不同于初始注册中使用的订阅的 CSP 伙伴时，会出现此错误。 进行此项检查是为了防止发生这种情况，这会导致为 CSP 伙伴计费，而该合作伙伴对使用的 Azure Stack 中心不负责。                                                                                                                                                                                                                                                                          |
| **InvalidCustomerSubscriptionId**   | **客户订阅标识符**无效。 请确保提供了有效的 Azure 订阅。                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **CustomerSubscriptionNotFound**    | 在 "**注册名称**" 下找不到**客户订阅标识符**。 请确保使用有效的 Azure 订阅，并使用 PUT 操作将订阅 ID 添加到注册。                                                   | 如果尝试 v 已将某个租户添加到订阅，但找不到与该注册关联的客户订阅，则会发生此错误。 客户尚未添加到注册，或订阅 ID 未正确写入。                                                                                                                                                                                                                                                                                                                                |
| **UnauthorizedCspRegistration**     | 提供的**注册名称**未获得使用多租户的批准。 发送电子邮件到 azstCSP@microsoft.com，并包含注册名称、资源组和注册中使用的订阅标识符。                                                                                    | 必须为 Microsoft 的多租户批准注册，然后才能开始向其添加租户。                                                                                                                                                                                                                                                                                                                                                                                             |
| **CustomerSubscriptionsNotAllowed** | 客户订阅操作不支持断开连接的客户。 若要使用此功能，请在使用许可时向其付费。                                                                                                                                                                    | 尝试添加租户的注册是一个容量注册;也就是说，创建注册后，使用参数 `BillingModel Capacity`。 只允许使用即用即付注册来添加租户。 必须使用参数 `BillingModel PayAsYouUse`重新注册。                                                                                                                                                                                                                                                                                          |
| **InvalidCSPSubscription**          | 提供的**客户订阅标识符**不是有效的 CSP 订阅。 请确保提供了有效的 Azure 订阅。                                                                                                                                                        | 这很可能是因为客户订阅键入错误。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **MetadataResolverBadGatewayError** | 其中一个上游服务器返回了意外错误。 请稍后再试。 如果问题持续出现，请联系支持人员。                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="next-steps"></a>后续步骤

- 详细了解[云解决方案提供商的使用情况报告基础结构](azure-stack-csp-ref-infrastructure.md)。
- 若要了解有关 CSP 计划的详细信息，请参阅[云解决方案](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要了解有关如何从 Azure Stack 中心检索资源使用情况信息的详细信息，请参阅[Azure Stack 中心中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
