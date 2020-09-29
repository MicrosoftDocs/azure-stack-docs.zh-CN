---
title: Azure Stack Hub 服务、计划、套餐和订阅概述
description: Azure Stack Hub 服务、计划、套餐和订阅的概述。
author: BryanLa
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: 076ea0efb7307ce9a87b04c19608a5b3c723d3f4
ms.sourcegitcommit: e6665cfb15fae57218e58cd6de6053f16c1f9044
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89274069"
---
# <a name="azure-stack-hub-services-plans-offers-subscriptions-overview"></a>Azure Stack Hub 服务、计划、套餐和订阅概述

[Microsoft Azure Stack 中心](azure-stack-overview.md) 是一种混合云平台，可让你从数据中心提供服务。 服务包括虚拟机 (VM)、SQL Server 数据库、SharePoint、Exchange，甚至 [Azure 市场项](azure-stack-marketplace-azure-items.md)。 作为服务提供商，可以向租户提供服务。 在企业或政府机构中，你可以向员工提供本地服务。

## <a name="overview"></a>概述

Azure Stack Hub 操作员可以使用套餐、计划和订阅来配置及交付服务。 套餐包含一个或多个计划，而每个计划包含一个或多个配置有配额的服务。 通过创建计划并将其组合成不同的套餐，用户可以订阅该套餐和部署资源。 此结构可让你管理：

- 用户可以访问的服务和资源。
- 用户可以使用的资源量。
- 可以访问资源的区域。

若要交付服务，请遵循以下概要步骤：

1. 使用以下服务规划服务套餐：

   - 基础服务，例如计算、存储、网络或 Key Vault。
   - 增值服务，例如事件中心、应用服务、SQL Server 或 MySQL Server。

2. 创建包含一个或多个服务的计划。 创建计划时，需选择或创建配额，用于定义计划中每个服务的资源限制。
3. 创建包含一个或多个计划的套餐。 套餐可以包含基本计划和可选的附加计划。

创建套餐之后，用户可以订阅该套餐，以访问服务并部署资源。 用户可以根据需要订阅任意数量的产品/服务。 下图显示了某个用户订阅两个产品/服务的简单示例。 每个套餐包含一个或两个计划，每个计划可让用户访问特定的服务。

![包含套餐和计划的租户订阅](media/azure-stack-key-features/image4.png)

## <a name="services"></a>服务

你可以提供[基础结构即服务](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) 服务，使用户能够构建按需计算基础结构，并通过 Azure Stack Hub 用户门户进行预配和管理。

还可以通过 Microsoft 和第三方提供商为 Azure Stack Hub 部署[平台即服务](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) 服务。 可以部署的 PaaS 服务包括但不限于：

