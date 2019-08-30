---
title: Azure Stack 计划、套餐、配额和订阅概述 | Microsoft Docs
description: 向云操作员介绍 Azure Stack 计划、套餐、配额和订阅。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 72787bda5dd5fad372af33141e276b4852f86c39
ms.sourcegitcommit: 701685f0b59e5a3d1a8d39fe477b8df701a51cd2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70159520"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>计划、套餐、配额和订阅概述

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

在 [Azure Stack](azure-stack-overview.md) 中可以交付多种多样的服务，例如虚拟机、SQL Server 数据库、SharePoint、Exchange，甚至 [Azure 市场项](azure-stack-marketplace-azure-items.md)。 Azure Stack 操作员可以使用计划、套餐和配额，在 Azure Stack 中配置并交付此类服务。

套餐包含一个或多个计划，每个计划包含一个或多个服务。 通过创建计划并将其组合成不同的套餐，可以管理：

- 用户可以访问的服务和资源。
- 用户可以使用的资源量。
- 可以访问资源的区域。

交付服务时，需遵循以下概要步骤：

1. 添加要交付给用户的服务。
2. 创建包含一个或多个服务的计划。 创建计划时，需选择或创建配额，用于定义计划中每个服务的资源限制。
3. 创建包含一个或多个计划的套餐。 套餐可以包含基本计划和可选的附加计划。

创建套餐之后，用户可以订阅该套餐，以访问它提供的服务和资源。 用户可以根据需要订阅任意数量的产品/服务。 下图显示了某个用户订阅两个产品/服务的简单示例。 每个套餐包含一个或两个计划，每个计划可让用户访问服务。

![包含套餐和计划的租户订阅](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>计划

计划是对一个或多个服务的分组。 Azure Stack 操作员可以[创建计划](azure-stack-create-plan.md)并将其提供给用户。 反过来，用户可以订阅套餐，以便使用其所包括的计划和服务。 创建计划时，请务必设置配额、定义基本计划，并考虑包含可选的附加计划。

### <a name="quotas"></a>配额

为了帮助管理云容量，可以使用预配置的配额，或者为计划中的每个服务创建新配额。 配额定义用户订阅可以部署或使用的资源上限。 例如，配额可能允许用户最多创建五个虚拟机 (VM)。

> [!IMPORTANT]
> 在用户门户中出现可用的新配额或者强制实施更改的配额可能需要长达两小时的时间。

可按区域配置配额。 例如，为区域 A 提供计算服务的计划的配额可以是两个 VM。

>[!NOTE]
>在 Azure Stack 开发工具包中，只有一个区域（名为 *local*）可用。

详细了解 [Azure Stack 中的配额类型](azure-stack-quota-types.md)。

### <a name="base-plan"></a>基本计划

创建套餐时，服务管理员可以包含基本计划。 当用户订阅该套餐时，默认会包括这些基本计划。 当用户订阅时，即可访问这些基本计划中指定的所有资源提供程序（附带相应的配额）。

### <a name="add-on-plans"></a>附加计划

附加计划是添加到产品/服务的可选计划。 默认情况下，订阅中不包含附加计划。 套餐中提供的附加计划属于额外的计划（附带配额），订户可将其添加订阅中。 例如，可以提供具有有限资源的基本计划供试用，并为确定采用服务的客户提供具有更多实质资源的附加计划。

## <a name="offers"></a>套餐

套餐是创建的一个或多个计划的组，使用户能够订阅这些产品/服务。 例如，套餐 Alpha 可以包含计划 A 和计划 B，这两个计划分别提供一组计算服务和一组存储与网络服务。

[创建套餐](azure-stack-create-offer.md)时，必须至少包含一个基本计划，但也可以创建用户可添加到其订阅中的附加计划。

## <a name="subscriptions"></a>订阅

订阅是用户访问套餐的方式。 如果你是服务提供商的 Azure Stack 操作员，则用户（租户）可通过订阅你的套餐来购买你的服务。 如果你是组织的 Azure Stack 操作员，则用户（员工）可以订阅你提供的服务，而无需付费。

用户与套餐的每种组合都是一个唯一的订阅。 一个用户可订阅多个套餐，但每个订阅只适用于一个套餐。 计划、产品/服务和配额仅适用于唯一订阅, 不能在订阅之间共享。 用户创建的每个资源都与一个订阅相关联。

### <a name="default-provider-subscription"></a>默认提供商订阅

部署 Azure Stack 开发工具包时，系统会自动创建默认提供商订阅。 此订阅可用于管理 Azure Stack、部署其他资源提供程序，以及为用户创建计划和套餐。 出于安全和许可的原因，不应使用此订阅来运行客户工作负荷和应用程序。 不能更改默认提供商订阅的配额。

## <a name="next-steps"></a>后续步骤

有关计划和产品/服务的详细信息，请参阅[创建计划](azure-stack-create-plan.md)。
