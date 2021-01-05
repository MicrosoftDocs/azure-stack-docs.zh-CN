---
title: 开机并修复缩放单位节点
description: 了解如何开机并修复缩放单位节点
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: e5ba7b7ea35484293cc0f93958e7ed9d66f62d51
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867870"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>打开和修复缩放单位节点

**步骤**

若要修复并将缩放单位节点置于生产环境中，必须运行 Azure Stack 中心修复过程。

> [!NOTE]
> 修复过程大约需要3小时才能完成。

1.  在 **管理门户** 中，选择节点，并选择 " **修复**"。

    ![](media/image-52.png)

1.  提供与要修复的节点相对应的 **BMC IP 地址** ，然后选择 " **修复**"。

    ![](media/image-53.png)

1.  在通知窗格中监视进度：

    ![](media/image-54.png)
    
    
    > [!NOTE]
    > 如果修复过程在三个小时内未完成，或者出现问题，请与支持 Microsoft 支持部门的戴尔技术支持人员联系，以便进行进一步的故障排除。
    
    完成修复过程后，该节点返回到 **正在运行的操作状态**。
    
