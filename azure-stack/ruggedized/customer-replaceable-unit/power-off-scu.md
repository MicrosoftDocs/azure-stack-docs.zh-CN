---
title: 关机缩放单位节点
description: 了解如何关闭缩放单位节点
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 972c9e8f0b66470a6e1b7cbc81d40062ff34a950
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874704"
---
# <a name="powering-off-scale-unit-nodes"></a>关闭缩放单位节点的电源

在关闭缩放单位节点之前，必须先验证缩放单位的 \' 运行状况，并确定需要修复的节点。

如果 \' 未 **停止** 节点的 **电源状态**，请使用以下过程来安全关闭节点。

**步骤**

1.  排出缩放单位节点。

    1.  从管理门户中，选择需要修复的节点，然后选择 " **排出**"。

        ![](media/image-23.png)
        
    1.  出现提示时，输入要排出的节点的名称，然后选择 **"是"**。

        ![](media/image-24.png)
    
    1.  你将看到一条通知，指出排出正在进行。
    
        ![](media/image-25.png)
        
    1.  登录到 iDRAC 接口，并验证 \' **系统信息** 中的节点 s 服务标记。
    

2.  关闭缩放单位节点。

    1.  完成排出后，再次选择该节点，验证 **操作状态** 是否为 "正在 **维护** " 并选择 " **关闭**"。

        ![](media/image-26.png)
        
    1.  出现提示时，请选择 **"是"** 确认关闭。
    
        ![](media/image-27.png)
        
    1.  你将看到一条通知，指出关机正在进行。

        ![](media/image-28.png)
    
    1.  关闭完成后， **电源状态** 为 "已 **停止**"。
    
        ![](media/image-29.png)
        