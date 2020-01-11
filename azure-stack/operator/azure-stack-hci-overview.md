---
title: Azure Stack 集线器 HCI 概述
description: Azure Stack 集线器 HCI 是一种超聚合 Windows Server 2019 群集，使用经过验证的硬件在本地运行虚拟化工作负荷。 你还可以选择连接到 Azure 服务以实现基于云的备份、站点恢复等。 Azure Stack 集线器 HCI 解决方案使用 Microsoft 验证的硬件来确保最佳性能和可靠性，并包括对对等技术（如 NVMe 驱动器、永久性内存和远程直接内存访问（RDMA）网络）的支持。
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.date: 11/04/2019
ms.openlocfilehash: d35c38ab40543556d31af443f45c0c249ae76e2c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882481"
---
# <a name="azure-stack-hub-hci-overview"></a>Azure Stack 集线器 HCI 概述

Azure Stack 集线器 HCI 是超聚合 Windows Server 2019 群集，使用经过验证的硬件在本地运行虚拟化工作负荷。 你还可以选择连接到 Azure 服务以实现基于云的备份、站点恢复等。 Azure Stack 集线器 HCI 解决方案使用 Microsoft 验证的硬件来确保最佳性能和可靠性，并包括对对等技术（如 NVMe 驱动器、永久性内存和远程直接内存访问（RDMA）网络）的支持。

Azure Stack 集线器 HCI 是一种结合多种产品的解决方案：

- 来自 OEM 合作伙伴的硬件

- Windows Server 2019 Datacenter Edition

- Windows Admin Center

- Azure 服务（可选）

![Azure Stack 集线器 HCI 是 Microsoft 的超聚合解决方案，适用于各种硬件合作伙伴。](media/azure-stack-hci/azure-stack-hci-solution.png)

Azure Stack 集线器 HCI 是 Microsoft 的超聚合解决方案，适用于各种硬件合作伙伴。 请考虑以下超聚合解决方案方案，以帮助你确定 Azure Stack 集线器 HCI 是否是最适合你的需求的解决方案：

- **更换老化硬件。** 更换较旧的服务器和存储基础结构，在本地和边缘运行使用现有 IT 技能和工具的 Windows 和 Linux 虚拟机。

- **合并虚拟化工作负荷。** 在高效的超融合基础设施上合并旧版应用。 利用用于运行超大规模数据中心的相同类型的云效率，如 Microsoft Azure。

- **连接到 Azure 以使用混合云服务。** 简化对 Azure 中的云管理服务和安全服务的访问，这些服务包括场外备份、站点恢复、基于云的监视等。

## <a name="the-azure-stack-hub-family"></a>Azure Stack 集线器系列

Azure Stack 集线器 HCI 是 Azure 和 Azure Stack 中心系列的一部分，它使用与 Azure Stack 集线器相同的软件定义计算、存储和网络软件。 下面是不同解决方案的快速摘要（有关详细信息，请参阅[比较 Azure Stack 中心生态系统](compare-azure-azure-stack.md)）：

