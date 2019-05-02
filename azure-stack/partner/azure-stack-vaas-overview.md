---
title: 验证用作 Azure Stack 的服务的概述 |Microsoft Docs
description: 作为一项服务的 Azure Stack 验证的概述。
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
ms.openlocfilehash: ca22f29597cb452d6d33338b8ba0367c9377f6fb
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297916"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>验证用作 Azure Stack 的服务是什么？

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

作为一项服务 (VaaS) 验证是设计为解决方案合作伙伴共同工程与 Microsoft Azure Stack 产品/服务的本机 Azure 服务。 解决方案合作伙伴可以使用该服务检查其解决方案符合 Microsoft 的要求以及按预期方式使用 Azure Stack 工作。

VaaS 的主要用途是：

- 验证新的 Azure Stack 解决方案
- 验证 Azure Stack 软件的更改
- 进行数字签名解决方案合作伙伴包部署过程中使用
- 预览 VaaS 测试附件

## <a name="validate-a-new-azure-stack-solution"></a>验证新的 Azure Stack 解决方案

合作伙伴使用**解决方案验证**工作流，以验证新的 Azure Stack 解决方案。 该解决方案必须通过所需的硬件 Lab Kit (HLK) Azure Stack 组件测试。 若要验证的硬件配置范围，请在工作流必须运行两次为每个新的解决方案： 每次为最小和最大配置。

有关详细信息，请参阅[验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)。

## <a name="validate-changes-to-the-azure-stack-software"></a>验证对 Azure Stack 软件所做的更改

合作伙伴使用**包验证**工作流，以检查他们的解决方案，适用于最新的 Azure Stack 软件更新。 包验证工作流必须运行 Microsoft 推荐的硬件环境的修补和更新 (& U) 用于应用更新。 建议也在基线版本上运行工作流。

有关详细信息，请参阅[验证来自 Microsoft 的软件更新](azure-stack-vaas-validate-microsoft-updates.md)。

## <a name="get-digitally-signed-solution-partner-packages"></a>获取进行了数字签名的解决方案合作伙伴程序包

验证 Azure Stack 的更新，除了合作伙伴使用**包验证**工作流，以便验证对 OEM 自定义包，其中包括 Azure Stack 特定于合作伙伴的驱动程序、 固件和其他软件更新在 Azure Stack 软件的部署过程中使用。 部署验证的 Azure Stack 软件至少使用将受支持的最小大小的解决方案的当前版本的包。 包执行测试前提交给 VaaS。 如果测试成功，通知[ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)使用 Azure Stack 数字签名的包已完成测试并进行数字签名。 Microsoft 对包进行签名，并通知 Azure Stack 合作伙伴包是可供下载的 VaaS 门户。

有关详细信息，请参阅[验证 OEM 程序包](azure-stack-vaas-validate-oem-package.md)。

## <a name="preview-vaas-test-collateral"></a>预览 VaaS 测试附件

Microsoft 定期对新功能在 Azure Stack 中可用。 作为向市场提供这些功能的开发过程的一部分，新的测试附件都可以在**测试轮次**工作流。 测试通过工作流包括从其他工作流以允许非官方测试执行的测试附件。 不使用测试通过工作流提交以供审核的结果。 使用解决方案验证和包验证工作流来获取官方批准你的解决方案。

有关详细信息，请参阅[快速入门：用于验证的服务门户，作为计划第一次测试](azure-stack-vaas-schedule-test-pass.md)。

## <a name="validation-workflow-tests-summary"></a>验证工作流测试摘要

| 验证工作流 | 所需的测试 |
|----|------------|
| [新解决方案验证](azure-stack-vaas-validate-solution-new.md) | 云模拟引擎<br>计算 SDK 操作套件<br>磁盘标识测试<br>密钥保管库扩展 SDK 操作套件<br>密钥保管库 SDK 操作套件<br>网络 SDK 操作套件<br>存储帐户 SDK 操作套件<br> |
| [OEM 包验证](azure-stack-vaas-validate-oem-package.md) | OEM 扩展包验证<br>云模拟引擎 |
| [每月更新验证](azure-stack-vaas-validate-microsoft-updates.md) | 每月的 Azure Stack 更新验证<br>云模拟引擎<br> |

## <a name="next-steps"></a>后续步骤

- [设置您的验证为服务资源](azure-stack-vaas-set-up-resources.md)
- 了解有关[作为服务的关键概念验证](azure-stack-vaas-key-concepts.md)
