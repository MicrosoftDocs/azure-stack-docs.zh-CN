---
title: 删除 Azure Stack HCI 中的卷
description: 如何使用 Windows 管理中心删除 Azure Stack HCI 中的卷。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: ab99ed7a49fe2bf003245f139451895a85c4edbf
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025443"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>删除 Azure Stack HCI 中的卷

> 适用于： Windows Server 2019

本主题说明如何使用 Windows 管理中心删除[存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)群集上的卷。

观看有关如何删除卷的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>使用 Windows 管理中心删除卷

1. 在 Windows 管理中心，连接到存储空间直通群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在 "**卷**" 页上，选择 "**清单**" 选项卡，然后选择要删除的卷。
3. 在卷详细信息页的顶部，选择 "**删除**"。
4. 在 "确认" 对话框中，选中相应的复选框以确认要删除该卷，然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

有关其他必要的存储管理任务的分步说明，另请参阅：

- [规划存储空间直通中的卷](/windows-server/storage/storage-spaces/plan-volumes)
- [在存储空间直通中创建卷](/windows-server/storage/storage-spaces/create-volumes)
- [扩展存储空间直通中的卷](/windows-server/storage/storage-spaces/resize-volumes)