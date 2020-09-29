---
title: 使用 Windows Admin Center 创建 Azure Stack HCI 群集
description: 了解如何使用 Windows Admin Center 为 Azure Stack HCI 创建服务器群集
author: v-dasis
ms.topic: how-to
ms.date: 09/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b7c6c76353ff29f01eca458ca563517807ca0cd3
ms.sourcegitcommit: 9a3397f703ff9dd7d539372bd8e5fdbe6d6a0725
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "91019516"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>使用 Windows Admin Center 创建 Azure Stack HCI 群集

> 适用于 Azure Stack HCI 版本 v20H2 版

本文介绍了如何使用 Windows Admin Center 创建使用存储空间直通的 Azure Stack HCI 超融合群集。 Windows Admin Center 中的创建群集向导将为你执行大部分繁重的操作。 如果你想要使用 PowerShell 自行完成此操作，请参阅[使用 PowerShell 创建 Azure Stack HCI 群集](create-cluster-powershell.md)。 此 PowerShell 文章还是一个很好的信息来源，它深入介绍了向导的情况，适合用来进行故障排除。

在创建群集时，可以在两种群集类型之间进行选择：

- 包含至少两个服务器节点的标准群集，这些节点位于单个站点中。
- 包含至少四个服务器节点（跨两个站点）的延伸群集，每个站点至少有两个节点。

有关延伸群集的详细信息，请参阅[延伸群集概述](../concepts/stretched-clusters.md)。

如果你对 Azure Stack HCI 的测试感兴趣，但有限制或没有备用硬件，请查看 [AZURE STACK HCI 评估指南](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md)，其中介绍了如何使用嵌套虚拟化（在 Azure 中）或本地单一物理系统上的 Azure Stack HCI。

## <a name="before-you-run-the-wizard"></a>运行向导之前

在运行“创建群集”向导之前，请确保你已经完成以下任务：

- 已阅读[部署 Azure Stack HCI 之前的准备工作](before-you-start.md)中的硬件要求和其他要求。
- 在群集中的每台服务器上安装 Azure Stack HCI OS。 请参阅[部署 Azure Stack HCI 操作系统](operating-system.md)。
- 在每台服务器上拥有一个是本地管理员组成员的帐户。
- 在用于管理的 PC 或服务器上安装 Windows Admin Center。 请参阅[安装 Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install)。
- 对于延伸群集，请在 Active Directory 中预先设置两个站点。 但不要担心，向导也可以替你设置它们。

如果是在服务器（而不是 PC）上运行 Windows Admin Center，请使用是“网关管理员”组成员的帐户，或者使用 Windows Admin Center 服务器上的本地管理员组。

此外，Windows Admin Center 管理计算机必须加入到你要在其中创建群集的同一个 Active Directory 域或完全受信任的域。 你要加入群集的服务器不需要已属于此域，可以在群集创建过程中将它们添加到域。

下面是“创建群集”向导中的主要步骤：

1. **开始** - 确保每台服务器都满足加入群集的先决条件，并且具有加入群集所需的功能。
1. **联网** - 分配和配置网络适配器，为每台服务器创建虚拟交换机。
1. **群集** - 验证群集是否已正确设置。 对于延伸群集，还请设置两个站点。
1. **存储** - 配置存储空间直通。

向导完成后，请设置群集见证，注册到 Azure，并创建卷（如果要创建延伸群集，则还需设置站点之间的复制）。

好了，让我们开始：

1. 在 Windows Admin Center 中，在“所有连接”下单击“添加” 。
1. 在“添加资源”面板中，在“Windows Server 群集”下选择“新建”。  
1. 在“选择群集类型”下，选择“Azure Stack HCI”。

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="创建群集向导- HCI 选项" lightbox="media/cluster/create-cluster-type.png":::

1. 在“选择服务器位置”下，选择以下选项之一：

    - **一个站点中的所有服务器**
    - **两个站点中的服务器**（适用于延伸群集）

1. 完成后，单击“创建”。 现在，你将看到“创建群集”向导，如下所示。

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="“创建群集”向导 - 开始" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>步骤 1：入门

