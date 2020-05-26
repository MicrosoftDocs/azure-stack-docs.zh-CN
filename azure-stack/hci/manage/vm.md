---
title: 使用 Windows 管理中心管理 Azure Stack HCI 上的 Vm
description: 了解如何使用 Windows 管理中心在 Azure Stack HCI 的群集中创建和管理虚拟机。
author: v-dasis
ms.topic: article
ms.date: 05/20/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: f3ba9415cd5d91260067a1648bdb38c196be206a
ms.sourcegitcommit: d69eacbf48c06309b00d17c82ebe0ce2bc6552df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83794203"
---
# <a name="manage-vms-on-azure-stack-hci-using-windows-admin-center"></a>使用 Windows 管理中心管理 Azure Stack HCI 上的 Vm

> 适用于 Windows Server 2019

Windows 管理中心可用于在 Azure Stack HCI 上创建和管理虚拟机（Vm）。

## <a name="get-a-list-of-vms"></a>获取 Vm 列表

你可以轻松地查看服务器或群集中的所有 Vm。

:::image type="content" source="media/manage-vm/vm-inventory.png" alt-text="虚拟机屏幕":::

1. 在 Windows 管理中心的 "**工具**" 下，向下滚动并选择 "**虚拟机**"。
1. 右侧的 "**清单**" 选项卡列出了当前服务器或群集上的所有可用 vm，并提供了用于管理单个 vm 的命令。 你可以：
    - 查看在当前服务器或群集上运行的 Vm 的列表。
    - 查看 VM 的状态和主机服务器（如果正在查看群集的虚拟机）。 还可查看主机的 CPU 和内存使用情况，包括内存压力、内存需求和分配的内存、VM 的运行时间、检测信号状态和保护状态（使用 Azure Site Recovery）。
    - 创建新 VM。
    - 删除、启动、关闭、关闭、暂停、继续、重置或重命名虚拟机。 同时，保存 VM、删除已保存状态或创建检查点。
    - 更改 VM 的设置。
    - 通过 Hyper-v 主机连接到 VM 控制台。
    - 使用 Azure Site Recovery 复制 VM。
    - 对于可在多个 Vm 上运行的操作，例如 "启动"、"关闭"、"保存"、"暂停"、"删除" 或 "重置"，可以选择多个 Vm 并运行一次操作。

## <a name="view-vm-metrics"></a>查看 VM 指标

你可以从其专用页面查看特定 VM 的详细信息和性能图表。

:::image type="content" source="media/manage-vm/vm-details.png" alt-text="虚拟机详细信息屏幕":::

1. 在 "**工具**" 下，向下滚动并选择 "**虚拟机**"。
1. 单击右侧的 "**清单**" 选项卡，并选择 VM。 在后续页面上，可以执行以下操作：

   - 查看 CPU、内存、网络、IOPS 和 IO 吞吐量的实时和历史数据折线图（仅适用于超聚合群集的历史数据）
   - 查看、创建、应用、重命名和删除检查点。
   - 查看虚拟硬盘（.vhd）文件、网络适配器和主机服务器的详细信息。
   - 查看 VM 的状态。
   - 保存 VM、删除已保存状态或创建检查点。
   - 更改 VM 的设置。
   - 通过 Hyper-v 主机使用 VMConnect 连接到 VM 控制台。
   - 使用 Azure Site Recovery 复制 VM。

## <a name="view-aggregate-vm-metrics"></a>查看聚合 VM 指标

可以查看群集中所有 Vm 的资源使用情况和性能指标。

:::image type="content" source="media/manage-vm/host-metrics.png" alt-text="主机指标屏幕":::

1. 在 "**工具**" 下，向下滚动并选择 "**虚拟机**"。
1. 右侧的 "**摘要**" 选项卡为所选服务器或群集提供 hyper-v 主机资源和性能的整体视图，包括以下各项： 
    - 正在运行、已停止、已暂停和已保存的 Vm 的数量
    - 群集的最新运行状况警报或 Hyper-v 事件日志事件
    - 主机与来宾细目的 CPU 和内存使用情况
    - 用于分类的 IOPS 和 i/o 吞吐量的实时和历史数据折线图

## <a name="change-vm-settings"></a>更改 VM 设置

可为 VM 更改各种设置。

> [!NOTE]
> 对于正在运行的 VM，某些设置无法更改，你将需要先停止 VM。

1. 在 "**工具**" 下，向下滚动并选择 "**虚拟机**"。
1. 单击右侧的 "**清单**" 选项卡，选择 VM，然后单击 "**设置**"。

