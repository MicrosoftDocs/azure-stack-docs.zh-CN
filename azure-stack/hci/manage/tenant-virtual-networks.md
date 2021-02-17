---
title: 管理租户虚拟网络
description: 本主题提供了有关在部署软件定义的网络 (SDN) 后，如何使用 Windows 管理中心来创建、更新和删除 Hyper-v 网络虚拟化 (HNV) 虚拟网络的分步说明。
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: fb90a8f9df28ed8fd67330c42ae1e3d2f3aa445c
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562928"
---
# <a name="manage-tenant-virtual-networks"></a>管理租户虚拟网络

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019、Windows Server 2016

本主题提供了有关在部署软件定义的网络 (SDN) 后，如何使用 Windows 管理中心来创建、更新和删除 Hyper-v 网络虚拟化 (HNV) 虚拟网络的分步说明。

HNV 可帮助你隔离租户网络，使每个租户网络为单独的实体。 除非在虚拟网络之间配置公共访问工作负荷或对等互连，否则每个实体都不能进行交叉连接。

## <a name="create-a-virtual-network"></a>创建虚拟网络
使用 Windows 管理中心中的以下步骤来创建虚拟网络。

:::image type="content" source="./media/tenant-virtual-networks/create-virtual-network.png" alt-text="Windows 管理中心主屏幕的屏幕截图，其中显示了在其中创建虚拟网络名称和地址前缀的窗格。" lightbox="./media/tenant-virtual-networks/create-virtual-network.png":::

1. 在 Windows 管理中心主屏幕上的 " **所有连接**" 下，选择要在其上创建虚拟网络的群集。
1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **虚拟网络**"。
1. 在 " **虚拟网络**" 下，选择 " **清单** " 选项卡，然后选择 " **新建**"。
1. 在 " **虚拟网络** " 窗格中，键入虚拟网络的名称。
1. 在 " **地址前缀** " 下，选择 " **添加**"，然后在无类别域间路由 (CIDR) 表示法中键入地址前缀。 您可以选择添加更多地址前缀。
1. 在 " **子网**" 下，选择 " **添加**"，键入子网的名称，然后在 CIDR 表示法中提供地址前缀。

   >[!NOTE]
   > 子网地址前缀必须在虚拟网络的 **地址** 前缀中定义的地址前缀范围内。

1. 选择 " **提交** " 或 "添加更多子网"，然后选择 " **提交**"。
1. 在 " **虚拟网络** " 列表中，验证虚拟网络的状态是否 **正常**。

## <a name="get-a-list-of-virtual-networks"></a>获取虚拟网络的列表
你可以轻松地查看群集中的所有虚拟网络。

:::image type="content" source="./media/tenant-virtual-networks/list-virtual-networks.png" alt-text="显示虚拟网络列表的 Windows 管理中心中心的屏幕截图。" lightbox="./media/tenant-virtual-networks/list-virtual-networks.png":::

1. 在 Windows 管理中心主屏幕上的 " **所有连接**" 下，选择要在其上查看虚拟网络的群集。
1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **虚拟网络**"。
1. " **清单** " 选项卡列出了群集上所有可用的虚拟网络，并提供用于管理单个虚拟网络的命令。 方法：
    - 查看虚拟网络的列表。
    - 查看虚拟网络设置、每个虚拟网络的状态，以及连接到每个虚拟网络)  (Vm 的虚拟机数。
    - 更改虚拟网络的设置。
    - 删除虚拟网络。

## <a name="view-virtual-network-details"></a>查看虚拟网络详细信息
你可以从其专用页面查看特定虚拟网络的详细信息。

:::image type="content" source="./media/tenant-virtual-networks/view-virtual-network-details.png" alt-text="显示虚拟网络的详细信息视图的 Windows 管理中心的屏幕截图。" lightbox="./media/tenant-virtual-networks/view-virtual-network-details.png":::

1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **虚拟网络**"。
1. 选择 " **清单** " 选项卡，然后选择要查看其详细信息的虚拟网络。 在后续页面上，你可以：
    - 查看虚拟网络的 **预配状态** (Succeeded，Failed) 失败。
    - 查看虚拟网络的 **配置状态** (正常、错误、警告、未知) 。
    - 查看虚拟网络的基础 **逻辑网络** 。
    - 查看虚拟网络的 **地址空间** 。
    - 添加新的子网，删除现有子网，并修改虚拟网络子网的设置。
    - 查看虚拟机与虚拟网络的 **连接** 。

## <a name="change-virtual-network-settings"></a>更改虚拟网络设置
可以更新虚拟网络地址前缀、管理虚拟网络对等互连，并为虚拟网络配置边界网关协议 (BGP) 路由器和对等机。

:::image type="content" source="./media/tenant-virtual-networks/change-virtual-network-settings.png" alt-text="显示虚拟网络的 &quot;设置&quot; 视图的 Windows 管理中心的屏幕截图。" lightbox="./media/tenant-virtual-networks/change-virtual-network-settings.png":::

1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **虚拟网络**"。
1. 选择 " **清单** " 选项卡，选择虚拟网络，然后选择 " **设置**"。
1. 在 " **常规** " 选项卡上，您可以：
    - 删除现有地址前缀或添加新前缀。
    - 配置与另一个虚拟网络的对等互连。
    - 向虚拟网络添加 BGP 路由器。 为此，需要提供 BGP 路由器名称和自治系统编号 (ASN) 号。
    - 为 BGP 路由器添加一个或多个 BGP 对等节点。 为此，需要提供每个 BGP 对等名称和每个 BGP 对等节点的 ASN 编号。

## <a name="delete-a-virtual-network"></a>删除虚拟网络
如果不再需要虚拟网络，可以将其删除。

:::image type="content" source="./media/tenant-virtual-networks/delete-virtual-network.png" alt-text="Windows 管理中心的屏幕截图，显示 &quot;删除虚拟网络&quot; 确认提示。" lightbox="./media/tenant-virtual-networks/delete-virtual-network.png":::

1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **虚拟网络**"。
1. 选择 " **清单** " 选项卡，选择虚拟网络，然后选择 " **删除**"。
1. 在 " **删除虚拟网络** 确认" 提示符下，选择 " **删除**"。
1. 在 "虚拟网络" 搜索框旁，选择 " **刷新** " 以确保虚拟网络已删除。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：
- [Azure Stack HCI 中的软件定义的网络 (SDN)](../concepts/software-defined-networking.md)
