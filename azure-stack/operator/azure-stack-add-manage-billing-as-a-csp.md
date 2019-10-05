---
title: 以云解决方案提供商的身份管理 Azure Stack 的使用情况和计费 |Microsoft Docs
description: 了解如何将 Azure Stack 注册为云解决方案提供商（CSP）并添加计费的客户。
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
ms.openlocfilehash: e14c57cb5d0444c68619e6be5db688d7b1542e93
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961874"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-solution-provider"></a>以云解决方案提供商的身份管理 Azure Stack 的使用情况和计费

适用范围：*Azure Stack 集成系统*

本文介绍如何将 Azure Stack 注册为云解决方案提供商（CSP）以及如何添加客户。

作为 CSP，你可以使用 Azure Stack 的各种客户。 每个客户在 Azure 中都有一个 CSP 订阅。 必须将使用情况从 Azure Stack 定向到每个用户订阅。

下图显示了选择共享服务帐户所需的步骤，以及如何向 Azure Stack 帐户注册 Azure 帐户。 注册后，即可载入最终客户：

[![将使用情况和管理作为云解决方案提供程序流程启用]以(media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "作为云解决方案提供程序启用使用和管理")的过程](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>创建 CSP 或 APSS 订阅

### <a name="csp-subscription-types"></a>CSP 订阅类型

选择用于 Azure Stack 的共享服务帐户类型。 可用于注册多租户 Azure Stack 的订阅类型为：

- 云解决方案提供商
- 合作伙伴共享服务订阅

#### <a name="azure-partner-shared-services"></a>Azure 合作伙伴共享服务

直接 CSP 或 CSP 经销商运营 Azure Stack 时，应首选使用 Azure 合作伙伴共享服务 (APSS) 订阅进行注册。

APSS 订阅与共享服务租户相关联。 注册 Azure Stack 时，你为作为订阅所有者的帐户提供凭据。 用于注册 Azure Stack 的帐户可能不同于用于部署的管理员帐户。 而且，这两个帐户不需要属于同一个域；可以通过已使用的租户进行部署。 例如，可以使用 `ContosoCSP.onmicrosoft.com` 进行部署，然后使用另一租户（例如 `IURContosoCSP.onmicrosoft.com`）进行注册。 进行每日的 Azure Stack 管理时，必须记住使用 `ContosoCSP.onmicrosoft.com` 登录。 需要执行注册操作时，请使用 `IURContosoCSP.onmicrosoft.com` 登录到 Azure。

有关 APSS 订阅及其创建方法的说明，请参阅[添加 Azure 合作伙伴共享服务](/partner-center/shared-services)。

#### <a name="csp-subscriptions"></a>CSP 订阅

当 CSP 经销商或最终客户 Azure Stack 操作时，CSP 订阅是注册的首选。

## <a name="register-azure-stack"></a>注册 Azure Stack

使用之前部分中的信息创建的应用订阅，将 Azure Stack 注册到 Azure。 有关详细信息，请参阅[在 Azure 订阅中注册 Azure Stack](azure-stack-registration.md)。

## <a name="add-end-customer"></a>添加最终客户

若要配置 Azure Stack 以便向其 CSP 订阅报告新租户的资源使用情况，请参阅[将使用情况和计费添加到 Azure Stack](azure-stack-csp-howto-register-tenants.md)。

## <a name="charge-the-right-subscriptions"></a>对正确的订阅收费

Azure Stack 使用名为“注册”的功能。 注册是一个存储在 Azure 中的对象。 注册对象用于记录哪个或哪些 Azure 订阅用来对给定的 Azure Stack 收费。 本部分讲述注册的重要性。

通过注册，Azure Stack 可以执行以下操作：

- 将 Azure Stack 使用情况数据转发到 Azure Commerce，并对 Azure 订阅计费。
- 使用多租户 Azure Stack 部署报告每个客户在不同订阅上的使用情况。 使用多租户时，Azure Stack 可以在同一 Azure Stack 实例上支持不同的组织。

每个 Azure Stack 都有一个默认的订阅和许多租户订阅。 默认订阅是在没有特定于租户的订阅的情况下收费的 Azure 订阅。 它必须是要注册的第一个订阅。 若要使用多租户使用情况报告，订阅必须是 CSP 或 APSS 订阅。

然后，对于每个使用 Azure Stack 的租户，使用 Azure 订阅对注册进行更新。 租户订阅必须是 CSP 类型，并且必须汇总到拥有默认订阅的合作伙伴处。 无法注册其他人的客户。

当 Azure Stack 将使用情况信息转发到全局 Azure 时，Azure 中的服务会咨询注册，并将每个租户的使用情况映射到相应的租户订阅。 如果租户尚未注册，则该使用情况会转发到租户源自的 Azure Stack 实例的默认订阅。

由于租户订阅是 CSP 订阅，因此会将其帐单发送到 CSP 合作伙伴，而使用情况信息对最终客户不可见。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 CSP 计划，请参阅[云解决方案提供商计划](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要了解有关如何从 Azure Stack 检索资源使用情况信息的详细信息，请参阅[Azure Stack 中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
