---
title: Azure Stack HCI 解决方案概述
description: Azure Stack HCI 解决方案的概述。
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.date: 11/04/2019
ms.openlocfilehash: 3b08b5e65c0cff4ab82494261fb1663b16bf757d
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80812401"
---
# <a name="azure-stack-hci-solutions-overview"></a>Azure Stack HCI 解决方案概述

Azure Stack HCI 是超融合的 Windows Server 2019 群集，可在本地环境使用经过验证的硬件来运行虚拟化工作负荷。 你还可以选择连接到 Azure 服务以进行基于云的备份，Site Recovery 等。 Azure Stack HCI 解决方案使用 Microsoft 验证的硬件来确保最佳性能和可靠性。 这些解决方案包括对 NVMe 驱动器、永久性内存和远程直接内存访问（RDMA）网络等技术的支持。

Azure Stack HCI 是结合多个产品的解决方案：

- 来自 OEM 合作伙伴的硬件
- Windows Server 2019 Datacenter Edition
- Windows Admin Center
- Azure 服务（可选）

![Azure Stack HCI 解决方案](media/overview/azure-stack-hci-solution.png)

Azure Stack HCI 是 Microsoft 的超聚合解决方案，适用于各种硬件合作伙伴。 请考虑以下适用于超融合解决方案的场景，以帮助确定 Azure Stack HCI 是否是最符合需求的解决方案：

- **更换过时的硬件。** 将较旧的服务器和存储基础结构替换为本地并在其上运行 Windows 和 Linux 虚拟机（Vm），并提供现有的 IT 技能和工具。

- **整合虚拟化工作负荷。** 在高效的超融合基础结构上整合旧式应用。 利用用于运行超大规模数据中心的相同类型的云效率，如 Microsoft Azure。

- **连接到 Azure 以使用混合云服务。** 简化对 Azure 中的云管理和安全服务的访问，包括场外备份、Site Recovery、基于云的监视等。

## <a name="the-azure-stack-family"></a>Azure Stack 系列

Azure Stack HCI 属于 Azure 和 Azure Stack 系列，与 Azure Stack Hub 使用相同的软件定义计算、存储及网络软件。 下面简要介绍了不同的解决方案。 有关详细信息，请参阅[比较 Azure Stack 生态系统](../operator/compare-azure-azure-stack.md)。

- [Azure](https://azure.microsoft.com) -将公有云服务用于点播、自助服务计算资源，以迁移现有应用并使其现代化，并构建新的云本机应用。
- [Azure Stack 边缘](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)-加速机器学习工作负荷，在云托管的设备上运行容器化应用或本地虚拟化工作负载。
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) - 在本地运行虚拟化应用、更换和整合过时的服务器基础结构，以及连接到 Azure 以使用云服务。
- [Azure Stack Hub](../operator/azure-stack-overview.md) - 在连接中断时运行本地云应用，或者使用一致的 Azure 服务满足法规要求。

![Azure、Azure Stack 和 Azure Stack HCI](media/overview/azure-family.png)

若要了解详细信息，请访问以下链接：

