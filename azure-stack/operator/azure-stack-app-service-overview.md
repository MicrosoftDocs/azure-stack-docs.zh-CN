---
title: Azure Stack 上的 Azure 应用服务概述
description: Azure Stack Hub 上的 Azure 应用服务和 Azure Functions 概述。
author: BryanLa
ms.topic: article
ms.date: 05/05/2020
ms.author: BryanLa
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: 8cf8f5c42eea1ce1884760c974881712954f1af7
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847820"
---
# <a name="azure-app-service-and-azure-functions-on-azure-stack-hub-overview"></a>Azure Stack Hub 上的 Azure 应用服务和 Azure Functions 概述

Azure Stack 中心的 Azure App Service 是从 Azure Stack 中心提供 Microsoft Azure 的平台即服务（PaaS）产品。 该服务可让你的内部或外部客户为任何平台或设备创建 Web 应用、API 应用和 Azure Functions 应用。 他们可以将你的应用与本地应用集成，并可实现业务流程的自动化。 Azure Stack Hub 云操作员可以在完全托管的虚拟机 (VM) 上，使用他们选择的共享 VM 资源或专用 VM 运行客户的应用。

使用 Azure 应用服务可以自动执行业务流程和托管云 API。 Azure 应用服务作为单个集成服务，允许用户将各种组件（例如网站、REST API 和业务流程）组合到单个解决方案中。

## <a name="why-offer-azure-app-service-on-azure-stack-hub"></a>为什么在 Azure Stack Hub 上提供 Azure 应用服务？

下面是 Azure 应用服务的某些主要特性和功能：

- **多种语言和框架**：Azure 应用服务为 ASP.NET、Node.js、Java、PHP 和 Python 提供一流支持。 也可以在应用服务 VM 上运行 Windows PowerShell 和其他脚本或可执行文件。
- **DevOps 优化**：使用 GitHub、本地 Git 或 BitBucket 设置持续集成和部署。 通过测试和过渡环境推广更新，并使用 Azure PowerShell 或跨平台命令行接口 (CLI) 在应用服务中管理应用。
- **Visual Studio 集成**：Visual Studio 中的专用工具可简化创建和部署应用的工作。

## <a name="app-types-in-app-service"></a>应用服务中的应用类型

应用服务提供多种应用类型，每种类型负责托管特定的工作负荷：

- [Web 应用](/azure/app-service/overview)用于托管网站和 Web 应用。
- [API 应用](/azure/app-service/overview)用于托管 REST API。
- 用于托管事件驱动的无服务器工作负荷[Azure Functions v1](/azure/azure-functions) 。

“应用”一词是指专用于运行工作负荷的托管资源。  以 Web 应用为例，你可能习惯于将 Web 应用视为计算资源和应用代码，二者共同向浏览器提供功能。  在 Azure 应用服务中，Web 应用是 Azure Stack Hub 提供的用于托管应用代码的计算资源。

应用可以由多个不同类型的应用服务应用组成。 例如，如果应用由 Web 前端和 REST API 后端组成，则可以：

- 将两者（前端和 API）部署到单个 Web 应用。
- 将前端代码部署到 Web 应用，将后端代码部署到 API 应用。

   [![使用监视数据的应用服务概述](media/azure-stack-app-service-overview/image01.png "使用监视数据的应用服务概述")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>什么是应用服务计划？

应用服务资源提供程序使用 Azure 应用服务所用的相同代码，因此两者具有一些共同的概念。 在应用服务中，应用的定价容器称为应用服务计划。  它表示一组用来保存应用的专用 VM。 在一个给定订阅中，你可以有多个应用服务计划。

在 Azure 中，有共享辅助角色和专用辅助角色。 共享辅助角色支持高密度的多租户应用托管，并且只有一组共享辅助角色。 专用服务器只由一个租户使用，有三种大小：小型、中型和大型。 使用这些术语并非总是能够描述本地客户需求。 在 Azure Stack Hub 上的应用服务中，资源提供程序管理员会定义他们想要提供的辅助角色层。 根据自己的独特托管需求，你可以定义多组共享辅助角色或不同组的专用辅助角色。 使用这些辅助角色层定义，他们随后可以定义自己的定价 SKU。

## <a name="portal-features"></a>门户功能

Azure Stack Hub 上的 Azure 应用服务使用 Azure 应用服务所用的相同用户界面， 这同样适用于后端。 但是，某些功能在 Azure Stack Hub 中已禁用。 目前，Azure 特定的预期或这些功能需要的服务在 Azure Stack Hub 中尚不可用。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack Hub 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)
- [安装 Azure 应用服务资源提供程序](azure-stack-app-service-deploy.md)

还可以试用其他[平台即服务 (PaaS) 服务](service-plan-offer-subscription-overview.md)，如 [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)和 [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)。
