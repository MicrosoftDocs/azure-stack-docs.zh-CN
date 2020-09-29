---
title: 使用 Azure Site Recovery 保护 Azure Stack HCI VM
description: 使用 Windows Admin Center 通过 Azure Site Recovery 保护 Azure Stack HCI VM。
ms.topic: how-to
author: davannaw-msft
ms.author: dawhite
ms.date: 04/30/2020
ms.localizationpriority: low
ms.openlocfilehash: d66948092d97ac42ef9484d47e9b584ed6a80a90
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947565"
---
# <a name="protect-azure-stack-hci-vms-using-azure-site-recovery"></a>使用 Azure Site Recovery 保护 Azure Stack HCI VM

>适用于：Windows Server 2019

Windows Admin Center 简化了在服务器或群集上复制虚拟机 (VM) 的过程，使得你能够从自己的数据中心利用 Azure 的功能。 若要自动进行设置，请将 Windows Admin Center 连接到 Azure。

本文介绍了如何使用 Azure Site Recovery 来配置复制设置，以及如何在 Azure 门户中创建恢复计划。 完成这些任务后，Windows Admin Center 可以启动 VM 复制来保护 VM。 Azure Site Recovery 还保护物理服务器和群集节点。

若要了解详细信息，请参阅[将 Windows Server 连接到 Azure 混合服务](/windows-server/manage/windows-admin-center/azure/)。

## <a name="how-azure-site-recovery-works-with-windows-admin-center"></a>Azure Site Recovery 如何与 Windows Admin Center 协同工作
*Azure Site Recovery* 是一项 Azure 服务，用于复制 VM 上运行的工作负荷，以便在发生灾难时保护业务关键基础结构。 若要了解详细信息，请参阅[关于 Site Recovery](/azure/site-recovery/site-recovery-overview)。

Azure Site Recovery 包括以下两个组件：复制和故障转移。 复制部分通过将目标 VM 的 VHD 复制到 Azure 存储帐户，在发生灾难时保护你的 VM。 然后，当发生灾难时，你可以对 VM 进行故障转移并在 Azure 中运行它们。 你还可以执行不影响主要 VM 的测试性故障转移，在 Azure 中测试恢复过程。

要在发生灾难时保护你的 VM，单独完成复制组件的设置就足够了。 但是，在配置该过程的故障转移部分之前，无法在 Azure 中启动 VM。

如果要故障转移到 Azure VM，可以设置故障转移部分；初始设置期间不需要进行此设置。 如果主机服务器发生故障，你可以在那时配置故障转移组件并访问受保护 VM 的工作负荷。 但是，我们建议在发生灾难之前配置与故障转移相关的设置。

## <a name="prerequisites-and-planning"></a>先决条件和规划
若要完成本文中的步骤，需要满足以下先决条件：

- 若要复制到 Azure，承载要保护的 VM 的目标服务器必须具有 Internet 访问权限。
- 必须具有从 Windows Admin Center 到 Azure 的连接。 有关详细信息，请参阅[配置 Azure 集成](/windows-server/manage/windows-admin-center/azure/azure-integration)。
- 查看容量规划工具，以评估成功进行复制和故障转移需要满足的要求。 有关详细信息，请参阅[关于用于将 Hyper-V 灾难恢复到 Azure 的 Azure Site Recovery 部署规划器](/azure/site-recovery/hyper-v-site-walkthrough-capacity)。

## <a name="step-1-set-up-vm-protection-on-your-target-host"></a>步骤 1：在目标主机上设置 VM 保护
在每个主机服务器或群集（其中包含要保护的 VM）上完成以下步骤一次：
1. 在 Windows Admin Center 中的“所有连接”页面上，连接到承载着你要保护的 VM 的服务器或群集。
1. 在“工具”下，选择“虚拟机”，然后选择“清单”选项卡。
1. 选择任何 VM（不需要是你要保护的 VM）。
1. 展开“更多”子菜单，然后选择“设置 VM 保护”。

    :::image type="content" source="media/azure-site-recovery/set-up-vm-protection.png" alt-text="Windows Admin Center 中的“设置 VM 保护”子菜单选项。":::

1. 登录到你的 Azure 帐户。
1. 在“通过 Azure Site Recovery 设置主机”页面上输入所需信息，然后选择“设置”：

   - **订阅：** 要用于此主机上的 VM 复制的 Azure 订阅。
   - **资源组：** 一个新资源组名称。
   - **恢复服务保管库：** 此主机上受保护 VM 的 Azure Site Recovery 保管库的名称。
   - **位置：** 应在其中创建 Azure Site Recovery 资源的 Azure 区域。

    :::image type="content" source="media/azure-site-recovery/set-up-host-with-asr.png" alt-text="Windows Admin Center 中的“通过 Azure Site Recovery 设置主机”页面。":::

1. 等待，直至看到通知：**Site Recovery 设置已完成**。

此过程最长可能需要 10 分钟。 你可以转到“通知”（Windows Admin Center 右上角的钟形图标）来查看进度。

