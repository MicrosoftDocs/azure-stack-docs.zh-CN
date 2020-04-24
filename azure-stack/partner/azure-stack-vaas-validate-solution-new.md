---
title: 在 Azure Stack 中心验证解决方案
description: 了解如何使用解决方案验证工作流验证新的 Azure Stack 中心解决方案。
author: mattbriggs
ms.topic: quickstart
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2d47450af602928eb9220238ae945610fd9f0477
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661163"
---
# <a name="validate-a-solution-in-azure-stack-hub"></a>在 Azure Stack 中心验证解决方案

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何使用**解决方案验证**工作流来验证新的 Azure Stack 中心解决方案。

Azure Stack 中心解决方案是一种硬件清单（BoM），在满足 Windows Server 徽标认证要求后，会在 Microsoft 和合作伙伴之间联合达成共识。 当硬件 BoM 发生变化时，必须 recertified 解决方案。 有关何时重新验证解决方案的其他问题，请联系上[vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)的团队。

若要验证解决方案，请运行解决方案验证工作流两次。 针对受支持的“最低”** 配置运行一次。 再次运行*最大化*支持的配置。 如果这两个配置都通过了所有测试，Microsoft 将验证解决方案。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>创建解决方案验证工作流

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

2. 选择 "**解决方案验证**" 磁贴上的 "**启动**"。

    ![解决方案验证工作流磁贴](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

4. 选择**解决方案配置**。
    - **最小值**：为解决方案配置最小支持的节点数。
    - **最大值**：为解决方案配置最大支持的节点数。
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![解决方案验证信息](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 创建工作流后，不能修改环境参数。

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    你将被重定向到 "测试摘要" 页面。

## <a name="run-solution-validation-tests"></a>运行解决方案验证测试

在 "**解决方案验证测试摘要**" 页中，你将看到完成验证所需的测试列表。

在验证工作流中，**计划**测试使用工作流创建过程中指定的工作流级公共参数（[有关 Azure Stack 中心验证作为服务的工作流常见参数](azure-stack-vaas-parameters.md)，请参阅）。 如果任何测试参数值无效，则必须按照[修改工作流参数](azure-stack-vaas-monitor-test.md#change-workflow-parameters)中的指示 resupply 它们。

> [!NOTE]
> 在现有实例上计划验证测试会在门户中创建一个新实例来代替旧实例。 旧实例的日志将保留，但无法从门户进行访问。<br>
测试成功完成后，**计划**操作将变为禁用状态。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. 选择以下测试：
    - 云模拟引擎
    - 磁盘标识测试

3. 从上下文菜单中选择 "**计划**" 以打开计划测试实例的提示。

4. 查看测试参数，然后选择 "**提交**" 以计划执行测试。

![计划解决方案验证测试](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)