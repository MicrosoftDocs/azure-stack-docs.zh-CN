---
title: Azure Stack 中的区域管理 |Microsoft Docs
titleSuffix: Azure Stack
description: Azure Stack 中的区域管理概述。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: 38004b88f43ef59448ca99c3eb2762e5ca63e89c
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802281"
---
# <a name="region-management-in-azure-stack"></a>Azure Stack 中的区域管理

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 使用*区域*的概念，这是由组成 Azure Stack 基础结构的硬件资源组成的逻辑实体。 在区域管理中，可以找到成功操作 Azure Stack 基础结构所需的所有资源。

一个集成系统部署（称为*Azure Stack 云*）构成单个区域。 每个 Azure Stack 开发工具包（ASDK）都有一个名为**local**的区域。 如果部署第二个 Azure Stack 集成系统，或在单独的硬件上设置 ASDK 的另一个实例，则此 Azure Stack 云为不同的区域。

## <a name="information-available-through-the-region-management-tile"></a>通过 "区域管理" 磁贴提供的信息

Azure Stack 在 "**区域管理**" 磁贴中提供了一组区域管理功能。 此磁贴可在管理员门户的默认仪表板上的 Azure Stack 运算符中使用。 通过此磁贴，你可以监视和更新 Azure Stack 区域及其组件，它们是特定于区域的。

![Azure Stack 管理员门户中的 "区域管理" 磁贴](media/azure-stack-region-management/image1.png)

如果单击 "**区域管理**" 磁贴中的某个区域，则可以访问以下信息：

[![Azure Stack 管理员门户中 "区域管理" 边栏选项卡上的窗格说明](media/azure-stack-region-management/regionssm.png "Azure Stack 管理员门户中的 "区域管理" 边栏选项卡")](media/azure-stack-region-management/regions.png#lightbox)

1. **资源菜单**：访问不同的基础结构管理区域，以及查看和管理用户资源（如存储帐户和虚拟网络）。

2. **警报**：列出系统范围内的警报，并提供有关每个警报的详细信息。

3. **更新**：查看当前版本的 Azure Stack 基础结构、可用更新和更新历史记录。 你还可以更新集成系统。

4. **资源提供程序**：管理运行 Azure Stack 所需的组件所提供的用户功能。 每个资源提供程序都有一个管理体验。 此体验可以包括特定于资源提供程序的特定提供程序、度量值和其他管理功能的警报。

5. **基础结构角色**：运行 Azure Stack 所需的组件。 仅列出报告警报的基础结构角色。 通过选择角色，你可以查看与角色关联的警报，以及运行此角色的角色实例。

6. **属性**： "区域管理" 边栏选项卡中您的环境的注册状态和详细信息。 状态可以为 "**已注册**"、"**未注册**" 或 "已**过期**"。 如果已注册，还会显示用于注册 Azure Stack 的 Azure 订阅 ID，以及注册资源组和名称。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 中监视运行状况和警报](azure-stack-monitor-health.md)
- [在 Azure Stack 中管理更新](azure-stack-updates.md)
