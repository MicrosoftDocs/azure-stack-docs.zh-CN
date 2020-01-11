---
title: 替换 Azure Stack 集线器集成系统上的缩放单位节点
titleSuffix: Azure Stack Hub
description: 了解如何替换 Azure Stack 集线器集成系统上的物理缩放单位节点。
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
ms.openlocfilehash: 9e289420d10cace3c466c0b1ff18c4d84cf97800
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881971"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-hub-integrated-system"></a>替换 Azure Stack 集线器集成系统上的缩放单位节点

本文介绍了在 Azure Stack 集线器集成系统上替换物理计算机（也称为缩放单位节点）的一般过程。 实际的缩放单位节点更换步骤将因原始设备制造商（OEM）硬件供应商而异。 请参阅供应商的现场可更换单元 (FRU) 文档来了解特定于你的系统的详细步骤。

> [!CAUTION]  
> 固件分级对于本文中所述的操作的成功至关重要。 缺少此步骤可能会导致系统不稳定、性能降低、安全线程，或阻止 Azure Stack 集线器自动化部署操作系统。 在更换硬件时，请始终咨询硬件合作伙伴的文档，以确保应用的固件与[Azure Stack 中心管理员门户](azure-stack-updates.md)中显示的 OEM 版本相匹配。 有关详细信息和合作伙伴文档的链接，请参阅[替换硬件组件](azure-stack-replace-component.md)。

以下流程图显示了用来替换整个缩放单位节点的常规 FRU 进程。

![替换节点进程的流程图](media/azure-stack-replace-node/replacenodeflow.png)

\* 根据硬件的物理条件，可能不需要此操作。

> [!Note]  
> 如果关闭操作失败，建议使用 "排出" 操作，然后使用 "停止" 操作。 有关详细信息，请参阅[缩放单元节点操作 Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-node-actions)。

## <a name="review-alert-information"></a>查看警报信息

如果缩放单位节点已关闭，你将收到以下严重警报：

- 节点未连接到网络控制器
- 节点不可访问，无法实现虚拟机放置
- 缩放单元节点处于脱机状态

![缩放单位下的警报列表](media/azure-stack-replace-node/nodedownalerts.png)

如果打开 "**缩放单位" 节点处于脱机状态**，则警报描述将包含无法访问的缩放单位节点。 你还可以在硬件生命周期主机上运行的 OEM 特定监视解决方案中收到其他警报。

![节点脱机警报的详细信息](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>缩放单位节点更换过程

以下步骤提供了缩放单位节点更换过程的高级概述。 有关特定于系统的详细步骤，请参阅 OEM 硬件供应商的 FRU 文档。 请勿在未参考 OEM 提供的文档的情况下执行这些步骤。

1. 使用 "**关闭**" 操作以正常关闭缩放单位节点。 根据硬件的物理条件，可能不需要此操作。

2. 如果关机操作失败，请使用 "[排出](azure-stack-node-actions.md#drain)" 操作将 "缩放单位" 节点置于维护模式。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 在任何情况下，一次只能禁用和关闭一个节点，而不会破坏 S2D （存储空间直通）。

3. 缩放单位节点处于维护模式后，请使用 "[停止](azure-stack-node-actions.md#stop)" 操作。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 如果关机操作不起作用，请改用基板管理控制器（BMC） web 界面。

4. 替换物理计算机。 通常，此替换由 OEM 硬件供应商完成。
5. 使用 "[修复](azure-stack-node-actions.md#repair)" 操作将新的物理计算机添加到缩放单位。
6. 使用特权终结点[检查虚拟磁盘修复的状态](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint)。 使用新的数据驱动器，完全存储修复作业可能需要几个小时，具体取决于系统负载和使用的空间。
7. 修复操作完成后，验证是否已自动关闭所有活动警报。

## <a name="next-steps"></a>后续步骤

- 有关在系统通电时更换物理磁盘的信息，请参阅[更换磁盘](azure-stack-replace-disk.md)。 
- 有关更换需要关闭系统电源的硬件组件的信息，请参阅[更换硬件组件](azure-stack-replace-component.md)。
