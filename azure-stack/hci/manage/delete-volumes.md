---
title: 删除 Azure Stack HCI 和 Windows Server 中的卷
description: 如何使用 Windows 管理中心和 PowerShell 删除 Azure Stack HCI 和 Windows Server 中的卷。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: fdc7fd4ae102871d534d846dd653af077a75d49a
ms.sourcegitcommit: 02a4c34fb829e053016912a4fffcc51e32685425
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102532472"
---
# <a name="deleting-volumes-in-azure-stack-hci-and-windows-server"></a>删除 Azure Stack HCI 和 Windows Server 中的卷

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

本主题提供使用 Windows 管理中心或 PowerShell 删除卷的说明。

## <a name="delete-volumes-with-windows-admin-center"></a>删除包含 Windows 管理中心的卷

1. 在 Windows 管理中心，连接到群集，然后从左侧的 "**工具**" 窗格中选择 "**卷**"。
2. 在“卷”页上选择“清单”选项卡，然后选择要删除的卷。
3. 在卷详细信息页的顶部，选择“删除”。
4. 在“确认”对话框中，选中复选框以确认要删除该卷，然后选择“删除”。

   :::image type="content" source="media/delete-volumes/delete-volume.png" alt-text="选择要删除的卷，选择 &quot;删除&quot;，然后确认是否要清除该卷上的所有数据。" lightbox="media/delete-volumes/delete-volume.png":::

## <a name="delete-volumes-with-powershell"></a>通过 PowerShell 删除卷

使用 **VirtualDisk** Cmdlet 删除 **VirtualDisk** 对象，并将其所使用的空间返回给公开 **VirtualDisk** 对象的存储池。

首先，在管理 PC 上启动 PowerShell，并使用 **CimSession** 参数运行 **VirtualDisk** cmdlet，它是群集或服务器节点的名称，例如 *clustername.contoso.com*：

```PowerShell
Get-VirtualDisk -CimSession clustername.contoso.com
```

这将返回 -FriendlyName 参数的可能值列表，这些值对应于群集上的卷名。

### <a name="example"></a>示例

若要删除名为“Volume1”的镜像卷，请在 PowerShell 中运行以下命令：

```PowerShell
Remove-VirtualDisk -FriendlyName "Volume1"
```

系统会要求你确认是否要执行该操作并清除该卷包含的所有数据。 选择“Y”或“N”。

   > [!WARNING]
   > 此操作不可恢复。 此示例会永久删除 VirtualDisk 卷对象。

## <a name="next-steps"></a>后续步骤

如需其他重要的存储管理任务的分步说明，另请参阅：

- [规划卷](../concepts/plan-volumes.md)
- [创建卷](create-volumes.md)
- [扩展卷](extend-volumes.md)