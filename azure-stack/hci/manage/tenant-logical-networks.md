---
title: 管理租户逻辑网络
description: 本主题提供有关如何在部署网络控制器后使用 Windows 管理中心创建、更新和删除逻辑网络的分步说明。
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/02/2021
ms.openlocfilehash: 5cf88e5befd551eb7789388807c9c1e4df671dc9
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562947"
---
# <a name="manage-tenant-logical-networks"></a>管理租户逻辑网络

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019、Windows Server 2016

本主题提供有关如何在部署网络控制器后使用 Windows 管理中心创建、更新和删除逻辑网络的分步说明。 软件定义的网络 (SDN) 逻辑网络是基于 VLAN 的传统网络。

通过将基于 VLAN 的网络作为 SDN 逻辑网络进行建模，你可以将网络策略应用于连接到这些网络的工作负荷。 例如，你可以将 (Acl) 的安全访问控制列表应用到附加到 SDN 逻辑网络的工作负荷。 应用 Acl 将保护基于 VLAN 的工作负载免受外部和内部攻击。

## <a name="create-a-logical-network"></a>创建逻辑网络
使用 Windows 管理中心中的以下步骤创建逻辑网络。

:::image type="content" source="./media/tenant-logical-networks/create-logical-network.png" alt-text="显示 &quot;逻辑网络名称&quot; 框的 Windows 管理中心主屏幕屏幕截图。" lightbox="./media/tenant-logical-networks/create-logical-network.png":::

1. 在 Windows 管理中心主屏幕上的 " **所有连接**" 下，选择要在其上创建逻辑网络的群集。
1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **逻辑网络**"。
1. 在 " **逻辑网络**" 下，选择 " **清单** " 选项卡，然后选择 " **新建**"。
1. 在 " **逻辑网络** " 窗格中，键入逻辑网络的名称。
1. 在 " **逻辑子网**" 下，选择 " **添加**"。

    :::image type="content" source="./media/tenant-logical-networks/create-logical-network-subnet.png" alt-text="显示 &quot;逻辑子网&quot; 窗格的 Windows 管理中心主页屏幕屏幕截图。" lightbox="./media/tenant-logical-networks/create-logical-network-subnet.png":::

1. 在 " **逻辑子网** " 窗格中，键入子网的名称，然后提供以下信息：
    1. 网络的 **VLAN ID** 。
    1. 无类别域间路由 (CIDR) 表示法的 **地址前缀** 。
    1. 网络的 **默认网关** 。
    1. **DNS** 服务器地址（如果需要）。
    1. 如果逻辑网络要为外部客户端提供连接，请选中 " **公共逻辑网络** " 复选框。
1. 在 " **逻辑子网 IP 池**" 下，选择 " **添加** "，然后提供以下信息：
    1. 逻辑 **IP 池名称**。
    1. **起始 IP 地址**。
    1. **结束 IP 地址**。 起始 IP 地址和结束 IP 地址必须在为子网提供的地址前缀内。
    1. 选择 **添加** 。
1. 在 " **逻辑子网** " 页上，选择 " **添加**"。
1. 在 " **逻辑网络** " 页上，选择 " **提交**"。
1. 在 " **逻辑网络** " 列表中，验证逻辑网络的状态是否 **正常**。

## <a name="get-a-list-of-logical-networks"></a>获取逻辑网络列表
你可以轻松地查看群集中的所有逻辑网络。

:::image type="content" source="./media/tenant-logical-networks/list-logical-networks.png" alt-text="显示逻辑网络的 &quot;清单&quot; 窗格的 Windows 管理中心主页屏幕屏幕截图。" lightbox="./media/tenant-logical-networks/list-logical-networks.png":::

1. 在 Windows 管理中心主屏幕上的 " **所有连接**" 下，选择要在其上查看逻辑网络的群集。
1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **逻辑网络**"。
1. " **清单** " 选项卡列出了群集上所有可用的逻辑网络，并提供用于管理单个逻辑网络的命令。 方法：
    - 查看逻辑网络的列表。
    - 查看逻辑网络设置、每个逻辑网络的状态，以及是否为每个逻辑网络启用网络虚拟化。 如果启用了网络虚拟化，则还可以查看与每个逻辑网络相关联的虚拟网络数。
    - 更改逻辑网络的设置。
    - 删除逻辑网络。

## <a name="view-logical-network-details"></a>查看逻辑网络详细信息
你可以从其专用页面查看特定逻辑网络的详细信息。

:::image type="content" source="./media/tenant-logical-networks/view-logical-network-details.png" alt-text="显示逻辑网络详细信息视图的 Windows 管理中心的屏幕截图。" lightbox="./media/tenant-logical-networks/view-logical-network-details.png":::

1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **逻辑网络**"。
1. 选择 " **清单** " 选项卡，然后选择要查看其详细信息的逻辑网络。 在后续页面上，你可以：
    - 查看逻辑网络的预配状态 (Succeeded，Failed) 失败。
    - 查看是否为逻辑网络启用网络虚拟化。
    - 查看逻辑网络中的子网。
    - 添加新的子网，删除现有子网，并修改逻辑网络子网的设置。
    - 选择每个子网以定位到其 **子** 网页，你可以在其中添加、删除和修改逻辑子网 IP 池。
    - 查看与逻辑网络相关联的虚拟网络和连接。

## <a name="change-a-logical-networks-virtualization-setting"></a>更改逻辑网络的虚拟化设置
你可以更改逻辑网络的网络虚拟化设置。

:::image type="content" source="./media/tenant-logical-networks/change-logical-network-setting.png" alt-text="Windows 管理中心的屏幕截图，显示逻辑网络的 &quot;启用网络虚拟化&quot; 复选框选项。" lightbox="./media/tenant-logical-networks/change-logical-network-setting.png":::

1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **逻辑网络**"。
1. 选择 " **清单** " 选项卡，选择逻辑网络，然后选择 " **设置**"。
1. 如果计划在逻辑网络顶部部署虚拟网络，请在逻辑网络名称下，选中 " **启用网络虚拟化** " 复选框，然后选择 " **提交**"。

## <a name="delete-a-logical-network"></a>删除逻辑网络
如果不再需要逻辑网络，可以将其删除。

:::image type="content" source="./media/tenant-logical-networks/delete-logical-network.png" alt-text="Windows 管理中心的屏幕截图，显示删除逻辑网络的删除确认提示。" lightbox="./media/tenant-logical-networks/delete-logical-network.png":::

1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **逻辑网络**"。
1. 选择 " **清单** " 选项卡，选择逻辑网络，然后选择 " **删除**"。
1. 在 " **删除** 确认" 提示符下，选择 **"是"**。
1. 在 " **逻辑网络** 搜索" 框旁边，选择 " **刷新** " 以确保已删除逻辑网络。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：
- [管理租户虚拟网络](tenant-virtual-networks.md)
- [Azure Stack HCI 中的软件定义的网络 (SDN)](../concepts/software-defined-networking.md)