- [事件中心](event-hubs-rp-overview.md)
- [应用服务](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [MySQL Server](azure-stack-mysql-resource-provider-deploy.md)

还可以合并服务，以便为不同的用户集成和构建复杂的解决方案。

### <a name="quotas"></a>配额

为了帮助管理云容量，可以使用预配置的配额，或者为计划中的每个服务创建新配额。 配额定义用户订阅可以部署或使用的资源上限。 例如，配额可能允许用户最多创建五个 VM。

> [!IMPORTANT]
> 在用户门户中出现可用的新配额或者强制实施更改的配额可能需要长达两小时的时间。

可以按区域设置配额。 例如，为区域 A 提供计算服务的计划的配额可以是两个 VM。

>[!NOTE]
>在 Azure Stack 开发工具包 (ASDK) 中，只有一个区域（名为 *local*）可用。

详细了解 [Azure Stack Hub 中的配额类型](azure-stack-quota-types.md)。

## <a name="plans"></a>计划

计划是对一个或多个服务的分组。 Azure Stack Hub 操作员可以[创建计划](azure-stack-create-plan.md)并将其提供给用户。 反过来，用户可以订阅套餐，以便使用其所包括的计划和服务。 创建计划时，请务必设置配额、定义基本计划，并考虑包含可选的附加计划。

### <a name="base-plan"></a>基本计划

创建套餐时，服务管理员可以包含基本计划。 当用户订阅该套餐时，默认会包括这些基本计划。 当用户订阅时，即可访问这些基本计划中指定的所有资源提供程序（附带相应的配额）。

> [!NOTE]
> 如果某个产品/服务有多个基本计划，则计划的存储容量组合不能超过存储配额。

### <a name="add-on-plans"></a>附加计划

附加计划是添加到产品/服务的可选计划。 默认情况下，订阅中不包含附加计划。 套餐中提供的附加计划属于额外的计划（附带配额），订户可将其添加订阅中。 例如，可以提供具有有限资源的基本计划供试用，并为确定采用服务的客户提供具有更多实质资源的附加计划。

## <a name="offers"></a>产品

套餐是创建的一个或多个计划的组，使用户能够订阅这些产品/服务。 例如：套餐 Alpha 可以包含计划 A 和计划 B，这两个计划分别提供一组计算服务和一组存储与网络服务。

[创建套餐](azure-stack-create-offer.md)时，必须至少包含一个基本计划，但也可以创建用户可添加到其订阅中的附加计划。

计划套餐时，请记住以下几点：

**试用版套餐**：使用试用版套餐吸引新用户，然后这些用户可以再升级到其他服务。 若要创建试用版套餐，请创建一个较小的[基本计划](service-plan-offer-subscription-overview.md#base-plan)，其中包含一个可选的更大加载项计划。 另外，还可以创建包含小型基础计划的试用产品/服务，并提供一个更大的 "即付即用" 计划。

**容量规划**：你可能会担心用户占用大量资源，阻塞所有用户使用的系统。 若要帮助提高性能，可以[配置带有配额的计划](service-plan-offer-subscription-overview.md#plans)以限定使用量上限。

**授权供应商**：可以授权其他人在你的环境中创建套餐。 例如，如果你是服务提供商，可以将此功能[委托](azure-stack-delegated-provider.md)给经销商。 或者，如果你是组织，则可以委托给其他部门/子公司。

## <a name="subscriptions"></a>订阅

用户可以通过订阅访问套餐。 如果你是服务提供商的 Azure Stack Hub 操作员，则用户（租户）可通过订阅你的套餐来购买你的服务。 如果你是组织的 Azure Stack Hub 操作员，则用户（员工）可以订阅你提供的服务，而无需付费。

用户可以在登录到 Azure Stack Hub 后，创建新的订阅并获取现有订阅的访问权限。 每个订阅代表与单个套餐之间的关联。 分配给一个订阅的套餐（及其计划和配额）无法与其他订阅共享。 用户创建的每个资源都与一个订阅相关联。

作为 Azure Stack Hub 操作员，你可以查看有关租户订阅的信息，但无法访问这些订阅的内容，除非你是由该订阅的租户管理员通过 RBAC 显式添加的。 这使租户可以在 Azure Stack Hub 操作员和租户空间之间实施权力和职责分离。 

这种情况的例外情况是，订阅所有者无法向操作员提供访问订阅的权限，这要求管理员获取订阅的所有权，如[更改 Azure Stack Hub 用户订阅的账单所有者](azure-stack-change-subscription-owner.md)中所述。

### <a name="default-provider-subscription"></a>默认提供商订阅

部署 ASDK 时，系统会自动创建默认提供商订阅。 此订阅可用于管理 Azure Stack Hub、部署其他资源提供程序，以及为用户创建计划和套餐。 出于安全和许可的原因，不应使用此订阅来运行客户工作负荷和应用。 无法更改默认提供程序订阅的配额。

## <a name="next-steps"></a>后续步骤

若要详细了解如何创建计划、套餐和订阅，请从[创建计划](azure-stack-create-plan.md)开始。
