---
title: Azure Stack 服务、计划、产品/服务、订阅概述
description: 作为云操作员，我想了解 Azure Stack 服务、计划、产品和订阅。
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: 3559dd6bb684ef3a1ab1c90efcc3dfc814c811f3
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72287110"
---
# <a name="service-plan-offer-subscription-overview"></a>服务、计划、产品/服务、订阅概述

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

[Microsoft Azure Stack](azure-stack-overview.md)是一种混合云平台，可让你从数据中心提供服务。 虚拟机、SQL Server 数据库、SharePoint、Exchange 甚至[Azure Marketplace 项](azure-stack-marketplace-azure-items.md)等服务。 作为服务提供商，可以向租户提供服务。 在企业或政府机构中，你可以向员工提供本地服务。

## <a name="overview"></a>概述

作为 Azure Stack 操作员，你可以通过使用产品/服务、计划和订阅来配置和传递服务。 产品/服务包含一个或多个计划，每个计划都包括一个或多个服务，每个服务都配置了配额。 通过创建计划并将其组合到不同的产品/服务中，用户可以订阅你的产品/服务并部署资源。 这允许你管理：

- 用户可以访问的服务和资源。
- 用户可以使用的资源量。
- 可以访问资源的区域。

若要交付服务，请执行以下高级步骤：

1. 规划服务产品，使用：
   - 基础服务，如计算、存储、网络或 Key Vault。
   - 附加服务，例如应用服务、SQL Server 或 MySQL 服务器。
2. 创建一个包含一个或多个服务的计划。 创建计划时，需选择或创建配额，用于定义计划中每个服务的资源限制。
3. 创建包含一个或多个计划的套餐。 套餐可以包含基本计划和可选的附加计划。

创建产品/服务后，你的用户可以订阅该产品/服务，以访问产品提供的服务和部署资源。 用户可以根据需要订阅任意数量的产品/服务。 下图显示了某个用户订阅两个产品/服务的简单示例。 每个服务都有一个或两个计划，每个计划都为他们提供对特定服务的访问权限。

![包含套餐和计划的租户订阅](media/azure-stack-key-features/image4.png)

## <a name="services"></a>Services

你可以提供[基础结构即服务](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) 服务，使用户能够构建按需计算基础结构，并通过 Azure Stack 用户门户进行预配和管理。

你还可以将[平台即服务](https://azure.microsoft.com/overview/what-is-paas/)（PaaS）服务部署到 Microsoft 和其他第三方提供商提供的 Azure Stack。 可以交付的 PaaS 服务包括但不限于：

- [应用服务](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [MySQL 服务器](azure-stack-mysql-resource-provider-deploy.md)

你还可以结合服务来集成和构建不同用户的复杂解决方案。

### <a name="quotas"></a>配额

为了帮助管理云容量，可以使用预配置的配额，或者为计划中的每个服务创建新配额。 配额定义用户订阅可以部署或使用的资源上限。 例如，配额可能允许用户最多创建五个虚拟机 (VM)。

> [!IMPORTANT]
> 在用户门户中出现可用的新配额或者强制实施更改的配额可能需要长达两小时的时间。

可按区域配置配额。 例如，为区域 A 提供计算服务的计划的配额可以是两个 VM。

>[!NOTE]
>在 Azure Stack 开发工具包中，只有一个区域（名为 *local*）可用。

详细了解 [Azure Stack 中的配额类型](azure-stack-quota-types.md)。

## <a name="plans"></a>计划

计划是对一个或多个服务的分组。 Azure Stack 操作员可以[创建计划](azure-stack-create-plan.md)并将其提供给用户。 反过来，用户可以订阅套餐，以便使用其所包括的计划和服务。 创建计划时，请务必设置配额、定义基本计划，并考虑包含可选的附加计划。

### <a name="base-plan"></a>基本计划

创建套餐时，服务管理员可以包含基本计划。 当用户订阅该套餐时，默认会包括这些基本计划。 当用户订阅时，即可访问这些基本计划中指定的所有资源提供程序（附带相应的配额）。

### <a name="add-on-plans"></a>附加计划

附加计划是添加到产品/服务的可选计划。 默认情况下，订阅中不包含附加计划。 套餐中提供的附加计划属于额外的计划（附带配额），订户可将其添加订阅中。 例如，可以提供具有有限资源的基本计划供试用，并为确定采用服务的客户提供具有更多实质资源的附加计划。

## <a name="offers"></a>产品

套餐是创建的一个或多个计划的组，使用户能够订阅这些产品/服务。 例如，套餐 Alpha 可以包含计划 A 和计划 B，这两个计划分别提供一组计算服务和一组存储与网络服务。

[创建套餐](azure-stack-create-offer.md)时，必须至少包含一个基本计划，但也可以创建用户可添加到其订阅中的附加计划。

计划套餐时，请记住以下几点：

**试用版套餐**：使用试用版产品/服务可以吸引新用户，然后他们可以升级到其他服务。 若要创建试用版套餐，请创建一个较小的[基本计划](service-plan-offer-subscription-overview.md#base-plan)，其中包含一个可选的更大加载项计划。 另外，还可以创建包含小型基础计划的试用产品/服务，并提供一个更大的 "即付即用" 计划。

**容量规划**：你可能会担心用户占用大量资源，阻塞所有用户使用的系统。 若要帮助提高性能，可以[配置带有配额的计划](service-plan-offer-subscription-overview.md#plans)以限定使用量上限。

**授权供应商**：可以授权其他人在你的环境中创建套餐。 例如，如果你是服务提供商，可以将此功能[委托](azure-stack-delegated-provider.md)给经销商。 或者，如果你是组织，则可以委托给其他部门/子公司。

## <a name="subscriptions"></a>Subscriptions

订阅使用户能够访问你的产品/服务。 如果你是服务提供商的 Azure Stack 操作员，则用户（租户）可通过订阅你的套餐来购买你的服务。 如果你是组织的 Azure Stack 操作员，则用户（员工）可以订阅你提供的服务，而无需付费。 

用户通过登录到 Azure Stack 来创建新订阅并获取对现有订阅的访问权限。 每个订阅表示与单个产品/服务的关联。 分配给一个订阅的产品/服务（及其计划和配额）不能与其他订阅共享。 用户创建的每个资源都与一个订阅相关联。

### <a name="default-provider-subscription"></a>默认提供商订阅

部署 Azure Stack 开发工具包时，系统会自动创建默认提供商订阅。 此订阅可用于管理 Azure Stack、部署其他资源提供程序，以及为用户创建计划和套餐。 出于安全和许可的原因，不应使用此订阅来运行客户工作负荷和应用程序。 不能更改默认提供商订阅的配额。

## <a name="next-steps"></a>后续步骤

若要详细了解如何创建计划、产品/服务和订阅，请先[创建一个计划](azure-stack-create-plan.md)。
