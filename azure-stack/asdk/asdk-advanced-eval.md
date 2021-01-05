---
title: 高级 ASDK 评估任务
description: 了解高级 Azure Stack 开发工具包 (ASDK) 评估任务。
author: PatAltimore
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 912fb68695d281ee83273ff49f2f2aa24e468247
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873600"
---
# <a name="advanced-asdk-evaluation-tasks"></a>高级 ASDK 评估任务
在熟悉 Azure Stack 开发工具包 (ASDK) 服务的基本功能后，可以通过测试更高级的方案，来进一步加深对 Azure Stack 的了解。 Azure Stack 操作员文档中全面阐述了这些更高级的评估任务。

> [!NOTE]
> 尽管 ASDK 和生产以及多节点 Azure Stack 部署支持许多操作员任务，但 ASDK 部署并非支持所有使用方案。 有关详细信息，请参阅 [ASDK 和多节点 Azure Stack 的差异](asdk-what-is.md#asdk-and-multi-node-azure-stack-hub-differences)。

## <a name="delegate-offers-in-azure-stack"></a>在 Azure Stack 中委托套餐
Azure Stack 操作员经常需要委托他人来管理创建套餐以及注册用户。 例如，服务提供商可能需要经销商来代表他们注册和管理客户。 或者，如果你是企业的中心 IT 小组的成员，则可能需要下属公司在你不参与的情况下注册用户。

如果[在 Azure Stack 中委托套餐](../operator/azure-stack-delegated-provider.md)，接触和管理的用户数量比直接操作更多，从而帮助完成这些任务。

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>将 SQL 数据库提供给 Azure Stack 用户使用
Azure Stack 操作员可以创建套餐，使用户（租户）能够创建 SQL 数据库，以配合其云原生应用、网站和工作负荷使用。 通过向用户提供这些基于云的按需自定义数据库的访问权限，可以节省用户的时间和资源。

使用 SQL Server 资源提供程序适配器可[将 SQL 数据库作为 Azure Stack 的服务提供给 Azure Stack 用户](../operator/azure-stack-sql-resource-provider.md?view=azs-2002)。 安装资源提供程序之后，请将它连接到一个或多个 SQL Server 实例。

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>将 Web 和 API 应用提供给 Azure Stack 用户使用
Azure Stack 操作员可以创建套餐，允许用户（租户）创建 Azure Functions、Web 和 API 应用。 通过向用户提供这些基于云的按需应用的访问权限，可以节省用户的时间和资源。

部署应用服务资源提供程序，[将 Web 和 API 应用提供给 Azure Stack 用户使用](../operator/azure-stack-app-service-overview.md?view=azs-2002)。

## <a name="next-steps"></a>后续步骤

[详细了解如何使用 Azure Stack 集成系统提供服务](../operator/service-plan-offer-subscription-overview.md)
