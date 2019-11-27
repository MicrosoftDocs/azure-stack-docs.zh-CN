---
title: 更换 Azure Stack 集成系统上的缩放单元节点
titleSuffix: Azure Stack
description: 了解如何更换 Azure Stack 集成系统上的物理缩放单元节点。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 255802151183cc2b832aaa64f2110b9c9052a0ba
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465458"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>更换 Azure Stack 集成系统上的缩放单元节点

*适用于：Azure Stack 集成系统*

本文介绍更换 Azure Stack 集成系统上的物理计算机（也称为缩放单元节点）的一般过程。 实际的缩放单元节点更换步骤将因原始设备制造商 (OEM) 硬件供应商而异。 请参阅供应商的现场可更换单元 (FRU) 文档来了解特定于你的系统的详细步骤。

> [!CAUTION]  
> 固件分级对于本文中所述的操作的成功至关重要。 缺少此步骤可能会导致系统不稳定、性能降低、安全线程，或阻止 Azure Stack 自动部署操作系统。 在更换硬件时，请始终咨询硬件合作伙伴的文档，以确保应用的固件与[Azure Stack 管理员门户](azure-stack-updates.md)中显示的 OEM 版本匹配。 有关详细信息和合作伙伴文档的链接，请参阅[替换硬件组件](azure-stack-replace-component.md)。

以下流程图显示更换整个缩放单元节点的一般 FRU 过程。

![更换节点过程的流程图](media/azure-stack-replace-node/replacenodeflow.png)

*根据硬件的物理条件，可能不需要此操作。

> [!Note]  
> 如果关闭操作失败，建议使用 "排出" 操作，然后使用 "停止" 操作。 有关详细信息，请参阅 [Azure Stack 中的缩放单元节点操作](https://docs.microsoft.com/azure-stack/operator/azure-stack-node-actions)。

## <a name="review-alert-information"></a>查看警报信息

如果缩放单元节点已关闭，你会收到以下严重警报：

- 节点未连接到网络控制器
- 节点不可访问，无法实现虚拟机放置
- 缩放单元节点处于脱机状态

![缩放单元节点关闭的警报列表](media/azure-stack-replace-node/nodedownalerts.png)

如果开启“缩放单元节点已脱机”警报，警报说明会包含不可访问的缩放单元节点。 也可能会在硬件生命周期主机上运行的 OEM 特定的监视解决方案中收到其他警报。

![节点脱机警报的详细信息](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>缩放单元节点更换过程

提供以下步骤作为缩放单元节点更换过程的高级概述。 有关系统特有的详细步骤，请参阅 OEM 硬件供应商的 FRU 文档。 请勿在未参考 OEM 提供的文档的情况下执行这些步骤。

1. 使用 "**关闭**" 操作以正常关闭缩放单位节点。 根据硬件的物理条件，可能不需要此操作。

2. 万一关闭操作失败，请使用[清空](azure-stack-node-actions.md#drain)操作使缩放单元节点进入维护模式。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 在任何情况下，只能同时禁用一个节点并关机，而不中断 S2D（存储空间直通）。

3. 缩放单元节点处于维护模式后，请使用[停止](azure-stack-node-actions.md#stop)操作。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 在关闭电源操作不起作用的罕见情况下，请改用基板管理控制器 (BMC) Web 界面。

4. 更换物理计算机。 通常，此替换由 OEM 硬件供应商完成。
5. 使用[修复](azure-stack-node-actions.md#repair)操作将新的物理计算机添加到缩放单元。
6. 使用到特权终结点[检查虚拟磁盘修复状态](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint)。 利用新的数据驱动器，完整的存储修复作业可能需要数小时的时间，具体取决于系统负载和已使用的空间。
7. 修复操作完成后，验证是否已自动关闭所有活动警报。

## <a name="next-steps"></a>后续步骤

- 若要了解如何在系统通电的情况下更换物理磁盘，请参阅[更换磁盘](azure-stack-replace-disk.md)。 
- 若要了解如何完成需要系统断电才能进行的硬件组件更换操作，请参阅[更换硬件组件](azure-stack-replace-component.md)。
