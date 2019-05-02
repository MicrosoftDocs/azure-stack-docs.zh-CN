---
title: 监视和管理 Azure Stack VaaS 门户中的测试 |Microsoft Docs
description: 监视和管理 Azure Stack VaaS 门户中的测试。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9b64631b274423edda35189a942e2f142ae591f1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64299742"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>监视和管理 VaaS 门户中的测试

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

后计划对 Azure Stack 解决方案的测试，验证即服务 (VaaS) 将开始 reporting 测试的执行状态。 中的操作，例如重新安排和取消测试以及 VaaS 门户提供了此信息。

## <a name="navigate-to-the-workflow-tests-summary-page"></a>导航到工作流测试摘要页

1. 在解决方案仪表板中，选择具有至少一个工作流的现有解决方案。

    ![工作流磁贴](media/tile_all-workflows.png)

1. 选择**管理**工作流磁贴上。 下一步的页将列出为所选解决方案创建的工作流。

1. 选择要打开其摘要的测试的工作流名称。

## <a name="change-workflow-parameters"></a>更改工作流参数

您可以编辑[测试参数](azure-stack-vaas-parameters.md#test-parameters)任何类型的工作流的工作流创建过程中指定。

1. 在测试摘要页上，选择**编辑**按钮。

1. 提供新值根据[工作流通用参数作为服务的 Azure Stack 验证](azure-stack-vaas-parameters.md)。

1. 选择**提交**保存值。

> [!NOTE]
> 在中**测试轮次**工作流中，你将需要完成测试选择项并转到查看页，然后才能保存新的参数值。

### <a name="add-tests-test-pass-only"></a>添加测试 （仅用于测试传递）

中**测试轮次**工作流，这两种**添加测试**并**编辑**按钮允许您计划在工作流中的新测试。

> [!TIP]
> 选择**添加测试**如果只想要计划新的测试而无需编辑参数**测试轮次**工作流。

## <a name="managing-test-instances"></a>管理测试实例

对于非官方运行 (即**测试轮次**工作流)，测试摘要页列出了针对 Azure Stack 解决方案计划的测试。

对于官方运行 (即**验证**工作流)，测试摘要页列出了用于完成验证 Azure Stack 解决方案所需的测试。 在此页中计划验证测试。

每个计划的测试实例会显示以下信息：

| 列 | 描述 |
| --- | --- |
| 测试名称 | 名称和版本的测试。 |
| 类别 | 测试的目的。 |
| 已创建 | 在该计划测试的时间。 |
| 已启动 | 测试开始执行的时间。 |
| Duration | 测试运行的时间长度。 |
| 状态 | 状态或测试的结果。 预执行或正在进行中状态为： `Pending`， `Running`。 终端的状态为： `Cancelled`， `Failed`， `Aborted`， `Succeeded`。 |
| 代理名称 | 运行测试代理的名称。 |
| 总操作数 | 尝试在测试期间的操作的总数。 |
| 通过的操作数 | 已成功在测试期间的操作数目。 |
|  失败的操作 | 在测试期间失败的操作数目。 |

### <a name="actions"></a>操作

每个测试实例会列出可用的操作时选择其上下文菜单，可以采用 **[...]** 测试实例表中。

#### <a name="view-information-about-the-test-definition"></a>查看有关测试定义的信息

选择**查看信息**从上下文菜单查看测试定义的常规信息。 这是由每个具有相同名称和版本的测试实例共享。

| 测试属性 | 描述 |
| -- | -- |
| 测试名称 | 测试的名称。 |
| 测试版本 | 测试的版本。 |
| 发布者 | 测试的发布服务器。 |
| 类别 |  测试的目的。 |
| 目标服务 | Azure Stack 服务进行测试。 |
| 描述 | 对测试的说明。 |
| 估计持续时间(分钟) | 测试的预期的运行时。 |
| 链接 | 任何相关的测试的信息的联系点。 |

#### <a name="view-test-instance-parameters"></a>查看测试实例参数

选择**查看参数**从上下文菜单，查看在计划时提供给该测试实例的参数。 不显示密码等敏感字符串。 此操作是仅适用于已计划的测试。

此窗口包含的所有测试实例的以下元数据：

| 测试实例属性 | 描述 |
| -- | -- |
| 测试名称 | 测试的名称。 |
| 测试版本 | 测试的版本。 |
| 测试实例 ID | 标识该测试的特定实例的 GUID。 |

#### <a name="view-test-instance-operations"></a>查看测试实例操作

选择**查看操作**从上下文菜单查看操作的详细的状态在测试期间执行。 此操作是仅适用于已计划的测试。

![查看操作](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>下载已完成的测试实例的日志

选择**下载日志**从上下文菜单下载`.zip`测试执行过程中的日志输出的文件。 此操作是仅适用于已完成，即，包含的状态的测试的测试`Cancelled`， `Failed`， `Aborted`，或`Succeeded`。

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>重新计划测试实例，或计划测试

计划从管理页面测试取决于工作流在运行测试的类型。

##### <a name="test-pass-workflow"></a>测试通过工作流

测试通过工作流中**重新安排**测试实例重复使用原始的测试实例相同的参数集并*替换*原始结果，包括其日志。 你将需要重新输入密码等敏感字符串，当您重新计划。

1. 选择**重新计划**从上下文菜单打开以便重新计划的测试实例的提示。

1. 输入任何适用的参数。

1. 选择**提交**重新计划的测试实例，并替换现有实例。

##### <a name="validation-workflows"></a>验证工作流

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>取消测试实例

如果其状态是可能会取消的计划的测试`Pending`或`Running`。  

1. 选择**取消**从上下文菜单打开的提示的取消测试实例。

1. 选择**提交**取消测试实例。

## <a name="next-steps"></a>后续步骤

- [对验证作为服务进行故障排除](azure-stack-vaas-troubleshoot.md)
