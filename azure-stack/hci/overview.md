---
title: Azure Stack HCI 概述
description: Azure Stack HCI 是一种超聚合 Windows Server 2019 群集，使用经过验证的硬件在本地运行虚拟化工作负荷。 你还可以选择连接到 Azure 服务以实现基于云的备份、站点恢复等。 Azure Stack HCI 解决方案使用 Microsoft 验证的硬件来确保最佳性能和可靠性，并包括对对等技术（例如 NVMe 驱动器、永久性内存和远程直接内存访问（RDMA）网络）的支持。
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.date: 11/04/2019
ms.openlocfilehash: 2bf9af3db5a3577b62c59e92879a3680d9a3532a
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77519445"
---
# <a name="azure-stack-hci-overview"></a>Azure Stack HCI 概述

Azure Stack HCI 是超聚合 Windows Server 2019 群集，使用经过验证的硬件在本地运行虚拟化工作负荷。 你还可以选择连接到 Azure 服务以实现基于云的备份、站点恢复等。 Azure Stack HCI 解决方案使用 Microsoft 验证的硬件来确保最佳性能和可靠性，并包括对对等技术（例如 NVMe 驱动器、永久性内存和远程直接内存访问（RDMA）网络）的支持。

Azure Stack HCI 是组合几个产品的解决方案：

- 来自 OEM 合作伙伴的硬件

- Windows Server 2019 Datacenter edition

- Windows 管理中心

- Azure 服务（可选）

![Azure Stack HCI 是 Microsoft 的超聚合解决方案，适用于各种硬件合作伙伴。](media/overview/azure-stack-hci-solution.png)

Azure Stack HCI 是 Microsoft 的超聚合解决方案，适用于各种硬件合作伙伴。 请考虑以下超聚合解决方案方案，以帮助你确定 Azure Stack HCI 是否是最符合你的需求的解决方案：

- **刷新老化硬件。** 将较旧的服务器和存储基础结构替换为本地，并在现有 IT 技能和工具的基础上运行 Windows 和 Linux 虚拟机。

- **整合虚拟化工作负荷。** 使用高效的超聚合基础结构合并旧应用。 利用用于运行超大规模数据中心的相同类型的云效率，如 Microsoft Azure。

- **为混合云服务连接到 Azure。** 简化对 Azure 中的云管理和安全服务的访问，包括场外备份、站点恢复、基于云的监视等。

## <a name="the-azure-stack-family"></a>Azure Stack 系列

Azure Stack HCI 是 Azure 和 Azure Stack 系列的一部分，它使用与 Azure Stack 集线器相同的软件定义计算、存储和网络软件。 下面是不同解决方案的快速摘要（有关详细信息，请参阅[比较 Azure Stack 生态系统](../operator/compare-azure-azure-stack.md)）：

