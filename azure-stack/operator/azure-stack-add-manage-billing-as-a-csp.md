---
title: 作为云解决方案提供商管理 Azure Stack 集线器的使用情况和计费 |Microsoft Docs
description: 了解如何注册 Azure Stack 集线器作为云解决方案提供程序（CSP）并添加计费的客户。
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
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 7fcd34efefa9c82085274837829f989908f751ba
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881087"
---
# <a name="manage-usage-and-billing-for-azure-stack-hub-as-a-cloud-solution-provider"></a>作为云解决方案提供商管理 Azure Stack 集线器的使用情况和计费

本文介绍如何将 Azure Stack 集线器注册为云解决方案提供商（CSP）以及如何添加客户。

作为 CSP，你可以使用 Azure Stack 中心与多元化客户合作。 每个客户在 Azure 中都有 CSP 订阅。 必须将 Azure Stack 中心的使用情况定向到每个用户订阅。

下图显示了选择共享服务帐户所需的步骤，以及如何向 Azure Stack 中心帐户注册 Azure 帐户。 注册后，你可以加入你的最终客户：

[![将使用情况和管理作为云解决方案提供程序启用的过程](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "将使用情况和管理作为云解决方案提供程序启用的过程")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>创建 CSP 或应用订阅

### <a name="csp-subscription-types"></a>CSP 订阅类型

选择用于 Azure Stack 中心的共享服务帐户的类型。 可用于注册多租户 Azure Stack 中心的订阅类型包括：

- 云解决方案提供商
- 合作伙伴共享服务订阅

#### <a name="azure-partner-shared-services"></a>Azure 合作伙伴共享服务

当直接 CSP 或 CSP 分发 Azure Stack 集线器运行时，Azure 合作伙伴共享服务（应用）订阅是注册的首选。

应用订阅与共享服务租户相关联。 注册 Azure Stack 中心时，将为订阅所有者的帐户提供凭据。 用于注册 Azure Stack 集线器的帐户可能不同于用于部署的管理员帐户。 此外，这两个帐户不需要属于同一域;可以使用已在使用的租户进行部署。 例如，你可以使用 `ContosoCSP.onmicrosoft.com`，然后使用不同的租户进行注册;例如，`IURContosoCSP.onmicrosoft.com`。 你必须记得在执行日常 Azure Stack 中心管理时使用 `ContosoCSP.onmicrosoft.com` 进行登录。 需要执行注册操作时，请使用 `IURContosoCSP.onmicrosoft.com` 登录到 Azure。

有关应用订阅以及如何创建它们的说明，请参阅[添加 Azure 合作伙伴共享服务](/partner-center/shared-services)。

#### <a name="csp-subscriptions"></a>CSP 订阅

当 CSP 经销商或最终客户 Azure Stack 集线器上操作时，CSP 订阅是注册的首选。

## <a name="register-azure-stack-hub"></a>注册 Azure Stack 集线器

使用之前部分中的信息创建的应用订阅，将 Azure Stack 集线器注册到 Azure。 有关详细信息，请参阅[注册 Azure Stack 中心与 Azure 订阅](azure-stack-registration.md)。

## <a name="add-end-customer"></a>添加最终客户

若要配置 Azure Stack 集线器，以便将新租户的资源使用情况报告给其 CSP 订阅，请参阅[将使用情况和计费添加到 Azure Stack 中心](azure-stack-csp-howto-register-tenants.md)。

## <a name="charge-the-right-subscriptions"></a>向正确的订阅收费

Azure Stack 集线器使用称为 "*注册*" 的功能。 注册是存储在 Azure 中的对象。 注册对象记录了要用于向给定 Azure Stack 中心收取哪些 Azure 订阅。 本部分介绍注册的重要性。

使用注册，Azure Stack 中心可以：

- 将 Azure Stack 集线器使用数据转发到 Azure Commerce 并计费 Azure 订阅。
- 使用多租户 Azure Stack 中心部署报告不同订阅上的每个客户的使用情况。 多租户支持 Azure Stack 集线器支持同一 Azure Stack 中心实例上的不同组织。

对于每个 Azure Stack 中心，都有一个默认订阅和多个租户订阅。 默认订阅是在没有特定于租户的订阅的情况下收费的 Azure 订阅。 它必须是要注册的第一个订阅。 要使多租户使用情况报表有效，订阅必须是 CSP 或应用订阅。

然后，使用 Azure Stack 集线器的每个租户的 Azure 订阅更新注册。 租户订阅必须为 CSP 类型，并且必须汇总到拥有默认订阅的合作伙伴。 不能注册其他人的客户。

Azure Stack 中心将使用情况信息转发到全局 Azure，Azure 中的服务会咨询注册，并将每个租户的使用情况映射到相应的租户订阅。 如果某个租户尚未注册，则该使用情况将转到它源自的 Azure Stack 中心实例的默认订阅。

由于租户订阅是 CSP 订阅，因此会将其帐单发送到 CSP 合作伙伴，而使用情况信息对最终客户不可见。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 CSP 计划的详细信息，请参阅[云解决方案提供商计划](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要了解有关如何从 Azure Stack 中心检索资源使用情况信息的详细信息，请参阅[Azure Stack 中心中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
