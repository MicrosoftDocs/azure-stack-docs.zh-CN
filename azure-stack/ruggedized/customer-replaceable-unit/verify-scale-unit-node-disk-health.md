---
title: 验证缩放单位节点磁盘运行状况
description: 了解如何验证规模单元节点的磁盘运行状况
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: bebff3ff8970a43d0893f057a94ec3dfbf0a7fc9
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97390621"
---
# <a name="verifying-scale-unit-node-disk-health"></a>正在验证缩放单位节点磁盘运行状况

1.  连接到特权终结点 (PEP)。

    1.  有关连接到 PEP 的说明，请参阅特权访问工作站和特权终结点访问。

    1.  连接后，输入 PEP 会话， `Enter-PSSession -Session $pep` 。

2.  获取虚拟磁盘运行状况。

    1.  运行 `Get-VirtualDisk -cimsession "S-Cluster"` 验证虚拟磁盘运行状况。

        如果系统未返回 **"正常" 和 "** **正常**" **HealthStatus** 的 **OperationalStatus** ，请等待几分钟，然后再次运行该命令。
        
        ![](media/image-57.png)
        
    1.  运行 `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` 验证所有正在运行的存储作业是否已完成。
    
    1.  验证是否未返回任何结果。 如果作业仍在运行，如 **JobState** 所示，或所有作业标记为已完成，则等待另外10分钟，然后再次运行相同的命令。 最终状态应为 "未列出任何作业"。
    
    1.  如果需要，可以在 [使用 Azure Stack 中心 PowerShell 检查虚拟磁盘修复的状态](https://docs.microsoft.com/azure-stack/operator/azure-stack-replace-disk?view=azs-2002&check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell)中找到其他存储健康验证步骤。
        
