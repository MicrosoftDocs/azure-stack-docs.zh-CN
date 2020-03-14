---
title: Azure Stack 中心服务、计划、产品/服务、订阅概述
description: 概述 Azure Stack 中心服务、计划、产品/服务和订阅。
author: BryanLa
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: 0e5d71335fb5b7086341c7c06ec503aae1a8e728
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294381"
---
# <a name="azure-stack-hub-services-plans-offers-subscriptions-overview"></a>Azure Stack 中心服务、计划、产品/服务、订阅概述

[Microsoft Azure Stack 中心](azure-stack-overview.md)是一种混合云平台，可让你从数据中心提供服务。 服务包括虚拟机（Vm）、SQL Server 数据库、SharePoint、Exchange 甚至[Azure Marketplace 项](azure-stack-marketplace-azure-items.md)。 作为服务提供商，你可以为你的租户提供服务。 在企业或政府机构，你可以向员工提供本地服务。

## <a name="overview"></a>概述

作为 Azure Stack 中心操作员，你可以通过使用产品/服务、计划和订阅来配置和提供服务。 产品/服务包含一个或多个计划，每个计划都包括一个或多个服务，每个服务都配置了配额。 通过创建计划并将其组合到不同的产品/服务中，用户可以订阅你的产品/服务并部署资源。 此结构可用于管理：

- 用户可以访问的服务和资源。
- 用户可以使用的资源量。
- 哪些区域有权访问这些资源。

若要交付服务，请执行以下高级步骤：

1. 规划服务产品，使用：

   - 基础服务，如计算、存储、网络或 Key Vault。
   - 增值服务，如应用服务、SQL Server 或 MySQL Server。

2. 创建一个包含一个或多个服务的计划。 创建计划时，请选择或创建用于定义计划中每个服务的资源限制的配额。
3. 创建具有一个或多个计划的产品/服务。 产品/服务可以包含基本计划和可选的附加计划。

创建产品/服务后，你的用户可以订阅该产品/服务来访问服务和部署资源。 用户可以根据需要订阅任意数量的产品/服务。 下图显示了已订阅两个产品/服务的用户的简单示例。 每个服务都有一个或两个计划，每个计划都为他们提供对特定服务的访问权限。

![具有产品/服务和计划的租户订阅](media/azure-stack-key-features/image4.png)

## <a name="services"></a>服务

你可以提供[基础结构即服务](https://azure.microsoft.com/overview/what-is-iaas/)（IaaS）服务，使用户能够构建按需计算基础结构，该基础结构在 Azure Stack Hub 用户门户中进行设置和管理。

你还可以从 Microsoft 和其他第三方提供商为 Azure Stack 中心部署[平台即服务](https://azure.microsoft.com/overview/what-is-paas/)（PaaS）服务。 可以交付的 PaaS 服务包括但不限于：

- [应用服务](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [MySQL 服务器](azure-stack-mysql-resource-provider-deploy.md)

你还可以结合服务来集成和构建不同用户的复杂解决方案。

### <a name="quotas"></a>配额

为了帮助管理云容量，你可以使用预配置的*配额*，或为计划中的每个服务创建新配额。 配额定义用户订阅可以预配或使用的资源上限。 例如，某个配额可能允许用户创建最多五个 VM。

> [!IMPORTANT]
> 可能需要长达两个小时的时间，新配额才能在用户门户中使用，或在强制更改配额之前出现。

可以按区域设置配额。 例如，为区域 A 提供计算服务的计划可能有两个 Vm 的配额。

>[!NOTE]
>在 Azure Stack 开发工具包（ASDK）中，仅有一个区域（名为*local*）可用。

详细了解[Azure Stack 集线器中的配额类型](azure-stack-quota-types.md)。

## <a name="plans"></a>计划

计划是对一个或多个服务的分组。 作为 Azure Stack 中心操作员，你可以[创建](azure-stack-create-plan.md)提供给用户的计划。 反过来，用户可以订阅套餐，以便使用其所包括的计划和服务。 创建计划时，请确保设置配额，定义基本计划，并考虑包含可选的附加计划。

### <a name="base-plan"></a>基本计划

创建产品/服务时，服务管理员可以包含基本计划。 默认情况下，当用户订阅该产品/服务时，将包含这些基本计划。 当用户订阅时，他们可以访问这些基本计划中指定的所有资源提供程序（带有相应的配额）。

### <a name="add-on-plans"></a>附加计划

外接程序计划是您向产品/服务添加的可选计划。 默认情况下，订阅中不包含附加计划。 外接程序计划是订阅服务器可以添加到其订阅中的其他计划（带有配额）。 例如，你可以为试用提供有限资源的基本计划，并为决定采用该服务的客户提供更重要资源的附加计划。

## <a name="offers"></a>产品

"产品/服务" 是你创建的一个或多个计划的组，以便用户可以订阅它们。 例如： "产品/服务" Alpha 可以包含 "计划"，它提供一组计算服务和计划 B，后者提供一组存储和网络服务。

当你[创建产品/服务](azure-stack-create-offer.md)时，必须至少包含一个基本计划，但你也可以创建用户可以添加到其订阅中的外接程序计划。

计划产品/服务时，请记住以下几点：

**试用版**产品/服务：使用试用版产品/服务，以吸引新用户，然后他们可以升级到其他服务。 若要创建试用产品/服务，请创建具有可选的更大附加计划的小型[基本计划](service-plan-offer-subscription-overview.md#base-plan)。 另外，还可以创建包含小型基础计划的试用产品/服务，并提供一个更大的 "即付即用" 计划。

**容量规划**：你可能会担心获取大量资源并堵塞所有用户的系统的用户。 为帮助提高性能，可以[使用配额配置计划](service-plan-offer-subscription-overview.md#plans)以上限使用情况。

**委托的提供程序**：你可以向其他人授予在你的环境中创建产品/服务的能力。 例如，如果你是服务提供商，则可以将此功能[委托](azure-stack-delegated-provider.md)给分销商。 或者，如果你是组织，可以委托给其他部门/子公司。

## <a name="subscriptions"></a>Subscriptions

订阅允许用户访问你的产品/服务。 如果你是服务提供商的 Azure Stack 中心操作员，你的用户（租户）通过订阅产品/服务来购买你的服务。 如果你是组织中的 Azure Stack 中心运营商，则你的用户（员工）可以订阅所提供的服务而无需付费。

用户通过登录到 Azure Stack 中心来创建新订阅并获取对现有订阅的访问权限。 每个订阅表示与单个产品/服务的关联。 分配给某个订阅的产品/服务（及其计划和配额）不能与其他订阅共享。 用户创建的每个资源都与一个订阅相关联。

### <a name="default-provider-subscription"></a>默认提供程序订阅

部署 ASDK 时，会自动创建默认提供程序订阅。 此订阅可用于管理 Azure Stack 中心、部署其他资源提供程序，以及为用户创建计划和产品/服务。 出于安全和许可原因，不应使用它来运行客户工作负荷和应用。 不能更改默认提供商订阅的配额。

## <a name="next-steps"></a>后续步骤

若要详细了解如何创建计划、产品/服务和订阅，请先[创建一个计划](azure-stack-create-plan.md)。