向导的步骤 1 会指导你完成的操作包括：确保满足所有先决条件，添加服务器节点，安装所需的功能，然后在需要时重启每台服务器。

1. 查看向导中列出的先决条件，以确保每个服务器节点都已做好加入群集的准备。 完成后，单击 **“下一步”** 。
1. 在“将服务器添加到群集”页上，输入你的帐户的用户名和密码，然后单击“下一步” 。 该帐户必须是每台服务器上本地管理员组的成员。
1. 输入要添加的第一台服务器的名称，然后单击“添加”。
1. 对于将成为群集一部分的每台服务器，重复步骤 3。 完成后，单击 **“下一步”** 。
1. 如果需要，请在“将服务器加入域”页上指定将服务器加入域所需的域和帐户。 接着，可以将服务器重命名为更易记的名称，然后单击“下一步”。
1. 单击“安装功能”。 完成后，单击 **“下一步”** 。

    向导将为你安装以下必需的功能：

    - BitLocker
    - 数据中心桥接（适用于 RoCEv2 网络适配器）
    - 故障转移群集
    - 文件服务器
    - FS-Data-Deduplication 模块
    - Hyper-V
    - RSAT-AD-PowerShell 模块
    - 存储副本（仅为延伸群集安装）

1. 对于“安装更新”，需要时请单击“安装更新”。 完成后，单击“下一步”。
1. 对于“解决方案更新”，需要时请单击“安装扩展”。 完成后，单击“下一步”。
1. 需要时单击“重启服务器”。 验证每台服务器是否已成功启动。

## <a name="step-2-networking"></a>步骤 2：网络

向导的步骤2逐步讲解如何为群集配置虚拟交换机和其他网络元素。

> [!NOTE]
> 如果在任何网络或虚拟交换机步骤中看到列出了错误，请尝试再次单击“应用并测试”。

1. 在完成时选择“下一步:网络”。
1. 在“验证网络适配器”下，等待每个适配器旁边出现绿色的复选框，然后选择“下一步”。 

1. 对于“选择管理适配器”，请选择要用于每台服务器的一个或两个管理适配器。 出于管理目的，必须至少选择一个适配器，因为向导至少需要一个专用物理 NIC 进行群集管理。  指定进行管理所需的适配器后，系统就会将其从向导工作流的其余部分中排除。

    管理适配器有两个配置选项：

    - **用于管理的一个物理网络适配器**。 对于此选项，将支持 DHCP 或静态 IP 地址分配。

    - **用于管理的两个物理网络适配器**。 组合使用一对适配器时，仅支持静态 IP 地址分配。 如果所选适配器使用 DHCP 寻址（不管是针对其中的一个还是两个），则 DHCP IP 地址会在创建虚拟交换机之前转换为静态 IP 地址。

    可以通过使用组合的适配器与多个交换机建立单一连接，但只使用单个 IP 地址。 负载均衡会变得可用，并且容错功能是即时启用的，不需等待 DNS 记录更新。

    现在为每台服务器执行以下操作：

    - 选中“说明”复选框。 请注意，所有适配器都处于选中状态，向导可能会为你提供建议。
    - 取消选中不希望将其用于群集管理的那些适配器的复选框。

    > [!NOTE]
    > 可以使用 1 Gb 适配器作为管理适配器，但建议使用 10 Gb 或更快的适配器来传输存储和工作负荷 (VM) 流量。

1. 进行更改后，单击“应用并测试”。
1. 在“定义网络”下，确保每台服务器的每个网络适配器都有唯一的静态 IP 地址、子网掩码和 VLAN ID。 将鼠标指针悬停在每个表元素上，根据需要输入或更改值。 完成后，单击“应用并测试”。

    > [!NOTE]
    > 为了支持群集的 VLAN ID 配置，所有服务器中的所有网卡都必须支持 VLANID 属性。

