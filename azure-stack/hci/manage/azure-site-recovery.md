---
title: 使用 Azure Site Recovery 保护 Azure Stack HCI Vm
description: 使用 Windows 管理中心通过 Azure Site Recovery 保护 Azure Stack HCI Vm。
ms.topic: article
author: davannaw-msft
ms.author: dawhite
ms.date: 04/30/2020
ms.localizationpriority: low
ms.openlocfilehash: 01b6f16b3812b5f11f95d9d11f6563a1631fd690
ms.sourcegitcommit: 21cdab346fc242b8848a04a124bc16c382ebc6f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783931"
---
# <a name="protect-azure-stack-hci-vms-using-azure-site-recovery"></a>使用 Azure Site Recovery 保护 Azure Stack HCI Vm

>适用于：Windows Server 2019

Windows 管理中心简化了在服务器或群集上复制虚拟机（Vm）的过程，使你能够从自己的数据中心利用 Azure。 若要自动安装，请将 Windows 管理中心连接到 Azure。

本文介绍如何使用 Azure Site Recovery 来配置复制设置并在 Azure 门户中创建恢复计划。 完成这些任务后，Windows 管理中心可以启动 VM 复制来保护 Vm。 Azure Site Recovery 还保护物理服务器和群集节点。

若要了解详细信息，请参阅[将 Windows Server 连接到 Azure 混合服务](/windows-server/manage/windows-admin-center/azure/)。

## <a name="how-azure-site-recovery-works-with-windows-admin-center"></a>Azure Site Recovery 如何与 Windows 管理中心协同工作
*Azure Site Recovery*是一项 Azure 服务，用于复制 vm 上运行的工作负荷，以便确保关键业务基础结构免受灾难的破坏。 若要了解详细信息，请参阅[关于 Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)。

Azure Site Recovery 由两个组件组成：*复制*和*故障转移*。 复制部分通过将目标 VM 的 VHD 复制到 Azure 存储帐户来防止 Vm 发生灾难。 然后，可以故障转移 Vm，并在发生灾难时在 Azure 中运行它们。 你还可以执行测试故障转移，而不会影响你的主 Vm 来测试 Azure 中的恢复过程。

仅对复制组件完成设置足以防止 Vm 发生灾难。 但是，在配置进程的故障转移部分之前，无法在 Azure 中启动 Vm。

如果要故障转移到 Azure VM，可以设置故障转移部分;初始设置期间不需要此设置。 如果主机服务器发生故障，你可以在此时配置故障转移组件并访问受保护 VM 的工作负荷。 但是，我们建议在发生灾难之前配置与故障转移相关的设置。

## <a name="prerequisites-and-planning"></a>先决条件和规划
若要完成本文中的步骤，需要满足以下要求：

- 承载要保护的 Vm 的目标服务器必须具有 Internet 访问权限，才能将其复制到 Azure。
- 从 Windows 管理中心到 Azure 的连接。 有关详细信息，请参阅[配置 Azure 集成](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration)。
- 查看容量计划工具以评估成功复制和故障转移的要求。 有关详细信息，请参阅[关于 hyper-v 灾难恢复到 Azure 的 Azure Site Recovery 部署规划器](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-capacity)。

## <a name="step-1-set-up-vm-protection-on-your-target-host"></a>步骤 1：在目标主机上设置虚拟机保护
对于每个包含要保护的 Vm 的主机服务器或群集，请完成以下步骤一次：
1. 在 Windows 管理中心的 "**所有连接**" 页上，连接到承载要保护的 vm 的服务器或群集。
1. 在 "**工具**" 下，选择 "**虚拟机**"，然后选择 "**清单**" 选项卡。
1. 选择任何 VM （无需是要保护的 VM）。
1. 展开 "**更多**" 子菜单，然后选择 "**设置 VM 保护**"。

    :::image type="content" source="media/azure-site-recovery/set-up-vm-protection.png" alt-text="Windows 管理中心中的 "设置 VM 保护" 子菜单选项。":::

1. 登录到 Azure 帐户。
1. 在 "设置主机并 Azure Site Recovery" 页上，输入所需的信息，然后选择 "**设置**"：

   - **订阅：** 要用于此主机上的 VM 复制的 Azure 订阅。
   - **资源组：** 新的资源组名称。
   - **恢复服务保管库：** 此主机上受保护 Vm 的 Azure Site Recovery 保管库的名称。  
   - **位置：** 应在其中创建 Azure Site Recovery 资源的 Azure 区域。

    :::image type="content" source="media/azure-site-recovery/set-up-host-with-asr.png" alt-text="Windows 管理中心中的 "通过 Azure Site Recovery 设置主机" 页。":::

1. 请等待，直到看到通知：**站点恢复设置已完成**。
 
此过程可能需要长达10分钟的时间。 可以通过转到 "**通知**" （Windows 管理中心的右上角的钟形图标）观看进度。

