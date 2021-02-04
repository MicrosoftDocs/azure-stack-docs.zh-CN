---
title: 将 Azure Stack HCI 与 Azure Stack 中心进行比较
description: 本主题将帮助你确定 Azure Stack HCI 或 Azure Stack 中心是否适合你的组织。
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/03/2021
ms.openlocfilehash: e82980a5dca347549672dd519132c1f1754c3929
ms.sourcegitcommit: af2bec84471795c0f3ac62dcaf1347a64e529906
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554069"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>将 Azure Stack HCI 与 Azure Stack 中心进行比较

> 适用于： Azure Stack HCI，版本 20H2;Azure Stack 中心

当你的组织进行数字化变革时，你可能会发现，使用公有云服务以新式体系结构作为构建基础来更新旧式应用可以加快变革的速度。 但是，出于技术和法规障碍等原因，许多工作负荷必须保留在本地。 使用此表可帮助确定哪种 Microsoft 混合云策略可随时随地提供所需的服务，并为任何位置的工作负荷提供云创新。

| Azure Stack HCI | Azure Stack Hub |
| --------------- | --------------- |
| 相同的技能，熟悉的流程 | 新的技能，创新的流程 |
| 将数据中心连接到 Azure 服务 | 数据中心内的 Azure 服务 |

## <a name="when-to-use-azure-stack-hci"></a>何时使用 Azure Stack HCI

下表比较了 Azure Stack HCI 比 Azure Stack 中心更适合的用例。

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                         |
| ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| 使用 Azure Stack HCI 可以尽量减少远程办公室和分公司的占用空间。 首先，只需两个服务器和 switchless 的备份网络，就能实现最大的简洁性和经济性。 | Azure Stack 集线器至少需要四个服务器和它自己的网络交换机。 |
| 使用 Azure Stack HCI 来虚拟化经典企业应用（如 Exchange、SharePoint 和 SQL Server），并虚拟化 Windows Server 角色，例如文件服务器、DNS、DHCP、IIS 和 Active Directory。 提供对 Hyper-v 功能的无限制访问。| Azure Stack 集线器限制 Hyper-v 预配置和功能集以便与 Azure 保持一致。 | 
| 借助 Azure Stack HCI 可以使用软件定义的基础结构来代替过时的存储设备阵列或网络设备，而无需进行重大的重新改造。 可以直接访问和管理内置的 Hyper-V、存储空间直通和软件定义的网络 (SDN)。 | Azure Stack Hub 不会公开这些基础结构技术。 |

## <a name="when-to-use-azure-stack-hub"></a>何时使用 Azure Stack Hub

下表比较了 Azure Stack 集线器比 Azure Stack HCI 更适合的用例。

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                          |
| ------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Azure Stack HCI 原生不会实施或提供多租户。 | 针对自助式基础结构即服务 (IaaS) 使用 Azure Stack Hub，因为其具有强式隔离，并且可对位于相同位置的多个租户精确地跟踪使用情况和进行费用分摊。 非常适合用于服务提供商和企业私有云。 来自 Azure 市场的模板。 | 
| Azure Stack HCI 不打算在本地运行平台即服务 (PaaS) 服务，尽管它确实包括托管 [Azure Kubernetes 服务](/azure-stack/aks-hci/overview.md)的功能。 | 使用 Azure Stack 集线器开发和运行依赖于 PaaS 服务（如 Web 应用、函数或本地事件中心）的应用。 在 Azure Stack Hub 上运行的这些服务完全与在 Azure 中运行一样，从而提供一致的混合开发和运行时环境。 |
| Azure Stack HCI 原生不包括任何 DevOps 工具。 | 使用 Azure Stack Hub 现代化应用部署和配合 DevOps 做法的操作，这些做法包括基础结构即代码、持续集成和持续部署 (CI/CD)，以及 Azure 一致性 VM 扩展等便利功能。 非常适合用于开发和 DevOps 团队。 |

## <a name="next-steps"></a>后续步骤

- [比较 Azure Stack HCI 和 Windows Server 2019](compare-windows-server.md)
