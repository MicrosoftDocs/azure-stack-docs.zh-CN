---
title: 适用于 Azure Stack 的云服务提供商的使用情况报告基础结构 | Microsoft Docs
description: Azure Stack 包含跟踪由云服务提供商 (CSP) 提供服务的租户的使用情况所需的基础结构，并将使用情况转发到 Azure。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: alfredop
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 1b8b83491211ae26493a6bb41c7f0f219f47f620
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64291899"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>适用于云服务提供商的使用情况报告基础结构

Azure Stack 包括实时跟踪使用情况所需的基础结构，因此会将使用情况转发到 Azure。 在 Azure 中，Azure Commerce 会处理使用情况数据并根据使用情况向相应的 Azure 订阅收费。 这与在全球版 Azure 云中监视使用情况跟踪的方式相同。

应注意的是，某些概念在全球版 Azure 与 Azure Stack 中是一致的。 Azure Stack 有本地订阅，其履行的角色类似于 Azure 订阅。 本地订阅仅在本地适用。 将使用情况转发到 Azure 时，本地订阅会映射到 Azure 订阅。

Azure Stack 有本地使用情况计量。 本地使用情况将映射到在 Azure Commerce 中使用的计量。 但是，计量 ID 是不同的。 在本地使用的计量多余 Microsoft 用于计费的计量。

Azure Stack 和 Azure 中的服务计价方式有一些差异。 例如，在 Azure Stack 中，只根据 vcore/小时对 VM 收费，对所有 VM 系列的费率是相同的，这不同于 Azure。 原因是，在全球版 Azure 中，不同的价格反映了不同的硬件。 在 Azure Stack 中，由客户提供硬件，因此没有理由根据不同的 VM 类实施不同的费率。

可以找到在 Commerce 中使用的 Azure Stack 计量的情况，并可在合作伙伴中心找到其价格，采用的方式与对 Azure 服务的方式一样：

1. 在合作伙伴中心，转到“仪表板菜单” > “定价与产品/服务”。
2. 在“基于使用情况的服务”下，选择“当前”。
3. 打开“全球 CSP 价目表中的 Azure”电子表格。
4. 以 **Region = Azure Stack** 为条件进行筛选。

## <a name="usage-and-billing-error-codes"></a>使用情况和计费错误代码

向注册添加租户时，可能会遇到以下错误消息。

| 错误                           | 详细信息                                                                                                                                                                                                                                                                                                                           | 注释                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **RegistrationNotFound**            | 提供的注册未找到。 确保正确提供以下信息：<br>1.订阅标识符（提供的值：订阅标识符）；<br>2.资源组（提供的值：资源组）；<br>3.注册名称（提供的值：注册名称）。                             | 当指向初始注册的信息不正确时，通常会发生此错误。 如需验证注册的资源组和名称，可以通过列出所有资源的方式在 Azure 门户中找到它。 如果找到多个注册资源，请查看属性中的 **CloudDeploymentID**，然后选择其 **CloudDeploymentID** 与云的 CloudDeploymentID 匹配的注册。 若要查找 **CloudDeploymentID**，可以在 Azure Stack 上使用以下 PowerShell：<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| **BadCustomerSubscriptionId**       | 提供的客户订阅标识符和注册名称订阅标识符不是由同一 Microsoft 云服务提供商拥有。 查看客户订阅标识符是否正确。 如果问题持续出现，请联系支持人员。 | 如果客户订阅为 CSP 订阅，但它汇总到 CSP 合作伙伴时，该合作伙伴不同于在初始注册中使用的订阅所汇总到的合作伙伴，则会发生此错误。 进行该检查是为了防止出现特定的情况，该情况会导致对与所使用的 Azure Stack 无关的 CSP 合作伙伴收费。                                                                                                                                                                                                                                                                          |
| **InvalidCustomerSubscriptionId**   | “客户订阅标识符”无效。 确保提供有效的 Azure 订阅。                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **CustomerSubscriptionNotFound**    | 在 _registration 名称下找不到客户订阅标识符。 确保使用有效的 Azure 订阅，并已使用 PUT 操作将订阅标识符添加到注册。                                                   | 尝试验证是否已向订阅添加租户但却找不到与注册关联的客户订阅时，会发生此错误。 尚未将客户添加到订阅，或者订阅 ID 写入错误。                                                                                                                                                                                                                                                                                                                                |
| **UnauthorizedCspRegistration**     | 提供的注册名称尚未获批使用多租户。 请向 azstCSP@microsoft.com 发送电子邮件，并提供注册名称、资源组以及在注册中使用的订阅标识符。                                                                                    | 注册需要获得 Microsoft 的批准，在允许使用多租户后，才能开始向其添加租户。                                                                                                                                                                                                                                                                                                                                                                                             |
| **CustomerSubscriptionsNotAllowed** | 不支持对断开连接的客户执行客户订阅操作。 若要使用此功能，请使用“即用即付”许可重新注册。                                                                                                                                                                    | 你尝试向其添加租户的注册是一个容量注册，也就是说，在创建注册时使用了参数 `BillingModel Capacity`。 只允许“即用即付”注册添加租户。 必须使用参数 `BillingModel PayAsYouUse` 重新注册。                                                                                                                                                                                                                                                                                          |
| **InvalidCSPSubscription**          | 提供的“客户订阅标识符”不是有效的 CSP 订阅。 确保提供有效的 Azure 订阅。                                                                                                                                                        | 这很有可能是错误地键入了客户订阅。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **MetadataResolverBadGatewayError** | 某个上游服务器返回了异外错误。 请稍后重试。 如果问题持续出现，请联系支持人员。                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>用于计费和使用情况的条款

以下条款和概念适用于 Azure Stack 中的使用情况和计费：

| 术语 | 定义 |
| --- | --- |
| 直接 CSP 合作伙伴 | 直接云解决方案提供商 (CSP) 合作伙伴直接从 Microsoft 接收 Azure 和 Azure Stack 使用情况的发票，然后对客户直接收费。 |
| 间接 CSP | 间接经销商与间接提供商（也称分销商）合作。 经销商负责招揽最终客户；间接提供商负责维持与 Microsoft 的计费关系、管理客户计费，以及提供其他服务（例如产品支持）。 |
| 最终客户 | 最终客户是企业和政府机构，拥有应用程序以及其他在 Azure Stack 上运行的工作负荷。 |

## <a name="next-steps"></a>后续步骤

- 若要详细了解 CSP 计划，请参阅[云解决方案提供商计划](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](azure-stack-billing-and-chargeback.md)。

<!-- Update_Description: wording update -->