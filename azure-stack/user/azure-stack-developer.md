---
title: 为 Azure Stack 集线器开发应用 |Microsoft Docs
description: 使用 Azure 服务在 Azure Stack 中心构建应用原型的开发注意事项。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: ca4102e53d06b8f7c4876fd6cd0fd9e6d9ade42e
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820451"
---
# <a name="develop-for-azure-stack-hub"></a>针对 Azure Stack 中心进行开发

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

你现在可以开始开发应用程序，即使你没有 Azure Stack 集线器环境的访问权限。 Azure Stack 中心提供在你的数据中心运行的 Microsoft Azure 服务，这意味着你可以使用相同的 Azure 工具和进程在 Azure Stack 中心进行开发。

## <a name="development-considerations"></a>开发注意事项

通过进行一些准备，并使用以下主题中的指南，你可以使用 Azure 来模拟 Azure Stack 中心环境。

* 在 Azure 中，可以创建可部署到 Azure Stack 中心的 Azure 资源管理器模板。 有关开发模板的指南，请参阅[模板注意事项](azure-stack-develop-templates.md)，以确保可移植性。
* Azure 与 Azure Stack 中心之间的服务可用性和服务版本控制有所不同。 你可以使用[Azure Stack 中心策略模块](azure-stack-policy-module.md)将 Azure 服务可用性和资源类型限制到 Azure Stack 中心可用的内容。 限制服务可确保你的应用依赖于可用于 Azure Stack 中心的服务。
* [Azure Stack 集线器快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)是常见的方案示例，演示如何开发可部署到 Azure 和 Azure Stack 中心的模板。

## <a name="next-steps"></a>后续步骤

有关 Azure Stack 开发的详细信息，请参阅以下文章：

* [Azure 资源管理器模板最佳做法](azure-stack-develop-templates.md)
* [GitHub 上的 Azure Stack 集线器快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)
