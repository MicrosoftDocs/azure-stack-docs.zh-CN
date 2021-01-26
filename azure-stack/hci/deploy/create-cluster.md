---
title: 使用 Windows Admin Center 创建 Azure Stack HCI 群集
description: 了解如何使用 Windows Admin Center 为 Azure Stack HCI 创建服务器群集
author: v-dasis
ms.topic: how-to
ms.date: 01/22/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7b745ec5f521be3ed520b41a4eb5d788799ad20b
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772303"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>使用 Windows Admin Center 创建 Azure Stack HCI 群集

> 适用于 Azure Stack HCI 版本 v20H2 版

本文介绍如何使用 Windows 管理中心创建使用存储空间直通的 Azure Stack HCI 群集。 Windows 管理中心中的 "创建群集" 向导将为你执行大部分繁重的操作。 如果你想要使用 PowerShell 自行完成此操作，请参阅[使用 PowerShell 创建 Azure Stack HCI 群集](create-cluster-powershell.md)。 此 PowerShell 文章还是一个很好的信息来源，它深入介绍了向导的情况，适合用来进行故障排除。

在创建群集时，可以在两种群集类型之间进行选择：

- 包含至少两个服务器节点的标准群集，这些节点位于单个站点中。
- 包含至少四个服务器节点（跨两个站点）的延伸群集，每个站点至少有两个节点。

有关延伸群集的详细信息，请参阅[延伸群集概述](../concepts/stretched-clusters.md)。

如果你对 Azure Stack HCI 的测试感兴趣，但有限制或没有备用硬件，请查看 [AZURE STACK HCI 评估指南](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md)，其中介绍了如何使用嵌套虚拟化（在 Azure 中）或本地单一物理系统上的 Azure Stack HCI。

## <a name="before-you-run-the-wizard"></a>运行向导之前

在运行“创建群集”向导之前，请确保你已经完成以下任务：

- 请阅读 [系统要求](../concepts/system-requirements.md)中的硬件和相关要求。
- 请阅读 [物理网络要求](../concepts/physical-network-requirements.md) 并 Azure Stack HCI 的 [主机网络要求](../concepts/host-network-requirements.md) 。
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

在启动向导之前，请确保已安装最新的 Windows 管理中心扩展，特别是群集创建扩展。 为此，请执行以下操作：

1. 打开 Windows 管理中心，并单击右上角的 "设置" (齿轮图标) 。
1. 在“设置”  下选择“扩展”  。
1. 选择 " **群集创建** "，然后单击 " **安装**"。
1. 选择 " **群集管理器** "，然后单击 " **安装** "。

现在你已准备就绪，接下来让我们开始：

1. 在 Windows Admin Center 中，在“所有连接”下单击“添加” 。
1. 在 " **添加或创建资源** " 面板的 " **服务器群集**" 下，选择 " **新建**"。
1. 小于 **1。选择 "群集类型**"，选择 " **Azure Stack HCI**"。

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="创建群集向导- HCI 选项" lightbox="media/cluster/create-cluster-type.png":::

1. 在“选择服务器位置”下，选择以下选项之一：

    - **一个站点中的所有服务器**
    - **两个站点中的服务器**（适用于延伸群集）

1. 完成后，单击“创建”。 现在，你将看到“创建群集”向导，如下所示。

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="“创建群集”向导 - 开始" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>步骤 1：入门

向导的步骤 1 会指导你完成的操作包括：确保满足所有先决条件，添加服务器节点，安装所需的功能，然后在需要时重启每台服务器。

1. 检查 **1.1 检查** 向导中列出的先决条件，以确保每个服务器节点都已准备好群集。 完成后，单击 **“下一步”** 。
1. 在 **1.2 添加服务器** 上，输入你的帐户用户名和密码，然后单击 " **下一步**"。 该帐户必须是每台服务器上本地管理员组的成员。
1. 输入要添加的第一台服务器的名称，然后单击“添加”。
1. 对于将成为群集一部分的每台服务器，重复步骤 3。 完成后，单击 **“下一步”** 。
1. 如果需要，在 **1.3 上加入域** 时，请指定要将服务器加入到的域以及要使用的帐户。 如果需要，可以根据需要重命名服务器。 然后单击“下一步”  。
1. 在 **1.4 安装功能** 中，根据需要查看并添加功能。 完成后，单击 **“下一步”** 。

    向导将为你安装以下必需的功能：

    - BitLocker
    - 数据中心桥接（适用于 RoCEv2 网络适配器）
    - 故障转移群集
    - 文件服务器
    - FS-Data-Deduplication 模块
    - Hyper-V
    - RSAT-AD-PowerShell 模块
    - 已为延伸群集安装存储副本 () 

1. 在 **1.5 安装更新** 上，单击 " **安装更新** " 以安装任何操作系统更新。 完成后，单击“下一步”。
1. 在 **1.6 上安装硬件更新**，单击 "根据需要 **获取更新** " 以获取可用的供应商硬件更新。
1. 按照供应商特定的步骤在硬件上安装更新。 这些步骤包括对硬件执行对称和合规性检查，以确保更新成功。 可能需要重新运行某些步骤。
1. 在 **1.7 重新启动服务器** 上，单击 "需要时 **重新启动服务器** "。 验证每台服务器是否已成功启动。

