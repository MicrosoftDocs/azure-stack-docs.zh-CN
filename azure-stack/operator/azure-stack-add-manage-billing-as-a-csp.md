---
title: 管理充当云服务提供商的 Azure Stack 的使用情况和计费 | Microsoft Docs
description: 详细介绍如何将 Azure Stack 注册为云提供商 (CSP) 并添加客户以便计费。
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
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 0cd8503ac57c65fb4bcf2c75367caa75bfcc2fe5
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985835"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>管理充当云服务提供商的 Azure Stack 的使用情况和计费

*适用于：Azure Stack 集成系统*

本文详细介绍如何将 Azure Stack 注册为云提供商 (CSP) 并添加客户。

作为 CSP，你将与使用你的 Azure Stack 的各种客户协作。 每个客户在 Azure 中都有一个 CSP 订阅。 必须将使用情况从 Azure Stack 定向到每个用户订阅。

下图展示了必须执行哪些步骤，以便选择共享服务帐户并将 Azure 帐户注册到 Azure Stack 帐户。 注册后，即可载入最终客户：

[![以云服务提供商身份启用使用情况和管理的过程](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "以云服务提供商身份启用使用情况和管理的过程")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>创建 CSP 或 APSS 订阅

### <a name="cloud-service-provider-subscription-types"></a>云服务提供商订阅类型

选择用于 Azure Stack 的共享服务帐户的类型。 可以用来注册多租户 Azure Stack 的订阅类型为：

- 云服务提供商
- 合作伙伴共享服务订阅

#### <a name="azure-partner-shared-services"></a>Azure 合作伙伴共享服务

直接 CSP 或 CSP 经销商运营 Azure Stack 时，应首选使用 Azure 合作伙伴共享服务 (APSS) 订阅进行注册。

APSS 订阅与共享服务租户相关联。 注册 Azure Stack 时，请提供帐户的凭据，该帐户是订阅的所有者。 用于注册 Azure Stack 的帐户可以不同于用于部署的管理员帐户。 而且，这两个帐户不需要属于同一个域；可以通过已使用的租户进行部署。 例如，可以使用 `ContosoCSP.onmicrosoft.com` 进行部署，然后使用另一租户（例如 `IURContosoCSP.onmicrosoft.com`）进行注册。 进行每日的 Azure Stack 管理时，必须记住使用 `ContosoCSP.onmicrosoft.com` 登录。 需要执行注册操作时，请使用 `IURContosoCSP.onmicrosoft.com` 登录到 Azure。

有关 APSS 订阅及其创建方法的说明，请参阅[添加 Azure 合作伙伴共享服务](https://msdn.microsoft.com/partner-center/shared-services)。

#### <a name="csp-subscriptions"></a>CSP 订阅

在 CSP 经销商或最终客户运营 Azure Stack 的情况下，应首选云服务提供商 (CSP) 订阅进行注册。

## <a name="register-azure-stack"></a>注册 Azure Stack

使用根据前一部分中的信息创建的 APSS 订阅向 Azure 注册 Azure Stack。 有关详细信息，请参阅[将 Azure Stack 注册到 Azure 订阅](azure-stack-registration.md)。

## <a name="add-end-customer"></a>添加最终客户

若要配置 Azure Stack，以便在新租户使用资源时，系统将其使用情况报告给云服务提供商 (CSP) 订阅，请参阅[将租户添加到 Azure Stack 以获取使用情况和计费信息](azure-stack-csp-howto-register-tenants.md)。

## <a name="charge-the-right-subscriptions"></a>对正确的订阅收费

Azure Stack 使用名为“注册”的功能。 注册是一个存储在 Azure 中的对象。 注册对象用于记录哪个或哪些 Azure 订阅用来对给定的 Azure Stack 收费。 本部分讲述注册的重要性。

Azure Stack 可以通过注册执行以下操作：

- 将 Azure Stack 使用情况数据转发到 Azure Commerce，并对 Azure 订阅计费。
- 报告每个客户 ' s 上与多租户 Azure Stack 部署的不同订阅的使用情况。 使用多租户时，Azure Stack 可以在同一 Azure Stack 实例上支持不同的组织。

每个 Azure Stack 都有一个默认的订阅和许多租户订阅。 默认订阅为 Azure 订阅。在没有特定于租户的订阅时，会对该 Azure 订阅收费。 它必须是要注册的第一个订阅。 若要使用多租户使用情况报告，订阅必须是 CSP 或 APSS 订阅。

然后，对于每个使用 Azure Stack 的租户，使用 Azure 订阅对注册进行更新。 租户订阅必须是 CSP 类型，并且必须汇总到拥有默认订阅的合作伙伴处。 无法注册其他人的客户。

当 Azure Stack 将使用情况信息转发到全球版 Azure 时，Azure 中的一项服务会查阅该注册，并将每个租户的使用情况映射到相应的租户订阅。 如果租户尚未注册，则该使用情况会转发到租户源自的 Azure Stack 实例的默认订阅。

由于租户订阅是 CSP 订阅，其账单将发送至 CSP 合作伙伴，其使用情况信息对最终客户不可见。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 CSP 计划，请参阅[云解决方案提供商计划](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
