---
title: 使用 Windows Admin Center 管理 VM
description: 了解如何使用 Windows Admin Center 在 Azure Stack HCI 上的群集中创建和管理虚拟机。
author: v-dasis
ms.topic: how-to
ms.date: 11/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 73d705bf5b36509b3aed31afb09105f2da91862f
ms.sourcegitcommit: 08ef9545316798c9a21c2f9bc1da8c15cb648982
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360225"
---
# <a name="manage-vms-with-windows-admin-center"></a>使用 Windows Admin Center 管理 VM

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

可以使用 Windows Admin Center 在 Azure Stack HCI 上创建和管理虚拟机 (VM)。

## <a name="create-a-new-vm"></a>创建新 VM

可以使用 Windows 管理中心轻松创建新的 VM。

:::image type="content" source="media/manage-vm/new-vm.png" alt-text="新建 VM 屏幕" lightbox="media/manage-vm/new-vm.png":::

1. 在 Windows 管理中心主屏幕上的 " **所有连接** " 下，选择要在其上创建 VM 的服务器或群集。
1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 在 " **虚拟机** " 下选择 " **清单** " 选项卡，然后选择 " **添加** " 和 " **新建** "。
1. 在 " **新建虚拟机** " 下，输入 VM 的名称。
1. 选择“第 2 代(建议)”。
1. 在 " **主机** " 下，选择要在其上驻留 VM 的服务器。
1. 在 " **路径** " 下，从下拉列表中选择一个预先工作的文件路径，或者单击 " **浏览** " 以选择要将 VM 配置和虚拟硬盘 (VHD) 文件保存到的文件夹。 可以通过输入“\\server\share”作为路径浏览到网络上任何可用的 SMB 共享。

1. 在 " **虚拟处理器** " 下，选择虚拟处理器的数量以及是否要为 VM 启用嵌套虚拟化。
1. 在“内存”下，选择启动内存量（建议最少为 4 GB），并选择适合分配给 VM 的动态内存的最小和最大范围。
1. 在“网络”下，从下拉列表中选择一个网络适配器。
1. 在 " **存储** " 下，单击 " **添加** "，然后选择是创建新的空虚拟硬盘还是使用现有的虚拟硬盘。 如果使用现有的虚拟硬盘，请单击“浏览”并选择相应的文件路径。  
1. 在“操作系统”下，执行以下操作之一：
   - 如果要在创建 VM 之后为 VM 安装操作系统，请选择 " **以后安装操作系统** "。
   - 选择“从映像文件(*.iso)安装操作系统”，单击“浏览”，然后选择要使用的相应 .iso 映像文件。
1. 完成后，单击“创建”以创建 VM。
1. 若要启动 VM，请在“虚拟机”列表中，将鼠标指针悬停在新的 VM 上，在左侧启用它的复选框，然后选择“启动”。
1. 在“状态”下，验证 VM 状态是否为“正在运行”。

## <a name="get-a-list-of-vms"></a>获取 VM 列表

你可以轻松查看服务器上或群集中的所有 VM。

:::image type="content" source="media/manage-vm/vm-inventory.png" alt-text="虚拟机屏幕" lightbox="media/manage-vm/vm-inventory.png":::

1. 在 Windows Admin Center 中的“工具”下，向下滚动并选择“虚拟机”。
1. 右侧的“清单”选项卡列出了当前服务器上或群集上可用的所有 VM，并提供了用于管理各个 VM 的命令。 方法：
    - 查看在当前服务器或群集上运行的 VM 的列表。
    - 如果是查看群集的 VM，则查看 VM 的状态和主机服务器。 还可从主机角度查看 CPU 和内存使用情况，包括内存压力、内存需求和已分配的内存、VM 的运行时间、检测信号状态和保护状态（使用 Azure Site Recovery）。
    - 创建新 VM。
    - 对 VM 进行删除、启动、关闭、关机、暂停、恢复、重置或重命名操作。 还可以保存 VM、删除已保存的状态或创建检查点。
    - 更改 VM 的设置。
    - 通过 Hyper-V 主机连接到 VM 控制台。
    - 使用 Azure Site Recovery 复制 VM。
    - 对于可在多个 VM 上运行的操作，例如“启动”、“关机”、“保存”、“暂停”、“删除”或“重置”，可以选择多个 VM 并运行一次操作。

