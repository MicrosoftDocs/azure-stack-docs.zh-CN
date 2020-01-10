---
title: Azure Stack 中心中的区域管理 |Microsoft Docs
titleSuffix: Azure Stack Hub
description: Azure Stack 中心中的区域管理概述。
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
ms.openlocfilehash: 1e7127647ef4b0653e759c7008c9874e9a5d2be2
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809740"
---
# <a name="region-management-in-azure-stack-hub"></a>Azure Stack 中心的区域管理

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

Azure Stack 中心使用区域的概念，*区域*是逻辑实体，由组成 Azure Stack 中心基础结构的硬件资源组成。 在区域管理中，可以找到成功操作 Azure Stack 中心基础结构所需的所有资源。

一个集成系统部署（称为*Azure Stack 中心云*）组成一个区域。 每个 Azure Stack 开发工具包（ASDK）都有一个名为**local**的区域。 如果部署第二个 Azure Stack 集线器集成系统，或在单独的硬件上设置 ASDK 的另一个实例，则此 Azure Stack 中心云是不同的区域。

## <a name="information-available-through-the-region-management-tile"></a>通过 "区域管理" 磁贴提供的信息

Azure Stack 集线器具有 "**区域管理**" 磁贴中提供的一组区域管理功能。 此磁贴可在管理员门户的默认仪表板上的 Azure Stack 中心操作员使用。 通过此磁贴，你可以监视和更新 Azure Stack 中心区域及其组件，它们是特定于区域的。

![Azure Stack 中心管理员门户中的 "区域管理" 磁贴](media/azure-stack-region-management/image1.png)

如果单击 "**区域管理**" 磁贴中的某个区域，则可以访问以下信息：

[![Azure Stack 中心管理员门户中 "区域管理" 边栏选项卡上的窗格说明](media/azure-stack-region-management/regionssm.png "Azure Stack 中心管理员门户中的 "区域管理" 边栏选项卡")](media/azure-stack-region-management/regions.png#lightbox)

1. **资源菜单**：访问不同的基础结构管理区域，以及查看和管理用户资源（如存储帐户和虚拟网络）。

2. **警报**：列出系统范围内的警报，并提供有关每个警报的详细信息。

3. **更新**：查看当前版本的 Azure Stack 中心基础结构、可用更新和更新历史记录。 你还可以更新集成系统。

4. **资源提供程序**：管理运行 Azure Stack 中心所需的组件所提供的用户功能。 每个资源提供程序都有一个管理体验。 此体验可以包括特定于资源提供程序的特定提供程序、度量值和其他管理功能的警报。

5. **基础结构角色**：在 Azure Stack 集线器上运行所需的组件。 仅列出报告警报的基础结构角色。 通过选择角色，你可以查看与角色关联的警报，以及运行此角色的角色实例。

6. **属性**： "区域管理" 边栏选项卡中您的环境的注册状态和详细信息。 状态可以为 "**已注册**"、"**未注册**" 或 "已**过期**"。 如果已注册，还会显示用于注册 Azure Stack 中心的 Azure 订阅 ID，以及注册资源组和名称。

## <a name="next-steps"></a>后续步骤

- [监视 Azure Stack 集线器中的运行状况和警报](azure-stack-monitor-health.md)
- [管理 Azure Stack 集线器中的更新](azure-stack-updates.md)
