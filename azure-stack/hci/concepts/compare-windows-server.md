---
title: 将 Azure Stack HCI 与 Azure Stack 中心和 Windows Server 进行比较
description: 本主题将帮助你确定 Azure Stack HCI、Azure Stack 集线器或 Windows Server 是否适合你的组织。
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/11/2020
ms.openlocfilehash: bd1798ddfcf0897eeaedaeaae0cfb02eac1aaa1e
ms.sourcegitcommit: 79e8df69b139bfa21eb83aceb824b97e7f418c03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97365133"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub-and-windows-server"></a>将 Azure Stack HCI 与 Azure Stack 中心和 Windows Server 进行比较

> 适用于： Azure Stack HCI，版本 20H2;Windows Server 2019;Azure Stack 中心

许多客户都想知道 Windows Server、Azure Stack HCI 或 Azure Stack 中心是否更适合其需求。 本主题将帮助你确定哪一种适合你的组织。 

## <a name="compare-azure-stack-hci-to-windows-server"></a>将 Azure Stack HCI 与 Windows Server 进行比较

Windows Server 和 Azure Stack HCI 都提供了新版本路线图的相同高质量用户体验。

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

## <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>将 Azure Stack HCI 与 Azure Stack 中心进行比较

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

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Stack HCI？](../overview.md)
