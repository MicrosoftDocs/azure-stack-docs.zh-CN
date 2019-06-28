---
title: 适用于 Azure Stack 的云服务提供商的使用情况报告基础结构 | Microsoft Docs
description: Azure Stack 包含跟踪由云服务提供商 (CSP) 提供服务的租户的使用情况所需的基础结构，并将使用情况转发到 Azure。
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 0b4ff1e6c76bedc4618bfa527b0045d7bfce41af
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419482"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>适用于云服务提供商的使用情况报告基础结构

Azure Stack 包括实时跟踪使用情况所需的基础结构，因此会将使用情况转发到 Azure。 在 Azure 中，Azure Commerce 会处理使用情况数据并根据使用情况向相应的 Azure 订阅收费。 这与在全球版 Azure 云中监视使用情况跟踪的方式相同。

某些概念在全球版 Azure 与 Azure Stack 中是一致的。 Azure Stack 有本地订阅，其履行的角色类似于 Azure 订阅。 本地订阅仅在本地适用。 将使用情况转发到 Azure 时，本地订阅会映射到 Azure 订阅。

Azure Stack 有本地使用情况计量。 本地使用情况将映射到在 Azure Commerce 中使用的计量。 但是，计量 ID 是不同的。 在本地使用的计量多余 Microsoft 用于计费的计量。

Azure Stack 和 Azure 中的服务计价方式有一些差异。 例如，在 Azure Stack 中，只根据 vcore/小时对 VM 收费，对所有 VM 系列的费率是相同的，这不同于 Azure。 原因是，在全球版 Azure 中，不同的价格反映了不同的硬件。 在 Azure Stack 中，由客户提供硬件，因此没有理由根据不同的 VM 类实施不同的费率。

可以找到在 Commerce 中使用的 Azure Stack 计量的情况，并可在合作伙伴中心找到其价格，采用的方式与对 Azure 服务的方式一样：

1. 在合作伙伴中心，请转到**仪表板菜单**，然后选择**销售**，然后选择**定价和优惠**。
2. 在“基于使用情况的服务”下  ，选择“当前”  。
3. 打开“全球 CSP 价目表中的 Azure”  电子表格。
4. 以 **Region = Azure Stack** 为条件进行筛选。

## <a name="terms-used-for-billing-and-usage"></a>用于计费和使用情况的条款

以下条款和概念适用于 Azure Stack 中的使用情况和计费：

| 术语 | 定义 |
| --- | --- |
| 直接 CSP 合作伙伴 | 直接云服务提供商 (CSP) 合作伙伴直接从 Microsoft 接收 Azure 和 Azure Stack 使用情况的发票，然后对客户直接收费。 |
| 间接 CSP | 间接经销商与间接提供商（也称分销商）合作。 经销商负责招揽最终客户；间接提供商负责维持与 Microsoft 的计费关系、管理客户计费，以及提供其他服务（例如产品支持）。 |
| 最终客户 | 最终客户是企业和政府机构，拥有应用程序以及其他在 Azure Stack 上运行的工作负荷。 |

## <a name="next-steps"></a>后续步骤

- 若要详细了解 CSP 计划，请参阅[云解决方案](https://partner.microsoft.com/solutions/microsoft-cloud-solutions)。
- 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
