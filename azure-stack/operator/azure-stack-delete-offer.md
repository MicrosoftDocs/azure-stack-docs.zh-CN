---
title: 删除配额、 计划、 产品和订阅 |Microsoft Docs
description: 了解如何删除 Azure Stack 配额、 计划、 产品和订阅。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: sethm
ms.reviewer: ''
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 5a15d55a053fbacd3a947bd61d7186d93be77dbf
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987713"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>删除配额、 计划、 产品和订阅

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍如何删除配额、 计划、 产品/服务和再也不需要的订阅。 作为一般原则，可以删除仅什么未被使用。 例如，正在删除产品/服务时，才有可能属于该产品/服务没有订阅。

订阅是此一般原则的例外： 可以删除包含资源; 的订阅和订阅也会删除资源。

因此，如果你想要删除配额，则必须处理返回到任何计划和产品/服务，使用该配额： 从产品/服务，确保它们没有任何订阅，删除每个产品/服务，然后删除计划的使用配额，等等。

## <a name="delete-a-subscription"></a>删除订阅

若要删除的订阅，请选择**所有服务**，然后**用户订阅**，以便在系统上显示的所有订阅的列表。 如果正在某个产品/服务，还可以选择**订阅**从那里。

可以从此列表中删除订阅或使用 PowerShell 编写脚本为你使用的命令中所述的所有订阅中都删除[订阅-删除引用](/rest/api/azurestack/subscriptions/delete)。

> [!CAUTION]
> 删除订阅也会删除任何数据和它所包含的资源。

## <a name="delete-an-offer"></a>删除产品/服务

若要删除的产品/服务，在管理员门户中，请转到**所有服务**，然后**提供了**。 选择你想要删除，然后选择产品/的服务**删除**。

![delsub1](media/azure-stack-delete-offer/delsub1.png)

您只能删除产品/服务时不没有使用它的任何订阅。 如果存在订阅产品/服务，基于**删除**选项灰显。在这种情况下，请参阅[删除订阅](#delete-a-subscription)部分。

## <a name="delete-a-plan"></a>删除计划

若要删除某个计划，请在管理员门户中转到**所有服务**，然后**计划**。 选择你想要删除，然后选择的计划**删除**。

![delsub2](media/azure-stack-delete-offer/delsub2.png)

当没有任何产品/服务或使用它的订阅时，仅可以删除计划。 如果有产品/服务，使用计划、 删除计划，允许其发生故障，您将收到一条错误消息。 可以选择**父产品/服务**以显示使用计划的产品/服务的列表。 有关删除产品/服务的详细信息，请参阅[删除产品/服务](#delete-an-offer)。

计划可能具有已直接向订阅中添加为加载项计划，即使它们不是产品/服务的一部分。 在这种情况下，它们必须删除使用它们，然后才能删除该计划的订阅。

此外，计划不能从订阅删除它是否为该订阅给定的资源的唯一来源。 例如，如果计划 A 已添加到订阅 1，并且它是提供网络配额对订阅的唯一计划，它不能从订阅删除。 因此，无法删除它。

## <a name="edit-and-delete-a-quota"></a>编辑和删除配额

你可以查看和编辑使用管理员门户的现有配额： 选择**区域管理**，然后选择相关的资源提供程序，然后单击**配额**。 对于某些资源提供商，还可以删除配额。

![delsub3](media/azure-stack-delete-offer/delsub3.png)

或者，可以删除使用这些 REST Api 的某些配额：

- [计算](/rest/api/azurestack/quotas%20(compute)/delete)
- [网络](/rest/api/azurestack/quotas%20(network)/delete)
- [存储](/rest/api/azurestack/storagequotas/delete)

> [!NOTE]
> 如果没有使用它的任何当前计划，则无法删除配额。 必须先删除引用该配额的计划。

## <a name="next-steps"></a>后续步骤

- [创建订阅](azure-stack-subscribe-plan-provision-vm.md)
- [预配虚拟机](../user/azure-stack-create-vm-template.md)