## <a name="view-vm-details"></a>查看 VM 详细信息

你可以从特定 VM 的专用页面查看详细信息和性能图表。

:::image type="content" source="media/manage-vm/vm-details.png" alt-text="虚拟机详细信息屏幕" lightbox="media/manage-vm/vm-details.png":::

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 单击右侧的“清单”选项卡，然后选择 VM。 在后续页面上，可以执行以下操作：

   - 查看 CPU、内存、网络、IOPS 和 IO 吞吐量的实时和历史数据折线图（历史数据仅适用于超融合群集）
   - 查看、创建、应用、重命名和删除检查点。
   - 查看虚拟硬盘 (.vhd) 文件、网络适配器和主机服务器的详细信息。
   - 查看 VM 的状态。
   - 保存 VM、删除已保存的状态、导出或克隆 VM。
   - 更改 VM 的设置。
   - 通过 Hyper-V 主机使用 VMConnect 连接到 VM 控制台。
   - 使用 Azure Site Recovery 复制 VM。

## <a name="view-aggregate-vm-metrics"></a>查看聚合 VM 指标

你可以查看群集中所有 VM 的资源使用情况和性能指标。

:::image type="content" source="media/manage-vm/host-metrics.png" alt-text="主机指标屏幕" lightbox="media/manage-vm/host-metrics.png":::

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 右侧的“摘要”选项卡为所选服务器或群集提供 Hyper-V 主机资源和性能的整体视图，其中包括以下各项：
    - 正在运行、已停止、已暂停和已保存的 VM 的数量
    - 群集的最新运行状况警报或 Hyper-V 事件日志事件
    - CPU 和内存使用情况，包括主机与来宾细目
    - 群集的 IOPS 和 I/O 吞吐量的实时和历史数据折线图

## <a name="change-vm-settings"></a>更改 VM 设置

你可以为 VM 更改各种设置。

> [!NOTE]
> 对于正在运行的 VM，某些设置无法更改，你需要先停止 VM。

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 单击右侧的“清单”选项卡，选择 VM，然后单击“设置”。

1. 若要更改 VM 的启动/停止操作和常规设置，请选择“常规”并执行以下操作：
    - 若要更改 VM 名称，请在“名称”字段中输入名称
    - 若要更改默认的 VM 启动/停止操作，请从下拉框中选择适当的设置。
    - 若要更改暂停或启动 VM 的时间间隔，请在显示的字段中输入适当的值

        :::image type="content" source="media/manage-vm/vm-settings-general.png" alt-text="VM 常规设置屏幕" lightbox="media/manage-vm/vm-settings-general.png":::

1. 选择“内存”以更改 VM 启动内存、动态内存范围、内存缓冲区百分比和内存权重。

    :::image type="content" source="media/manage-vm/vm-settings-memory.png" alt-text="更改 VM 内存设置屏幕" lightbox="media/manage-vm/vm-settings-memory.png":::

1. 选择“处理器”以更改虚拟处理器的数量、启用嵌套虚拟化，或启用同步多线程处理 (SMT)。

    :::image type="content" source="media/manage-vm/vm-settings-processor.png" alt-text="更改 VM 处理器设置屏幕" lightbox="media/manage-vm/vm-settings-processor.png":::

1. 若要更改现有磁盘的大小，请将 **大小 (GB) 的大小** 修改。 若要添加新的虚拟磁盘，请选择“磁盘”，然后选择是创建空的虚拟磁盘还是使用现有的虚拟磁盘或 ISO (.iso) 映像文件。 单击“浏览”并选择虚拟磁盘或映像文件的路径。

    :::image type="content" source="media/manage-vm/vm-settings-disk.png" alt-text="更改 VM 磁盘设置屏幕" lightbox="media/manage-vm/vm-settings-disk.png":::

