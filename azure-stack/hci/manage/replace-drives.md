---
title: 更换 Azure Stack HCI 上的故障驱动器
description: 如何更换 Azure Stack HCI 上的故障驱动器。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: d3d03f1e5cc89186e2eb8198b52e96bffbd21768
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866477"
---
# <a name="replace-failed-drives-on-azure-stack-hci"></a>更换 Azure Stack HCI 上的故障驱动器

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

Azure Stack HCI 可使用直接连接的 SATA、SAS、NVMe 或永久性内存驱动器，每个驱动器使用物理方式仅连接到一个服务器。 如果驱动器发生故障，你将需要访问物理服务器硬件来更换它。

## <a name="find-the-alert"></a>查找警报
当驱动器发生故障时，在“Windows 管理中心”仪表板左上角的“警报”区域中会显示警报。 还可以从左侧的导航栏中选择“驱动器”，或者单击右下角的磁贴上的“查看驱动器 >”链接来浏览驱动器并查看其状态。 在“视图”选项卡中，网格支持排序、分组和关键字搜索。

1. 在仪表板中，单击警报以查看详细信息，例如驱动器的物理位置。
1. 若要了解详细信息，请单击“驱动器”详细信息页的“转到驱动器”快捷方式。
1. 如果硬件支持，则可以单击“打开/关闭指示灯”来控制驱动器的指示灯。
   存储空间直通会自动停用并撤离故障驱动器。 发生此情况时，驱动器状态为“已停用”，其存储容量条为空。
1. 移除故障驱动器，并插入更换驱动器。

## <a name="wait-for-the-alert-to-clear"></a>等待警报清除
在“驱动器”>“清单”中，将显示新的驱动器。 最终，警报将清除，卷将修复回正常状态，存储将重新平衡到新驱动器上 — 无需用户操作。

## <a name="next-steps"></a>后续步骤
- 若要了解如何在不同级别（包括在驱动器级别）跟踪存储运行状况，请参阅[运行状况和工作状态](/windows-server/storage/storage-spaces/storage-spaces-states)。
- 如果使用的是 PMem，请[了解并部署永久性内存](/windows-server/storage/storage-spaces/deploy-pmem)