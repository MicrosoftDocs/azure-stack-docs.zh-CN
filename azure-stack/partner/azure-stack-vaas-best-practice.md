---
title: Azure Stack 验证最佳实践。 | Microsoft Docs
description: 本文讨论验证即服务的最佳实践。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 930a8ea40fde7a021a893e5289d16fa73398300f
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167268"
---
# <a name="best-practices-for-validation-as-a-service"></a>验证即服务的最佳实践

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

本文介绍了有关在验证即服务（VaaS）中管理资源的最佳实践。 有关 VaaS 资源的概述，请参阅[作为服务关键概念的验证](azure-stack-vaas-key-concepts.md)。

## <a name="solution-management"></a>解决方案管理

### <a name="naming-convention-for-vaas-solutions"></a>VaaS 解决方案的命名约定

为在 VaaS 中注册的所有解决方案使用一致的命名约定。 例如，可以按如下所示的硬件属性构造解决方案名称：

|产品名称 | 唯一硬件元素1 | 唯一硬件元素2 | 解决方案名称
|---|---|---|---|
我的解决方案 XYZ |  所有闪存 | 我的交换机 X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>何时创建新的 VaaS 解决方案

针对同一个硬件 SKU 运行工作流时，请使用相同的 VaaS 解决方案。 仅当硬件 SKU 发生变化时，才应创建新的 VaaS 解决方案。

## <a name="workflow-management"></a>工作流管理

### <a name="naming-convention-for-vaas-workflows"></a>VaaS 工作流的命名约定

对所有 VaaS 工作流运行使用一致的命名约定。 例如，在以下生成属性中构造工作流名称，如下所示：

|内部版本号（主要） | 日期 | 解决方案大小 | 工作流名称
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>后续步骤

- 了解[作为服务关键概念的验证](azure-stack-vaas-key-concepts.md)
