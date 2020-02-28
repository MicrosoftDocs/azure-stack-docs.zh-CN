---
title: 验证即服务概述
titleSuffix: Azure Stack Hub
description: Azure Stack 集线器验证即服务的概述。
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4f6255ee26eb686b78640f95b44ee5286693d081
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704635"
---
# <a name="what-is-validation-as-a-service-for-azure-stack-hub"></a>什么是 Azure Stack 集线器的验证即服务？

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

验证即服务（VaaS）是一项本机 Azure 服务，专为与 Microsoft 联合工程 Azure Stack 中心产品的解决方案合作伙伴而设计。 解决方案合作伙伴可以使用该服务来检查其解决方案是否满足 Microsoft 的要求并按预期方式在 Azure Stack 中心工作。

VaaS 的主要用途是：

- 正在验证新 Azure Stack 中心解决方案。
- 验证对 Azure Stack 中心软件所做的更改。
- 对部署过程中使用的解决方案伙伴包进行数字签名。
- 预览 VaaS 测试宣传资料。

## <a name="validate-a-new-azure-stack-hub-solution"></a>验证新的 Azure Stack 中心解决方案

合作伙伴使用**解决方案验证**工作流来验证新的 Azure Stack 中心解决方案。 解决方案必须通过所需的硬件实验室工具包（HLK） Azure Stack 集线器组件测试。 若要验证一系列硬件配置，必须为每个新解决方案运行两次工作流：每个工作流都适用于最小和最大配置。

有关详细信息，请参阅[验证新的 Azure Stack 中心解决方案](azure-stack-vaas-validate-solution-new.md)。

## <a name="validate-changes-to-the-azure-stack-hub-software"></a>验证对 Azure Stack 中心软件所做的更改

合作伙伴使用**包验证**工作流来检查其解决方案是否适用于最新的 Azure Stack 中心软件更新。 必须在 Microsoft 推荐的硬件环境中运行包验证工作流，其中修补和更新（P & U）用于应用更新。 建议也在基线生成上运行工作流。

有关详细信息，请参阅[通过 Microsoft 验证软件更新](azure-stack-vaas-validate-microsoft-updates.md)。

## <a name="get-digitally-signed-solution-partner-packages"></a>获取数字签名的解决方案伙伴包

除了验证 Azure Stack 集线器更新，合作伙伴还使用**包验证**工作流来验证对 OEM 自定义包的更新。 这些更新包括 Azure Stack 集线器合作伙伴特定的驱动程序、固件和 Azure Stack 中心软件部署过程中使用的其他软件。 使用至少受支持的最小大小的解决方案，在当前版本的 Azure Stack 中心软件上部署要验证的包。 在执行测试之前，会将包提交给 VaaS。 如果测试成功，请通知[vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)包已完成测试，并应通过 Azure Stack 中心数字签名进行数字签名。 Microsoft 对包进行签名，并通知 Azure Stack 中心伙伴包可在 Azure Stack 中心验证门户中下载。

有关详细信息，请参阅 "[验证 OEM 包](azure-stack-vaas-validate-oem-package.md)"。

## <a name="preview-vaas-test-collateral"></a>预览 VaaS 测试宣传资料

Microsoft 定期在 Azure Stack 中心提供新功能。 作为将这些功能提供给市场的开发过程的一部分，在**测试通过**工作流中提供了新的测试辅助功能。 测试通过工作流包括来自其他工作流的测试宣传资料，以允许执行非正式测试。 不要使用测试通过工作流提交要审批的结果。 使用解决方案验证和包验证工作流来获得解决方案的官方批准。

有关详细信息，请参阅[快速入门：使用 Azure Stack 集线器验证门户计划第一次测试](azure-stack-vaas-schedule-test-pass.md)。

## <a name="validation-workflow-tests-summary"></a>验证工作流测试摘要

| 验证工作流 | 必需的测试 |
|----|------------|
| [新解决方案验证](azure-stack-vaas-validate-solution-new.md) | 云模拟引擎<br>计算 SDK 操作套件<br>磁盘标识测试<br>KeyVault 扩展 SDK 操作套件<br>KeyVault SDK 操作套件<br>网络 SDK 操作套件<br>存储帐户 SDK 操作套件<br> |
| [OEM 包验证](azure-stack-vaas-validate-oem-package.md) | OEM 扩展包验证<br>云模拟引擎 |
| [每月更新验证](azure-stack-vaas-validate-microsoft-updates.md) | 每月 Azure Stack 中心更新验证<br>云模拟引擎<br> |

## <a name="next-steps"></a>后续步骤

- [将验证设置为服务资源](azure-stack-vaas-set-up-resources.md)
- 了解[作为服务关键概念的验证](azure-stack-vaas-key-concepts.md)
