---
title: 删除配额、计划、套餐和订阅
description: 了解如何删除 Azure Stack Hub 配额、计划、套餐和订阅。
author: bryanla
ms.topic: article
ms.date: 08/13/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 3c8c8b424d6ce555b19f4a77dbb8275b731f488e
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836117"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>删除配额、计划、套餐和订阅

本文介绍如何删除不再需要的配额、计划、套餐和订阅。 作为一般原则，只能删除未使用的内容。 例如，只能删除没有附属订阅的套餐。

订阅是此一般原则的例外：你可以删除包含资源的订阅，并将资源与订阅一起删除。

如果要删除配额，则必须通过使用该配额的任何计划和产品/服务进行恢复。 从产品/服务开始，确保它们没有订阅，删除每个产品/服务，然后删除使用该配额的计划等。

## <a name="delete-a-subscription"></a>删除订阅

若要删除某个订阅，请依次选择“所有服务”和“用户订阅”，显示系统上所有订阅的列表。******** 如果正在处理某个产品/服务，则还可以从此处选择**订阅**。

您可以从此列表中删除订阅，也可以使用 PowerShell 编写一个脚本来删除所有订阅。 [订阅-删除引用](/rest/api/azurestack/subscriptions/delete)中介绍了这些命令。

> [!CAUTION]
> 删除某个订阅也会删除其包含的所有数据和资源。

## <a name="delete-an-offer"></a>删除套餐

若要删除某个套餐，请在管理员门户中依次转到“所有服务”、“套餐”。******** 选择要删除的套餐，然后选择“删除”。****

![Azure Stack 中心管理员门户中删除订阅](media/azure-stack-delete-offer/delsub1.png)

只能删除未被任何订阅使用的套餐。 如果基于该产品/服务的订阅存在，则 "**删除**" 选项不可用。 对于这种情况，请参阅[删除订阅](#delete-a-subscription)部分。

## <a name="delete-a-plan"></a>删除计划

若要删除某个计划，请在管理员门户中依次转到“所有服务”、“计划”。******** 选择要删除的计划，然后选择“删除”。****

![Azure Stack 中心管理员门户中删除计划](media/azure-stack-delete-offer/delsub2.png)

只能删除未被任何套餐或订阅使用的计划。 如果有使用该计划的产品/服务，请删除该计划，使其失败，你将收到一条错误消息。 可以选择“父套餐”显示正在使用该计划的套餐列表。**** 有关删除套餐的详细信息，请参阅[删除套餐](#delete-an-offer)。

计划可能已直接作为附加计划添加到订阅，即使它们不是产品/服务的一部分也是如此。 在此情况下，必须先从使用这些附加计划的订阅中删除这些附加计划，然后才能删除该计划。

此外，如果某一计划是该订阅的给定资源的唯一源，则不能将其从订阅中删除。 例如，如果已将计划 A 添加到订阅1，而且它是提供订阅的网络配额的唯一计划，则无法将其从订阅中删除。 因此，不能删除它。

## <a name="edit-and-delete-a-quota"></a>编辑和删除配额

可以使用管理员门户查看和编辑现有配额：选择 "**区域管理**"，然后选择相关的资源提供程序，然后选择 "**配额**"。 也可以删除特定资源提供程序的配额。

![删除 Azure Stack 中心管理员门户中的配额](media/azure-stack-delete-offer/delsub3.png)

还可以使用以下 REST Api 删除某些配额：

- [Compute](/rest/api/azurestack/quotas%20(compute)/delete)
- [Network](/rest/api/azurestack/quotas%20(network)/delete)

> [!NOTE]
> 如果有任何当前计划使用该配额，则无法删除该配额。 必须先删除引用该配额的计划。

## <a name="next-steps"></a>后续步骤

- [创建订阅](azure-stack-subscribe-plan-provision-vm.md)
- [预配虚拟机](../user/azure-stack-create-vm-template.md)
