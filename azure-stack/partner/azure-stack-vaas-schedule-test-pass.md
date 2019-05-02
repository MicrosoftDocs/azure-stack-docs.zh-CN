---
title: 用于验证作为 Azure Stack 门户的服务计划第一次测试 |Microsoft Docs
description: 用于验证作为 Azure Stack 门户的服务计划第一个测试。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 3fb5c3deeddb6f3ee381ca45df76feebf3405b21
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64300501"
---
# <a name="scheduling-a-test"></a>计划测试

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

计划中的服务 (VaaS) 门户，为验证 Azure Stack 解决方案的测试。 VaaS 解决方案表示与特定硬件清单的清单 (BoM) 的 Azure Stack 解决方案。 您可以计划一个测试，以检查你的硬件可以运行 Azure Stack。

若要检查你的解决方案，请创建一个测试的工作流。 VaaS 解决方案的上下文中运行 VaaS 工作流。 它表示一组执行您的硬件上的 Azure Stack 部署的功能的测试套件。 添加你的解决方案的环境参数并选择一个或多个要在解决方案上运行的测试。

尽管可以使用测试通过工作流运行任何测试提供 VaaS，包括验证工作流，从测试的测试流程工作流的结果不被视为*官方*。 有关正式的验证工作流的信息，请参阅[工作流](azure-stack-vaas-key-concepts.md#workflows)。

## <a name="prerequisites"></a>必备组件

在执行本快速入门之前，应完成以下各项：

- [设置您的验证为服务资源](azure-stack-vaas-set-up-resources.md)
- [部署本地代理](azure-stack-vaas-local-agent.md)（必需）
- [作为服务的关键概念验证](azure-stack-vaas-key-concepts.md)（必需）

## <a name="start-a-workflow"></a>启动工作流

![登录到 VaaS 门户](media/vaas_portalsignin.png)

登录到门户、 选择或创建一个解决方案，然后选择该解决方案。

1. 登录到[VaaS 门户](https://azurestackvalidation.com)。
2. 键入一个现有的解决方案的名称或选择**新的解决方案**若要创建新的解决方案。 有关说明，请参阅[VaaS 门户中创建一个解决方案](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal)。
3. 选择**启动**上**测试轮次**磁贴。

## <a name="specify-parameters"></a>指定参数

![替换文字](media/vaas_test_pass_parameters.png)

提供应用于工作流中的所有测试的参数。

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. 选择**下一步**来选择测试计划。

## <a name="select-tests-to-run"></a>选择要运行的测试

创建工作流后，将计划选择的测试。

1. 选择你想要在工作流中运行的测试。

    如果您想要的任何测试覆盖的常见参数 （即，在上一节中提供的参数），选择上**编辑**链接接下来，若要指定新值。

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. 选择**下一步**若要检查的工作流。

## <a name="review-and-submit"></a>查看和提交

完成工作流创建。

1. 查看显示的信息。

    该服务将使用所提供的信息创建工作流并将计划选定的测试。

    如果任何内容显示不正确，请使用**上一步**按钮转到前面的部分。

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)