- [Azure](https://azure.microsoft.com) -将公有云服务用于点播、自助服务计算资源，以迁移现有应用并使其现代化，并构建新的云本机应用。
- [Azure Stack 边缘](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)-加速机器学习工作负荷，在云托管的设备上运行容器化应用或本地虚拟化工作负载。
- [AZURE STACK HCI](https://azure.microsoft.com/overview/azure-stack/hci) -在本地运行虚拟化的应用，替换和合并老化服务器基础结构，并连接到 Azure 以获取云服务。
- [Azure Stack 中心](../operator/azure-stack-overview.md)-使用一致的 Azure 服务在本地运行云应用、断开连接或满足法规要求。

![Azure Stack Edge 是一项云管理的设备，用于在边缘运行机器学习和容器化应用程序，Azure Stack HCI 是一种超聚合的解决方案，用于在本地运行 Vm 和存储，而 Azure Stack 中心提供与 Azure 一致的云本地服务。](media/overview/azure-family.png)

若要了解详细信息，请访问以下链接：

- 在我们的[AZURE STACK HCI](https://azure.microsoft.com/overview/azure-stack/hci)解决方案网站上了解详细信息。
- 观看 Microsoft 专家 Jeff Woolsey 和 Vijay Tewari[介绍了新的 AZURE STACK HCI 解决方案](https://aka.ms/AzureStackOverviewVideo)。

## <a name="hyperconverged-efficiencies"></a>超聚合效率

Azure Stack HCI 解决方案将高度虚拟化的计算、存储和网络整合到行业标准的 x86 服务器和组件上。 结合相同群集中的资源，可以更轻松地进行部署、管理和缩放。 通过所选的命令行自动化或 Windows 管理中心进行管理。

为你的服务器应用程序实现业界领先的虚拟机性能，具有 Hyper-v、Microsoft 云的基本虚拟机监控程序技术，以及存储空间直通技术，内置支持 NVMe、永久性内存和远程直接内存访问（RDMA）网络。

通过受防护的虚拟机、网络 microsegmentation 和本机加密来帮助保护应用和数据的安全。

## <a name="hybrid-capabilities"></a>混合功能

你可以利用云和本地协作，并在公有云中使用超聚合基础结构平台。 你的团队可以开始通过内置集成到 Azure 基础结构管理服务来构建云技能：

- 用于实现高可用性和灾难恢复即服务（DRaaS）的 Azure Site Recovery。

- Azure Monitor 是一种集中式中心，用于跟踪应用程序、网络和基础结构中发生的情况，以及由 AI 提供支持的高级分析。

- 云见证，使用 Azure 作为群集仲裁的轻型关联断开器。

- Azure 备份可实现场外数据保护，并可防止勒索软件。

- Azure 更新管理适用于在 Azure 和本地运行的 Windows Vm 的更新评估和更新部署。

- 通过点到站点 VPN 将本地资源与 Azure 中的 Vm 连接到 azure 网络适配器。

- 使用 Azure 文件同步将文件服务器与云同步。

有关详细信息，请参阅[将 Windows Server 连接到 Azure 混合服务](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index)。

## <a name="management-tools-and-system-center"></a>管理工具和 System Center

Azure Stack HCI 使用与 Azure Stack 集线器相同的虚拟化和软件定义的存储和网络软件。 但是，对于 Azure Stack HCI，你对群集拥有完全管理权限，并且可以直接管理其任何技术：

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [软件定义的网络](https://docs.microsoft.com/windows-server/networking/sdn/)
- [故障转移群集](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

若要管理这些技术，可以使用以下管理工具：

- [Windows 管理中心](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- 其他管理工具，例如[服务器管理器](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager)和 MMC 管理单元
- 非 Microsoft 工具，例如 5Nine Manager

如果你选择使用 System Center 来部署和管理你的基础结构，则使用 System Center 虚拟机管理（VMM）和 System Center Operations Manager。 利用 VMM，你可以预配和管理创建虚拟机和服务并将其部署到私有云所需的资源。 利用 Operations Manager，你可以监视企业中的服务、设备和操作，以确定即时操作的问题。

## <a name="hardware-partners"></a>硬件伙伴

可以从20个合作伙伴那里购买运行 Windows Server 2019 的验证 Azure Stack HCI 解决方案。 你首选的 Microsoft 合作伙伴可在没有漫长的设计和构建时间的情况下启动并运行。 它们还提供实现和支持服务的单一联系点。

请访问[AZURE STACK HCI 网站](https://azure.microsoft.com/overview/azure-stack/hci)，查看这些 Microsoft 合作伙伴当前提供的70多 Azure Stack HCI 解决方案： ASUS、Axellio、Bluechip、DataON、Dell EMC、FUJITSU、HPE、Hitachi、Huawei、联想、NEC、primeLine 解决方案、QCT、SecureGUARD 和 Supermicro。

## <a name="video-based-learning"></a>基于视频的学习

下面是 Microsoft Ignite 2019 会话中的一些视频：

- [Microsoft Ignite Live 2019-与 Azure Stack HCI 入门](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [发现 Azure Stack HCI](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [通过 Azure Stack HCI 实现零售商店或分支机构的现代化](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [Azure Stack HCI 的新增功能：45分钟内的45件](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [开始 Azure Stack HCI 部署](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [收缩数据的情况： Azure Stack HCI 中的重复数据删除](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Dave Kawula 在 Azure Stack HCI 上的字段中的说明](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

下面是混合云虚拟事件的视频：

- [Azure Stack HCI |混合云虚拟事件](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="faq"></a>常见问题解答

### <a name="what-do-azure-stack-hub-and-azure-stack-hci-solutions-have-in-common"></a>Azure Stack 集线器和 Azure Stack HCI 解决方案有哪些共同点？

Azure Stack HCI 解决方案采用与 Azure Stack 集线器相同的基于 Hyper-v 的软件定义计算、存储和网络技术。 这两种产品都符合严格的测试和验证条件，以确保底层硬件平台的可靠性和兼容性。

### <a name="how-are-they-different"></a>那么其不同之处在哪里？

利用 Azure Stack Hub，你可以在本地运行云服务。 可以在本地运行 Azure IaaS 和 PaaS 服务，以便在任何位置一致地构建和运行云应用程序，这些应用程序可在本地 Azure 门户进行管理。

利用 Azure Stack HCI，你可以在本地运行虚拟化工作负荷，并通过 Windows 管理中心和熟悉的 Windows Server 工具进行管理。 你可以选择连接到 Azure 以实现混合方案，例如基于云的站点恢复、监视等。

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>为什么 Microsoft 将其 HCI 产品/服务引入到 Azure Stack 系列？

Microsoft 的超聚合技术已经是 Azure Stack 中心的基础。

许多 Microsoft 客户都有复杂的 IT 环境，我们的目标是提供满足这些需求的解决方案，使其能够满足适当的业务需求。 Azure Stack HCI 是以前在我们的硬件合作伙伴中提供的基于 Windows Server 2016 的 Windows Server 软件定义（WSSD）解决方案的演变。 我们已将其引入 Azure Stack 系列，因为我们已经开始提供新的选项，以与 Azure 无缝连接，实现基础结构管理服务。

### <a name="does-azure-stack-hci-need-to-be-connected-to-azure"></a>Azure Stack HCI 是否需要连接到 Azure？

不是，它是可选的。 你可以利用与 Azure 的集成来实现混合方案，如场外备份和灾难恢复，以及基于云的监视和更新管理，但这些方案是可选的。 不能从 Internet 断开连接。

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>Azure Stack HCI 如何与 Windows Server 相关联？

Windows Server 2019 是几乎每个 Azure 产品的基础。 你需要的所有功能都将继续在 Windows Server 中提供和支持。 建议使用 Azure Stack HCI 在本地部署 HCI，使用我们的合作伙伴提供的 Microsoft 验证硬件。

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack-hub"></a>能否从 Azure Stack HCI 升级到 Azure Stack 中心？ 

不能，但客户可以将其工作负荷从 Azure Stack HCI 迁移到 Azure Stack 中心或 Azure。

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>哪些 Azure 服务可以连接到 Azure Stack HCI？

有关可以 Azure Stack HCI 连接到的 Azure 服务的更新列表，请参阅将[Windows Server 连接到 Azure 混合服务](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index)。

### <a name="how-does-the-cost-of-azure-stack-hci-compare-to-azure-stack-hub"></a>Azure Stack HCI 的成本如何与 Azure Stack 中心进行比较？ 

Azure Stack 中心作为完全集成的系统销售，其中包括服务和支持。 你可以作为所管理的系统购买 Azure Stack 集线器，也可以作为我们的合作伙伴提供的完全托管服务。 除基本系统外，在 Azure Stack 集线器或 Azure 上运行的 Azure 服务将按即用即付方式销售。

Azure Stack HCI 解决方案遵循传统购买模型。 你可以从各种现有通道购买 Azure Stack HCI 合作伙伴和软件2019（具有软件定义的数据中心功能和 Windows 管理中心）的已验证硬件。 对于可用于 Windows 管理中心的 Azure 服务，请使用 Azure 订阅付费。

### <a name="how-do-i-buy-azure-stack-hci-solutions"></a>如何实现买 Azure Stack HCI 解决方案？

执行以下步骤:

1. 从你首选的硬件合作伙伴那里购买经过 Microsoft 验证的硬件系统。
1. 安装用于管理的 Windows Server 2019 Datacenter edition 和 Windows 管理中心，并为云服务连接到 Azure 的功能
1. 选择性地使用 Azure 帐户将基于云的管理和安全服务附加到工作负载。

![若要购买 Azure Stack HCI 解决方案，请选择最适合你的需求的硬件合作伙伴和配置。](media/overview/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-hub-and-azure-stack-hci"></a>比较 Azure Stack 集线器和 Azure Stack HCI

当你的组织进行数字转换时，你可能会发现，你可以通过使用公有云服务在现代体系结构上构建并刷新旧版应用来更快地移动。 然而，出于包括技术和法规障碍的原因，许多工作负荷必须在本地。 下表帮助你确定哪些 Microsoft 混合云策略在你需要的位置提供所需的内容，并为工作负荷提供云创新。

| Azure Stack 中心 | Azure Stack HCI |
| --------------- | --------------- |
| 新技能、创新流程 | 相同的技能，熟悉的过程 |
| 数据中心的 Azure 服务 | 将数据中心连接到 Azure 服务 |

### <a name="when-to-use-azure-stack-hub"></a>何时使用 Azure Stack 中心

| Azure Stack 中心 | Azure Stack HCI |
| --------------- | --------------- |
| 将 Azure Stack 集线器用于自助服务基础结构即服务（IaaS），并为多个分离型租户提供强大的隔离和精确的使用情况跟踪和计费。 适用于服务提供商和企业私有云。 Azure Marketplace 中的模板。 | Azure Stack HCI 不会以本机方式强制执行或提供多租户。 |
| 使用 Azure Stack 集线器开发和运行依赖于平台即服务（PaaS）服务（如 Web 应用、函数或本地事件中心）的应用。 这些服务在 Azure Stack 集线器上运行的方式与在 Azure 中运行完全一样，提供一致的混合开发和运行时环境。 | Azure Stack HCI 不会在本地运行 PaaS 服务。 |
| 使用 Azure Stack 集线器，通过 DevOps 实践（例如，基础结构即代码、持续集成和持续部署（CI/CD）和便利功能，如 Azure 一致性 VM 扩展）实现应用部署和操作的现代化。 适用于开发团队和 DevOps 团队。 | Azure Stack HCI 本身不包含任何 DevOps 工具。 |

### <a name="when-to-use-azure-stack-hci"></a>何时使用 HCI Azure Stack

| Azure Stack 中心 | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack 集线器需要至少4个节点及其自己的网络交换机。 | 将 Azure Stack HCI 用于远程办公室和分支的最小占用空间。 仅从2个服务器节点开始，并 switchless 的后端到后联网，实现最大简易性和经济性。 硬件服务从4个驱动器开始，64 GB 内存，适用于 $ 10k/节点。 |
| Azure Stack 集线器限制了 Hyper-v 配置和功能集，以便与 Azure 保持一致。 | 使用 Azure Stack HCI 实现经典企业应用（如 Exchange、SharePoint 和 SQL Server）的基本 Hyper-v 虚拟化，以及虚拟化 Windows Server 角色（如文件服务器、DNS、DHCP、IIS 和 AD）。 不受限制地访问所有 Hyper-v 功能，如受防护的 Vm。|
| Azure Stack 中心不会公开这些基础结构技术。 | 使用 Azure Stack HCI 来使用软件定义的基础结构代替老化存储阵列或网络设备，而无需使用主要示意图。 内置 Hyper-v、存储空间直通和软件定义的网络（SDN）可直接访问和管理。 |