1. 若要添加、删除或更改网络适配器设置，请选择“网络”并执行以下操作：
    - 指定要使用的虚拟交换机，以及是否要启用虚拟 LAN 标识（还必须指定 VLAN 标识符）
    - 若要更改网络适配器的其他设置，请单击“高级”，以便能够执行以下操作：
        - 选择动态或静态 MAC 地址类型
        - 启用 MAC 地址欺骗
        - 启用带宽管理并指定最大/最小范围

        :::image type="content" source="media/manage-vm/vm-settings-network.png" alt-text="更改 VM 网络设置屏幕" lightbox="media/manage-vm/vm-settings-network.png":::

1. 选择“引导顺序”以添加引导设备或更改 VM 引导顺序。

    :::image type="content" source="media/manage-vm/vm-settings-boot.png" alt-text="更改 VM 引导顺序屏幕" lightbox="media/manage-vm/vm-settings-boot.png":::

1. 选择要启用 VM 检查点的 **检查** 点，选择检查点类型，并指定检查点文件位置。

    > [!NOTE]
    > 建议使用“生产”检查点设置，并使用来宾操作系统中的备份技术创建数据一致的检查点。 “标准”设置使用 VHD 快照创建包含应用程序和服务状态的检查点。

     :::image type="content" source="media/manage-vm/vm-settings-checkpoint.png" alt-text="更改 VM 检查点屏幕" lightbox="media/manage-vm/vm-settings-checkpoint.png":::

1. 选择 " **关联规则** " 可为 VM 创建关联规则。 有关创建关联规则的详细信息，请参阅 [为 Vm 创建服务器和站点关联规则](vm-affinity.md)。

    :::image type="content" source="media/manage-vm/vm-affinity.png" alt-text="VM 相关性规则屏幕" lightbox="media/manage-vm/vm-affinity.png":::

1. 若要更改 VM 安全设置，请选择“安全性”并执行以下操作：
    - 选择“启用安全引导”以防止在引导时运行未经授权的代码（建议）。 同时，从下拉框中选择一个 Microsoft 模板或开源模板
    - 对于 " **模板** "，请选择要使用的安全模板

    - 在“加密支持”下，你可以执行以下操作：

        - 选择“启用受信任的平台模块”，以便能够使用硬件加密服务模块

        - 启用对状态和虚拟机迁移流量的加密

        > [!NOTE]
        > 加密支持要求使用 VM 的密钥保护程序 (KP)。 如果尚不存在此 KP，则选择其中一个选项将生成允许在此主机上运行 VM 的 KP。

    - 在“安全策略”下选择“启用屏蔽”，为 VM 提供更多保护选项。

        :::image type="content" source="media/manage-vm/vm-settings-security.png" alt-text="更改 VM 安全设置" lightbox="media/manage-vm/vm-settings-security.png":::

## <a name="move-a-vm-to-another-server-or-cluster"></a>将 VM 移到另一个服务器或群集

你可以轻松地将虚拟机移动到另一个服务器或另一个群集，如下所示：

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 在 " **清单** " 选项卡下，从列表中选择一个 VM，然后选择 " **管理 > 移动** "。
1. 从列表中选择一台服务器，然后选择“移动”。
1. 如果要移动 VM 及其存储，请选择是将它移动到另一个群集还是同一群集中的另一台服务器。

    :::image type="content" source="media/manage-vm/vm-more-move.png" alt-text="移动 VM 屏幕" lightbox="media/manage-vm/vm-more-move.png":::

1. 如果只想移动 VM 的存储，请选择将其移到相同的路径，或选择不同的配置、检查点或智能分页路径。

    :::image type="content" source="media/manage-vm/vm-move-storage.png" alt-text="移动 VM 存储屏幕" lightbox="media/manage-vm/vm-move-storage.png":::

## <a name="join-a-vm-to-a-domain"></a>将 VM 加入域

