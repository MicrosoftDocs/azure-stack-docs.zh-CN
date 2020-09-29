---
title: 在 Azure Stack Hub 验证门户中管理测试
titleSuffix: Azure Stack Hub
description: 了解如何在 Azure Stack Hub 验证门户中管理测试。
author: mattbriggs
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 28791e9687dead6a571097e23258fb5b6aff1200
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764760"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>监视和管理 VaaS 门户中的测试

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

针对 Azure Stack 中心解决方案计划测试后，验证即服务 (VaaS) 将开始报告测试执行状态。 此信息在 Azure Stack Hub 验证门户中提供，还提供重新计划和取消测试等操作。

## <a name="navigate-to-the-workflow-tests-summary-page"></a>导航到 "工作流测试摘要" 页

1. 在解决方案仪表板上，选择至少具有一个工作流的现有解决方案。

    ![解决方案仪表板上的工作流磁贴](media/tile_all-workflows.png)

1. 选择 "工作流" 磁贴上的 " **管理** "。 下一页列出为所选解决方案创建的工作流。

1. 选择工作流名称以打开其 "测试摘要"。

## <a name="change-workflow-parameters"></a>更改工作流参数

可以在为任何类型的工作流创建工作流的过程中编辑指定的 [测试参数](azure-stack-vaas-parameters.md#test-parameters) 。

1. 在 "测试摘要" 页上，选择 " **编辑**"。

1. 根据 [Azure Stack 集线器验证的工作流通用参数](azure-stack-vaas-parameters.md)提供新值作为服务。

1. 选择 " **提交** " 以保存值。

> [!NOTE]
> 在 **测试通过** 工作流中，需要完成测试选择，并进入 "检查" 页面，然后才能保存新参数值。

### <a name="add-tests-test-pass-only"></a>仅 (测试传递添加测试) 

在 **测试通过** 工作流中，" **添加测试** " 和 " **编辑** " 按钮都允许您计划工作流中的新测试。

> [!TIP]
> 如果要计划新的测试，而不需要编辑**测试通过**工作流的参数，请选择 "**添加测试**"。

## <a name="manage-test-instances"></a>管理测试实例

对于非正式运行 (**测试通过** 工作流) ，"测试摘要" 页将列出针对 Azure Stack 中心解决方案计划的测试。

对于官方 () **验证** 工作流运行，"测试摘要" 页将列出完成 Azure Stack 中心解决方案的验证所需的测试。 从此页计划验证测试。

每个计划的测试实例都显示以下信息：

| 列 | 说明 |
| --- | --- |
| 测试名称 | 测试的名称和版本。 |
| Category | 测试的目的。 |
| 创建 | 计划测试的时间。 |
| Started | 测试开始执行的时间。 |
| 持续时间 | 测试运行的时间长度。 |
| 状态 | 测试的状态或结果。 执行前或正在进行的状态为： `Pending` 、 `Running` 。 终端状态为： `Cancelled` 、 `Failed` 、 `Aborted` 和 `Succeeded` 。 |
| 代理名称 | 运行测试的代理的名称。 |
| 总操作数 | 在测试期间尝试的操作的总数。 |
| 通过的操作数 | 测试过程中成功的操作的数目。 |
|  失败的操作 | 测试过程中失败的操作的数目。 |

### <a name="actions"></a>操作

每个测试实例都列出了在测试实例表中选择其上下文菜单 **[...]** 时可以执行的操作。

#### <a name="view-information-about-the-test-definition"></a>查看有关测试定义的信息

从上下文菜单中选择 " **查看信息** " 以查看有关测试定义的一般信息。 此信息由具有相同名称和版本的每个测试实例共享。

| 测试属性 | 说明 |
| -- | -- |
| 测试名称 | 测试的名称。 |
| 测试版本 | 测试的版本。 |
| Publisher | 测试的发行者。 |
| Category |  测试的目的。 |
| 目标服务 | 正在测试的 Azure Stack 中心服务。 |
| 说明 | 对测试的说明。 |
| 估计持续时间(分钟) | 测试的预期运行时。 |
| 链接 | 有关测试或联系点的任何相关信息。 |

#### <a name="view-test-instance-parameters"></a>查看测试实例参数

从上下文菜单中选择 " **查看参数** " 以查看在计划时间提供给测试实例的参数。 不显示敏感字符串（如密码）。 此操作仅适用于已计划的测试。

此窗口包含所有测试实例的以下元数据：

| 测试实例属性 | 说明 |
| -- | -- |
| 测试名称 | 测试的名称。 |
| 测试版本 | 测试的版本。 |
| 测试实例 ID | 标识测试的特定实例的 GUID。 |

#### <a name="view-test-instance-operations"></a>查看测试实例操作

从上下文菜单中选择 " **查看操作** "，以查看在测试期间执行的操作的详细状态。 此操作仅适用于已计划的测试。

![查看操作](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>下载已完成测试实例的日志

从上下文菜单中选择 " **下载日志** " 可在 `.zip` 测试执行过程中下载日志输出的文件。 此操作仅适用于已完成的测试，这意味着它们的状态为 `Cancelled` 、、 `Failed` `Aborted` 或 `Succeeded` 。

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>重新计划测试实例或计划测试

从管理页计划测试取决于运行测试的工作流的类型。

##### <a name="test-pass-workflow"></a>测试通过工作流

在测试通过工作流中重新设置测试实例 **重新使用与** 原始测试实例相同的一组参数，并 *替换* 原始结果（包括其日志）。 重新计划时，需要重新输入敏感字符串（如密码）。

1. 从上下文菜单中选择 "重新 **计划** "，以打开重新计划测试实例的提示。

1. 输入任何适用的参数。

1. 选择 " **提交** " 以重新计划测试实例并替换现有的实例。

##### <a name="validation-workflows"></a>验证工作流

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>取消测试实例

如果计划的测试的状态为或，则可以将其取消 `Pending` `Running` 。  

1. 从上下文菜单中选择 " **取消** "，以打开用于取消测试实例的提示。

1. 选择 " **提交** " 以取消测试实例。

## <a name="next-steps"></a>后续步骤

- [验证作为服务的验证](azure-stack-vaas-troubleshoot.md)
