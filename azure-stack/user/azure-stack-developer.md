---
title: 开发适用于 Azure Stack 的应用 | Microsoft Docs
description: 在 Azure Stack 中制作应用程序原型的开发注意事项
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
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 4273326001dc79abb7ea522c3bd4c01e46bceb2b
ms.sourcegitcommit: 6fcd5df8b77e782ef72f0e1419f1f75ec8c16c04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991279"
---
# <a name="develop-for-azure-stack"></a>为 Azure Stack 进行开发

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

即使没有 Azure Stack 环境的访问权限，也可以立即开始开发应用程序。 由于 Azure Stack 交付在数据中心中运行的 Microsoft Azure 服务，可以使用类似的工具和流程开发在 Azure Stack 上，就像使用 Azure。

## <a name="development-considerations"></a>开发注意事项

通过一些准备工作并使用以下主题中的指导，可以使用 Azure 来模拟 Azure Stack 环境。

* 在 Azure 中，可以创建可部署到 Azure Stack 的 Azure 资源管理器模板。 请参阅[模板注意事项](azure-stack-develop-templates.md)，获取有关开发模板的指导，以确保可移植性。
* Azure 与 Azure Stack 之间的服务可用性和服务版本控制存在差异。 可以使用 [Azure Stack 策略模块](azure-stack-policy-module.md)来限制 Azure Stack 可用的 Azure 服务可用性和资源类型。 约束服务可确保应用程序依赖可用于 Azure Stack 的服务。
* [Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)是常见的方案示例，演示了如何开发可部署到 Azure 和 Azure Stack 的模板。

## <a name="next-steps"></a>后续步骤

有关 Azure Stack 开发的详细信息，请参阅以下文章：

* [Azure 资源管理器模板最佳做法](azure-stack-develop-templates.md)
* [GitHub 上的 Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)