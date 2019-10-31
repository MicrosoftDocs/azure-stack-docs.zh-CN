---
title: 交互式功能验证作为服务 Azure Stack 验证中的测试 |Microsoft Docs
description: 了解如何为具有验证即服务的 Azure Stack 创建交互式功能验证测试。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5da64bbd13e2bfde5b469c1ae0de89d85a054e3c
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167250"
---
# <a name="interactive-feature-verification-testing"></a>交互式功能验证测试  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

你可以使用交互式功能验证测试框架来请求你的系统测试。 请求测试时，Microsoft 使用框架来准备需要手动交互步骤的测试。 Microsoft 可以使用框架将多个独立的自动测试链接在一起。

本文介绍了一个简单的手动方案。 测试检查替换 Azure Stack 中的磁盘。 框架收集每个步骤的诊断日志。 你可以在发现问题时对其进行调试。 该框架还允许共享其他工具或进程生成的日志，并使你能够提供有关该方案的反馈。

> [!Important]  
> 本文介绍执行磁盘标识的步骤。 这只是一个演示，因为从测试通过工作流收集的任何结果都不能用于新的解决方案验证。

## <a name="overview-of-interactive-testing"></a>交互式测试概述

磁盘更换的测试是一种常见方案。 在此示例中，测试包含五个步骤：

1. 创建新的**测试通过**工作流。
2. 选择 "**磁盘标识测试**"。
3. 在出现提示时完成手动步骤。
4. 检查方案的结果。
5. 将测试结果发送给 Microsoft。

## <a name="create-a-new-test-pass"></a>创建新的测试通过

如果没有可用的现有测试阶段，请按照[计划测试](azure-stack-vaas-schedule-test-pass.md)的说明进行操作。

## <a name="schedule-the-test"></a>计划测试

1. 选择 "**磁盘标识测试**"。

    > [!Note]  
    > 随着对测试辅助功能的改进，测试的版本将递增。 除非 Microsoft 指出，否则应始终使用最高版本。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. 通过选择 "**编辑**" 提供域管理员用户名和密码。

1. 选择要在其上启动测试的相应测试执行代理/DVM。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. 选择 "**提交**" 以启动测试。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. 从上一步中选择的代理访问交互式测试的 UI。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. 按照**文档**和**验证**链接，查看有关如何执行此方案的 Microsoft 的说明。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. 选择“**下一步**”。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. 按照说明运行预检查脚本。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. 成功完成预检查脚本后，请根据 "**信息**" 选项卡上的**文档**和**验证**链接运行手动方案（磁盘替换）。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > 请不要在执行手动方案时关闭此对话框。

1. 执行完手动方案后，请按照说明运行 post 检查脚本。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. 手动方案（磁盘替换）成功完成后，选择 "**下一步**"。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > 如果关闭窗口，则测试在完成之前将停止。

1. 提供测试体验的反馈。 这些问题将帮助 Microsoft 评估方案的成功率和发行质量。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. 附加要提交给 Microsoft 的任何日志文件。

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. 接受反馈提交 EULA。

1. 选择 "**提交**" 以将结果发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)
