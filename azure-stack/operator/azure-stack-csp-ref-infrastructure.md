---
title: 适用于 Azure Stack 中心的云解决方案提供商的使用情况报告基础结构 |Microsoft Docs
description: 了解用于跟踪云解决方案提供商（CSP）所服务租户的使用情况报告基础结构。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 6dfb744b4e8a91036a16c4461ccbe584305af384
ms.sourcegitcommit: ecb541f53255c6a4433724ad2d20fb93c4720ce1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76706807"
---
# <a name="usage-reporting-infrastructure-for-cloud-solution-providers"></a>云解决方案提供商的使用情况报告基础结构

Azure Stack 中心包含跟踪使用情况时所需的基础结构，并将其转发到 Azure。 在 Azure 中，Azure Commerce 处理[使用情况数据并](azure-stack-billing-and-chargeback.md)向适当的 Azure 订阅收取费用。 此过程的工作方式与在全球 Azure 云中使用跟踪的方式相同。

全局 Azure 与 Azure Stack 中心之间的一些概念是一致的。 Azure Stack 中心具有本地订阅，可满足 Azure 订阅的类似角色。 本地订阅仅在本地有效。 将使用情况转发到 Azure 时，本地订阅会映射到 Azure 订阅。

Azure Stack 中心具有本地使用情况计量。 本地使用情况映射到 Azure commerce 中使用的计量。 但计量 Id 不同。 与 Microsoft 用于计费的指标在本地更多。

在 Azure Stack 集线器和 Azure 中，服务的定价方式有所不同。 例如，在 Azure Stack 集线器中，Vm 费用仅基于 vcore/小时，与 Azure 不同，每个 VM 系列的费率都相同。 原因在于，在全球 Azure 中，不同的价格反映了不同的硬件。 在 Azure Stack 中心，客户提供硬件，因此没有理由为不同的 VM 类收取不同的费率。

你可以了解有关在合作伙伴中心使用的 Azure Stack 中心计量器和其价格的信息。 此过程与适用于 Azure 服务的过程相同：

1. 在合作伙伴中心，请在 "**仪表板**" 菜单上，选择 "**销售**"，然后选择 "**定价和产品/服务**"。
2. 在 "**基于使用情况的服务**" 下，选择 "**当前**"。
3. **在全局 CSP 价目表**电子表格中打开 Azure。
4. 按**区域筛选 Azure Stack 中心**。

## <a name="terms-used-for-billing-and-usage"></a>用于计费和使用情况的术语

以下术语和概念用于 Azure Stack 中心中的使用情况和计费：

| 条款 | 定义 |
| --- | --- |
| 直接 CSP 合作伙伴 | 直接 CSP 合作伙伴直接从 Microsoft 接收适用于 Azure 的发票并 Azure Stack 集线器使用情况并直接向客户收取费用。 |
| 间接 CSP | 间接经销商使用间接提供程序（也称为分发服务器）。 经销商招聘最终用户;间接提供商与 Microsoft 保持计费关系，管理客户计费，并提供其他服务，如产品支持。 |
| 最终客户 | 最终客户是企业和政府机构，拥有在 Azure Stack Hub 上运行的应用和其他工作负荷。 |

## <a name="next-steps"></a>后续步骤

- 若要了解有关 CSP 计划的详细信息，请参阅[云解决方案](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要了解有关如何从 Azure Stack 中心检索资源使用情况信息的详细信息，请参阅[Azure Stack 中心中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
