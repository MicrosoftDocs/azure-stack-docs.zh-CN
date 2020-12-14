---
title: 通过 USB 连接访问 iDRAC
description: 了解如何通过 USB 连接访问 iDRAC
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 7381aae6b7b08de01e27f895d79519a024a1955a
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97390987"
---
# <a name="accessing-the-idrac-interface-over-a-direct-usb-connection"></a>通过直接 USB 连接访问 iDRAC 接口

通过 iDRAC direct 功能，可以将便携式计算机直接连接到 iDRAC USB 端口。 此功能可让你直接与 iDRAC 接口（如 web 接口、RACADM 和 WSMan）交互，以实现高级服务器管理和服务。



若要通过 USB 端口访问 iDRAC 接口，请通过便携式计算机执行以下操作。

**步骤**

1.  在便携式计算机上，关闭所有无线网络并断开与任何其他硬连线网络的连接。

2.  将便携式计算机的微型 USB 电缆连接到位于服务器正面的 iDRAC 直接端口。
    请参阅关系图中的第4项。

    ![](media/image-67.png)

3.  等待笔记本电脑获取 IP 地址169.254.0.4。

    可能需要几秒钟才能获得 IP 地址。 IDRAC 获取 IP 地址169.254.0.3。

4.  连接到 iDRAC web 界面。

    若要访问 iDRAC web 界面，请打开浏览器，并访问169.254.0.3。

5.  完成所需的活动。

    

    > [!NOTE]
    > 当 iDRAC 使用 USB 端口（上图中的第3项）时，LED 将闪烁指示活动。 每秒闪烁频率为四。
    
6.  断开微型 USB 电缆。

    完成所需操作后，从系统中断开 USB 电缆的连接。 LED 熄灭。
    
