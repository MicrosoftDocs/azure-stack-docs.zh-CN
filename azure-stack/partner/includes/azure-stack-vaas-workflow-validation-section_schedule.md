---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 4d428e13b00a6f941ea9d6967adce5e1881cb7c1
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "77625419"
---
在验证工作流中，**计划**测试使用工作流创建过程中指定的工作流级公共参数（[有关 Azure Stack 中心验证作为服务的工作流常见参数](../azure-stack-vaas-parameters.md)，请参阅）。 如果任何测试参数值无效，则必须按照[修改工作流参数](../azure-stack-vaas-monitor-test.md#change-workflow-parameters)中的指示 resupply 它们。

> [!NOTE]
> 在现有实例上计划验证测试会在门户中创建一个新实例来代替旧实例。 旧实例的日志将保留，但无法从门户进行访问。 测试成功完成后，**计划**操作将变为禁用状态。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. 从上下文菜单中选择 "**计划**" 以打开计划测试实例的提示。

1. 查看测试参数，然后选择 "**提交**" 以计划执行测试。