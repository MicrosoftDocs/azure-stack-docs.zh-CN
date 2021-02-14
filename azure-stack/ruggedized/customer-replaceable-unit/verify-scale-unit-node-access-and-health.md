---
title: 验证缩放单位节点访问和运行状况
description: 了解如何验证缩放单位节点的访问和运行状况
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d636b5ebb0d07c2711a17b2b2f10651480c38068
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488014"
---
# <a name="verifying-scale-unit-node-access-and-health"></a>验证缩放单位节点的访问和运行状况

登录到特权访问工作站，启动管理员门户，验证系统运行状况，获取特权终结点 IP 地址，并确定需要排出或恢复哪些节点。

1.  使用远程桌面连接到特权访问工作站。

2.  访问 Azure Stack 中心管理员门户。

    用从客户获取的凭据登录到 Azure Stack 中心管理员门户。
        
3.  获取特权终结点 IP 地址。


    选择 " **区域管理** " 磁贴，然后选择 " **属性**"。 滚动到窗格底部，找到 " **特权终结点 ip 地址** " 字段中的 ip 地址。 请记下这些操作，在此过程中，可能需要此过程，或在出现任何问题时提供支持。

    [![显示 "管理" 页的屏幕截图，其中突出显示了 "特权终结点地址" 部分。](media/image-18-inline.png)](media/image-18-expanded.png#lightbox)
    
4.  查看任何当前警报。

    在 " **区域管理**" 中，选择 " **警报** " 并查看当前警报。 如果出现任何意外警报，请验证 Microsoft 支持部门是否可以清除或安全地忽略它们。
    
    [![显示 "属性" 页的屏幕截图，其中突出显示了 "Name" 部分。](media/image-19-inline.png)](media/image-19-expanded.png#lightbox)
    
5.  标识缩放单位节点。

    如果只提供服务标记，并且无法识别哪个节点在 Azure Stack 中心管理门户中出现问题 (即节点电源状态已) 停止，则使用以下步骤将缩放单位节点关联到服务标记：
    
    1.  在 "**区域管理**" 中，选择 "**缩放单位**"，然后选择群集 **"群集"。** 选择“节点”。
    
    1.  若要获取节点服务标记，请选择 " **BMC** IP 地址" 链接，该链接将在新的选项卡或窗口中打开服务器的 iDRAC web 界面。

        [![显示 "节点" 页的屏幕截图，其中突出显示了 "BMC" 列。](media/image-20-inline.png)](media/image-20-expanded.png#lightbox) 
    
    1.  登录到 iDRAC 接口，并验证 " **系统信息** " 窗格中的节点的服务标记。
    
    1.  为每个节点重复此过程，并将这些服务标记与计划的硬件更换相关联，以确定需要服务的节点。

        [![屏幕截图显示 "仪表板"，其中突出显示了 "服务标记"。](media/image-21-inline.png)](media/image-21-expanded.png#lightbox)
    