1. 若要更改 VM 启动/停止操作和常规设置，请选择 "**常规**"，然后执行以下操作：
    - 若要更改 VM 名称，请在 "**名称**" 字段中输入
    - 若要更改默认 VM 启动/停止操作，请从下拉框中选择适当的设置。
    - 若要更改暂停或启动 VM 的时间间隔，请在显示的字段中输入适当的值

        :::image type="content" source="media/manage-vm/vm-settings-general.png" alt-text="VM 常规设置屏幕":::

1. 选择 "**内存**" 以更改 VM 启动内存、动态内存范围、内存缓冲区百分比和内存权重。

    :::image type="content" source="media/manage-vm/vm-settings-memory.png" alt-text="更改 VM 内存设置屏幕":::

1. 选择 "**处理器**" 以更改虚拟处理器的数量，启用嵌套虚拟化，或启用同步多线程处理（SMT）。

    :::image type="content" source="media/manage-vm/vm-settings-processor.png" alt-text="更改 VM 处理器设置屏幕":::

1. 若要添加新的虚拟磁盘，请选择 "**磁盘**"，然后选择是创建空虚拟磁盘还是使用现有虚拟磁盘或 iso （.iso）映像文件。 单击 "**浏览**" 并选择虚拟磁盘或映像文件的路径。

    :::image type="content" source="media/manage-vm/vm-settings-disk.png" alt-text="更改 VM 磁盘设置屏幕":::

1. 若要添加、删除或更改网络适配器设置，请选择 "**网络**"，然后执行以下操作：
    - 指定要使用的虚拟交换机，以及是否要启用虚拟 LAN 标识（还必须指定 VLAN 标识符）
    - 若要更改网络适配器适配器的其他设置，请单击 "**高级**" 以执行以下操作：
        - 选择动态或静态 MAC 地址类型
        - 启用 MAC 地址欺骗
        - 启用带宽管理并指定最大/最小范围

        :::image type="content" source="media/manage-vm/vm-settings-network.png" alt-text="更改 VM 网络设置屏幕":::

1. 选择 "**启动顺序**" 以添加启动设备或更改 VM 启动顺序。

    :::image type="content" source="media/manage-vm/vm-settings-boot.png" alt-text="更改 VM 启动顺序屏幕":::

1. 选择 "**检查**点" 以启用 VM 检查点，选择检查点类型，然后选择 "检查点位置"。

    > [!NOTE]
    > 建议使用**生产**检查点设置，并使用来宾操作系统中的备份技术创建数据一致的检查点。 **标准**设置使用 VHD 快照创建具有应用程序和服务状态的检查点。

     :::image type="content" source="media/manage-vm/vm-settings-checkpoint.png" alt-text="更改 VM 检查点屏幕":::

1. 若要更改 VM 安全设置，请选择 "**安全**"，然后执行以下操作：
    - 选择 "**启用安全启动**" 可帮助防止在启动时运行未经授权的代码（建议使用）。 同时，从下拉框中选择 Microsoft 或开源模板

    - 在 "**加密支持**" 下，你可以

        - 选择 "**启用受信任的平台模块**"，以便能够使用硬件加密服务模块

        - 启用状态和虚拟机迁移流量的加密

        > [!NOTE]
        > 加密支持需要 VM 的密钥保护程序（KP）。 如果尚未提供，选择其中一个选项将生成允许在此主机上运行 VM 的 KP。

    - 在 "**安全策略**" 下，选择 "为 VM 启用其他保护选项**防护**"。

        :::image type="content" source="media/manage-vm/vm-settings-security.png" alt-text="更改 VM 安全设置":::

## <a name="create-a-new-vm"></a>创建新 VM

可以使用 Windows 管理中心轻松创建新的虚拟机。

:::image type="content" source="media/manage-vm/new-vm.png" alt-text="新 VM 屏幕":::

1. 在 Windows 管理中心主页的 "**所有连接**" 下，选择要在其上创建虚拟机的服务器或群集。
1. 在 "**工具**" 下，向下滚动并选择 "**虚拟机**"。
1. 在 "**虚拟机**" 下，选择 "**清单**" 选项卡，然后选择 "**新建**"。
1. 在 "**新建虚拟机**" 下，输入 VM 的名称。
1. 选择 "**第2代（推荐）**"。
1. 从下拉列表中选择一个预先工作的文件路径，或者单击 "**浏览**" 选择要将 VM 配置和虚拟硬盘（VHD）文件保存到的文件夹。 可以通过将路径输入为* \\ server\share*，在网络上浏览任意可用的 SMB 共享。