可以轻松地将 VM 加入到域中，如下所示：

:::image type="content" source="media/manage-vm/vm-domain-join.png" alt-text="移动 VM 域加入屏幕" lightbox="media/manage-vm/vm-domain-join.png":::

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 在 " **清单** " 选项卡下，从列表中选择一个 VM，然后选择 " **管理 > 域加入** "。
1. 输入要加入的域的名称，以及域用户名和密码。
1. 输入 VM 用户名和密码。
1. 完成后，单击 " **加入** "。

## <a name="clone-a-vm"></a>克隆 VM

可以轻松地克隆 VM，如下所示：

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 选择右侧的“清单”选项卡。 从列表中选择 VM，然后选择 " **管理 > 克隆** "。
1. 指定克隆的 VM 的名称和路径。
1. 请在 VM 上运行 Sysprep （如果尚未这样做）。

:::image type="content" source="media/manage-vm/vm-clone.png" alt-text="克隆 VM 屏幕" lightbox="media/manage-vm/vm-clone.png":::

## <a name="import-or-export-a-vm"></a>导入或导出 VM

你可以轻松地导入或导出 VM。 下面的过程介绍了导入流程。

:::image type="content" source="media/manage-vm/vm-more-import.png" alt-text="导入 VM 屏幕" lightbox="media/manage-vm/vm-more-import.png":::

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 在 " **清单** " 选项卡上，选择 " **添加 > 导入** "。
1. 输入包含 VM 的文件夹名称，或单击“浏览”并选择一个文件夹。
1. 选择要导入的 VM。
1. 如果需要，请为 VM 创建唯一 ID。
1. 完成后，选择“导入”。

对于导出 VM，该过程类似：

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 在 " **清单** " 选项卡上，选择要导出的 VM 列表。
1. 选择 " **管理 > 导出** "。
1. 输入要将 VM 导出到的路径。

:::image type="content" source="media/manage-vm/vm-export.png" alt-text="导出 VM 屏幕" lightbox="media/manage-vm/vm-export.png":::

## <a name="view-vm-event-logs"></a>查看 VM 事件日志

你可以查看 VM 事件日志，如下所述：

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 在右侧的“摘要”选项卡上，选择“查看所有事件”。
1. 选择一个事件类别并展开视图。

## <a name="connect-to-a-vm-by-using-remote-desktop"></a>使用远程桌面连接到 VM

你还可以使用远程桌面协议 (RDP) 连接通过 Hyper-V 主机管理 VM，而不使用 Windows Admin Center。

1. 在 " **工具** " 下，向下滚动并选择 " **虚拟机** "。
1. 在 " **清单** " 选项卡上，从列表中选择 "选择虚拟机"，并选择 " **连接" > 连接** 或 **连接 > 下载 RDP 文件** "选项。 这两个选项都使用 VMConnect 工具通过 Hyper-V 主机连接到来宾 VM，并要求你为 Hyper-V 主机输入管理员用户名和密码凭据。

    - “连接”选项在 Web 浏览器中使用远程桌面连接到 VM。

    - “下载 RDP 文件”选项会下载一个 .rdp 文件，你可以打开该文件以使用远程桌面连接应用 (mstsc.exe) 进行连接。

## <a name="protect-vms-with-azure-site-recovery"></a>使用 Azure Site Recovery 保护 VM

你可以使用 Windows Admin Center 配置 Azure Site Recovery 并将本地 VM 复制到 Azure。 这是一项可选的增值服务。 若要开始，请参阅[使用 Azure Site Recovery 保护 VM](azure-site-recovery.md)。

:::image type="content" source="media/manage-vm/vm-more-azure.png" alt-text="设置 Azure Site Recovery 屏幕" lightbox="media/manage-vm/vm-more-azure.png":::

## <a name="next-steps"></a>后续步骤

你还可以使用 Windows PowerShell 创建和管理 VM。 有关详细信息，请参阅[使用 Windows PowerShell 管理 Azure Stack HCI 上的 VM](vm-powershell.md)。