## <a name="step-2-networking"></a>步骤 2：网络

向导的步骤2逐步指导你为群集配置虚拟交换机、网络适配器和其他网络元素。 RDMA (支持 iWARP 和 RoCE ) 网络适配器。

有关 RDMA 和 Hyper-v 主机联网 Azure Stack HCI 的详细信息，请参阅 [主机网络要求](../concepts/host-network-requirements.md)。

> [!NOTE]
> 如果在任何网络或虚拟交换机步骤中看到列出了错误，请选择 " **应用并再次测试** "。

1. 选择“下一页:网络”。
1. 在 **2.1 检查网络适配器** 时，等待每个适配器旁边出现绿色复选框，然后选择 " **下一步**"。

1. 在 **2.2 中，选择 "管理适配器**"，选择要用于每个服务器的一个或两个管理适配器。 出于管理目的，必须至少选择一个适配器，因为向导至少需要一个专用物理 NIC 进行群集管理。  指定进行管理所需的适配器后，系统就会将其从向导工作流的其余部分中排除。

    :::image type="content" source="media/cluster/create-cluster-management-adapters.png" alt-text="创建群集向导-选择管理适配器" lightbox="media/cluster/create-cluster-management-adapters.png":::

    管理适配器有两个配置选项：

    - 一个用于管理的物理网络适配器。 对于此选项，DHCP 或静态 IP 地址分配受支持。

    - 两个已组成一组以用于管理的物理网络适配器。 组合使用一对适配器时，仅支持静态 IP 地址分配。 如果所选适配器使用 DHCP 寻址 (两个) 中的一个或两个都为，则在创建虚拟交换机之前，DHCP IP 地址将转换为静态 IP 地址。

    可以通过使用组合的适配器与多个交换机建立单一连接，但只使用单个 IP 地址。 负载均衡会变得可用，并且容错功能是即时启用的，不需等待 DNS 记录更新。

    现在为每台服务器执行以下操作：

    - 选中“说明”复选框。 请注意，所有适配器都处于选中状态，向导可能会为你提供建议。
    - 清除不希望用于群集管理的适配器的复选框。

    > [!NOTE]
    > 可以使用 1 Gb 适配器作为管理适配器，但建议使用 10 Gb 或更快的适配器来传输存储和工作负荷 (VM) 流量。

1. 进行更改后，单击“应用并测试”。
1. 在“定义网络”下，确保每台服务器的每个网络适配器都有唯一的静态 IP 地址、子网掩码和 VLAN ID。 将鼠标指针悬停在每个表元素上，根据需要输入或更改值。 完成后，单击“应用并测试”。

    > [!NOTE]
    > 若要支持群集的 VLAN ID 配置，所有服务器上的所有网络适配器都必须支持 VLANID 属性。

1. 等到每台服务器的“状态”列都显示为“已通过”后，单击“下一步”。   此步骤验证具有相同子网和 VLAN ID 的所有适配器之间的网络连接性。 在下一步中创建虚拟交换机后，提供的 IP 地址将从物理适配器传输到虚拟适配器。 这可能需要几分钟才能完成，具体取决于配置的适配器数量。

1. 在 " **2.3 虚拟交换机**" 下，选择下列选项之一（如果适用）。 根据存在的网络适配器数量，并非所有选项都可用：

    - **跳过虚拟交换机创建** -选择是否要在以后设置虚拟交换机。
    - **同时为计算和存储创建一个虚拟交换机** -如果要为 vm 和存储空间直通使用同一虚拟交换机，请选择此选项。 这是 "聚合" 选项。
    - **仅创建一个虚拟交换机用于计算** -选择是否只为 vm 使用虚拟交换机。
    - **创建两个虚拟交换机** -选择是否需要专用于 vm 和存储空间直通的专用虚拟交换机。

        :::image type="content" source="media/cluster/create-cluster-virtual-switches.png" alt-text="创建群集向导-虚拟交换机" lightbox="media/cluster/create-cluster-virtual-switches.png":::

    下表显示了针对各种网络适配器配置支持和启用的虚拟交换机配置：

    | 选项 | 1-2 个适配器 | 3 个以上的适配器 | 组合适配器 |
    | :------------- | :--------- |:--------| :---------|
    | 单个交换机（计算 + 存储） | enabled | enabled  | 不支持 |
    | 单个交换机（仅计算） | 不支持| enabled | enabled |
    | 两个交换机 | 不支持 | enabled | enabled |

1. 根据需要更改交换机和其他配置设置的名称，然后单击“应用并测试”。 创建虚拟交换机后，每台服务器的“状态”列都应显示为“已通过”。

