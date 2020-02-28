---
title: 在 Azure Stack Hub 验证门户中计划测试
titleSuffix: Azure Stack Hub
description: 了解如何在 Azure Stack Hub 验证门户中计划测试。
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e41fe02946b3f08d34cdb0a6d81c08885ef9d71b
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704601"
---
# <a name="scheduling-a-test"></a>计划测试

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

在 Microsoft Azure Stack 验证门户中计划 Azure Stack 中心解决方案的测试。 验证即服务（VaaS）解决方案表示具有特定硬件材料清单（BoM）的 Azure Stack 集线器解决方案。 你可以计划测试，以检查硬件是否可以 Azure Stack 集线器运行。

若要检查解决方案，请为测试创建工作流。 VaaS 工作流在 VaaS 解决方案的上下文中运行。 它代表一组测试套件，用于在硬件上执行 Azure Stack 中心部署的功能。 添加解决方案的环境参数，并选择要在解决方案中运行的一个或多个测试。

虽然测试通过工作流可用于运行由 VaaS 提供的任何测试，包括验证工作流中的测试，但测试通过工作流的结果不会被视为*官方*。 有关官方验证工作流的信息，请参阅[工作流](azure-stack-vaas-key-concepts.md#workflows)。

## <a name="prerequisites"></a>必备条件

在执行本快速入门之前，请完成以下任务：

- [将验证设置为服务资源](azure-stack-vaas-set-up-resources.md)。
- [部署本地代理](azure-stack-vaas-local-agent.md)（必需）。
- [验证作为服务关键概念](azure-stack-vaas-key-concepts.md)（必需）。

## <a name="start-a-workflow"></a>启动工作流

![登录到 VaaS 门户](media/vaas_portalsignin.png)

登录到门户，选择或创建解决方案，然后选择解决方案。

1. 登录到[VaaS 门户](https://azurestackvalidation.com)。
2. 键入现有解决方案的名称，或选择 "**新建解决方案**" 创建新解决方案。 有关说明，请参阅[在 VaaS 门户中创建解决方案](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-azure-stack-hub-validation-portal)。
3. 选择 "**测试轮次**" 磁贴上的 "**开始**"。

## <a name="specify-parameters"></a>指定参数

![在 VaaS 门户中指定参数](media/vaas_test_pass_parameters.png)

提供应用于工作流中的所有测试的参数。

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. 选择 "**下一步**" 以选择要计划的测试。

## <a name="select-tests-to-run"></a>选择要运行的测试

创建工作流后，将计划所选测试。

1. 选择要在工作流中运行的测试。

    如果要为任何测试覆盖公共参数（在上一节中提供的参数），请选择 "指定新值" 旁边的 "**编辑**" 链接。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. 选择 "**下一步**" 以查看工作流。

## <a name="review-and-submit"></a>查看并提交

完成工作流创建。

1. 查看显示的信息。

    该服务将创建包含所提供信息的工作流，并计划所选测试。

    如果出现错误，请使用 "**上一步**" 按钮前往前面的部分。

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)
