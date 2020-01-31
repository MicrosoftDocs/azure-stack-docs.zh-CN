---
title: 在 Azure Stack 验证即服务中验证 Microsoft 的软件更新
description: 了解如何通过验证即服务验证 Microsoft 的软件更新。
author: mattbriggs
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5e4438f0c3a95b2254422a428741a28b685cb9f5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884006"
---
# <a name="validate-software-updates-from-microsoft"></a>从 Microsoft 验证软件更新

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 会定期发布 Azure Stack 软件的更新。 提供这些更新是为了 Azure Stack 共同工程合作伙伴。 这些更新是在公开发布之前提供的。 你可以根据解决方案检查更新，并向 Microsoft 提供反馈。

使用命名约定指定 Azure Stack 的 Microsoft 软件更新，例如1803，表示更新的值为2018年3月。 有关 Azure Stack 更新策略、步调和发行说明的信息，请参阅[Azure Stack 服务策略](../operator/azure-stack-servicing-policy.md)。

## <a name="prerequisites"></a>必备组件

在 VaaS 中练习每月更新过程之前，应熟悉以下各项：

- [作为服务关键概念的验证](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>必需的测试

对于每月软件验证，必须按以下顺序执行以下测试：

- OEM 验证工作流

## <a name="validating-software-updates"></a>正在验证软件更新

1. 创建新的**包验证**工作流。
1. 对于上面所需的测试，请按照[运行包验证测试](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)中的说明进行操作。 有关**每月 Azure Stack 更新验证**测试的其他说明，请参阅下面的部分。

如果有疑问或问题，请联系[VaaS 帮助](mailto:vaashelp@microsoft.com)。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)