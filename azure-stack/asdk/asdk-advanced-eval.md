---
title: 高级 ASDK 评估任务
description: 了解高级 Azure Stack 开发工具包（ASDK）评估任务。
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 2c4a83ea024f83c0181cfe77c598519746deaeed
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695353"
---
# <a name="advanced-asdk-evaluation-tasks"></a>高级 ASDK 评估任务
熟悉基本的 Azure Stack 开发工具包（ASDK）服务特性和功能后，可以通过测试更高级的方案进一步加深对 Azure Stack 的了解。 这些更高级的评估任务已完全记录在 Azure Stack 运算符文档中。

> [!NOTE]
> 尽管 ASDK 和生产和多节点 Azure Stack 部署支持许多运算符任务，但 ASDK 部署不支持所有使用方案。 有关详细信息，请参阅[ASDK 和多节点 Azure Stack 差异](asdk-what-is.md#asdk-and-multi-node-azure-stack-hub-differences)。

## <a name="delegate-offers-in-azure-stack"></a>在 Azure Stack 中委托套餐
作为 Azure Stack 操作员，你经常需要让其他人负责创建产品/服务和注册用户。 例如，如果你是服务提供商，你可能希望经销商以你的名义注册客户并进行管理。 或者，如果你是企业中的中心 IT 小组的成员，则可能希望子公司注册用户，而无需你的干预。

通过[委派 Azure Stack 中的产品/服务](../operator/azure-stack-delegated-provider.md)，可以通过使用户能够访问和管理比直接更多的用户，帮助你完成这些任务。

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>使 SQL 数据库对 Azure Stack 用户可用
作为 Azure Stack 操作员，你可以创建产品/服务，让你的用户（租户）创建可用于其云原生应用、网站和工作负荷的 SQL 数据库。 通过向用户提供这些基于云的自定义数据库，可以节省时间和资源。

使用 SQL Server 资源提供程序适配器向[Azure Stack 用户提供](../operator/azure-stack-tutorial-sql-server.md)Azure Stack 的服务的 SQL 数据库。 安装资源提供程序后，可将其连接到一个或多个 SQL Server 实例。

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>向 Azure Stack 用户提供 web 和 API 应用
作为 Azure Stack 操作员，你可以创建产品/服务，让你的用户（租户）创建 Azure Functions 和 web 和 API 应用。 通过向用户提供对这些基于云的应用程序和基于云的应用程序的访问权限，可以节省时间和资源。

部署应用服务资源提供程序，[使 web 和 API 应用可供你的 Azure Stack 用户使用](../operator/azure-stack-tutorial-app-service.md)。

## <a name="next-steps"></a>后续步骤

[详细了解如何通过 Azure Stack 集成系统来提供服务](../operator/service-plan-offer-subscription-overview.md)
