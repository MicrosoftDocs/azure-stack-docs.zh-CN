---
title: 作为服务关键概念的验证
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack 集线器验证即服务的关键概念。
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5199a9f3b8dcf9a3ac43d9563688cba8da949ce3
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294161"
---
# <a name="validation-as-a-service-key-concepts"></a>作为服务关键概念的验证

本文介绍验证即服务（VaaS）中的关键概念。

## <a name="solutions"></a>解决方案

VaaS 解决方案表示具有特定硬件材料清单（BoM）的 Azure Stack 集线器解决方案。 VaaS 解决方案充当针对 Azure Stack 中心解决方案运行的工作流的容器。

### <a name="create-a-solution-in-the-azure-stack-hub-validation-portal"></a>在 Azure Stack Hub 验证门户中创建解决方案

1. 登录到[Azure Stack 中心验证门户](https://azurestackvalidation.com)。
2. 在解决方案仪表板上，选择 "**新建解决方案**"。
3. 输入解决方案的名称。 有关命名建议，请参阅[VaaS 解决方案的命名约定](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions)。
4. 选择 "**保存**" 以创建解决方案。

## <a name="workflows"></a>工作流

VaaS 工作流在 VaaS 解决方案的上下文中运行。 它表示一组测试套件，用于执行 Azure Stack 中心部署的功能。 应为 Azure Stack 中心解决方案的每个部署或软件更新创建一个工作流。

工作流按测试方案类型分类。 在非正式测试中，**测试通过**工作流可让你从所有可用的 VaaS 宣传品中选择测试。 在官方测试中，**验证**工作流以 Microsoft 选择的特定测试方案为目标。

![VaaS 工作流磁贴](media/tile_all-workflows.png)

> [!NOTE]
> **包验证**工作流目前支持两种方案：[验证 OEM 包](azure-stack-vaas-validate-oem-package.md)和[验证 Microsoft 中的软件更新](azure-stack-vaas-validate-microsoft-updates.md)。

有关工作流类型的详细信息，请参阅[什么是 Azure Stack 中心的验证即服务？](azure-stack-vaas-overview.md)。

### <a name="getting-started-with-vaas-workflows"></a>VaaS 工作流入门

1. 在解决方案仪表板上，创建一个新的解决方案或选择一个现有解决方案。 这会刷新并激活工作流磁贴。
2. 若要创建新的工作流，请在任何磁贴上选择 "**启动**"。 有关特定于每个工作流的信息，请参阅以下文章：
    - 测试通过：[快速入门：使用 Azure Stack 集线器验证门户计划第一次测试](azure-stack-vaas-schedule-test-pass.md)
    - 解决方案验证：[验证新的 Azure Stack 中心解决方案](azure-stack-vaas-validate-solution-new.md)
    - 包验证（每月更新）：[从 Microsoft 验证软件更新](azure-stack-vaas-validate-microsoft-updates.md)
    - 包验证（包签名）：[验证 OEM 包](azure-stack-vaas-validate-oem-package.md)

3. 若要管理或监视现有工作流，请在 "工作流" 磁贴上选择 "在**管理**"。 选择工作流的名称，并使用 "**编辑**" 按钮查看属性或更改常见的测试参数。

有关工作流属性和参数的详细信息，请参阅[Azure Stack Hub 验证即服务的工作流常见参数](azure-stack-vaas-parameters.md)。

## <a name="tests"></a>测试

VaaS 中的测试包含一套针对 Azure Stack 中心解决方案运行的操作。 测试具有不同的预期用途（例如功能或可靠性），并面向 Azure Stack 集线器的一项或多项服务。 每个测试都定义了其自己的一组参数，其中一些参数由包含工作流的常用参数指定。

有关管理和监视测试的详细信息，请参阅[在 Azure Stack Hub 验证门户中监视和管理测试](azure-stack-vaas-monitor-test.md)。

有关测试参数的详细信息，请参阅[Azure Stack Hub 验证即服务的工作流常见参数](azure-stack-vaas-parameters.md)。

## <a name="agents"></a>代理

VaaS 代理驱动测试执行。 两种类型的代理运行 VaaS 测试：

- **云代理**。 这是 VaaS 中可用的默认代理。 无需进行任何设置，但这需要与环境进行绑定连接，并且可以从 internet 解析 Azure Stack 中心终结点。 某些测试与云代理不兼容。
- **本地代理**。 这样，你就可以在不可行的情况下，在与环境进行绑定连接的情况下运行验证。 某些测试需要通过本地代理执行。

本地代理未绑定到任何特定的 Azure Stack 集线器或 VaaS 解决方案。 最佳做法是，它们应在 Azure Stack 中心环境之外运行。

有关添加本地代理的说明，请参阅[部署本地代理](azure-stack-vaas-local-agent.md)。

## <a name="next-steps"></a>后续步骤

- [验证即服务的最佳实践](azure-stack-vaas-best-practice.md)