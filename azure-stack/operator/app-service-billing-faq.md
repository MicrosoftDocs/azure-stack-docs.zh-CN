---
title: Azure Stack 中心帐单概述和常见问题 Azure App ServiceMicrosoft Docs
description: 有关如何按流量计费和计费 Azure App Service Azure Stack 中心的详细信息。
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
ms.openlocfilehash: 6b2383ada3a4de1d5c33db07d847b5ca2c5c5948
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881342"
---
# <a name="azure-app-service-on-azure-stack-hub-billing-overview-and-faq"></a>Azure Stack 中心帐单概述和常见问题 Azure App Service

本文说明了如何为云操作员提供 Azure Stack 中心的 Azure App Service，以及如何对其租户计费以便使用服务。

## <a name="billing-overview"></a>帐单概述

Azure Stack 中心云操作员选择将 Azure Stack 集线器上的 Azure App Service 部署到其 Azure Stack 集线器戳记上，以向其客户提供 Azure App Service 和 Azure Functions 的租户功能。 Azure App Service 的资源提供程序包含多个可在基础结构和辅助角色层之间划分的角色类型。

不会对基础结构角色计费，因为这些角色是服务的核心操作所必需的。 基础结构角色可根据需要进行扩展，以支持云操作员租户的需求。 基础结构角色如下所示：

- 控制器
- 管理角色
- 发布者
- 前端

辅助角色层由两种主要类型组成：共享和专用。 根据以下条件，将工作人员使用情况计费到云操作员。

## <a name="shared-workers"></a>共享辅助角色

共享辅助角色是多种租户的多租户和共享应用服务计划和基于消耗的 Azure 功能。 共享工作人员在 Azure App Service 资源提供程序中标记为 "就绪" 时发出用量计量。

## <a name="dedicated-workers"></a>专用辅助角色

专用工作人员与租户创建的应用服务计划相关联。 例如，在 S1 SKU 中，默认情况下，租户可以扩展到10个实例。 当租户创建 S1 应用服务计划时，Azure App Service 会将小型辅助角色层规模集中的一个实例分配给该租户的应用服务计划。 然后，分配的辅助角色将不再可分配给任何其他租户。 如果租户选择将应用服务计划扩展到10个实例，则将从可用池中删除9个更多的工作线程，并将其分配给租户的应用服务计划。

当专用工作人员处于以下情况时，将发出计量：

- 在 Azure App Service 资源提供程序中标记为 "就绪"。
- 分配给应用服务计划。

使用此计费模型，云操作员可以预配一个专用工作线程池，使其可供客户使用，而无需支付工作人员的租户应用服务计划。 

例如，假设小型辅助角色层中有20个辅助角色。 如果有五个客户创建了两个 S1 应用服务计划，每个客户将应用服务计划扩展到了两个实例，则没有辅助角色可用。 因此，没有任何客户或新客户的容量扩大或创建新的应用服务计划。 

云操作员可以通过查看 Azure Stack 中心管理的 Azure App Service 配置中的辅助角色层来查看每个辅助角色的可用辅助进程数。

![应用服务-辅助角色层屏幕][1]

## <a name="see-customer-usage-by-using-the-azure-stack-hub-usage-service"></a>请参阅使用 Azure Stack 集线器使用情况服务的客户使用情况

云操作员可以查询[Azure Stack 中心租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md) ，为其客户检索使用情况信息。 可以在 "[使用情况常见问题解答](azure-stack-usage-related-faq.md)" 中找到应用服务发出的、用于描述租户使用情况的所有单个计量。 然后，这些计量用于计算每个客户订阅的使用情况以计算费用。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>如何实现在必备组件中提供 SQL Server 和文件服务器基础结构的许可？

Azure App Service 资源提供程序所需的 SQL Server 和文件服务器基础结构的许可在[入门文章之前](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server)Azure Stack 中心的 Azure App Service 中进行了介绍。

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>使用情况常见问题解答列出了租户指标，而不是这些指标的价格。 可以在哪里找到它们？

作为云操作员，你可以免费将自己的定价模型应用于你的客户。 使用情况服务提供用量计量。 然后，你可以使用计量数量根据你确定的定价模型向客户收费。 设置定价的功能使操作员可以与其他 Azure Stack 中心操作员区分开来。

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>作为 CSP，如何为客户提供免费和共享的 Sku 来试用服务？

作为云操作员，你可以免费提供免费和共享 Sku，因为这些 Sku 托管在共享工作进程中。 若要将此成本降到最低，可以选择将共享辅助角色层向下扩展到最小值。 

例如，若要提供免费和共享的应用服务计划 Sku，并提供基于消耗的功能，你至少需要一个可用的 A1 实例。 共享辅助角色是多租户的，因此可以托管多个客户应用，每个应用由应用服务沙箱单独隔离和保护。 通过以这种方式缩放共享辅助角色层，可将开支限制为每月一个 vCPU 的成本。

然后，你可以选择创建配额，用于计划，该计划仅提供免费和共享 Sku，并限制你的客户可以创建的免费和共享应用服务计划数。

## <a name="sample-scripts-to-assist-with-billing"></a>帮助计费的示例脚本

Azure App Service 团队创建了一个示例 PowerShell 脚本，用于帮助查询 Azure Stack 中心使用情况服务。 云操作员可以使用这些示例脚本为租户准备自己的帐单。 示例脚本位于 GitHub 的[Azure Stack 集线器工具存储库](https://github.com/Azure/AzureStack-tools)中。 应用服务脚本位于 "[使用情况" 下的 AppService 文件夹](https://github.com/Azure/AzureStack-Tools/tree/master/Usage/AppService)中。

可用的示例脚本包括：

- [AppServiceBillingRecords](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceBillingRecords.ps1)：此示例从 Azure Stack 中心使用情况 API 中获取 Azure Stack 中心计费记录 Azure App Service。
- [AppServiceSubscriptionUsage](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceSubscriptionUsage.ps1)：此示例计算每个订阅的 Azure Stack 中心用量量 Azure App Service。 此脚本根据使用情况 API 的数据和云运营商每个计量器提供的价格计算用量。
- [UserSubscriptions](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Suspend-UserSubscriptions.ps1)：此示例根据云操作员指定的使用量限制挂起或启用订阅。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 中心租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
