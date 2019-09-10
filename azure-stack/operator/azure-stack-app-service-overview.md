---
title: Azure Stack 上的应用服务概述 | Microsoft Docs
description: Azure Stack 上的应用服务概述。
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: anwestg
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 698d38e971d37457d50637cb2e7e2ffe88890c20
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70808220"
---
# <a name="app-service-on-azure-stack-overview"></a>Azure Stack 上的应用服务概述

适用于：*Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 上的 Azure App Service 是从 Azure Stack Microsoft Azure 提供的平台即服务（PaaS）产品。 此服务使你的内部或外部客户能够为任何平台或设备创建 web、API 和 Azure Functions 应用。 它们可将你的应用与本地应用集成并实现其业务流程的自动化。 Azure Stack 云操作员可以在完全托管的虚拟机（Vm）上运行客户，并选择共享 VM 资源或专用 Vm。

使用 Azure 应用服务可以自动执行业务流程和托管云 API。 Azure 应用服务作为单个集成服务，允许用户将各种组件（例如网站、REST API 和业务流程）组合到单个解决方案中。

## <a name="why-offer-azure-app-service-on-azure-stack"></a>为什么在 Azure Stack 上提供 Azure 应用服务？

下面是应用服务的某些主要特性和功能：

- **多种语言和框架**：应用服务为 ASP.NET、Node.js、Java、PHP 和 Python 提供一流支持。 也可以在应用服务 VM 上运行 Windows PowerShell 和其他脚本或可执行文件。
- **DevOps 优化**：使用 GitHub、本地 Git 或 BitBucket 设置持续集成和部署。 通过测试和过渡环境升级更新，并使用 Azure PowerShell 或跨平台命令行接口（CLI）在应用服务中管理应用。
- **Visual Studio 集成**：Visual Studio 中的专用工具可简化创建和部署应用的工作。

## <a name="app-types-in-app-service"></a>应用服务中的应用类型

应用服务提供多种应用类型，每种类型负责托管特定的工作负荷：

- 用于托管网站和 web 应用的[Web 应用](/azure/app-service/overview)。
- [API 应用](/azure/app-service/overview)用于托管 REST API。
- Azure Functions 用于托管事件驱动的无服务器工作负荷。

“应用”一词是指专用于运行工作负荷的托管资源。 采用*web 应用*作为示例，你可能习惯于将 web 应用视为计算资源和应用代码，同时将功能传递到浏览器。 在应用服务中，web 应用是 Azure Stack 提供的用于托管应用代码的计算资源。

您的应用程序可以由多个不同类型的应用服务应用组成。 例如，如果您的应用程序由 web 前端和 REST API 后端组成，则可以：

- 将（前端和 API）部署到单个 web 应用
- 将前端代码部署到 Web 应用，将后端代码部署到 API 应用。

   [![使用监视数据的应用服务概述](media/azure-stack-app-service-overview/image01.png "使用监视数据的应用服务概述")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>什么是应用服务计划？

应用服务资源提供程序使用 Azure 应用服务所用的相同代码，因此两者具有一些共同的概念。 在应用服务中，应用的定价容器称为 "*应用服务计划*"。 它表示用于保存应用的专用 Vm 集。 在一个给定订阅中，你可以有多个应用服务计划。

在 Azure 中，有共享辅助角色和专用辅助角色。 共享辅助角色支持高密度和多租户应用托管，并且只有一组共享辅助角色。 专用服务器只由一个租户使用，有三种大小：小型、中型和大型。 使用这些术语并非总是能够描述本地客户需求。 在 Azure Stack 上的应用服务中，资源提供程序管理员可以定义要提供的辅助角色层。 根据自己的独特托管需求，你可以定义多组共享辅助角色或不同组的专用辅助角色。 使用这些辅助角色层定义，他们随后可以定义自己的定价 SKU。

## <a name="portal-features"></a>门户功能

Azure Stack 上的应用服务使用 Azure 应用服务所用的相同用户界面， 这同样适用于后端。 但是，某些功能在 Azure Stack 中已禁用。 目前，Azure 特定的预期或这些功能需要的服务在 Azure Stack 中尚不可用。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)
- [安装应用服务资源提供程序](azure-stack-app-service-deploy.md)

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-offer-services-overview.md)，如 [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)和 [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)。
