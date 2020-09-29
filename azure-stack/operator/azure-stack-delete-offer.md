---
title: 删除配额、计划、套餐和订阅
description: 了解如何删除 Azure Stack Hub 配额、计划、套餐和订阅。
author: bryanla
ms.topic: article
ms.date: 08/13/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 3ceea390376610ca1354a1b015ca91a5edd096e5
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/21/2020
ms.locfileid: "86566288"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>删除配额、计划、套餐和订阅

本文介绍如何删除不再需要的配额、计划、套餐和订阅。 一般原则是只能删除不使用的项。 例如，只能删除没有附属订阅的套餐。

订阅对此一般原则例外：可以删除包含资源的订阅；资源将随着订阅一起删除。

如果要删除配额，则必须重新完成使用该配额的所有计划和套餐。 从套餐开始，确保其没有订阅，删除每个套餐，然后删除使用该配额的计划，依此类推。

## <a name="delete-a-subscription"></a>删除订阅

若要删除某个订阅，请依次选择“所有服务”和“用户订阅”，显示系统上所有订阅的列表。**** **** 如果你正在处理某个套餐，则也可以从此处选择“订阅”****。

可以从此列表中删除订阅，或者使用 PowerShell 编写脚本来删除所有订阅。 这些命令记录在[“订阅 - 删除”参考](/rest/api/azurestack/subscriptions/delete)中。

> [!CAUTION]
> 删除某个订阅也会删除其包含的所有数据和资源。

## <a name="delete-an-offer"></a>删除套餐

若要删除某个套餐，请在管理员门户中依次转到“所有服务”、“套餐”。**** **** 选择要删除的套餐，然后选择“删除”。****

![在 Azure Stack Hub 管理员门户中删除订阅](media/azure-stack-delete-offer/delsub1.png)

只能删除未被任何订阅使用的套餐。 如果订阅基于套餐存在，则“删除”选项会不可用****。 对于这种情况，请参阅[删除订阅](#delete-a-subscription)部分。

## <a name="delete-a-plan"></a>删除计划

若要删除某个计划，请在管理员门户中依次转到“所有服务”、“计划”。**** **** 选择要删除的计划，然后选择“删除”。****

![在 Azure Stack Hub 管理员门户中删除计划](media/azure-stack-delete-offer/delsub2.png)

只能删除未被任何套餐或订阅使用的计划。 如果有套餐使用了该计划，删除该计划将会失败，并且会收到错误消息。 可以选择“父套餐”显示正在使用该计划的套餐列表。**** 有关删除套餐的详细信息，请参阅[删除套餐](#delete-an-offer)。

即使计划不是套餐的一部分，这些计划也可能已作为附加计划直接添加到订阅。 在此情况下，必须先从使用这些附加计划的订阅中删除这些附加计划，然后才能删除该计划。

此外，如果某个计划是订阅中给定资源的唯一源，则无法从该订阅中删除该计划。 例如，如果计划 A 已添加到订阅 1，并且它是向订阅提供网络配额的唯一计划，则无法从该订阅中删除该计划。 因此也就无法删除该计划。

## <a name="edit-and-delete-a-quota"></a>编辑和删除配额

可以使用管理员门户查看和编辑现有配额：选择“区域管理”，选择相关的资源提供程序，然后选择“配额”**** ****。 也可以删除特定资源提供程序的配额。

![在 Azure Stack Hub 管理员门户中删除配额](media/azure-stack-delete-offer/delsub3.png)

或者，可以使用以下 REST API 删除某些配额：

- [计算](/rest/api/azurestack/quotas/delete)
- [网络](/rest/api/azurestack/quotas%20(network)/delete)
- [存储](/rest/api/azurestack/storagequotas/delete)

> [!NOTE]
> 如果当前有任何计划正在使用某个配额，则无法删除该配额。 必须先删除引用该配额的计划。

## <a name="next-steps"></a>后续步骤

- [创建订阅](azure-stack-subscribe-plan-provision-vm.md)
- [预配虚拟机](../user/azure-stack-create-vm-template.md)