1. 步骤 **2.4 RDMA** 是可选的。 如果使用的是 RDMA，请选择 " **配置 RDMA (建议)** " 复选框，然后单击 " **下一步**"。

    :::image type="content" source="media/cluster/create-cluster-rdma.png" alt-text="创建群集向导-配置 RDMA" lightbox="media/cluster/create-cluster-rdma.png":::

    有关分配带宽预留的信息，请参阅 "[主机网络要求](../concepts/host-network-requirements.md)" 中的 "[流量带宽分配](../concepts/host-network-requirements.md#traffic-bandwidth-allocation)" 部分。

1. 选择 " **高级**"，然后选择 " **数据中心桥接 (DCB)** " 复选框。

1. 在 " **群集检测信号**" 下，分配优先级和带宽预留百分比。

1. 在 " **存储**" 下，分配优先级和带宽预留百分比。

1. 完成后，选择 " **应用更改** "，然后单击 " **下一步**"。

1. 在 **2.5 上定义网络** 时，请查看并编辑列出的每个适配器的名称、IP 地址、子网掩码、VLAN ID 和默认网关字段。

    :::image type="content" source="media/cluster/create-cluster-define-networks.png" alt-text="创建群集向导-定义网络" lightbox="media/cluster/create-cluster-define-networks.png":::

1. 完成后，单击“应用并测试”。 如果适配器的状态不正常，则可能需要 **重试连接测试** 。

## <a name="step-3-clustering"></a>步骤 3：群集功能

向导的步骤 3 可确保目前的所有操作都已正确设置，并且在进行延伸群集部署的情况下会自动设置两个站点，然后实际创建群集。 也可在 Active Directory 中预先设置站点。

1. 在完成时选择“下一步:群集功能”。
1. 在 **3.1 上验证群集**，选择 " **验证**"。 验证可能需要几分钟。

    如果出现“凭据安全服务提供程序(CredSSP)”弹出窗口，请选择“是”以暂时启用 CredSSP，以便向导继续。  创建群集并完成向导后，需禁用 CredSSP 以提高安全性。 如果遇到 CredSSP 问题，请参阅[对 CredSSP 进行故障排除](../manage/troubleshoot-credssp.md)以获取更多信息。

1. 检查所有验证状态，下载报告以获取有关任何故障的详细信息，进行更改，然后单击“再次验证”（如果需要）。 也可以 **下载报表** 。 根据需要重复此步骤，直到所有验证检查都通过。 如果一切正常，请单击 " **下一步**"。
1. 在 **3.2 创建群集** 上，输入群集的名称。

    :::image type="content" source="media/cluster/create-cluster.png" alt-text="创建群集向导-创建群集" lightbox="media/cluster/create-cluster.png":::

1. 在 " **IP 地址**" 下，选择要使用的静态或动态 IP 地址。 IP 地址必须采用以下格式输入： *ip 地址/当前子网长度*。 例如： 10.0.0.200/24。
1. 选择“高级”。 此处有几个选项：

    - **向 DNS 注册群集并 Active Directory**
    - **将符合条件的存储添加到群集 (建议)**

1. 在 " **网络**" 下，选择是 **使用 (推荐的所有网络)** 还是 **指定一个或多个不使用的网络**。

1. 完成后，单击“创建群集”。

1. 对于延伸群集，在 **3.3 "将服务器分配到站点**" 上，命名将使用的两个站点。

1. 接下来，将每台服务器分配给一个站点。 稍后需设置站点之间的复制。 完成后，单击 " **应用更改**"。

## <a name="step-4-storage"></a>步骤 4：存储

向导的步骤 4 将引导你为群集设置存储空间直通。

1. 在完成时选择“下一步:存储”。
1. 在 **4.1 清洗驱动器** 上，如果你的部署有意义，则可以选择 **删除驱动器** 。
1. 在 **4.2 检查驱动器** 上，单击 **>** 每个服务器旁边的图标以验证磁盘是否正在运行且已连接。 如果一切正常，请单击 " **下一步**"。
1. 在 **4.3** 上，单击 " **下一步**"。
1. 下载并查看验证报告。 如果一切正常，请单击“下一步”。 否则，请 **再次运行验证**。
1. 在 **4.4 上启用存储空间直通**，单击 " **启用**"。
1. 下载并查看报表。 如果一切正常，请单击“完成”。 
1. 选择 " **前往连接列表**"。
1. 几分钟后，你应该会在列表中看到你的群集。 选择它以查看 "群集概述" 页。

在域中复制群集名称可能需要一些时间，尤其是在新添加了工作组服务器 Active Directory 时。 虽然群集可能显示在 Windows Admin Center 中，但可能尚不能连接到它。

如果在一段时间后解析群集未成功，在大多数情况下，你可以替换服务器名称而不是群集名称。

## <a name="next-steps"></a>后续步骤

- 将群集注册到 Azure。 请参阅[管理 Azure 注册](../manage/manage-azure-registration.md)。
- 设置见证服务器。 请参阅[设置群集见证](../manage/witness.md)。
- 对群集进行最终验证。 请参阅[验证 Azure Stack HCI 群集](validate.md)