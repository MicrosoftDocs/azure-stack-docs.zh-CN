---
title: 使用数据中心防火墙通过 Windows 管理中心配置 Acl
description: 本主题提供有关如何使用 Windows 管理中心来创建和配置访问控制列表 (Acl) 使用数据中心防火墙和 Acl 在软件定义的网络 (SDN) 逻辑网络和虚拟网络中管理数据流量流的说明。
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 03/04/2021
ms.openlocfilehash: 342f2771d74a2347cd53fa9364089a934885c671
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102194222"
---
# <a name="use-datacenter-firewall-to-configure-acls-with-windows-admin-center"></a>使用数据中心防火墙通过 Windows 管理中心配置 Acl

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019、Windows Server 2016

本主题提供有关如何使用 Windows 管理中心来创建和配置访问控制列表 (Acl) 使用数据中心防火墙管理数据流量流的分步说明。 它还提供了有关管理软件定义的网络 (SDN) 逻辑网络和虚拟网络上的 Acl 的说明。 通过创建 Acl 并将其应用到子网或网络接口来启用和配置数据中心防火墙。 若要了解详细信息，请参阅 [什么是数据中心防火墙？](../concepts/datacenter-firewall-overview.md) 若要使用 PowerShell 脚本来执行此操作，请参阅 [使用数据中心防火墙通过 PowerShell 配置 acl](use-datacenter-firewall-powershell.md)。

在配置 Acl 之前，需要部署网络控制器。 要了解网络控制器，请参阅 [什么是网络控制器？](../concepts/network-controller-overview.md) 若要使用 PowerShell 脚本部署网络控制器，请参阅 [部署 SDN 基础结构](sdn-express.md)。

此外，如果想要将 Acl 应用于 SDN 逻辑网络，则需要首先创建逻辑网络。 同样，如果要将 Acl 应用于 SDN 虚拟网络，则需要首先创建虚拟网络。 要了解详细信息，请参阅：
- [管理租户逻辑网络](tenant-logical-networks.md)
- [管理租户虚拟网络](tenant-virtual-networks.md)

## <a name="create-an-acl"></a>创建 ACL
可以轻松地在 Windows 管理中心中创建 ACL。

:::image type="content" source="./media/access-control-lists/create-acl.png" alt-text="显示 &quot;访问控制列表名称&quot; 框的 Windows 管理中心主页屏幕屏幕截图。" lightbox="./media/access-control-lists/create-acl.png":::

1. 在 Windows 管理中心主屏幕上的 " **所有连接**" 下，选择要在其上创建 ACL 的群集。
1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **访问控制列表**"。
1. 在 " **访问控制列表**" 下，选择 " **清单** " 选项卡，然后选择 " **新建**"。
1. 在 " **访问控制列表** " 窗格中，键入 ACL 的名称，然后选择 " **提交**"。
1. 在 " **访问控制列表**" 下，验证新 ACL 的 **预配状态** 是否显示 " **成功**"。

## <a name="create-acl-rules"></a>创建 ACL 规则
创建 ACL 后，便可以创建 Acl 规则了。 若要将 ACL 规则应用于入站和出站流量，需要创建两个规则。

:::image type="content" source="./media/access-control-lists/create-acl-rules.png" alt-text="显示 &quot;访问控制规则&quot; 窗格的 Windows 管理中心的屏幕截图。" lightbox="./media/access-control-lists/create-acl-rules.png":::

1. 在 Windows 管理中心主屏幕上的 " **所有连接**" 下，选择要在其上创建 ACL 的群集。
1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **访问控制列表**"。
1. 在 " **访问控制列表**" 下，选择 " **清单** " 选项卡，然后选择刚刚创建的 ACL。
1. 在 " **访问控制规则**" 下，选择 " **新建**"。
1. 在 " **访问控制规则** " 窗格中，提供以下信息：
    1. 规则的 **名称**。
    1. 规则的 **优先级**–可接受的值为 **101** 到 **65000**。 值越小，表示优先级越高。
    1. **类型** –可以是入站或出站。
    1. **协议** –指定与传入或传出数据包匹配的协议。 可 **接受的值包括：** **TCP** 和 **UDP**。
    1. **源地址前缀** –指定要与传入或传出数据包匹配的源地址前缀。 如果提供 *，则表示所有源地址。
    1. **源端口范围** –指定与传入或传出数据包匹配的源端口范围。 如果提供 *，则表示所有源端口。
    1. **目标地址前缀** –指定要与传入或传出数据包匹配的目标地址前缀。 如果提供 *，则表示所有目标地址。
    1. **目标端口范围** –指定要与传入或传出数据包匹配的目标端口范围。 如果提供 *，则表示所有目标端口。
    1. **操作** -如果满足上述条件，则指定以允许或阻止数据包。 可接受的值为 **Allow** 和 **Deny**。
    1. **日志记录** –指定为规则启用或禁用日志记录。 如果启用了日志记录，则会在主机计算机上记录与此规则匹配的所有流量。