1. 在 "**虚拟处理器**" 下，选择虚拟处理器的数量以及是否要启用嵌套虚拟化。
1. 在 "**内存**" 下，选择启动内存量（建议最少为 4 GB），并选择适用于分配给 VM 的动态内存的最小和最大范围。
1. 在 "**网络**" 下，从下拉列表中选择一个网络适配器。
1. 在 "**存储**" 下，单击 "**添加**"，然后选择是要创建新的虚拟硬盘还是使用现有的虚拟硬盘。 如果使用现有的虚拟硬盘，请单击 "**浏览**" 并选择相应的文件路径。  
1. 在 "**操作系统**" 下，执行以下操作之一：
   - 如果以后要为 VM 安装操作系统，请选择 "**以后安装操作系统**"。
   - 选择 "**从映像文件安装操作系统（* .iso）**"，单击 "**浏览**"，然后选择要使用的相应 .iso 映像文件。
   - 如果要在以后使用此方法在 VM 上安装操作系统，请选择 "**从基于网络的安装服务器安装操作系统**"。 请确保已选择以前的网络适配器，否则它将不起作用。
1. 完成后，单击 "**创建**" 以创建 VM。
1. 若要启动 VM，请在 "**虚拟机**" 列表中，将鼠标悬停在新的 VM 上，在左侧启用它旁边的复选框，然后选择 "**启动**"。
1. 在 "**状态**" 下，验证 VM 状态是否为 "**正在运行**"。

## <a name="move-a-vm-to-another-server-in-the-cluster"></a>将 VM 移到群集中的另一台服务器

可以轻松地将虚拟机移动到群集中的另一台服务器，如下所示：

:::image type="content" source="media/manage-vm/vm-more-move.png" alt-text="移动 VM 屏幕":::

1. 在 "**工具**" 下，向下滚动并选择 "**虚拟机**"。
1. 选择右侧的 "**清单**" 选项卡。 从列表中选择虚拟机，并选择 "**更多 > 移动**"。
1. 从列表中选择服务器，然后选择 "**移动**"。
1. 在 "**移动虚拟机**" 下，选择 "**故障转移群集**"，然后输入要将 VM 移到的群集名称和群集节点。
1. 成功移动后，会在 "**主机服务器**" 下的列表中看到名称已更新。

## <a name="import-or-export-a-vm"></a>导入或导出 VM

可以轻松地导入或导出 VM。 以下过程描述了导入过程。

:::image type="content" source="media/manage-vm/vm-more-import.png" alt-text="导入 VM 屏幕":::

1. 在 "**工具**" 下，向下滚动并选择 "**虚拟机**"。
1. 选择右侧的 "**清单**" 选项卡。 从列表中选择群集虚拟机，并选择 "**更多 > 导入**"。
1. 输入包含 VM 的文件夹名称，或者单击 "**浏览**" 并选择一个文件夹。
1. 选择要导入的虚拟机。
1. 如果需要，请为 VM 创建唯一 ID。
1. 完成后，选择 "**导入**"。

对于导出 VM，该过程非常相似。 只需选择 "**更多 > 导出**。

## <a name="view-vm-event-logs"></a>查看 VM 事件日志

可以按如下所示查看 VM 事件日志：

1. 在 "**工具**" 下，向下滚动并选择 "**虚拟机**"。
1. 在右侧的 "**摘要**" 选项卡上，选择 "**查看所有事件**"。
1. 选择事件类别并展开视图。

## <a name="connect-to-a-vm-by-using-remote-desktop"></a>使用远程桌面连接到 VM

你还可以使用远程桌面协议（RDP）连接通过 Hyper-v 主机管理 Vm，而不是使用 Windows 管理中心。

1. 在 "**工具**" 下，向下滚动并选择 "**虚拟机**"。
1. 选择右侧的 "**清单**" 选项卡。 从列表中选择一个虚拟机，并选择 "**更多 > 连接**或**多个 > 下载 RDP 文件**" 选项。 这两个选项都使用 VMConnect 工具通过 Hyper-v 主机连接到来宾 VM，并要求你为 Hyper-v 主机输入管理员用户名和密码凭据。

    - **连接**选项在 web 浏览器中使用远程桌面连接到 VM。

    - "**下载 rdp 文件**" 选项会下载一个 .rdp 文件，你可以打开该文件以连接到远程桌面连接应用（mstsc）。

## <a name="protect-vms-with-azure-site-recovery"></a>Azure Site Recovery 保护 Vm

可以使用 Windows 管理中心来配置 Azure Site Recovery 并将本地 Vm 复制到 Azure。 这是一个可选的值添加服务。 若要开始使用，请参阅[使用 Azure Site Recovery 保护 vm](azure-site-recovery.md)。

:::image type="content" source="media/manage-vm/vm-more-azure.png" alt-text="安装 Azure Site Recovery 屏幕":::

## <a name="next-steps"></a>后续步骤

你还可以使用 Windows PowerShell Hyper-v cmdlet 来创建和管理 Vm。 有关详细信息，请参阅[hyper-v](https://docs.microsoft.com/powershell/module/hyper-v/?view=win10-ps)。