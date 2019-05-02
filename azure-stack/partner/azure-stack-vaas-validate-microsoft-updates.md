---
title: 验证 Azure Stack 验证作为一项服务中的 Microsoft 软件更新 |Microsoft Docs
description: 了解如何从作为服务的验证与 Microsoft 的软件更新进行验证。
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
ms.openlocfilehash: 67d17ffc7c2b8fcdb766ccf8a8248da8665a42d8
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297564"
---
# <a name="validate-software-updates-from-microsoft"></a>验证来自 Microsoft 的软件更新

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 会定期发布到 Azure Stack 软件更新。 这些更新提供给 Azure Stack coengineering 合作伙伴。 中的公开可用的提前提供了更新。 可以针对你的解决方案检查更新，并向 Microsoft 提供反馈。

指定 Microsoft 软件更新部署到 Azure Stack 使用命名约定，例如，1803年的该值指示此更新是为 2018 年 3 月。 有关 Azure Stack 更新策略、发布节奏以及发行说明的信息，请参阅 [Azure Stack 服务策略](../operator/azure-stack-servicing-policy.md)。

## <a name="prerequisites"></a>必备组件

在执行 VaaS 中的每月更新过程之前，您应熟悉以下各项：

- [作为服务的关键概念验证](azure-stack-vaas-key-concepts.md)
- [交互式功能验证测试](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>所需的测试

应为每月软件验证的以下顺序执行以下测试：

1. 每月的 Azure Stack 更新验证
2. 云模拟引擎

## <a name="validating-software-updates"></a>验证软件更新

1. 创建一个新**包验证**工作流。
1. 所需的测试更高版本，请按照中的说明[运行包验证测试](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)。 请参阅以下部分了解更多说明**每月的 Azure Stack 更新验证**测试。

### <a name="apply-the-monthly-update"></a>应用每月更新

1. 选择要执行针对测试代理。
1. 计划**每月的 Azure Stack 更新验证**。
1. 提供当前模具上部署的 OEM 扩展包的位置和 OEM 扩展包将在更新期间应用到的位置。 若要配置的这些包的 Url，请参阅[管理包进行验证](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation)。
1. 请在 UI 中的步骤执行从所选的代理。

如果您有问题或顾虑，请联系[VaaS 帮助](mailto:vaashelp@microsoft.com)。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)