- [Azure](https://azure.microsoft.com) -将公有云服务用于点播、自助服务计算资源，以迁移现有应用并使其现代化，并构建新的云本机应用。
- [Azure Stack 集线器边缘](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)-加速机器学习工作负荷，在云托管的设备上运行容器化应用或本地虚拟化工作负载。
- [Azure Stack 集线器 HCI](https://azure.microsoft.com/overview/azure-stack/hci) -在本地运行虚拟化的应用，替换和合并老化服务器基础结构，并连接到 Azure 以获取云服务。
- [Azure Stack 中心](azure-stack-overview.md)-使用一致的 Azure 服务在本地运行云应用、断开连接或满足法规要求。

![Azure Stack 集线器边缘是一项云管理的设备，用于在边缘运行机器学习和容器化应用程序，Azure Stack 集线器 HCI 是一种超聚合的解决方案，用于在本地运行 Vm 和存储，而 Azure Stack 中心提供云本机本地的 Azure 一致服务。](media/compare-azure-azure-stack/azure-stack-family.png)

若要了解详细信息，请访问以下链接：

- 在我们的[Azure Stack 集线器 HCI](https://azure.microsoft.com/overview/azure-stack/hci)解决方案网站上了解详细信息。
- 观看 Microsoft 专家 Jeff Woolsey 和 Vijay Tewari[介绍了新的 Azure Stack 集线器 HCI 解决方案](https://aka.ms/AzureStackOverviewVideo)。

## <a name="hyperconverged-efficiencies"></a>超融合效率

Azure Stack 集线器 HCI 解决方案将高度虚拟化的计算、存储和网络整合到行业标准的 x86 服务器和组件上。 将资源组合到同一群集中可以更方便地进行部署、管理和缩放。 自行选择命令行自动化或 Windows Admin Center 进行管理。

使用 Hyper-V（Microsoft 云的基础性虚拟机监控程序技术）和存储空间直通技术（为 NVMe、持久性内存、远程直接内存访问 (RDMA) 网络提供内置支持），针对服务器应用程序实现行业领先的虚拟机性能。

通过受防护的虚拟机、网络 microsegmentation 和本机加密来帮助保护应用和数据的安全。

## <a name="hybrid-capabilities"></a>混合功能

可以充分利用能够与公有云中的超融合基础设施平台配合使用的云和本地功能。 你的团队可以开始构建能够以内置方式集成到 Azure 基础结构管理服务的云技能：

- Azure Site Recovery，可以实现高可用性和灾难恢复即服务 (DRaaS)。

- Azure Monitor 是一种集中式中心，用于跟踪应用程序、网络和基础结构中发生的情况，以及由 AI 提供支持的高级分析。

- 云见证，将使用 Azure 作为群集仲裁的轻型中断程序。

- Azure 备份，用于场外数据保护和勒索软件防护。

- Azure 更新管理，可以针对 Azure 中运行的和本地运行的 Windows VM 进行更新评估和更新部署。

- Azure 网络适配器，可通过点到站点 VPN 将本地资源与 Azure 中的 VM 连接。

- 使用 Azure 文件同步将文件服务器与云同步。

有关详细信息，请参阅 [Connecting Windows Server to Azure hybrid services](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index)（将 Windows Server 连接到 Azure 混合服务）。

## <a name="management-tools-and-system-center"></a>管理工具和 System Center

Azure Stack 集线器 HCI 使用与 Azure Stack 集线器相同的虚拟化和软件定义的存储和网络软件。 但是，对于 Azure Stack 集线器 HCI，你对群集拥有完全管理权限，并且可以直接管理其任何技术：

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [软件定义的网络](https://docs.microsoft.com/windows-server/networking/sdn/)
- [故障转移群集](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

若要管理这些技术，可以使用以下管理工具：

- [Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- 其他管理工具，例如[服务器管理器](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager)和 MMC 管理单元
- 非 Microsoft 工具，例如 5Nine Manager

如果你选择使用 System Center 来部署和管理你的基础结构，则使用 System Center 虚拟机管理（VMM）和 System Center Operations Manager。 可以使用 VMM 预配和管理所需资源，以便创建虚拟机和服务并将其部署到私有云。 可以使用 Operations Manager 监视整个企业的服务、设备和运营，在确定问题后立即行动。

## <a name="hardware-partners"></a>硬件合作伙伴

可以从20个合作伙伴购买运行 Windows Server 2019 的验证 Azure Stack 集线器 HCI 解决方案。 你首选的 Microsoft 合作伙伴可在没有漫长的设计和构建时间的情况下启动并运行。 它们还提供实现和支持服务的单一联系点。

请访问[Azure Stack 中心 hci 网站](https://azure.microsoft.com/overview/azure-stack/hci)，查看这些 Microsoft 合作伙伴当前提供的70多 AZURE STACK 中心 HCI 解决方案： ASUS、Axellio、Bluechip、DataON、Dell EMC、FUJITSU、HPE、Hitachi、Huawei、联想、NEC、primeLine 解决方案、QCT、SecureGUARD 和 Supermicro。

## <a name="faq"></a>常见问题

### <a name="what-do-azure-stack-hub-and-azure-stack-hub-hci-solutions-have-in-common"></a>Azure Stack 集线器和 Azure Stack 集线器 HCI 解决方案有哪些共同点？

Azure Stack 集线器 HCI 解决方案采用与 Azure Stack 集线器相同的基于 Hyper-v 的软件定义计算、存储和网络技术。 两种套餐都符合严格的测试和验证标准，可以确保可靠性并与基础硬件平台兼容。

### <a name="how-are-they-different"></a>那么其不同之处在哪里？

利用 Azure Stack Hub，你可以在本地运行云服务。 可以在本地运行 Azure IaaS 和 PaaS 服务，以便在任何位置一致地构建和运行云应用程序，这些应用程序可在本地 Azure 门户进行管理。

利用 Azure Stack 集线器 HCI，你可以在本地运行虚拟化工作负荷，并通过 Windows 管理中心和熟悉的 Windows Server 工具进行管理。 对于混合方案（例如基于云的站点恢复、监视等），可以选择连接到 Azure。

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-hub-family"></a>为什么 Microsoft 将其 HCI 产品/服务引入 Azure Stack 集线器系列？

Microsoft 的超聚合技术已经是 Azure Stack 中心的基础。

许多 Microsoft 客户有着复杂的 IT 环境，我们的目标是根据其业务需求针对其技术提供相应的解决方案。 Azure Stack 集线器 HCI 是以前在我们的硬件合作伙伴中提供的基于 Windows Server 2016 的 Windows Server 软件定义（WSSD）解决方案的演变。 我们已将其引入 Azure Stack 集线器系列，因为我们已经开始提供新的选项，以与 Azure 无缝连接以实现基础结构管理服务。

### <a name="does-azure-stack-hub-hci-need-to-be-connected-to-azure"></a>Azure Stack 集线器 HCI 是否需要连接到 Azure？

不是，它是可选的。 你可以利用与 Azure 的集成来实现混合方案，如场外备份和灾难恢复，以及基于云的监视和更新管理，但这些方案是可选的。 不能从 Internet 断开连接。

### <a name="how-does-azure-stack-hub-hci-relate-to-windows-server"></a>Azure Stack 集线器 HCI 与 Windows Server 有何关联？

Windows Server 2019 是几乎所有 Azure 产品的基础。 我们会继续在 Windows Server 中提供所有重要的功能及其相关支持。 建议使用 Azure Stack 集线器 HCI，使用合作伙伴提供的 Microsoft 验证硬件在本地部署 HCI。

### <a name="can-i-upgrade-from-azure-stack-hub-hci-to-azure-stack-hub"></a>能否从 Azure Stack 集线器 HCI 升级到 Azure Stack 中心？ 

不能，但客户可以将其工作负荷从 Azure Stack 集线器 HCI 迁移到 Azure Stack 中心或 Azure。

### <a name="what-azure-services-can-i-connect-to-azure-stack-hub-hci"></a>哪些 Azure 服务可以连接到 Azure Stack 集线器 HCI？

有关可以 Azure Stack 集线器 HCI 连接到的 Azure 服务的更新列表，请参阅将[Windows Server 连接到 Azure 混合服务](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index)。

### <a name="how-does-the-cost-of-azure-stack-hub-hci-compare-to-azure-stack-hub"></a>Azure Stack 中心 HCI 的成本如何与 Azure Stack 中心进行比较？ 

Azure Stack 中心作为完全集成的系统销售，其中包括服务和支持。 你可以作为所管理的系统购买 Azure Stack 集线器，也可以作为我们的合作伙伴提供的完全托管服务。 除基本系统外，在 Azure Stack 集线器或 Azure 上运行的 Azure 服务将按即用即付方式销售。

Azure Stack 集线器 HCI 解决方案遵循传统购买模型。 你可以从各种现有通道购买 Azure Stack 集线器 HCI 合作伙伴和软件2019（带有软件定义的数据中心功能和 Windows 管理中心）的已验证硬件。 对于可以与 Windows Admin Center 配合使用的 Azure 服务，请通过 Azure 订阅付款。

### <a name="how-do-i-buy-azure-stack-hub-hci-solutions"></a>如何实现买 Azure Stack 中心 HCI 解决方案？

执行以下步骤:

1. 从首选的硬件合作伙伴处购买经 Microsoft 验证的硬件系统。
1. 安装 Windows Server 2019 Datacenter Edition 和 Windows Admin Center，以便进行管理并连接到 Azure 以使用云服务。
1. （可选）使用 Azure 帐户将基于云的管理和安全服务附加到工作负荷。

![若要购买 Azure Stack 集线器 HCI 解决方案，请选择最适合你的需求的硬件合作伙伴和配置。](media/azure-stack-hci/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-hub-and-azure-stack-hub-hci"></a>比较 Azure Stack 集线器和 Azure Stack 集线器 HCI

在组织进行数字转型时，你可能会发现，如果使用公有云服务在现代体系结构上进行构建并更换旧版应用，则可加快转型速度。 但是，由于某些原因（包括技术和法规障碍），许多工作负荷必须保留在本地。 下表帮助你确定哪些 Microsoft 混合云策略在你需要的位置提供所需的内容，并为工作负荷提供云创新。

| Azure Stack Hub | Azure Stack 集线器 HCI |
| --------------- | --------------- |
| 新技能，创新的流程 | 相同的技能，熟悉的流程 |
| Azure 服务位于数据中心内 | 将数据中心连接到 Azure 服务 |

### <a name="when-to-use-azure-stack-hub"></a>何时使用 Azure Stack 中心

| Azure Stack Hub | Azure Stack 集线器 HCI |
| --------------- | --------------- |
| 将 Azure Stack 集线器用于自助服务基础结构即服务（IaaS），并为多个分离型租户提供强大的隔离和精确的使用情况跟踪和计费。 适用于服务提供商和企业私有云。 模板来自 Azure 市场。 | Azure Stack 集线器 HCI 不会以本机方式强制执行或提供多租户。 |
| 使用 Azure Stack 集线器开发和运行依赖于平台即服务（PaaS）服务（如 Web 应用、函数或本地事件中心）的应用。 这些服务在 Azure Stack 集线器上运行的方式与在 Azure 中运行完全一样，提供一致的混合开发和运行时环境。 | Azure Stack 集线器 HCI 不在本地运行 PaaS 服务。 |
| 使用 Azure Stack 集线器，通过 DevOps 实践（例如，基础结构即代码、持续集成和持续部署（CI/CD）和便利功能，如 Azure 一致性 VM 扩展）实现应用部署和操作的现代化。 适用于开发团队和 DevOps 团队。 | Azure Stack 集线器 HCI 本身并不包含任何 DevOps 工具。 |

### <a name="when-to-use-azure-stack-hub-hci"></a>何时使用 Azure Stack 集线器 HCI

| Azure Stack Hub | Azure Stack 集线器 HCI |
| --------------- | --------------- |
| Azure Stack 集线器需要至少4个节点及其自己的网络交换机。 | 将 Azure Stack 集线器 HCI 用于远程办公室和分支的最小占用空间。 一开始只需 2 个服务器节点，进行无交换机背靠背网络连接，特别简单且经济适用。 硬件套餐最低配置为 4 个驱动器、64 GB 内存，每个节点往往不到 1 万美元。 |
| Azure Stack 集线器限制了 Hyper-v 配置和功能集，以便与 Azure 保持一致。 | 使用 Azure Stack 集线器 HCI 实现经典企业应用（如 Exchange、SharePoint 和 SQL Server）的基本 Hyper-v 虚拟化，以及虚拟化 Windows Server 角色（如文件服务器、DNS、DHCP、IIS 和 AD）。 不受限制地访问所有 Hyper-V 功能，例如受防护的 VM。|
| Azure Stack 中心不会公开这些基础结构技术。 | 使用 Azure Stack 集线器 HCI 来使用软件定义的基础结构来代替老化存储阵列或网络设备，而无需使用主要示意图。 内置 Hyper-v、存储空间直通和软件定义的网络（SDN）可直接访问和管理。 |
