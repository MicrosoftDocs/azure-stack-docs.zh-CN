---
title: 交互式功能验证测试中作为服务的 Azure Stack 验证 |Microsoft Docs
description: 了解如何为 Azure Stack 验证为服务创建交互式功能验证测试。
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
ms.openlocfilehash: d3db8ea8639f73f3522ddaa358195e7c9ef2f9a9
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64303251"
---
# <a name="interactive-feature-verification-testing"></a>交互式功能验证测试  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

交互式功能验证测试框架可用于请求有关您的系统测试。 当请求在测试时，Microsoft 使用该框架准备需要交互式的手动步骤的测试。 Microsoft 可以使用框架链接在一起的多个独立自动测试。

本指南介绍了一个简单的手动方案。 该测试检查替换为 Azure Stack 中的磁盘。 框架收集诊断日志在每个步骤。 如您找到它们，您可以调试问题。 框架还允许在共享由其他工具或进程，生成的日志，并使你可以提供反馈的场景。

> [!Important]  
> 本文引用的步骤来执行磁盘标识。 这是因为从测试通过工作流中收集的任何结果不能使用新的解决方案验证只是演示。

## <a name="overview-of-interactive-testing"></a>交互式测试的概述

有关磁盘更换的测试是一种常见方案。 在此示例中，测试具有五个步骤：

1. 创建一个新**测试轮次**工作流。
2. 选择**磁盘标识测试**。
3. 完成手动步骤出现提示时。
4. 检查的方案的结果。
5. 将测试结果发送给 Microsoft。

## <a name="create-a-new-test-pass"></a>创建新的测试流程

如果您不具备现有测试传递可用，请遵循的指导[计划测试](azure-stack-vaas-schedule-test-pass.md)。

## <a name="schedule-the-test"></a>计划测试

1. 选择**磁盘标识测试**。

    > [!Note]  
    > 随着所做的改进测试辅助资料，测试版本将递增。 除非 Microsoft 指示，否则，应始终使用最高版本。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. 通过选择提供域管理员用户名和密码**编辑**。

1. 选择适当的测试执行代理/DVM 上启动测试。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. 选择**提交**以启动测试。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. 从上一步中所选代理访问交互式测试的 UI。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. 请按照**文档**并**验证**链接，以查看如何执行这种情况下从 Microsoft 的说明。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. 选择“**下一步**”。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. 按照运行预检查脚本的说明。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. 预检查脚本已成功完成后，每运行一次手动方案 （磁盘更换） 作为**文档**并**验证**的链接从**信息**选项卡。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > 执行手动方案时，不要关闭对话框。

1. 在完成执行的手动方案，请按照运行后检查脚本的说明。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. 手动方案 （磁盘更换） 的成功完成后，选择**下一步**。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > 如果关闭窗口，该测试将停止之前完成。

1. 为测试体验提供反馈。 这些问题将有助于 Microsoft 评估成功速率和发布质量的方案。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. 附加你要向 Microsoft 提交任何日志文件。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. 接受反馈提交最终用户许可协议。

1. 选择**提交**将结果发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)
