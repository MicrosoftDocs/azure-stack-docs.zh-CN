---
title: 在 Azure Stack 计费概述和常见问题的 azure 应用服务 |Microsoft Docs
description: 有关如何在 Azure Stack 上的 Azure 应用服务是计量和收费的详细信息。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: 7b11f00c98c6433ad035b6190789276ea5aa6fa7
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66213014"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>在 Azure Stack 计费概述和常见问题的 azure 应用服务

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文档中的指导说明了云操作员如何计费的产品/服务在 Azure Stack 上的 Azure 应用服务和如何它们又计费服务的使用情况其租户。

## <a name="billing-overview"></a>帐单概览

Azure Stack 云操作员选择到其 Azure Stack 模具上部署 Azure Stack 上的 Azure 应用服务，以便为客户提供 Azure 应用服务和 Azure Functions 的租户功能。  Azure 应用服务资源提供程序包含多个类型的基础结构和辅助角色层之间可以划分的角色。

基础结构角色不会计费，因为它们所需的服务的核心操作。  如需支持云操作员的租户的需求，可以扩大基础结构角色。  基础结构角色如下所示：

- 控制器
- 管理角色
- 发布服务器
- 前端

辅助角色层包含两种主要类型： 共享和专用。 工作线程使用情况计费到根据以下条件的云操作员。

## <a name="shared-workers"></a>共享辅助角色

共享辅助角色是多租户和主机免费和共享应用服务计划和多个租户基于消费的 Azure Functions。 共享辅助角色发出用量计量表标记为准备就绪可在 Azure 应用服务资源提供程序时。

## <a name="dedicated-workers"></a>专用辅助角色

专用工作线程已绑定到的租户创建应用服务计划。 例如，在 S1 SKU 中，默认情况下租户可以扩展到 10 个实例默认情况下。 因此，当租户创建一个 S1 应用服务计划时，Azure 应用服务会分配一个小型辅助角色层规模集到该租户的应用服务计划中的实例。 随后，已分配工作线程不再可用于分配给任何其他租户。  如果租户选择扩展到 10 的应用服务计划实例，然后进一步 9 工作者可用池中删除和分配给租户的应用服务计划。

计量器发出的专用辅助角色时：

- 标记为准备就绪可在 Azure 应用服务资源提供程序。
- 分配到应用服务计划。

因此，这种结算模型使云操作员可以预配的供客户无需支付工作线程，直到它们有效地保留了其租户的应用服务计划使用的专用辅助角色池。 例如，如果小型的辅助角色层中有 20 个辅助角色，然后具有 5 个客户创建两个 S1 应用服务计划每个，它们每个扩展最多两个实例的应用服务计划，然后将已没有可用的辅助角色。 因此，还将没有任何客户或新客户，若要横向扩展或创建新的应用服务计划的容量。 通过查看在 Azure Stack 管理的 Azure 应用服务配置中辅助角色层，云操作员可以查看每个辅助角色层的可用工作线程的当前数目。

![应用服务辅助角色层][1]

## <a name="see-customer-usage-using-the-azure-stack-usage-service"></a>请参阅使用 Azure Stack 使用情况服务的客户使用情况

云操作员可以查询[Azure Stack 租户使用情况 API](azure-stack-tenant-resource-usage-api.md)检索使用情况信息的客户，可以发现所有的单个标准可以该应用程序服务发出来描述中的租户使用情况[使用情况常见问题](azure-stack-usage-related-faq.md). 然后可以使用这些指标来计算每个使用计算费用的客户订阅的使用情况。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-pre-requisites"></a>如何实现 SQL Server 的许可证，先决条件中所需的文件服务器基础结构

SQL 和文件服务器基础结构，所需的 Azure 应用服务资源提供程序，介绍在 Azure Stack-Azure 应用服务中的授权[在开始之前](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server)一文。

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>租户指标，但不是在哪里找到它们这些指标的价格，列出了使用情况常见问题

云操作员可以自由地应用自己的定价模型添加到其最终客户。 使用率服务提供了用量计量，云操作员然后必须使用测定仪数量要向其客户基于它们所确定的定价模型收取费用。 具有此功能以设置定价启用运算符来区分从其他 Azure Stack 操作员。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 租户使用情况 API](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png