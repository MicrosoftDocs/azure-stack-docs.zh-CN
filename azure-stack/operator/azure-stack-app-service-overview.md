---
title: Azure Stack 概述上的 Azure App Service
description: Azure Stack 中心的 Azure App Service 和 Azure Functions 的概述。
author: BryanLa
ms.topic: article
ms.date: 01/13/2020
ms.author: BryanLa
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: ac9c6707e0b6e179fdae3dac0b4ec94bad11e6d7
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77688876"
---
# <a name="azure-app-service-and-azure-functions-on-azure-stack-hub-overview"></a>Azure Stack 中心概述的 Azure App Service 和 Azure Functions 概述

Azure Stack 中心的 Azure App Service 是从 Azure Stack 中心提供 Microsoft Azure 的平台即服务（PaaS）产品。 此服务使你的内部或外部客户能够为任何平台或设备创建 web、API 和 Azure Functions 应用。 它们可将你的应用与本地应用集成并实现其业务流程的自动化。 Azure Stack 中心云操作员可以在完全托管的虚拟机（Vm）上运行客户的共享 VM 资源或专用 Vm。

Azure App Service 使你能够自动执行业务流程和托管云 Api。 作为单个集成服务，Azure App Service 允许你将各种组件（如网站、REST Api 和业务流程）组合到单个解决方案中。

## <a name="why-offer-azure-app-service-on-azure-stack-hub"></a>为什么在 Azure Stack 中心提供 Azure App Service？

下面是 Azure App Service 的一些主要特性和功能：

- **多种语言和框架**： Azure App Service 对 ASP.NET、Node.js、JAVA、PHP 和 Python 提供一流支持。 你还可以在应用服务 Vm 上运行 Windows PowerShell 和其他脚本或可执行文件。
- **DevOps 优化**：通过 GitHub、本地 Git 或 BitBucket 设置持续集成和部署。 通过测试和过渡环境升级更新，并使用 Azure PowerShell 或跨平台命令行接口（CLI）在应用服务中管理应用。
- **Visual studio 集成**： visual studio 中的专用工具可简化创建和部署应用的工作。

## <a name="app-types-in-app-service"></a>应用服务中的应用类型

应用服务提供多种应用类型，每种类型都用于托管特定的工作负荷：

- 用于托管网站和 web 应用的[Web 应用](/azure/app-service/overview)。
- 用于托管 REST Api 的[API 应用](/azure/app-service/overview)。
- 用于托管事件驱动的无服务器工作负荷的 Azure Functions。

"*应用程序*" 一词是指专用于运行工作负荷的托管资源。 采用*web 应用*作为示例，你可能习惯于将 web 应用视为计算资源和应用代码，同时将功能传递到浏览器。 在 Azure App Service 中，web 应用是 Azure Stack 中心提供的用于托管应用代码的计算资源。

您的应用程序可以由多个不同类型的应用服务应用组成。 例如，如果您的应用程序由 web 前端和 REST API 后端组成，则可以：

- 将（前端和 API）部署到单个 web 应用。
- 将前端代码部署到 Web 应用，将后端代码部署到 API 应用。

   [![应用服务概述与监视数据](media/azure-stack-app-service-overview/image01.png "应用服务概述与监视数据")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>什么是应用服务计划？

应用服务资源提供程序使用的代码与 Azure App Service 使用的代码相同，因此共享一些常见概念。 在应用服务中，应用的定价容器称为 "*应用服务计划*"。 它表示用于保存应用的专用 Vm 集。 在给定的订阅中，可以有多个应用服务计划。

在 Azure 中，存在共享和专用的辅助角色。 共享辅助角色支持高密度和多租户应用托管，并且只有一组共享辅助角色。 专用服务器仅由一个租户使用，并具有三种大小：小型、中型和大型。 不能始终使用这些术语来描述本地客户的需求。 在 Azure Stack Hub 上的应用服务中，资源提供程序管理员定义要提供的辅助角色层。 根据你的独特托管需求，你可以定义多组共享辅助角色或不同的专用工作人员集。 通过使用这些辅助角色层定义，他们可以定义自己的定价 Sku。

## <a name="portal-features"></a>门户功能


Azure Stack 集线器上的 Azure App Service 使用 Azure App Service 所使用的相同用户界面。 后端也是如此。 但 Azure Stack 集线器禁用了某些功能。 这些功能所需的特定于 Azure 的期望或服务目前在 Azure Stack 集线器中不可用。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 集线器上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)
- [安装 Azure App Service 资源提供程序](azure-stack-app-service-deploy.md)

你还可以试用其他[平台即服务（PaaS）服务](service-plan-offer-subscription-overview.md)，例如[SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)和[MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)。
