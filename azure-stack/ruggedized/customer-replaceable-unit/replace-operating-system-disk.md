---
title: 替换操作系统磁盘
description: 了解如何替换操作系统磁盘
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 529a81a8c63e2fe72da4618e7f359832b2267ea3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97390626"
---
# <a name="replacing-an-operating-system-disk"></a>替换操作系统磁盘

使用以下过程来替换缩放单位节点中出现故障的操作系统磁盘。

## <a name="prerequisites"></a>先决条件

1.  查看本指南开头的 *说明、注意事项和警告* 。

2.  查看处理预防措施。

3.  如果使用缩放单位节点，则使用战术性云设备中的缩放单位节点所需的知识。

4.  完成验证缩放单位节点的访问和运行状况。

5.  完成关闭扩展单元节点的功能。

    对于 Azure Stack 集线器缩放单位节点，操作系统将从驻留在戴尔 PowerEdge Boot 优化存储解决方案 (老板) 卡的镜像对的 2 SSD 模块上运行。 系统必须关闭电源，才能更换操作系统磁盘。
    
## <a name="steps"></a>步骤

1.  找到机架中的物理节点。

2.  验证节点是否关闭。 电源 LED 应为橙色。

    > [!CAUTION]
    > 在断开正在处理的服务器上的电缆之前，请确保每个电缆均已正确标记。 必须将电缆重新连接到相同的端口。
    
3.  替换失败的 2 SSD 模块。

    遵循适用于扩展单元节点的 [DELL EMC PowerEdge R640 安装和服务手册](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) 中的第2个 SSD 模块更换过程。
    
4.  在节点上通电。

    重新连接电源后，如果服务器不会自动重新启动，则按下电源按钮打开节点。
    
## <a name="next-steps"></a>后续步骤

1.  完成开机并修复缩放单位节点。

2.  完成验证缩放单位节点的运行状况。

