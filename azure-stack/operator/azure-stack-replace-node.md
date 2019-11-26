---
title: Replace a scale unit node on an Azure Stack integrated system
titleSuffix: Azure Stack
description: Learn how to replace a physical scale unit node on an Azure Stack integrated system.
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
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Replace a scale unit node on an Azure Stack integrated system

*Applies to: Azure Stack integrated systems*

This article describes the general process to replace a physical computer (also referred to as a scale unit node) on an Azure Stack integrated system. Actual scale unit node replacement steps will vary based on your original equipment manufacturer (OEM) hardware vendor. 请参阅供应商的现场可更换单元 (FRU) 文档来了解特定于你的系统的详细步骤。

> [!CAUTION]  
> Firmware leveling is critical for the success of the operation described in this article. Missing this step can lead to system instability, performance decrease, security threads, or prevent Azure Stack automation from deploying the operating system. Always consult your hardware partner's documentation when replacing hardware to ensure the applied firmware matches the OEM Version displayed in the [Azure Stack administrator portal](azure-stack-updates.md). For more information and links to partner documentation, see [Replace a hardware component](azure-stack-replace-component.md).

The following flow diagram shows the general FRU process to replace an entire scale unit node.

![Flow chart for replace node process](media/azure-stack-replace-node/replacenodeflow.png)

*This action may not be required based on the physical condition of the hardware.

> [!Note]  
> If the shutdown operation does fail, it's recommended to use the drain operation followed by the stop operation. For more information, see [Scale unit node actions in Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-node-actions).

## <a name="review-alert-information"></a>Review alert information

If a scale unit node is down, you'll receive the following critical alerts:

- 节点未连接到网络控制器
- 节点不可访问，无法实现虚拟机放置
- 缩放单元节点处于脱机状态

![List of alerts for scale unit down](media/azure-stack-replace-node/nodedownalerts.png)

If you open the **Scale unit node is offline** alert, the alert description contains the scale unit node that's inaccessible. You may also receive additional alerts in the OEM-specific monitoring solution that's running on the hardware lifecycle host.

![Details of node offline alert](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Scale unit node replacement process

The following steps are provided as a high-level overview of the scale unit node replacement process. See your OEM hardware vendor's FRU documentation for detailed steps that are specific to your system. Don't follow these steps without referring to your OEM-provided documentation.

1. Use the **Shutdown** action to gracefully shut down the scale unit node. This action may not be required based on the physical condition of the hardware.

2. In the unlikely case the shutdown action fails, use the [Drain](azure-stack-node-actions.md#drain) action to put the scale unit node into maintenance mode. This action may not be required based on the physical condition of the hardware.

   > [!NOTE]  
   > In any case, only one node can be disabled and powered off at the same time without breaking the S2D (Storage Spaces Direct).

3. After the scale unit node is in maintenance mode, use the [Stop](azure-stack-node-actions.md#stop) action. This action may not be required based on the physical condition of the hardware.

   > [!NOTE]  
   > In the unlikely case that the Power off action doesn't work, use the baseboard management controller (BMC) web interface instead.

4. Replace the physical computer. Typically, this replacement is done by your OEM hardware vendor.
5. Use the [Repair](azure-stack-node-actions.md#repair) action to add the new physical computer to the scale unit.
6. Use the privileged endpoint to [check the status of virtual disk repair](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). With new data drives, a full storage repair job can take multiple hours depending on system load and consumed space.
7. After the repair action has finished, validate that all active alerts have been automatically closed.

## <a name="next-steps"></a>后续步骤

- For information about replacing a physical disk while the system is powered on, see [Replace a disk](azure-stack-replace-disk.md). 
- For information about replacing a hardware component that requires the system to be powered off, see [Replace a hardware component](azure-stack-replace-component.md).
