---
title: 在 Azure Stack 计费概述和常见问题的 azure 应用服务 |Microsoft Docs
description: 有关 Azure Stack 上的 Azure 应用服务是按流量计费的方式以及计费的详细信息。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 8b17020419183067793309055a6487f31da51246
ms.sourcegitcommit: d67ae2877e3b049bf70b2567b917e55394fb8984
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67712678"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>在 Azure Stack 计费概述和常见问题的 azure 应用服务

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍云操作员如何计费的产品/服务在 Azure Stack 上的 Azure 应用服务，他们可以如何计费的服务使用其租户。

## <a name="billing-overview"></a>帐单概览

Azure Stack 云操作员选择将 Azure Stack 上的 Azure 应用服务部署到其 Azure Stack 戳向其客户提供 Azure 应用服务和 Azure Functions 的租户功能上。 Azure 应用服务资源提供程序包含多个类型的基础结构和辅助角色层之间可以划分的角色。

基础结构角色不会计费，因为它们是服务的核心操作所必需的。 如需支持云操作员的租户的需求，可以扩大基础结构角色。 基础结构角色如下所示：

- 控制器
- 管理角色
- 发布者
- 前端

辅助角色层包含两种主要类型： 共享和专用。 工作线程使用情况计费到根据以下条件的云操作员。

## <a name="shared-workers"></a>共享辅助角色

共享辅助角色是多租户和主机免费和共享应用服务计划以及基于消费的 Azure functions 用于许多租户。 共享辅助角色发出用量计量表标记为准备就绪可在 Azure 应用服务资源提供程序时。

## <a name="dedicated-workers"></a>专用辅助角色

专用工作线程已绑定到租户创建的应用服务计划。 例如，在 S1 SKU 中，租户可以扩展到 10 个实例默认情况下。 当租户创建一个 S1 应用服务计划时，Azure 应用服务会分配一个小型辅助角色层规模集到该租户的应用服务计划中的实例。 分配的工作就不再可用于分配给任何其他租户。 如果租户选择应用服务计划缩放为 10 个实例，九个更多辅助角色已从可用的池并分配给租户的应用服务计划。

计量器发出的专用辅助角色时：

- 标记为准备就绪可在 Azure 应用服务资源提供程序。
- 分配到应用服务计划。

这种结算模型使云操作员可以预配的供客户使用无需支付工作线程，直到它们有效地保留其租户的应用服务计划的专用辅助角色池。 

例如，假设在小型的辅助角色层中有 20 个辅助角色。 如果您有五个客户创建两个 S1 应用服务计划每个，它们每个扩展应用服务计划最多两个实例，则必须没有可用的辅助角色。 因此，此外还有没有任何客户或新客户，若要横向扩展或创建新的应用服务计划的容量。 

通过查看在 Azure Stack 管理的 Azure 应用服务配置中辅助角色层，云操作员可以查看每个辅助角色层的可用工作线程的当前数目。

![应用服务-辅助角色层屏幕][1]

## <a name="see-customer-usage-by-using-the-azure-stack-usage-service"></a>使用 Azure Stack 使用情况服务来查看客户的使用情况

云操作员可以查询[Azure Stack 租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)要检索其客户的使用情况信息。 您可以找到所有应用服务会发出来描述中的租户使用情况的单独计量[使用情况常见问题](azure-stack-usage-related-faq.md)。 这些指标则用于计算每个客户订阅计算费用的使用情况。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>如何实现 SQL Server 的许可证，在先决条件中所需的文件服务器基础结构？

Azure Stack 上的 Azure 应用服务中介绍了 SQL Server 和文件服务器基础结构，所需的 Azure 应用服务资源提供程序的许可[在开始之前](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server)一文。

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>使用情况常见问题列出租户指标，但不是为这些指标的价格。 在哪里找到它们？

云操作员，你可以自由地应用自己的定价模型添加到你的客户。 使用率服务提供了用量计量。 然后可以使用测定仪数量来向你客户基于你所确定的定价模型收取费用。 设置定价的能力使操作员能够与其他 Azure Stack 操作员区分开来。

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>作为 CSP，如何我提供免费和共享的 Sku，供客户试用服务？

云操作员，则会产生成本为提供免费和共享的 Sku，因为它们托管在共享辅助角色。 该成本降到最低，您可以选择纵向缩减到最低限度的共享辅助角色层。 

例如，若要提供免费和共享应用服务计划的 Sku 并提供基于消费的函数，需要一个 A1 实例可用的最小值。 共享辅助角色都是多租户，因此他们可以托管多个客户应用，每个单独隔离和保护的应用服务沙盒。 通过以这种方式扩展共享辅助角色层，可以限制每个月的 1 vCPU 的成本在开支。

然后可以选择创建的配额，用于计划，它仅提供了免费的共享 Sku，可以创建您的客户的免费和共享应用服务计划的数量限制。

## <a name="sample-scripts-to-assist-with-billing"></a>示例脚本来帮助进行计费

Azure 应用服务团队创建了示例 PowerShell 脚本来帮助进行查询 Azure Stack 使用情况服务。 云操作员可以使用这些示例脚本来准备为其租户自己计费。 示例脚本位于[Azure Stack 工具存储库](https://github.com/Azure/AzureStack-tools)在 GitHub 中。 应用服务脚本位于[AppService 文件夹下使用情况](https://github.com/Azure/AzureStack-Tools/tree/master/Usage/AppService)。

可用的示例脚本是：

- [Get-AppServiceBillingRecords](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceBillingRecords.ps1):此示例从 Azure Stack 使用情况 API 提取 Azure Stack 计费记录上的 Azure 应用服务。
- [Get-AppServiceSubscriptionUsage](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceSubscriptionUsage.ps1):此示例计算每个订阅的 Azure Stack 使用情况数量上的 Azure 应用服务。 此脚本计算基于使用情况 API 和由云操作员提供每个计量的价格中的数据的使用情况数量。
- [挂起 UserSubscriptions](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Suspend-UserSubscriptions.ps1):此示例将挂起或启用基于使用情况限制云运算符指定的订阅。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