>[!NOTE]
> 此过程会自动在目标服务器或节点上安装 Azure Site Recovery 代理（如果你要配置群集），并使用指定的**存储帐户**和**保管库**在指定的**位置**创建**资源组**。 它还向 Azure Site Recovery 服务注册目标主机并配置默认的复制策略。

## <a name="step-2-select-vms-to-protect"></a>步骤 2：选择要保护的 VM
完成以下步骤来保护你的 VM：
1. 在 Windows Admin Center 中，返回到你在上一个任务中配置的服务器或群集。
1. 在“工具”下，选择“虚拟机”，然后选择“清单”选项卡。
1. 选择要保护的 VM，展开“更多”子菜单，然后选择“保护 VM”。

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting.png" alt-text="Windows Admin Center 中的“保护 VM”设置选项。":::

1. 查看保护 VM 时的容量要求。 有关详细信息，请参阅[为 Hyper-V VM 灾难恢复规划容量](/azure/site-recovery/site-recovery-capacity-planner)。

    如果要使用高级存储帐户，请在 Azure 门户中创建一个。 若要了解详细信息，请参阅 [Azure 有哪些可用的磁盘类型？](/azure/storage/common/storage-premium-storage)中的“高级 SSD”部分 Windows Admin Center 中提供的“新建”选项将创建一个标准存储帐户。

1. 输入要用于此 VM 的复制的**存储帐户**的名称，然后选择“保护 VM”，为 VM 启用复制。

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting-asr.png" alt-text="在 Windows Admin Center 中定义 Azure Site Recovery 的存储帐户来保护 VM。":::

    Azure Site Recovery 启动复制过程。 如果“虚拟机清单”网格的“受保护”列中的值变为“是”，则 VM 受保护。   此过程可能需要几分钟时间。

## <a name="step-3-configure-and-run-a-test-failover-in-the-azure-portal"></a>步骤 3：在 Azure 门户中配置并运行测试性故障转移
在开始 VM 复制之前，不需要完成此步骤。 VM 仅由复制提供保护。 但是，我们建议在设置 Azure Site Recovery 时配置故障转移设置。

请完成以下步骤来准备到 Azure VM 的故障转移：
1. 设置一个 Azure 网络，以便已进行故障转移的虚拟机将其附加到此 VNET。 若要了解详细信息，请参阅[设置从本地 Hyper-V VM 到 Azure 的灾难恢复](/azure/site-recovery/hyper-v-site-walkthrough-prepare-azure)。

    >[!NOTE]
    > Windows Admin Center 会自动完成此资源中的步骤。 你只需要设置 Azure 网络。

1. 运行测试性故障转移。 若要了解详细信息，请参阅[运行到 Azure 的灾难恢复演练](/azure/site-recovery/hyper-v-site-walkthrough-test-failover)。

## <a name="step-4-create-recovery-plans"></a>步骤 4：创建恢复计划
Azure Site Recovery 中的恢复计划使你能够对整个应用程序的 VM 集合进行故障转移和恢复。 可以单独恢复受保护的 VM。 但更好的方法是将应用程序的 VM 添加到恢复计划中。 然后，可以通过恢复计划对整个应用程序进行故障转移。 你还可以使用恢复计划的测试性故障转移功能来测试应用程序的恢复。

使用恢复计划，你可以对 VM 进行分组，按顺序排好它们在故障转移期间的启动顺序，以及自动执行其他恢复步骤。 在保护 VM 后，你可以在 Azure 门户中转到 Azure Site Recovery 保管库，为 VM 创建恢复计划。 若要了解详细信息，请参阅[创建和自定义恢复计划](/azure/site-recovery/site-recovery-create-recovery-plans)。

## <a name="step-5-monitor-replicated-vms-in-azure"></a>步骤 5：监视 Azure 中的已复制 VM
若要确认在服务器注册过程中没有出现故障，请转到 **Azure 门户**，依次选择“所有资源”、“恢复服务保管库”、“作业”和“Site Recovery 作业”。 “恢复服务保管库”名称是在本文第一个任务的步骤 6 中指定的名称。

若要监视 VM 复制，请转到“恢复服务保管库”，然后转到“复制的项”。

若要查看已注册到保管库的所有服务器，请转到“恢复服务保管库”、“Site Recovery 基础结构”，然后转到“Hyper-V 主机”（在“Hyper-V 站点”部分下）。

## <a name="known-issue"></a>已知问题 ##
向群集注册 Azure Site Recovery 时，如果节点无法安装 Azure Site Recovery 或无法注册 Azure Site Recovery 服务，则无法保护你的 VM。 若要在 Azure 门户中验证是否已注册群集中的所有节点，请依次转到“恢复服务保管库”、“作业”和“Site Recovery 作业”。

## <a name="next-steps"></a>后续步骤
有关 Azure Site Recovery 的详细信息，另请参阅：

- [有关 Azure Site Recovery 的一般问题](/azure/site-recovery/site-recovery-faq)