1. 等到每台服务器的“状态”列都显示为“已通过”后，单击“下一步”。   此步骤验证具有相同子网和 VLAN ID 的所有适配器之间的网络连接性。 在下一步中创建虚拟交换机后，提供的 IP 地址将从物理适配器传输到虚拟适配器。 这可能需要几分钟才能完成，具体取决于配置的适配器数量。

1. 在“虚拟交换机”下，根据情况选择以下选项之一。 并非所有选项都会显示，具体取决于存在的适配器数量：

    - **跳过虚拟交换机创建**
    - **同时为计算和存储创建一个虚拟交换机**
    - **仅为计算创建一个虚拟交换机**
    - **创建两个虚拟交换机**

    > [!NOTE]
    > 如果要部署用于 SDN 的网络控制器 (在 **步骤5：** 向导的 SDN) 中，你将需要一个虚拟交换机。 因此，如果你在此处选择不创建虚拟交换机，而不是在向导外部创建它，则向导将不会部署网络控制器。

    下表显示了针对各种网络适配器配置支持和启用的虚拟交换机配置：

    | 选项 | 1-2 个适配器 | 3 个以上的适配器 | 组合适配器 |
    | :------------- | :--------- |:--------| :---------|
    | 单个交换机（计算 + 存储） | enabled | enabled  | 不支持 |
    | 单个交换机（仅计算） | 不支持| enabled | enabled |
    | 两个交换机 | 不支持 | enabled | enabled |

1. 根据需要更改交换机和其他配置设置的名称，然后单击“应用并测试”。 创建虚拟交换机后，每台服务器的“状态”列都应显示为“已通过”。

## <a name="step-3-clustering"></a>步骤 3：群集功能

向导的步骤 3 可确保目前的所有操作都已正确设置，并且在进行延伸群集部署的情况下会自动设置两个站点，然后实际创建群集。 也可在 Active Directory 中预先设置站点。

1. 在完成时选择“下一步:群集功能”。
1. 在“验证群集”下，选择“验证”。 验证可能需要几分钟。

    如果出现“凭据安全服务提供程序(CredSSP)”弹出窗口，请选择“是”以暂时启用 CredSSP，以便向导继续。  创建群集并完成向导后，需禁用 CredSSP 以提高安全性。 如果遇到 CredSSP 问题，请参阅[对 CredSSP 进行故障排除](../manage/troubleshoot-credssp.md)以获取更多信息。

1. 检查所有验证状态，下载报告以获取有关任何故障的详细信息，进行更改，然后单击“再次验证”（如果需要）。 根据需要重复此步骤，直到所有验证检查都通过。
1. 在“创建群集”下，输入群集的名称。
1. 在“网络”下选择首选配置。
1. 在“IP 地址”下，选择要使用的动态或静态 IP 地址。
1. 完成后，单击“创建群集”。

1. 对于延伸群集，请在“将服务器分配给站点”下指定要使用的两个站点。

1. 接下来，将每台服务器分配给一个站点。 稍后需设置站点之间的复制。 完成后，单击“应用”。

## <a name="step-4-storage"></a>步骤 4：存储

向导的步骤 4 将引导你为群集设置存储空间直通。

1. 在完成时选择“下一步:存储”。
1. 在“验证驱动器”下，单击每台服务器旁边的 **>** 图标以验证磁盘是否正常工作并处于已连接状态，然后单击“下一步” 。
1. 在“清理驱动器”下，单击“清理驱动器”以清空数据驱动器。  准备就绪后，单击“下一步”。
1. 在“验证存储”下，单击“下一步”。
1. 查看验证结果。 如果一切正常，请单击“下一步”。
1. 在“启用存储空间直通”下，单击“启用”。 
1. 下载报告并查看。 如果一切正常，请单击“完成”。

恭喜！你现在有了一个群集。

创建群集后，在整个域中复制群集名称可能要花费一些时间，尤其是在工作组服务器新添加到 Active Directory 中的情况下。 虽然群集可能显示在 Windows Admin Center 中，但可能尚不能连接到它。

如果解析群集在一段时间后未成功，则在大多数情况下，你可以使用群集中的服务器名称来代替群集名称。

