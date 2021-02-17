---
title: 以云解决方案提供商的身份管理 Azure Stack 集线器使用情况和计费
description: 了解如何将 Azure Stack Hub 注册为云解决方案提供商 (CSP) 并添加客户以便计费。
author: sethmanheim
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 5d7a6dc3f0087f50fa97a0e6f61eee30037f54cc
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562544"
---
# <a name="manage-usage-and-billing-for-azure-stack-hub-as-a-cloud-solution-provider"></a>管理充当云解决方案提供商的 Azure Stack Hub 的使用情况和计费

本文介绍如何将 Azure Stack Hub 注册为云解决方案提供商 (CSP) 以及如何添加客户。

作为 CSP，你将与使用 Azure Stack Hub 的各种客户协作。 每个客户在 Azure 中都有一个 CSP 订阅。 必须将使用情况从 Azure Stack Hub 定向到每个用户订阅。

下图展示了必须执行哪些步骤，以便选择共享服务帐户并将 Azure 帐户注册到 Azure Stack Hub 帐户。 注册后，即可载入最终客户：

[![以云解决方案提供商身份启用使用情况跟踪和管理功能的过程](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.svg "以云解决方案提供商身份启用使用情况跟踪和管理功能的过程")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.svg)

## <a name="create-a-csp-or-apss-subscription"></a>创建 CSP 或 APSS 订阅

### <a name="csp-subscription-types"></a>CSP 订阅类型

选择用于 Azure Stack Hub 的共享服务帐户的类型。 可以用来注册多租户 Azure Stack Hub 的订阅类型为：

- 云解决方案提供商
- 合作伙伴共享服务订阅

我们创建了一个教程视频，帮助你了解如何管理你的多租户权限：

> [!VIDEO https://www.youtube.com/embed/ZP6jkbLeS34]

#### <a name="azure-partner-shared-services"></a>Azure 合作伙伴共享服务

当直接 CSP 或 CSP 分销商 Azure Stack 集线器上操作时，Azure 合作伙伴共享服务 (应用) 订阅是注册的首选。

APSS 订阅与共享服务租户相关联。 注册 Azure Stack 中心时，将为订阅所有者的帐户提供凭据。 用于注册 Azure Stack 集线器的帐户可能不同于用于部署的管理员帐户。 而且，这两个帐户不需要属于同一个域；可以通过已使用的租户进行部署。 例如，可以使用 `ContosoCSP.onmicrosoft.com` 进行部署，然后使用另一租户（例如 `IURContosoCSP.onmicrosoft.com`）进行注册。 你必须记得在 `ContosoCSP.onmicrosoft.com` 执行日常 Azure Stack 集线器管理时使用登录。 需要执行注册操作时，请使用 `IURContosoCSP.onmicrosoft.com` 登录到 Azure。

有关 APSS 订阅及其创建方法的说明，请参阅[添加 Azure 合作伙伴共享服务](/partner-center/shared-services)。

#### <a name="csp-subscriptions"></a>CSP 订阅

当 CSP 经销商或最终客户 Azure Stack 集线器上操作时，CSP 订阅是注册的首选。

## <a name="register-azure-stack-hub"></a>注册 Azure Stack Hub

使用之前部分中的信息创建的应用订阅，将 Azure Stack 集线器注册到 Azure。 有关详细信息，请参阅 [注册 Azure Stack 中心与 Azure 订阅](azure-stack-registration.md)。

## <a name="add-end-customer"></a>添加最终客户

若要配置 Azure Stack 集线器，以便将新租户的资源使用情况报告给其 CSP 订阅，请参阅 [将使用情况和计费添加到 Azure Stack 中心](azure-stack-csp-howto-register-tenants.md)。

## <a name="charge-the-right-subscriptions"></a>对正确的订阅收费

Azure Stack 集线器使用称为 " *注册*" 的功能。 注册是一个存储在 Azure 中的对象。 注册对象记录哪个 Azure 订阅 () 用于为给定的 Azure Stack 中心收取费用。 本部分讲述注册的重要性。

使用注册，Azure Stack 中心可以：

- 将 [Azure Stack 集线器使用数据](azure-stack-billing-and-chargeback.md) 转发到 azure Commerce 并计费 azure 订阅。
- 使用多租户 Azure Stack 中心部署报告不同订阅上的每个客户的使用情况。 多租户支持 Azure Stack 集线器支持同一 Azure Stack 中心实例上的不同组织。

对于每个 Azure Stack 中心，都有一个默认订阅和多个租户订阅。 默认订阅为 Azure 订阅。在没有特定于租户的订阅时，会对该 Azure 订阅收费。 它必须是要注册的第一个订阅。 若要使用多租户使用情况报告，订阅必须是 CSP 或 APSS 订阅。

然后，使用 Azure Stack 集线器的每个租户的 Azure 订阅更新注册。 租户订阅必须是 CSP 类型，并且必须汇总到拥有默认订阅的合作伙伴处。 无法注册其他人的客户。

Azure Stack 中心将使用情况信息转发到全局 Azure，Azure 中的服务会咨询注册，并将每个租户的使用情况映射到相应的租户订阅。 如果某个租户尚未注册，则该使用情况将转到它源自的 Azure Stack 中心实例的默认订阅。

由于租户订阅是 CSP 订阅，其账单将发送至 CSP 合作伙伴，并且使用情况信息对最终客户不可见。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 CSP 计划，请参阅[云解决方案提供商计划](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要了解有关如何从 Azure Stack 中心检索资源使用情况信息的详细信息，请参阅 [Azure Stack 中心中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
