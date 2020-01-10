---
title: 删除配额、计划、产品和订阅 |Microsoft Docs
description: 了解如何删除 Azure Stack 集线器配额、计划、产品和订阅。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: c957997308c5b2aa0fb4e2cfb97f79860c04da82
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75811730"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>删除配额、计划、产品/服务和订阅

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

本文介绍如何删除不再需要的配额、计划、产品和订阅。 作为一般原则，只能删除未使用的内容。 例如，仅当不存在属于该产品/服务的订阅时，才可以删除该产品/服务。

订阅是此一般原则的例外情况：可以删除包含资源的订阅;并且资源将与订阅一起删除。

因此，如果你想要删除配额，则必须通过使用该配额的任何计划和产品/服务进行恢复：从产品/服务开始，确保它们没有订阅，删除每个产品/服务，然后删除使用该配额的计划等。

## <a name="delete-a-subscription"></a>删除订阅

若要删除订阅，请选择 "**所有服务**"，然后选择 "**用户订阅**"，以显示系统上所有订阅的列表。 如果正在处理某个产品/服务，则还可以从此处选择**订阅**。

你可以从此列表中删除订阅，也可以使用 PowerShell 编写一个脚本，该脚本将使用 "[订阅-删除" 引用](/rest/api/azurestack/subscriptions/delete)中记录的命令来删除所有订阅。

> [!CAUTION]
> 删除订阅还会删除其包含的所有数据和资源。

## <a name="delete-an-offer"></a>删除产品/服务

若要删除产品，请在管理员门户中，依次前往 "**所有服务**" 和 "**产品**/服务"。 选择要删除的产品/服务，然后选择 "**删除**"。

![delsub1](media/azure-stack-delete-offer/delsub1.png)

如果没有订阅，则只能删除该产品/服务。 如果订阅基于该产品/服务存在，则 "**删除**" 选项不可用。 在这种情况下，请参阅[删除订阅](#delete-a-subscription)部分。

## <a name="delete-a-plan"></a>删除计划

若要删除某个计划，请在管理员门户中转到 "**所有服务**"，然后依次指向 "**计划**"。 选择要删除的计划，然后选择 "**删除**"。

![delsub2](media/azure-stack-delete-offer/delsub2.png)

仅当没有可用的产品/服务或订阅时，才能删除该计划。 如果有使用该计划的产品/服务，请删除该计划，使其失败，你将收到一条错误消息。 您可以选择 "**父产品/服务**" 以显示使用该计划的产品/服务的列表。 有关删除产品/服务的详细信息，请参阅[删除产品/服务](#delete-an-offer)。

计划可能已直接添加到作为附加计划的订阅，即使它们不是产品/服务的一部分也是如此。 在这种情况下，必须从使用这些订阅的订阅中删除它们，然后才能删除计划。

此外，如果某一计划是该订阅的给定资源的唯一源，则不能将其从订阅中删除。 例如，如果已将计划 A 添加到订阅1，而且它是提供订阅的网络配额的唯一计划，则无法将其从订阅中删除。 因此，不能删除它。

## <a name="edit-and-delete-a-quota"></a>编辑和删除配额

可以使用管理员门户查看和编辑现有配额：选择 "**区域管理**"，然后选择相关的资源提供程序，然后单击 "**配额**"。 还可以删除某些资源提供程序的配额。

![delsub3](media/azure-stack-delete-offer/delsub3.png)

或者，可以使用以下 REST Api 删除某些配额：

- [计算](/rest/api/azurestack/quotas%20(compute)/delete)
- [Network](/rest/api/azurestack/quotas%20(network)/delete)
- [存储](/rest/api/azurestack/storagequotas/delete)

> [!NOTE]
> 如果有任何当前计划使用该配额，则无法删除该配额。 必须先删除引用配额的计划。

## <a name="next-steps"></a>后续步骤

- [创建订阅](azure-stack-subscribe-plan-provision-vm.md)
- [预配虚拟机](../user/azure-stack-create-vm-template.md)