## <a name="step-5-sdn-optional"></a>步骤5： SDN (可选) 

向导的第5步逐步介绍如何在群集上为软件定义的网络 (SDN) 设置网络控制器。 网络控制器设置完成后，可用于配置 SDN 的其他组件，如软件负载平衡器和 RAS 网关。

> [!NOTE]
> 向导的这一步是可选的。

:::image type="content" source="media/cluster/create-cluster-network-controller.png" alt-text="创建群集向导-SDN 网络控制器" lightbox="media/cluster/create-cluster-network-controller.png":::

1. 选择 **下一步： SDN**。
1. 在 " **主机**" 下，输入网络控制器的名称。
1. 指定 Azure Stack HCI VHD 文件的路径。 使用 " **浏览** " 来查找速度更快。
1. 指定要为网络控制器专用的 Vm 数量。 建议使用三到五台 VM 以实现高可用性。
1. 在 " **网络**" 下，输入 VLAN ID。
1. 对于 **VM 网络寻址**，选择 " **DHCP** " 或 " **静态**"。
1. 如果选择了 **DHCP**，请输入网络控制器 vm 的名称和 IP 地址。
1. 如果选择了 " **静态**"，请执行以下操作：
    1. 指定子网前缀。
    1. 指定默认网关。
    1. 指定一个或多个 DNS 服务器。 单击 " **添加** " 以添加其他 DNS 服务器。
1. 在 " **凭据**" 下，输入用于将网络控制器 vm 加入到群集域的用户名和密码。
1. 输入这些 Vm 的本地管理密码。
1. 在 " **高级**" 下，输入 vm 的路径。
1. 输入 **mac 地址池开始** 和 **mac 地址池结束**的值。
1. 完成后，单击 **“下一步”** 。
1. 等待向导完成其作业。 一直在此页上，直到所有进度任务完成。 然后单击“完成”。
 
如果网络控制器部署失败，请执行以下操作，然后重试：

- 停止并删除向导创建的所有网络控制器 Vm。  

- 清理向导创建的任何 VHD 装入点。  

- 确保 Hyper-v 主机上至少有 50 GB 的可用空间。  

## <a name="after-you-complete-the-wizard"></a>完成向导后

向导完成后，你仍需完成一些重要任务。

第一个任务是在每台服务器上禁用凭据安全支持提供程序 (CredSSP) 协议，这是出于安全方面的考虑。 记住，需要为向导启用 CredSSP。 如果遇到 CredSSP 问题，请参阅[对 CredSSP 进行故障排除](../manage/troubleshoot-credssp.md)以获取更多信息。

1. 在 Windows Admin Center 中，在“所有连接”下选择刚创建的群集。
1. 在“工具”下，选择“服务器”。
1. 在右窗格中，选择群集中的第一台服务器。
1. 在“概览”下，选择“禁用 CredSSP”。 此时会看到顶部的红色“CredSSP 已启用”横幅消失。
1. 对群集中的每台服务器重复步骤 3 和 4。

好了，现在还需要执行以下其他任务：

- 设置群集见证。 请参阅[设置群集见证](witness.md)。
- 创建卷。 请参阅[创建卷](../manage/create-volumes.md)。
- 对于延伸群集，请使用存储副本创建卷并设置复制。 请参阅[为延伸群集创建卷并设置复制](../manage/create-stretched-volumes.md)。

## <a name="next-steps"></a>后续步骤

- 将群集注册到 Azure。 请参阅[管理 Azure 注册](../manage/manage-azure-registration.md)。
- 对群集进行最终验证。 请参阅[验证 Azure Stack HCI 群集](validate.md)
- 预配 VM。 请参阅[使用 Windows Admin Center 管理 Azure Stack HCI 上的 VM](../manage/vm.md)。
- 你还可以使用 PowerShell 部署群集。 请参阅[使用 PowerShell 创建 Azure Stack HCI 群集](create-cluster-powershell.md)。
- 你还可以使用 PowerShell 部署网络控制器。 请参阅 [使用 PowerShell 部署网络控制器](network-controller-powershell.md)。