>[!NOTE]
> 此过程会自动将 Azure Site Recovery 代理安装到目标服务器或节点（如果配置群集）上，并在指定**位置**创建具有指定**存储帐户**和**保管库**的**资源组**。 它还将目标主机注册到 Azure Site Recovery 服务，并配置默认复制策略。

## <a name="step-2-select-vms-to-protect"></a>步骤2：选择要保护的 Vm
完成以下步骤以保护 Vm：
1. 在 Windows 管理中心，返回到在上一个任务中配置的服务器或群集。
1. 在 "**工具**" 下，选择 "**虚拟机**"，然后选择 "**清单**" 选项卡。
1. 选择要保护的 VM，展开 "**更多**" 子菜单，然后选择 "**保护 vm**"。

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting.png" alt-text="Windows 管理中心中的 "保护 VM" 设置选项。":::

1. 查看用于保护 VM 的容量要求。 有关详细信息，请参阅[规划 HYPER-V VM 灾难恢复的容量](https://docs.microsoft.com/azure/site-recovery/site-recovery-capacity-planner)。

    如果您想要使用高级存储帐户，请在 Azure 门户中创建一个。 若要了解详细信息，请参阅[Azure 中的可用磁盘类型](https://docs.microsoft.com/azure/storage/common/storage-premium-storage)**高级 SSD**部分？ Windows 管理中心提供**的 "新建" 选项创建一个**标准存储帐户。

1. 输入用于此 VM 复制的**存储帐户**的名称，然后选择 "**保护 vm** " 以为 vm 启用复制。

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting-asr.png" alt-text="定义用于 Azure Site Recovery 的存储帐户以保护 Windows 管理中心中的 VM。":::

    Azure Site Recovery 开始复制过程。 当**虚拟机库存**网格的 "**受保护**" 列中的值更改为 **"是"** 时，VM 受保护。 此进程需要花费几分钟时间。  

## <a name="step-3-configure-and-run-a-test-failover-in-the-azure-portal"></a>步骤 3：在 Azure 门户配置和运行测试故障转移
在开始 VM 复制之前，不需要完成此步骤。 仅通过复制来保护 VM。 但是，我们建议在设置 Azure Site Recovery 时配置故障转移设置。
 
完成以下步骤以准备故障转移到 Azure VM：
1. 设置已故障转移的 VM 将附加到此 VNET 的 Azure 网络。 若要了解详细信息，请参阅[设置本地 Hyper-v vm 到 Azure 的灾难恢复](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-prepare-azure)。

    >[!NOTE]
    > Windows 管理中心会自动完成此资源中的步骤。 只需设置 Azure 网络。

1. 运行测试故障转移。 若要了解详细信息，请参阅[运行灾难恢复演练到 Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-test-failover)。

## <a name="step-4-create-recovery-plans"></a>步骤4：创建恢复计划
Azure Site Recovery 中的恢复计划使你可以故障转移和恢复整个应用程序的 Vm 集合。 可以单独恢复受保护的 Vm。 但更好的方法是将应用程序的 Vm 添加到恢复计划。 然后，可以通过恢复计划对整个应用程序进行故障转移。 你还可以使用恢复计划的测试故障转移功能来测试应用程序的恢复。

恢复计划使你可以对 Vm 进行分组，按顺序对 Vm 进行故障转移，并自动执行其他恢复步骤。 保护 Vm 后，可以在 Azure 门户中转到 Azure Site Recovery 保管库，为其创建恢复计划。 若要了解详细信息，请参阅[创建和自定义恢复计划](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。

## <a name="step-5-monitor-replicated-vms-in-azure"></a>步骤5：监视 Azure 中的已复制 Vm
若要在服务器注册过程中验证是否失败，请参阅 " **Azure 门户**"，选择 "**所有资源**"，选择 "**恢复服务保管库**"，选择 "**作业**"，然后选择 " **Site Recovery 作业**"。 **恢复服务保管库**名称是在本文第一项任务步骤6中指定的名称。

若要监视 VM 复制，请前往**恢复服务保管库**，然后**复制项目**。

若要查看已注册到保管库的所有服务器，请访问**恢复服务保管库**， **Site Recovery 基础结构**，然后访问**Hyper-v 主机**（在 "hyper-v 站点" 部分下）。

## <a name="known-issue"></a>已知问题 ##
向群集注册 Azure Site Recovery 时，如果节点无法安装 Azure Site Recovery 或注册 Azure Site Recovery 服务，则可能不会保护你的 Vm。 若要验证是否已在 Azure 门户中注册群集中的所有节点，请参阅**恢复服务保管库**、**作业**，然后**Site Recovery 作业**。

## <a name="next-steps"></a>后续步骤
有关 Azure Site Recovery 的详细信息，另请参阅：

- [有关 Azure Site Recovery 的一般问题](https://docs.microsoft.com/azure/site-recovery/site-recovery-faq)