- 请查看我们的[AZURE STACK HCI](https://azure.microsoft.com/overview/azure-stack/hci)解决方案网站。
- 观看 Microsoft 专家 Jeff Woolsey 和 Vijay Tewari [介绍新的 Azure Stack HCI 解决方案](https://aka.ms/AzureStackOverviewVideo)。

## <a name="hyperconverged-efficiencies"></a>超融合效率

Azure Stack HCI 解决方案在行业标准的 x86 服务器和组件上结合了高度虚拟化的计算、存储和网络功能。 在同一群集中组合资源可以更轻松地进行部署、管理及缩放。 通过所选的命令行自动化或 Windows 管理中心进行管理。

使用 Hyper-v 为服务器应用实现业界领先的 VM 性能，使用 Microsoft 云的基本虚拟机监控程序技术，并存储空间直通技术，内置支持 NVMe、永久性内存和远程直接内存访问（RDMA）网络。

通过受防护的 Vm、网络 microsegmentation 和本机加密来帮助保护应用和数据的安全。

## <a name="hybrid-capabilities"></a>混合功能

你可以在公有云中充分利用云和本地协作的超聚合基础结构平台。 团队可以通过内置集成开始对 Azure 基础结构管理服务构建云技能：

- Azure Site Recovery 可以实现高可用性和灾难恢复即服务 (DRaaS)。
- Azure Monitor 是一种集中式中心，用于跟踪应用、网络和基础结构中发生的情况，以及由 AI 提供支持的高级分析。
- 云见证使用 Azure 作为群集仲裁的轻量决胜策略。
- Azure 备份可以保护场外数据和防范勒索软件。
- Azure 更新管理可以评估更新，以及更新 Azure 和本地环境中运行的 Windows VM 部署。
- Azure 网络适配器可通过点到站点 VPN 将本地资源连接到 Azure 中的 VM。
- 使用 Azure 文件同步将文件服务器与云同步。

有关详细信息，请参阅[将 Windows Server 连接到 Azure 混合服务](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index)。

## <a name="management-tools-and-system-center"></a>管理工具和 System Center

Azure Stack HCI 与 Azure Stack Hub 使用相同的虚拟化和软件定义存储和网络软件。 但是，使用 Azure Stack HCI 可在群集上拥有完全管理员权限，并且可以直接管理其所有技术：

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [软件定义的网络](https://docs.microsoft.com/windows-server/networking/sdn/)
- [故障转移群集](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

若要管理这些技术，可以使用以下管理工具：

- [Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- 其他管理工具（如[服务器管理器](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager)和 MMC 管理单元）
- 非 Microsoft 工具，如 5Nine Manager

如果你选择使用 System Center 部署和管理基础结构，将使用 System Center 虚拟机管理 (VMM) 和 System Center Operations Manager。 利用 VMM，你可以预配和管理创建 Vm 和服务并将其部署到私有云所需的资源。 使用 Operations Manager 可以监视整个企业中的服务、设备和操作，从而识别问题并立即采取措施。

## <a name="hardware-partners"></a>硬件合作伙伴

可以从 20 家合作伙伴购买运行 Windows Server 2019 的已验证 Azure Stack HCI 解决方案。 首选的 Microsoft 合作伙伴可帮助你正常运行解决方案，消除冗长的设计和生成时间。 他们还可提供单一联络点来帮助你完成服务的实施与支持。

请访问[AZURE STACK HCI 网站](https://azure.microsoft.com/overview/azure-stack/hci)，查看这些 Microsoft 合作伙伴当前提供的70多 Azure Stack HCI 解决方案： ASUS、Axellio、Blue 芯片、DataON、Dell EMC、FUJITSU、HPE、Hitachi、Huawei、联想、NEC、primeLine 解决方案、QCT、SecureGUARD 和 Supermicro。

## <a name="video-based-learning"></a>基于视频的学习

下面是一些来自 Microsoft Ignite 2019 研讨会的视频：

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

### <a name="what-do-azure-stack-hub-and-azure-stack-hci-solutions-have-in-common"></a>Azure Stack Hub 和 Azure Stack HCI 解决方案有哪些共同之处？

Azure Stack HCI 解决方案采用与 Azure Stack 集线器相同的基于 Hyper-v 的软件定义计算、存储和网络技术。 这两个产品都符合严格的测试和验证准则，目的是确保与基础硬件平台配合时的可靠性和兼容性。

### <a name="how-are-they-different"></a>那么其不同之处在哪里？

使用 Azure Stack Hub 可在本地运行云服务。 你可以在本地运行 Azure IaaS 和 PaaS 服务，在任何位置按本地 Azure 门户部署和运行云应用。

使用 Azure Stack HCI 可在本地运行虚拟化工作负荷，并使用 Windows 管理中心和你熟悉的 Windows Server 工具进行管理。 你可以选择连接到 Azure 以实现混合方案，例如基于云的 Site Recovery、监视等。

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>Microsoft 为何将 HCI 产品/服务纳入 Azure Stack 系列？

Microsoft 的超聚合技术已经是 Azure Stack 中心的基础。

许多 Microsoft 客户使用复杂的 IT 环境，而我们的目标是提供符合客户需求的解决方案，让他们使用适当的技术来满足适当的业务需求。 Azure Stack HCI 是基于 Windows Server 2016 的创新 Windows Server 软件定义 (WSSD) 解决方案，以前由我们的硬件合作伙伴提供。 我们已将其引入 Azure Stack 系列，因为我们已经开始提供新的选项，以与 Azure 无缝连接，实现基础结构管理服务。

### <a name="does-azure-stack-hci-need-to-be-connected-to-azure"></a>Azure Stack HCI 是否需要连接到 Azure？

不是，它是可选的。 你可以利用与 Azure 的集成来实现混合方案，例如场外备份和灾难恢复，以及基于云的监视和更新管理，但这是可选的。 不能从 internet 断开连接。

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>Azure Stack HCI 与 Windows Server 有何关系？

Windows Server 2019 几乎是每个 Azure 产品的基础。 你重视的所有功能都继续在 Windows Server 中提供，并且受到支持。 Azure Stack HCI 是在本地部署 HCI 的建议方式，可以使用我们的合作伙伴提供的经过 Microsoft 验证的硬件。

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack-hub"></a>是否可以从 Azure Stack HCI 升级到 Azure Stack Hub？

不能，但客户可将工作负荷从 Azure Stack HCI 迁移到 Azure Stack Hub 或 Azure。

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>哪些 Azure 服务可连接到 Azure Stack HCI？

有关可与 Azure Stack HCI 连接的 Azure 服务的更新列表，请参阅[将 Windows Server 连接到 Azure 混合服务](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index)。

### <a name="how-does-the-cost-of-azure-stack-hci-compare-to-azure-stack-hub"></a>Azure Stack HCI 和 Azure Stack Hub 的成本有何差别？

Azure Stack Hub 以完全集成的系统销售，其中包括服务和支持。 你可以购买 Azure Stack Hub 作为你管理的系统，也可以作为我们的合作伙伴提供的完全托管服务。 除基本系统外，在 Azure Stack 集线器或 Azure 上运行的 Azure 服务将按即用即付方式销售。

Azure Stack HCI 解决方案遵循传统的购买模式。 可以向 Azure Stack HCI 合作伙伴购买经验证的硬件，以及从现有的各种渠道购买软件（具有软件定义的数据中心功能的 Windows Server 2019 Datacenter Edition 以及 Windows 管理中心）。 对于与 Windows 管理中心配合使用的 Azure 服务，可以通过 Azure 订阅付费。

### <a name="how-do-i-buy-azure-stack-hci-solutions"></a>如何购买 Azure Stack HCI 解决方案？

请执行下列步骤：

1. 从首选的硬件合作伙伴购买经 Microsoft 验证的硬件系统。
1. 安装用于管理的 Windows Server 2019 Datacenter edition 和 Windows 管理中心，并为云服务连接到 Azure 的功能。
1. 选择性地使用 Azure 帐户将基于云的管理和安全服务附加到工作负荷。

![购买 Azure Stack HCI 解决方案的步骤](media/overview/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-hub-and-azure-stack-hci"></a>Azure Stack Hub 和 Azure Stack HCI 的比较

当你的组织进行数字转换时，你可能会发现，你可以通过使用公有云服务在现代体系结构上构建并刷新旧版应用来更快地移动。 但是，出于技术和法规障碍等原因，许多工作负荷必须保留在本地。 下表可帮助你确定哪种 Microsoft 混合云策略可随时随地提供所需的服务，并为任何位置的工作负荷提供云创新。

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| 新的技能，创新的流程 | 相同的技能，熟悉的流程 |
| 数据中心内的 Azure 服务 | 将数据中心连接到 Azure 服务 |

### <a name="when-to-use-azure-stack-hub"></a>何时使用 Azure Stack Hub

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| 针对自助式基础结构即服务 (IaaS) 使用 Azure Stack Hub，因为其具有强式隔离，并且可对位于相同位置的多个租户精确地跟踪使用情况和进行费用分摊。 非常适合用于服务提供商和企业私有云。 来自 Azure 市场的模板。 | Azure Stack HCI 原生不会实施或提供多租户。 |
| 在本地使用 Azure Stack Hub 开发和运行依赖于 Web 应用、Functions 或事件中心等平台即服务 (PaaS) 的应用。 这些服务在 Azure Stack 集线器上运行的方式与在 Azure 中运行完全一样，提供一致的混合开发和运行时环境。 | Azure Stack HCI 不会在本地运行 PaaS 服务。 |
| 使用 Azure Stack 集线器，通过 DevOps 实践（例如，基础结构即代码、持续集成和持续部署（CI/CD）和便利功能，如 Azure 一致性 VM 扩展）实现应用部署和操作的现代化。 非常适合用于开发和 DevOps 团队。 | Azure Stack HCI 原生不包括任何 DevOps 工具。 |

### <a name="when-to-use-azure-stack-hci"></a>何时使用 Azure Stack HCI

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack Hub 至少需要 4 个节点，并需要自身的网络交换机。 | 使用 Azure Stack HCI 可以尽量减少远程办公室和分公司的占用空间。 一开始可以仅使用 2 个服务器节点和无交换机的后端到后端网络，以实现最高简便度和负担能力。 硬件服务从4个驱动器开始，64 GB 内存，适用于 $ 10k/节点。 |
| 为了与 Azure 保持一致，Azure Stack Hub 将限制 Hyper V 可配置性和功能。 | 使用 Azure Stack HCI 可为经典企业应用（例如 Exchange、SharePoint 和 SQL Server）实现单纯有用的 Hyper-V 虚拟化，以及虚拟化 Windows Server 角色，例如文件服务器、DNS、DHCP、IIS 和 AD。 不受限制地访问所有 Hyper-V 功能，例如受防护的 VM。|
| Azure Stack Hub 不会公开这些基础结构技术。 | 借助 Azure Stack HCI 可以使用软件定义的基础结构来代替过时的存储设备阵列或网络设备，而无需进行重大的重新改造。 可以直接访问和管理内置的 Hyper-V、存储空间直通和软件定义的网络 (SDN)。 |
