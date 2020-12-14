---
title: 更换网络子卡
description: 了解如何更换网络子卡
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 03a67a92ce8ff873b50fb9e39e4870811ba4b1a5
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97390882"
---
# <a name="replacing-a-network-daughter-card"></a>更换网络子卡

使用以下过程来替换网络子卡或卡。

## <a name="prerequisites"></a>先决条件

1.  查看本指南开头的 *注释、注意事项和警告*

2.  查看处理预防措施。

3.  审阅

    -   使用战术性云设备中的缩放单位节点时所需的知识

    -   如果使用的是硬件生命周期主机，则使用第5页上的硬件生命周期主机所需的知识

4.  完成

    -   验证缩放单位节点的访问和运行状况（如果使用缩放单位节点）

    -   验证硬件生命周期主机访问和运行状况（如果使用的是硬件生命周期主机）

5.  完成

    -   如果使用缩放单位节点，请关闭缩放单位节点

    -   如果使用的是硬件生命周期主机，请关闭硬件生命周期主机


## <a name="steps"></a>步骤

1.  找到机架中的物理节点。

2.  验证节点是否关闭。 电源 LED 应为橙色。

    > [!CAUTION]
    > 在断开正在处理的服务器上的电缆之前，请确保每个电缆均已正确标记。 必须将电缆重新连接到相同的端口。
    
3.  更换网络子卡或卡。

    遵循适用于规模单元节点或硬件生命周期主机的 [DELL EMC PowerEdge R640 安装和服务手册](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) 中的网络子卡更换过程。
    
4.  在节点上通电。

    重新连接电源后，如果服务器不会自动重新启动，请按 "电源" 按钮以重新打开该节点。
    
## <a name="next-steps"></a>后续步骤

如果使用缩放单位节点：

1.  完成开机并修复缩放单位节点。

2.  完成验证缩放单位节点的运行状况。 如果使用的是硬件生命周期主机：

    -   完成验证硬件生命周期主机运行状况
    
