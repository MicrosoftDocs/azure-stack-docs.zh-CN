---
title: 从 Microsoft 验证软件更新
titleSuffix: Azure Stack Hub
description: 了解如何通过将 Azure Stack 集线器验证作为服务来验证 Microsoft 的软件更新。
author: mattbriggs
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a8e0b3ee678fc56a94a947ab6d390d9e99296977
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625350"
---
# <a name="validate-software-updates-from-microsoft"></a>从 Microsoft 验证软件更新

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft 会定期发布 Azure Stack 中心软件的更新。 这些更新提供给 Azure Stack 中心共同工程合作伙伴。 更新是在公开发布更新之前提供的。 你可以根据解决方案检查更新，并向 Microsoft 提供反馈。

使用命名约定指定 Azure Stack 集线器的 Microsoft 软件更新。 例如，名称1803表示更新的2018年3月。 有关 Azure Stack 中心服务策略和发行说明的信息，请参阅[Azure Stack 中心维护策略](../operator/azure-stack-servicing-policy.md)。

## <a name="prerequisites"></a>必备条件

在执行验证即服务（VaaS）中的每月更新过程之前，应熟悉以下各项：

- [作为服务关键概念的验证](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>必需的测试

对于每月软件验证，必须按以下顺序执行以下测试：

- OEM 验证工作流

## <a name="validating-software-updates"></a>正在验证软件更新

1. 创建新的**包验证**工作流。
1. 对于上面所需的测试，请按照[运行包验证测试](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests)中的说明进行操作。 请参阅以下部分，了解有关**每月 Azure Stack 中心更新验证**测试的其他说明。

如果有疑问或问题，请联系[VaaS 帮助](mailto:vaashelp@microsoft.com)。

## <a name="next-steps"></a>后续步骤

- [监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)