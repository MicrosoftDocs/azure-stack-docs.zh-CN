---
title: Azure Stack HCI 解决方案概述
description: Azure Stack HCI 是一种超融合基础设施 (HCI) 群集解决方案，它在混合的本地环境中托管虚拟化的 Windows 和 Linux 工作负载以及其存储。 Azure 混合服务通过基于云的监视、站点恢复和 VM 备份等功能，以及 Azure 门户中所有 Azure Stack HCI 部署的中心视图增强群集。
ms.topic: overview
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/11/2020
ms.openlocfilehash: 9e8c66207ad4601aa7a5ee84b4ee3eb8d7e32654
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343276"
---
# <a name="azure-stack-hci-solution-overview"></a>Azure Stack HCI 解决方案概述

Azure Stack HCI 是一种超聚合基础结构， (HCI) 群集解决方案，该解决方案在混合的本地环境中托管虚拟化 Windows 和 Linux 工作负荷及其存储。 Azure 混合服务通过基于云的监视、站点恢复和 VM 备份等功能，以及 Azure 门户中所有 Azure Stack HCI 部署的中心视图增强群集。 你可以使用现有工具（包括 Windows Admin Center、System Center 和 PowerShell）管理群集。

Azure Stack HCI，版本20H2 现 [可供下载](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)。 它适用于运行虚拟化工作负载且内置混合云连接的本地群集。 因此，Azure Stack HCI 作为 Azure 服务交付，并根据 Azure 订阅计费。 Azure Stack HCI 现在还具备托管 Azure Kubernetes 服务的功能；有关详细信息，请参阅 [Azure Stack HCI 上的 Azure Kubernetes 服务](../aks-hci/overview.md)。

请花几分钟时间观看 Azure Stack HCI 的高级功能的视频：

