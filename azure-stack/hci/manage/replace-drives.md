---
title: 替换 Azure Stack HCI 上的故障驱动器
description: 如何替换 Azure Stack HCI 上的故障驱动器。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: e0aaed5d444a0d7b617ecd2ccd350a9812be8a2c
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025432"
---
# <a name="replace-failed-drives-on-azure-stack-hci"></a>替换 Azure Stack HCI 上的故障驱动器

Azure Stack HCI 适用于直接连接的 SATA、SAS 或 NVMe 驱动器，它们在物理上只连接到一台服务器。 如果驱动器发生故障，你将需要访问物理服务器硬件来替换它。

## <a name="find-the-alert"></a>查找警报
驱动器发生故障时， **Windows 管理中心**面板的 "左上方**警报**" 区域中会出现警报。 你还可以从左侧导航栏中选择 "**驱动器**"，或单击右下角的磁贴上的 "**查看驱动器 >** " 链接来浏览驱动器并查看其状态。 在 "**视图**" 选项卡中，网格支持排序、分组和关键字搜索。

1. 在仪表板中，单击警报以查看详细信息，如驱动器的物理位置。
1. 若要了解详细信息，请单击**驱动器**详细信息页的 "**前往驱动器**" 快捷方式。
1. 如果硬件支持，可以单击 "**打开/关闭指示灯**" 来控制驱动器指示灯。
   存储空间直通自动停用和撤离故障驱动器。 发生此情况时，驱动器状态为 "已停用"，其存储容量条为空。
1. 删除发生故障的驱动器，并插入其替换。

## <a name="wait-for-the-alert-to-clear"></a>等待警报清除
在**驱动器 > 清单**中，将显示新的驱动器。 此时，警报会被清除，卷将恢复为 "正常" 状态，并且存储将重新平衡到新驱动器上–无需用户操作。

## <a name="next-steps"></a>后续步骤
-  若要了解如何在不同级别（包括在驱动器级别）跟踪存储运行状况，请参阅[运行状况和运行状态](/windows-server/storage/storage-spaces/storage-spaces-states)。
