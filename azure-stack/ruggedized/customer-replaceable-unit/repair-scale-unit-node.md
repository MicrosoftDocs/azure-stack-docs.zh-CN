---
title: 开机并修复缩放单位节点
description: 了解如何开机并修复缩放单位节点
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 0a2aa54234b5dccc8f1ce3425906425064463910
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488116"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>打开和修复缩放单位节点

**步骤**

若要修复并将缩放单位节点置于生产环境中，必须运行 Azure Stack 中心修复过程。

> [!NOTE]
> 修复过程大约需要3小时才能完成。

1.  在 **管理门户** 中，选择节点，并选择 " **修复**"。

    ![屏幕截图，显示具有节点并选中 "修复" 操作的 "管理节点" 页。](media/image-52.png)

1.  提供与要修复的节点相对应的 **BMC IP 地址** ，然后选择 " **修复**"。

    ![屏幕截图显示了一个选定节点的 "管理节点" 页面，已突出显示 I P 地址并显示 "修复节点" 对话框。](media/image-53.png)

1.  在通知窗格中监视进度：

    ![显示 "通知" 窗格和 "修复节点正在运行" 的屏幕截图。](media/image-54.png)
    
    
    > [!NOTE]
    > 如果修复过程在三个小时内未完成，或者出现问题，请使用 Microsoft 支持部门打开一个事例进行进一步的故障排除。
    
    完成修复过程后，该节点返回到 **正在运行的操作状态**。
    
