---
title: Azure Stack Hub 中的区域管理
titleSuffix: Azure Stack Hub
description: Azure Stack Hub 中的区域管理概述。
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: e40d79dcac58035172d4e292461fd0950ee77979
ms.sourcegitcommit: 03aad17afe8519536066c735c59ad1bdfe8de083
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89041632"
---
# <a name="region-management-in-azure-stack-hub"></a>Azure Stack Hub 中的区域管理

Azure Stack Hub 使用“区域”的概念，这些区域是由构成 Azure Stack Hub 基础结构的硬件资源组成的逻辑实体。** 在区域管理中，可以找到成功操作 Azure Stack Hub 基础结构所需的所有资源。

一个集成系统部署（称为 *Azure Stack Hub 云*）可构成一个区域。 每个 Azure Stack 开发工具包 (ASDK) 都有一个名为 **local** 的区域。 如果部署第二个 Azure Stack Hub 集成系统，或者在单独的硬件上设置 ASDK 的另一个实例，则此 Azure Stack Hub 云为不同的区域。

## <a name="information-available-through-the-region-management-tile"></a>通过区域管理磁贴提供的信息

Azure Stack Hub 在“区域管理”**** 磁贴中提供了一组区域管理功能。 Azure Stack Hub 操作员可在管理员门户的默认仪表板上访问此磁贴。 在此屏幕中，你可以监视和更新 Azure Stack 中心区域及其组件，它们是特定于区域的。

![Azure Stack Hub 管理员门户中的“区域管理”磁贴](media/azure-stack-region-management/image1.png)

如果选择“区域管理”**** 磁贴中的一个区域，可以访问以下信息：

[![Azure Stack Hub 管理员门户中“区域管理”边栏选项卡上的窗格说明](media/azure-stack-region-management/regionssm.png "Azure Stack Hub 管理员门户中的“区域管理”边栏选项卡")](media/azure-stack-region-management/regions.png#lightbox)

1. **资源菜单**：访问不同的基础结构管理区域，并查看和管理用户资源，例如存储帐户和虚拟网络。

2. **警报**：列出系统范围内的警报，并提供有关每个警报的详细信息。

3. **更新**：查看 Azure Stack Hub 基础结构的当前版本、可用更新和更新历史记录。 也可以更新集成系统。

4. **资源提供程序**：管理运行 Azure Stack Hub 时所需组件提供的用户功能。 每个资源提供程序均附带管理体验。 这项体验可能包括特定提供程序的警报、指标和其他特定于资源提供程序的管理功能。

5. **基础结构角色**：运行 Azure Stack Hub 时所需的组件。 仅报告警报的基础结构角色会列出。 通过选择角色，可以查看与角色关联的警报，以及运行此角色的角色实例。

6. **属性**：“区域管理”边栏选项卡中的环境注册状态和详细信息。 状态可能是“已注册”、“未注册”或“已过期”。**** **** **** 如果是已注册，则还会显示用于注册 Azure Stack Hub 的 Azure 订阅 ID，以及注册资源组和名称。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack Hub 中监视运行状况和警报](azure-stack-monitor-health.md)
- [在 Azure Stack Hub 中管理更新](azure-stack-updates.md)
