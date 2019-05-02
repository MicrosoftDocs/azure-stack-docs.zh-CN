---
title: Azure Stack 验证作为服务的关键概念 |Microsoft Docs
description: 作为一项服务介绍在 Azure Stack 验证中的重要概念。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3830dc15cebe24a8fb910b641b7b986437aeec52
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298499"
---
# <a name="validation-as-a-service-key-concepts"></a>作为服务的关键概念验证

本文介绍作为一项服务 (VaaS) 验证中的重要概念。

## <a name="solutions"></a>解决方案

VaaS 解决方案表示与特定硬件清单的清单 (BoM) 的 Azure Stack 解决方案。 VaaS 解决方案充当针对 Azure Stack 解决方案运行的工作流的容器。

### <a name="create-a-solution-in-the-vaas-portal"></a>VaaS 门户中创建一个解决方案

1. 登录到[VaaS 门户](https://azurestackvalidation.com)。
2. 在解决方案仪表板中，选择**新的解决方案**。
3. 输入解决方案的名称。 有关命名建议，请参阅[VaaS 解决方案的命名约定](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions)。
4. 选择**保存**创建解决方案。

## <a name="workflows"></a>工作流

VaaS 解决方案的上下文中运行 VaaS 工作流。 它表示一组执行的 Azure Stack 部署的功能的测试套件。 应为 Azure Stack 解决方案的每个部署或软件更新创建工作流。

工作流进行分类的测试方案类型。 在非官方测试中，**测试轮次**工作流，可以选择测试中所有可用 VaaS 宣传材料。 在正式测试中，**验证**工作流目标 Microsoft 选中的特定测试方案。

![VaaS 工作流磁贴](media/tile_all-workflows.png)

> [!NOTE]
> **包验证**工作流当前支持两种方案：[验证 OEM 包](azure-stack-vaas-validate-oem-package.md)并[来自 Microsoft 的软件更新进行验证](azure-stack-vaas-validate-microsoft-updates.md)。

工作流类型的详细信息，请参阅[验证作为 Azure Stack 的服务是什么？](azure-stack-vaas-overview.md)。

### <a name="getting-started-with-vaas-workflows"></a>开始使用 VaaS 工作流

1. 在解决方案仪表板中创建新的解决方案，或选择一个现有。 此刷新，并激活工作流磁贴。
2. 若要创建新的工作流，请选择**启动**的任意磁贴。 特定于每个工作流的信息，请参阅以下文章：
    - 测试通过：[快速入门：用于验证的服务门户，作为计划第一次测试](azure-stack-vaas-schedule-test-pass.md)
    - 解决方案验证：[验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)
    - 包验证 （每月更新）：[验证来自 Microsoft 的软件更新](azure-stack-vaas-validate-microsoft-updates.md)
    - 包验证 （包签名）：[验证 OEM 程序包](azure-stack-vaas-validate-oem-package.md)

3. 若要管理或监视现有工作流，请选择上**管理**工作流磁贴上。 选择的工作流并使用名称**编辑**按钮以查看属性或更改常用的测试参数。

有关工作流属性和参数的详细信息，请参阅[工作流通用参数作为服务的 Azure Stack 验证](azure-stack-vaas-parameters.md)。

## <a name="tests"></a>测试数

VaaS 中的测试包含一系列针对 Azure Stack 解决方案运行的操作。 测试具有不同的预期的目的，由一个类别，如功能或可靠性和目标 Azure Stack 的一个或多个服务。 每个测试定义自己的一组指定的参数，其中一些是由包含工作流常见参数。

有关如何管理和监视测试的详细信息，请参阅[监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)。

有关测试参数详细信息，请参阅[工作流通用参数作为服务的 Azure Stack 验证](azure-stack-vaas-parameters.md)。

## <a name="agents"></a>代理

VaaS 代理驱动测试执行。 两种类型的代理运行 VaaS 测试：

- **云代理**。 这是默认代理 VaaS 中可用。 无需设置是必需的但这需要与你的环境的入站连接和 Azure Stack 终结点必须可从 internet 解析。 一些测试都不符合云代理。
- 一个**本地代理**。 这样可以与你的环境的入站连接不可行的方案中运行验证。 某些测试都需要通过本地代理，来执行。

本地代理不受限于任何特定的 Azure Stack 或 VaaS 解决方案。 最佳做法是，它们应运行 Azure Stack 环境外部。

添加本地代理的说明，请参阅[部署本地代理](azure-stack-vaas-local-agent.md)。

## <a name="next-steps"></a>后续步骤

- [作为一项服务验证的最佳做法](azure-stack-vaas-best-practice.md)