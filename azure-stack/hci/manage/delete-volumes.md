---
title: 在 Azure Stack HCI 中删除卷
description: 如何使用 Windows 管理中心和 PowerShell 删除 Azure Stack HCI 中的卷。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/17/2020
ms.openlocfilehash: cf556a9b6c130907e8607d8e5b9436b71756a3d4
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "79511889"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>在 Azure Stack HCI 中删除卷

> 适用于：Windows Server 2019

本主题说明如何使用 Windows Admin Center 在[存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)群集上删除卷。

观看有关如何使用 Windows 管理中心删除卷的快速视频。

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>使用 Windows 管理中心删除卷

1. 在 Windows 管理中心，连接到存储空间直通群集，然后从 "**工具**" 窗格中选择 "**卷**"。
2. 在 "**卷**" 页上，选择 "**清单**" 选项卡，然后选择要删除的卷。
3. 在卷详细信息页的顶部，选择 "**删除**"。
4. 在 "确认" 对话框中，选中相应的复选框以确认要删除该卷，然后选择 "**删除**"。

## <a name="delete-volumes-using-powershell"></a>使用 PowerShell 删除卷

使用**VirtualDisk** cmdlet 删除存储空间直通中的卷。 此 cmdlet 用于删除**VirtualDisk**对象，并返回它用于公开**VirtualDisk**对象的存储池的空间。

首先，在管理 PC 上启动 PowerShell，并使用**CimSession**参数（存储空间直通群集或服务器节点的名称）运行**VirtualDisk** cmdlet，例如*clustername.microsoft.com*： 

```PowerShell
Get-VirtualDisk -CimSession clustername.microsoft.com
```

这会返回 **-FriendlyName**参数的可能值列表，这些值对应于群集上的卷名称。

### <a name="example"></a>示例

若要删除名为 Volume1 的镜像卷 *，请*在 PowerShell 中运行以下命令：

```PowerShell
Remove-VirtualDisk -FriendlyName "Volume1"
```

系统会要求确认是否要执行该操作并清除卷包含的所有数据。 选择 "Y" 或 "N"。

   > [!WARNING]
   > 这不是可恢复操作。 此示例永久删除**VirtualDisk**卷对象。

## <a name="next-steps"></a>后续步骤

有关其他必要的存储管理任务的分步说明，另请参阅：

- [规划存储空间直通中的卷](../concepts/plan-volumes.md)
- [在存储空间直通中创建卷](create-volumes.md)
- [扩展存储空间直通中的卷](extend-volumes.md)