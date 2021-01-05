---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 15afed256db592def52f06f8507bcb266d2ad9d3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874773"
---
在验证工作流中， **计划** 测试使用工作流创建过程中指定的工作流级公共参数 (参阅 [Azure Stack 中心验证作为服务) 的工作流通用参数](../azure-stack-vaas-parameters.md) 。 如果任何测试参数值无效，则必须按照 [修改工作流参数](../azure-stack-vaas-monitor-test.md#change-workflow-parameters)中的指示 resupply 它们。

> [!NOTE]
> 在现有实例上计划验证测试会在门户中创建一个新实例来代替旧实例。 旧实例的日志将保留，但无法从门户进行访问。 测试成功完成后， **计划** 操作将变为禁用状态。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. 从上下文菜单中选择 " **计划** " 以打开计划测试实例的提示。

1. 查看测试参数，然后选择 " **提交** " 以计划执行测试。