> [!VIDEO https://www.youtube.com/embed/fw8RVqo9dcs]

从其核心来看，Azure Stack HCI 是一个结合了以下各项的解决方案：

- 来自 OEM 合作伙伴的已验证硬件
- Azure Stack HCI 操作系统
- Windows Admin Center
- Azure 服务

:::image type="content" source="media/overview/azure-stack-hci-solution.png" alt-text="Azure Stack HCI OS 在经过验证的硬件上运行，由 Windows Admin Center 管理，并连接到 Azure" border="false":::

Azure Stack HCI 版本 20H2 提供了 Windows Server 中不存在的新功能，例如可以使用 Windows Admin Center 创建超融合群集，该群集使用存储空间直通来实现出色的存储性价比。 这包括跨站点扩展群集以实现跨站点自动故障转移的选项。 有关详细信息，请参阅 [Azure Stack HCI 中的新增功能](#whats-new-in-azure-stack-hci)。

## <a name="use-cases-for-azure-stack-hci"></a>Azure Stack HCI 的用例

Azure Stack HCI 有很多用例，尽管它不适用于非虚拟化的工作负载。 客户通常会在以下情况下选择 Azure Stack HCI：

### <a name="data-center-consolidation-and-modernization"></a>数据中心合并和现代化

使用 Azure Stack HCI 更新和整合过时的虚拟化主机可以提高可伸缩性，并使环境更易于管理和保护。 这也是一个停用旧的 SAN 存储以减少占用情况和总拥有成本的机会。 操作和系统管理通过统一的工具和接口以及单一支持点进行了简化。

### <a name="remote-and-branch-offices"></a>远程办公室和分支机构

Azure Stack HCI 是一种将远程办公室和分支机构、零售商店以及现场站点实现现代化的经济实惠的好方法，双服务器群集解决方案在每个位置的起价不超过 2 万美元。 创新的嵌套复原能力允许卷在同时发生多个硬件故障时仍保持联机且可访问。 通过云见证技术可以使用 Azure 作为群集仲裁的轻量级辅助措施，在不产生第三台主机成本的情况下就可以避免出现裂脑的情况。 客户还可以在 Azure 门户中集中查看远程 Azure Stack HCI 部署。

### <a name="virtual-desktops"></a>虚拟机

许多组织希望在本地托管虚拟桌面，以实现低延迟和数据主权。 Azure Stack HCI 可以提供类似本地的性能。

### <a name="high-performance-virtualized-workloads"></a>高性能虚拟化工作负载

Azure Stack HCI 可以为 SQL Server 数据库和其他对性能敏感的虚拟化工作负载提供业界卓越的性能，这些负载每秒需要数百万的存储 IOPS 或数据库事务，通过 NVMe SSD、可靠的 RDMA 堆栈和永久性内存提供稳定的低延迟。

## <a name="azure-integration-benefits"></a>Azure 集成权益

Azure Stack HCI 针对混合基础结构进行了独特的定位，允许你利用云和本地资源协同工作，并以本机方式监视、保护和备份到云。

在向 Azure 注册 Azure Stack HCI 群集后，可以首先将 Azure 门户用于：

- **监视：** 在单个全局视图中查看所有 Azure Stack HCI 群集，你可以在其中按资源组对它们进行分组并对其进行标记。
- **计费：** 通过 Azure 订阅为 Azure Stack HCI 付费。

我们正在努力创建更多的功能，请继续关注以获得更多功能。

还可以订阅其他 Azure 混合服务：

- Azure Site Recovery 可以实现高可用性和灾难恢复即服务 (DRaaS)。
- Azure Monitor 是一个集中式中心，通过 AI 驱动的高级分析来跟踪应用、网络和基础结构中发生的所有事件。
- 云见证使用 Azure 作为群集仲裁的轻量决胜策略。
- Azure 备份可以保护场外数据和防范勒索软件。
- Azure 更新管理可以评估更新，以及更新 Azure 和本地环境中运行的 Windows VM 部署。
- Azure 网络适配器可通过点到站点 VPN 将本地资源连接到 Azure 中的 VM。
- **Azure 文件同步** 将文件服务器与云同步。

有关详细信息，请参阅[将 Windows Server 连接到 Azure 混合服务](/windows-server/manage/windows-admin-center/azure/index)。

## <a name="why-azure-stack-hci"></a>为什么选择 Azure Stack HCI？

Azure Stack HCI 是世界一流的集成虚拟化堆栈，基于已经大规模部署的成熟技术，包括 Hyper-V、存储空间直通和 Azure 启发的软件定义网络 (SDN)。 客户选择 Azure Stack HCI 的原因有很多，其中包括：

- Hyper-V 和服务器管理员非常熟悉，能够利用现有的虚拟化和存储概念和技能。
- 它适用于现有的数据中心进程和工具，例如 Microsoft System Center、Active Directory、组策略和 PowerShell 脚本。
- 它适用于热门的第三方备份、安全和监视工具。
- 硬件选择灵活，客户可以选择在其地理位置上具有最佳服务和支持的供应商。
- Microsoft 与硬件供应商之间的联合支持可改善客户体验。
- 无缝的完整堆栈更新使它很容易保持最新状态。
- 生态系统灵活且广泛，IT 专业人员可以灵活地构建最能满足其需求的解决方案。

## <a name="what-you-need-for-azure-stack-hci"></a>开始使用 Azure Stack HCI 时所需的条件

若要开始使用，你需要具备以下条件：

- 包含 [Azure Stack HCI 目录](https://hcicatalog.azurewebsites.net)中的两个或更多服务器的群集，从首选的 Microsoft 硬件合作伙伴处购买
- [Azure 订阅](https://azure.microsoft.com/)
- 为群集中的每个服务器建立 internet 连接，该连接可通过 HTTPS 出站流量连接到众所周知的 Azure 终结点，至少每30天
- 对于跨站点延伸的群集，你需要在站点之间至少有 1 Gb 的连接（首选 25 Gb RDMA 连接），如果你想要在两个站点中同时进行写入的同步复制，则平均延迟为 5 毫秒往返
- 如果打算使用软件定义的网络 (SDN) ，则需要 Azure Stack HCI 操作系统的虚拟硬盘 (VHD) 才能创建网络控制器 Vm (参阅 [计划部署网络控制器](concepts/network-controller.md)) 

有关详细信息，请参阅[系统需求](concepts/system-requirements.md)。 关于 Azure Stack HCI 上的 Azure Kubernetes 服务的要求，请参阅 [Azure Stack HCI 上的 AKS 要求](../aks-hci/overview.md#what-you-need-to-get-started)。

## <a name="hardware-partners"></a>硬件合作伙伴

你可以从你首选的 Microsoft 合作伙伴购买经验证的 Azure Stack HCI 解决方案，使其在没有漫长的设计和构建时间的情况下正常运行。 Microsoft 合作伙伴还提供实现和支持服务的单一联系点。 你可以购买经过验证的节点或集成系统，其中包括预安装的 Azure Stack HCI 操作系统以及用于驱动程序和固件更新的合作伙伴扩展。

请访问[Azure Stack HCI 解决方案](https://azure.microsoft.com/overview/azure-stack/hci)页或浏览 [Azure Stack HCI 目录](https://hcicatalog.azurewebsites.net)，查看 Microsoft 合作伙伴（如 ASUS、Axellio、Blue Chip、DataON、Dell EMC、Fujitsu、HPE、Hitachi、Huawei、Lenovo、NEC、primeLine Solutions、QCT、SecureGUARD 和 Supermicro）当前提供的 70 多种 Azure Stack HCI 解决方案。

## <a name="software-partners"></a>软件合作伙伴

有多种 Microsoft 合作伙伴处理 Azure Stack HCI 功能的软件，同时允许 IT 管理员使用熟悉的工具。 若要了解详细信息，请参阅 [适用于 AZURE STACK HCI 的实用工具应用程序](concepts/utility-applications.md)。

## <a name="licensing-billing-and-pricing"></a>许可、计费和定价

Azure Stack HCI 计费基于每个物理处理器核心（而不是永久许可证）的每月订阅费用。 当客户连接到 Azure 时，所使用的核心数量会自动上传并进行评估，以进行计费。 成本不会因物理处理器核心之外的消耗而变化，这意味着更多的 Vm 成本更低，并且能够运行密度越大虚拟环境的客户会获得奖励。

客户可以从预安装了 Azure Stack HCI 操作系统的硬件合作伙伴处购买经过验证的服务器，或者从 OEM 购买经过验证的裸机服务器，然后订阅 Azure Stack HCI 服务并从 [Azure 门户](https://azure.microsoft.com/products/azure-stack/hci/)下载 Azure Stack HCI 操作系统。

## <a name="management-tools"></a>管理工具

使用 Azure Stack HCI 可在群集上拥有完全管理员权限，并且可以直接管理其所有技术：

- [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [软件定义的网络](/windows-server/networking/sdn/)
- [故障转移群集](/windows-server/failover-clustering/failover-clustering-overview)

若要管理这些技术，可以使用以下管理工具：

- [Windows 管理中心](/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](/powershell/)
- 其他管理工具（例如[服务器管理器](/windows-server/administration/server-manager/server-manager)）和 MMC 管理单元

## <a name="the-azure-stack-family"></a>Azure Stack 系列

Azure Stack HCI 属于 Azure 和 Azure Stack 系列，与 Azure Stack Hub 使用相同的软件定义计算、存储及网络软件。 下面提供了不同解决方案的快速摘要。 有关详细信息，请参阅[比较 Azure Stack 生态系统](../operator/compare-azure-azure-stack.md)。

- [Azure](https://azure.microsoft.com) - 将公有云服务用于按需自助式计算资源，以迁移和现代化现有应用及生成新的云原生应用。
- [Azure Stack 边缘](/azure/databox-online/data-box-edge-overview) -加速机器学习工作负荷，在云托管的设备上运行容器化应用或本地虚拟化工作负载。
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) - 在本地运行虚拟化应用、更换和整合过时的服务器基础结构，以及连接到 Azure 以使用云服务。
- [Azure Stack Hub](../operator/azure-stack-overview.md) - 在连接中断时运行本地云应用，或者使用一致的 Azure 服务满足法规要求。

:::image type="content" source="media/overview/azure-family-updated.png" alt-text="Azure Stack 系列解决方案关系图" border="false":::

## <a name="compare-windows-server-and-azure-stack-hci"></a>比较 Windows Server 和 Azure Stack HCI

许多客户都想知道是 Windows Server 还是 Azure Stack HCI 更适合其需求。 下表可帮助你确定哪一个最适合你的组织。 Windows Server 和 Azure Stack HCI 都提供了新版本路线图的相同高质量用户体验。

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| 最佳来宾和传统服务器 | 软件定义数据中心的最佳虚拟化主机，包括存储空间直通 |
| 使用传统的软件许可模式在任何位置运行 | 在首选供应商的硬件上运行，但作为 Azure 服务交付并向 Azure 帐户收费 |
| 两个安装选项：带桌面体验的服务器或服务器核心 | 基于轻度自定义的服务器核心 |

### <a name="when-to-use-windows-server"></a>何时使用 Windows Server

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server 是一种通用性高的多用途操作系统，具有几十个角色和数百个功能，包括来宾权限。 | Azure Stack HCI 不包括来宾权限，旨在用于超融合的新式体系结构。 |
| 使用 Windows Server 可运行 VM 或进行涉及所有传统服务器角色的裸机安装，包括 Active Directory、文件服务、DNS、DHCP、Internet Information Services (IIS)、容器主机/来宾、SQL Server、Exchange Server、主机保护者服务 (HGS) 等。 | Azure Stack HCI 旨在作为 Hyper-V 虚拟化主机，仅许可用于运行少量服务器角色；任何其他角色都必须在 VM 内运行。 |

### <a name="when-to-use-azure-stack-hci"></a>何时使用 Azure Stack HCI

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server 可以在本地或云中运行，但它本身并不是完整的超融合产品/服务。| 使用 Azure Stack HCI 在本地运行 VM，可以选择跨两个站点进行延伸并连接到 Azure 混合服务。 通过这种方式可以轻松地现代化和保护数据中心与分支机构，实现业界卓越 SQL Server 数据库性能，并在本地运行虚拟桌面以实现低延迟和数据主权|
| Windows Server 就像一把优质的多用途“瑞士军刀”，适用于所有 Windows Server 角色，无论是否虚拟化。 | 使用 Azure Stack HCI 虚拟化 Exchange、SharePoint 和 SQL Server 等经典企业应用，并虚拟化 Windows Server 角色（例如文件服务器、DNS、DHCP、IIS 和 AD）。 包括不受限制地访问所有 Hyper-V 功能，例如受防护的 VM。|
| 许多 Windows Server 部署在过时硬件上运行。 | 借助 Azure Stack HCI 可以使用软件定义的基础结构来代替过时的存储设备阵列或网络设备，而无需进行重大的重新改造。 可以直接访问和管理内置的 Hyper-V、存储空间直通和软件定义的网络 (SDN)。 在 Windows 或 Linux VM 中运行应用。|

## <a name="compare-azure-stack-hub-and-azure-stack-hci"></a>Azure Stack Hub 和 Azure Stack HCI 的比较

当你的组织进行数字化变革时，你可能会发现，使用公有云服务以新式体系结构作为构建基础来更新旧式应用可以加快变革的速度。 但是，出于技术和法规障碍等原因，许多工作负荷必须保留在本地。 使用此表可帮助确定哪种 Microsoft 混合云策略可随时随地提供所需的服务，并为任何位置的工作负荷提供云创新。

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| 新的技能，创新的流程 | 相同的技能，熟悉的流程 |
| 数据中心内的 Azure 服务 | 将数据中心连接到 Azure 服务 |

### <a name="when-to-use-azure-stack-hub"></a>何时使用 Azure Stack Hub

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| 针对自助式基础结构即服务 (IaaS) 使用 Azure Stack Hub，因为其具有强式隔离，并且可对位于相同位置的多个租户精确地跟踪使用情况和进行费用分摊。 非常适合用于服务提供商和企业私有云。 来自 Azure 市场的模板。 | Azure Stack HCI 原生不会实施或提供多租户。 |
| 在本地使用 Azure Stack Hub 开发和运行依赖于 Web 应用、Functions 或事件中心等平台即服务 (PaaS) 的应用。 在 Azure Stack Hub 上运行的这些服务完全与在 Azure 中运行一样，从而提供一致的混合开发和运行时环境。 | Azure Stack HCI 不会在本地运行 PaaS 服务。 |
| 使用 Azure Stack Hub 现代化应用部署和配合 DevOps 做法的操作，这些做法包括基础结构即代码、持续集成和持续部署 (CI/CD)，以及 Azure 一致性 VM 扩展等便利功能。 非常适合用于开发和 DevOps 团队。 | Azure Stack HCI 原生不包括任何 DevOps 工具。 |

### <a name="when-to-use-azure-stack-hci"></a>何时使用 Azure Stack HCI

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack Hub 至少需要 4 个节点，并需要自身的网络交换机。 | 使用 Azure Stack HCI 可以尽量减少远程办公室和分公司的占用空间。 一开始可以仅使用 2 个服务器节点和无交换机的后端到后端网络，以实现最高简便度和负担能力。 硬件产品的入门配置为 4 个驱动器和 64 GB 内存，费用不超过 10000 美元/节点。 |
| 为了与 Azure 保持一致，Azure Stack Hub 将限制 Hyper V 可配置性和功能。 | 使用 Azure Stack HCI 可为经典企业应用（例如 Exchange、SharePoint 和 SQL Server）实现单纯有用的 Hyper-V 虚拟化，以及虚拟化 Windows Server 角色，例如文件服务器、DNS、DHCP、IIS 和 AD。 不受限制地访问所有 Hyper-V 功能，例如受防护的 VM。|
| Azure Stack Hub 不会公开这些基础结构技术。 | 借助 Azure Stack HCI 可以使用软件定义的基础结构来代替过时的存储设备阵列或网络设备，而无需进行重大的重新改造。 可以直接访问和管理内置的 Hyper-V、存储空间直通和软件定义的网络 (SDN)。 |

## <a name="whats-new-in-azure-stack-hci"></a>Azure Stack HCI 的新增功能

Windows Admin Center 版本 2009 为 Azure Stack HCI 添加了许多功能，其中包括：

- Azure Kubernetes 服务托管功能：现在可以安装 [Azure Stack HCI 上的 Azure Kubernetes 服务](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)预览版。
- 群集创建向导中包含软件定义的网络：群集创建向导现在提供了用于在[群集创建](deploy/create-cluster.md#step-5-sdn-optional)过程中部署[软件定义的网络 (SDN)](concepts/software-defined-networking.md) 网络控制器功能的选项。
- **群集创建向导中 (rdma) 增强的远程直接内存访问**：群集创建向导现在可以为 IWARP 和 RoCE 网络适配器配置 RDMA，包括数据中心桥接 (DCB) 。

有关新功能的详细信息，请参阅 [在 Windows 管理中心中公布群集创建扩展的公开上市](https://techcommunity.microsoft.com/t5/windows-admin-center-blog/announcing-general-availability-of-the-cluster-creation/ba-p/1978332)。

与基于 Windows Server 2019 的解决方案相比，运行 Azure Stack HCI 版本 20H2 的群集具有以下新功能：

- **Windows Admin Center 中的新功能**：由于能够通过直观的 UI 创建和更新超融合群集，Azure Stack HCI 比以往任何时候都更容易使用。
- **自动故障转移的延伸群集**：具有存储副本复制和自动 VM 故障转移功能的多站点群集提供本机灾难恢复和业务连续性。
- **相关性和消除相关性规则**：这些规则可以类似于 Azure 如何使用可用性区域将 vm 和存储与多个容错域一起或在群集中分离，如扩展群集。
- **Azure 门户集成**：Azure Stack HCI 的 Azure 门户体验旨在查看全球范围内的所有 Azure Stack HCI 群集以及正在开发中的新功能。
- **高性能工作负荷的 GPU 加速**：AI/ML 应用程序可以从使用 GPU 提高性能中获益。
- **BitLocker 加密**：你现在可以使用 BitLocker 对 Azure Stack HCI 上的数据卷内容进行加密，帮助政府和其他客户遵守 FIPS 140-2 和 HIPAA 等标准。
- **提高了存储空间直通卷的修复速度**：快速、无缝地修复卷。

Windows Admin Center 版本 20H2 还为基于 Windows Server 的群集提供了新的群集更新 UI，包括原始的 Azure Stack HCI 解决方案。 虽然你可以将新建群集创建向导用于 Windows Server，但它不能使用存储空间直通创建 Windows Server 群集。需要 Azure Stack HCI 操作系统。

## <a name="roles-you-can-run-without-virtualizing"></a>无需虚拟化即可运行的角色

由于 Azure Stack HCI 旨在作为虚拟化主机，供你在虚拟机中运行所有工作负载，因此 Azure Stack HCI 术语允许只运行托管虚拟机所需的内容。

这意味着你可以运行以下服务器角色：

- Hyper-V
- 网络控制器和软件定义的网络所需的其他组件 (SDN) 

但任何其他角色和应用都必须运行在 VM 中。 请注意，你可以运行用于管理托管 VM 和为了托管 VM 正常运行所需的实用程序、应用和服务。

## <a name="video-based-learning"></a>基于视频的学习

此处提供了 Azure 扩展网络视频：

- [通过 Windows Server 和混合网络实现到 Azure 的无缝连接](https://www.youtube.com/watch?v=do2_4Y2p9dk)

Azure Stack HCI 的基于 Windows Server 2019 的原始版本的视频：

- [Azure Stack 和 Azure Stack HCI 概述视图](https://aka.ms/AzureStackOverviewVideo)
- [Microsoft Ignite Live 2019-与 Azure Stack HCI 入门](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [发现 Azure Stack HCI](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [通过 Azure Stack HCI 实现零售商店或分支机构的现代化](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [Azure Stack HCI 的新增功能：45分钟内的45件](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [开始 Azure Stack HCI 部署](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [收缩数据的情况： Azure Stack HCI 中的重复数据删除](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Dave Kawula 在 Azure Stack HCI 上的字段中的说明](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

下面是混合云虚拟事件的视频：

- [Azure Stack HCI |混合云虚拟事件](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="next-steps"></a>后续步骤

- [下载 Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)
- [在 Windows 管理中心使用 Azure Stack HCI](get-started.md)
