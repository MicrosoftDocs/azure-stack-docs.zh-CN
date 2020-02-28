---
title: 交互式功能验证测试
titleSuffix: Azure Stack Hub
description: 了解如何为具有验证即服务的 Azure Stack 集线器创建交互式功能验证测试。
author: mattbriggs
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a1ef4a77bdc4f4dd83a7bced78c85d64ae7836c3
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704720"
---
# <a name="interactive-feature-verification-testing"></a>交互式功能验证测试  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

你可以使用交互式功能验证测试框架来请求你的系统测试。 请求测试时，Microsoft 使用框架来准备需要手动交互步骤的测试。 Microsoft 可以使用框架将多个独立的自动测试链接在一起。

本文介绍了一个简单的手动方案。 测试检查替换 Azure Stack 集线器中的磁盘。 框架收集每个步骤的诊断日志。 你可以在发现问题时对其进行调试。 该框架还允许共享其他工具或进程生成的日志，并使你能够提供有关该方案的反馈。

> [!Important]  
> 本文介绍执行磁盘标识测试的步骤。 这只是一个演示，从测试通过工作流收集的任何结果不能用于新的解决方案验证。

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

    ![磁盘标识测试— Azure Stack 集线器中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2. 选择 "**编辑**" 并提供域管理员用户名和密码。

3. 选择要在其上启动测试的相应测试执行代理/DVM。

    ![选择测试执行代理— Azure Stack 集线器中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4. 选择 "**提交**" 以启动测试。

    ![查看并提交测试— Azure Stack 集线器中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image6.png)

5. 从上一步中选择的代理访问交互式测试的 UI。

    ![磁盘标识测试— Azure Stack 集线器中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image8.png)

6. 按照**文档**和**验证**链接，查看有关如何执行此方案的 Microsoft 的说明。

    ![磁盘标识测试中的文档和验证链接](media/azure-stack-vaas-interactive-feature-verification/image9.png)

7. 选择“**下一页**”。

    ![选择下一步— Azure Stack 集线器中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image10.png)

8. 按照说明运行预检查脚本。

    ![运行预检查脚本-Azure Stack 集线器中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image11.png)

9. 成功完成预检查脚本后，请按照 "**信息**" 选项卡上的**文档**和**验证**链接中所述，运行手动方案（磁盘替换）。

    ![运行手动方案-Azure Stack 集线器中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > 请不要在执行手动方案时关闭此对话框。

10. 执行完手动方案后，请按照说明运行 post 检查脚本。

    ![运行 postcheck 脚本-Azure Stack 集线器中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image13.png)

11. 手动方案（磁盘替换）成功完成后，选择 "**提交**"。

    ![提交驱动器标识测试— Azure Stack 集线器中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > 如果关闭窗口，则测试在完成之前将停止。

12. 提供测试体验的反馈。 这些问题将帮助 Microsoft 评估方案的成功率和发行质量。

    ![在 Azure Stack 中心提供交互式测试体验的反馈](media/azure-stack-vaas-interactive-feature-verification/image15.png)

13. 附加要提交给 Microsoft 的任何日志文件。

    ![附加日志文件-Azure Stack 中心中的交互式测试](media/azure-stack-vaas-interactive-feature-verification/image16.png)

14. 接受反馈提交 EULA。

15. 选择 "**提交**" 以将结果发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

- [监视和管理 Azure Stack 中心验证门户中的测试](azure-stack-vaas-monitor-test.md)