1. 选择“提交”。 

## <a name="apply-an-acl-to-a-virtual-network"></a>将 ACL 应用到虚拟网络
创建 ACL 和规则后，需要将其应用到虚拟网络子网、逻辑网络子网或网络接口。

:::image type="content" source="./media/access-control-lists/apply-acl-virtual-network.png" alt-text="显示 &quot;虚拟子网&quot; 窗格的 Windows 管理中心的屏幕截图。" lightbox="./media/access-control-lists/apply-acl-virtual-network.png":::

1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **虚拟网络**"。
1. 选择 " **清单** " 选项卡，然后选择一个虚拟网络。 在随后的页面上，选择一个虚拟网络子网，然后选择 " **设置**"。
1. 从下拉列表中选择一个 ACL，然后选择 " **提交**"。

    完成最后一个步骤会将 ACL 关联到虚拟网络子网，并将其应用于连接到虚拟网络子网的所有计算机。

## <a name="apply-an-acl-to-a-network-interface"></a>将 ACL 应用于网络接口
 (VM) 或更高版本中创建虚拟机时，可以将 ACL 应用于网络接口。

:::image type="content" source="./media/access-control-lists/apply-acl-network-interface.png" alt-text="Windows 管理中心的屏幕截图，显示 &quot;网络设置&quot; 选项以将 ACL 与网络接口相关联。" lightbox="./media/access-control-lists/apply-acl-network-interface.png":::

1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **虚拟机**"。
1. 选择 " **清单** " 选项卡，选择一个 VM，然后选择 " **设置**"。
1. 在 " **设置** " 页上，选择 " **网络**"。
1. 向下滚动到 " **访问控制列表**"，展开下拉列表，选择 ACL，然后选择 " **保存网络设置**"。

    完成最后一个步骤会将 ACL 与网络接口相关联，并将其应用于网络接口的所有传入和传出流量。

## <a name="get-a-list-of-acls"></a>获取 Acl 列表
可以轻松地在列表中查看群集中的所有 Acl。

:::image type="content" source="./media/access-control-lists/get-acl-list.png" alt-text="Windows 管理中心的屏幕截图，显示 &quot;清单&quot; 选项卡上的 Acl 列表。" lightbox="./media/access-control-lists/get-acl-list.png":::

1. 在 Windows 管理中心主屏幕上的 " **所有连接**" 下，选择要在其上查看 acl 列表的群集。
1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **访问控制列表**"。
1. " **清单** " 选项卡显示群集上可用的 acl 列表，并提供可用于管理列表中各个 acl 的命令。 可以：
    - 查看 Acl 列表。
    - 查看每个 ACL 的规则数，以及应用于每个 ACL 的应用的子网和 Nic 数。
    - 查看每个 ACL 的 **预配状态** (**Succeeded**， **Failed**) 。
    - 删除 ACL。
    - 如果在列表中选择 ACL，则可以查看其规则。 然后，你可以添加、删除或修改 ACL 规则设置。

## <a name="delete-an-acl"></a>删除 ACL
如果不再需要 ACL，可以将其删除。

>[!NOTE]
> 从 Acl 列表中删除 ACL 后，请确保它不与子网或网络接口相关联。

:::image type="content" source="./media/access-control-lists/delete-acl.png" alt-text="Windows 管理中心的屏幕截图，显示删除 ACL 的确认提示。" lightbox="./media/access-control-lists/delete-acl.png":::

1. 在 " **工具**" 下，向下滚动到 " **网络** " 区域，然后选择 " **访问控制列表**"。
1. 选择 " **清单** " 选项卡，在列表中选择一个 ACL，然后选择 " **删除**"。
1. 在 " **删除确认** " 提示符下，选择 **"是"**。
1. 在 "搜索" 框旁边，选择 " **刷新** " 以确保 ACL 已被删除。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：
- [Azure Stack HCI 和 Windows Server 中 (SDN) 软件定义的网络](../concepts/software-defined-networking.md)
