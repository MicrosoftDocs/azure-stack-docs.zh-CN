---
title: 添加或删除 Azure Stack HCI 群集的服务器
description: 了解如何在 Azure Stack HCI 的群集中添加或删除服务器节点
ms.topic: article
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 4d8e122c8a0b68740ecf34f8140322ee627d03e7
ms.sourcegitcommit: 91f4baa7a770b7a82f1ddccec4dbac14be530d06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83768983"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>添加或删除 Azure Stack HCI 群集的服务器

> 适用于：Windows Server 2019

可以在 Azure Stack HCI 的群集中轻松地添加或删除服务器。 请记住，对于 CPU 类型、内存、驱动器数量以及驱动器的类型和大小，每个新的物理服务器都必须与群集中的服务器的其余部分严格匹配。

每次添加或删除服务器时，还必须执行群集验证，以确保群集正常运行。

## <a name="obtain-server-hardware-from-your-oem"></a>从 OEM 获取服务器硬件

第一步是从原始 OEM 获取新的 HCI 硬件。 添加新的服务器硬件时，请始终参考 OEM 提供的文档，以便在群集中使用。

1. 将新的物理服务器置于机架中并进行正确的布线。
1. 启用物理交换机端口并调整访问控制列表（Acl）和 VLAN Id （如果适用）。
1. 在基板管理控制器（BMC）中配置正确的 IP 地址，并按 OEM 说明应用所有 BIOS 设置。
1. 使用 OEM 提供的工具将当前固件基线应用于所有组件。
1. 运行 OEM 验证测试，以确保与现有群集服务器同质化。

## <a name="add-the-server-to-the-cluster"></a>将服务器添加到群集

服务器启动正确后，请使用 Windows 管理中心将服务器加入群集。

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="添加服务器屏幕":::

1. 在**Windows 管理中心**的顶部下拉箭头中，选择 "**群集管理器**"。
1. 在 "**群集连接**" 下，选择群集。
1. 在 "**工具**" 下，选择 "**服务器**"。
1. 在 "**服务器**" 下，选择 "**清单**" 选项卡。
1. 在 "**清单**" 选项卡上，选择 "**添加**"。
1. 在 "**服务器名称**" 中，输入要添加的服务器的完全限定的域名，单击 "**添加**"，然后再次单击底部的 "**添加**"。
1. 验证是否已将服务器成功添加到群集。

## <a name="remove-a-server-from-the-cluster"></a>从群集中删除服务器

从群集中删除服务器的步骤类似于向群集中添加服务器的步骤。

请记住，删除服务器时，也会删除与该服务器关联的所有虚拟机、驱动器和工作负荷。

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="删除服务器对话框":::

1. 在**Windows 管理中心**的顶部下拉箭头中，选择 "**群集管理器**"。
1. 在 "**群集连接**" 下，选择群集。
1. 在 "**工具**" 下，选择 "**服务器**"。
1. 在 "**服务器**" 下，选择 "**清单**" 选项卡。
1. 在 "**清单**" 选项卡上，选择要删除的服务器，然后选择 "**删除**"。
1. 若要同时从存储池中删除任何服务器驱动器，请启用该复选框。
1. 验证是否已从群集中成功删除该服务器。

## <a name="validate-the-cluster"></a>验证群集

每当在群集中添加或删除服务器时，都必须验证群集。 群集验证必须通过而不受 Microsoft 支持。

:::image type="content" source="media//manage-cluster/validate-cluster.png" alt-text="验证群集对话框":::

1. 在**Windows 管理中心**的顶部下拉箭头中，选择 "**群集管理器**"。
1. 在 "**工具**" 下，选择 "**服务器**"。
1. 在 "**服务器**" 下选择 "**清单**" 选项卡，选择 "**更多**"，然后选择 "**验证群集**"。
1. 验证是否所有验证步骤均已成功通过。

## <a name="next-steps"></a>后续步骤

- 若要了解有关群集验证的详细信息，请参阅[验证故障转移群集的硬件](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134244(v=ws.11))。