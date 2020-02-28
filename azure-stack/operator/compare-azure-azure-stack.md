---
title: 全局 Azure、Azure Stack 集线器、Azure Stack HCI 之间的差异
titleSuffix: Azure Stack Hub
description: 了解全球 Azure、Azure Stack 集线器和 Azure Stack HCI 之间的差异。
author: justinha
ms.topic: overview
ms.date: 05/03/2019
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: b6186a836ac38d94c3b15aa7de45ebc80a8accd1
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696186"
---
# <a name="differences-between-global-azure-azure-stack-hub-and-azure-stack-hci"></a>全局 Azure、Azure Stack 集线器和 Azure Stack HCI 之间的差异

Microsoft 在一个 Azure 生态系统中提供 Azure 和 Azure Stack 中心服务系列。 使用与 Azure 资源管理器相同的应用程序模型、自助服务门户和 Api，提供基于云的功能，无论你的企业使用的是全球 Azure 还是本地资源。

本文介绍全局 Azure、Azure Stack 中心和 Azure Stack HCI 功能之间的差异。 它提供了一些常见方案建议，可帮助你做出最佳选择，以便为你的组织提供基于云的 Microsoft 服务。

![Azure 生态系统概述](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>全球 Azure

Microsoft Azure 是一组不断扩展的云服务，可帮助您的组织满足您的业务挑战。 使用最喜欢的工具和框架，可以自由地在大规模全局网络上生成、管理和部署应用。

全球 Azure 提供在全球54地区提供的100多个服务。 有关全球 Azure 服务的最新列表，请参阅[*可用产品（按区域*](https://azure.microsoft.com/regions/services)）。 Azure 中提供的服务按类别列出，还可以通过预览版公开发布或提供。

有关全球 Azure 服务的详细信息，请参阅[Azure 入门](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1)。

## <a name="azure-stack-hub"></a>Azure Stack 中心

Azure Stack 中心是 Azure 的扩展，可将云计算的灵活性和革新带入本地环境。 Azure Stack 中心部署在本地，可用于提供连接到 internet （和 Azure）或未连接到 internet 的环境中的 Azure 一致服务。 Azure Stack 集线器使用与全球 Azure 相同的基础技术，其中包括基础结构即服务（IaaS）、软件即服务（SaaS）和可选的平台即服务（PaaS）功能的核心组件。 这些功能包括：

- 适用于 Windows 和 Linux 的 Azure Vm
- Azure Web 应用和功能
- Azure Key Vault
- Azure 资源管理器
- Azure 市场
- 容器
- 管理工具（计划、产品/服务、RBAC 等）

Azure Stack 中心的 PaaS 功能是可选的，因为 Azure Stack 中心不是由 Microsoft 运行的，而是由我们的客户操作。 这意味着，如果你已准备好从最终用户抽象底层基础结构和进程，你就可以为要最终用户的任何 PaaS 服务提供服务。 但 Azure Stack 中心包含多个可选的 PaaS 服务提供程序，包括应用服务、SQL 数据库和 MySQL 数据库。 这些资源提供程序以资源提供程序的形式提供，因此它们是多租户准备的、随时间推移而更新的标准 Azure Stack 中心更新、在 Azure Stack 中心门户中可见，并与 Azure Stack 中心完美集成。

除了上面所述的资源提供程序外，还提供了更多的 PaaS 服务，并将其测试为 Azure 资源管理器在 IaaS 中运行的[基于模板的解决方案](https://github.com/Azure/AzureStack-QuickStart-Templates)。 作为 Azure Stack 中心操作员，你可以将它们作为 PaaS 服务提供给你的用户，包括：

- Service Fabric
- Kubernetes 容器服务
- 以太坊区块链
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack-hub"></a>Azure Stack 中心的示例用例：

- 财务建模
- 临床和理赔数据
- IoT 设备分析
- 零售分类优化
- 提供链优化
- 工业 IoT
- 预见性维护
- 智能城市
- 公民参与

了解 Azure Stack 中心[Azure Stack 中心](azure-stack-overview.md)的详细信息。

## <a name="azure-stack-hci"></a>Azure Stack HCI

[AZURE STACK HCI](../hci/overview.md)解决方案使你能够在本地运行虚拟机，并使用超聚合基础结构（HCI）解决方案轻松连接到 Azure。 使用一致的本地 Azure 服务构建并运行云应用，以满足法规或技术要求。 除了在本地运行虚拟化应用外，Azure Stack HCI 还能让你使用 Windows 管理中心来替换和合并老化服务器基础结构，并使用云服务连接到 Azure。

Azure Stack HCI 提供由 Hyper-v 和 Windows Server 2019 软件定义数据中心（SDDC）存储空间直通提供支持的已验证 HCI 解决方案。 Windows 管理中心用于对 Azure 服务进行管理和集成访问，例如：

- Azure 备份
- Azure Site Recovery
- Azure Monitor 和更新

有关可以 Azure Stack HCI 连接到的 Azure 服务的更新列表，请参阅将[Windows Server 连接到 Azure 混合服务](https://docs.microsoft.com/windows-server/azure-hybrid-services/index)。

### <a name="example-use-cases-for-azure-stack-hci"></a>Azure Stack HCI 的示例用例

- 远程或分支机构系统
- 数据中心合并
- 虚拟桌面基础结构
- 业务关键基础结构
- 成本较低的存储
- 云中的高可用性和灾难恢复
- 企业应用程序，如 SQL Server

请访问[AZURE STACK HCI 网站](https://azure.microsoft.com/overview/azure-stack/hci/)，查看 Microsoft 合作伙伴当前提供的70多个 Azure Stack HCI 解决方案。

## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器管理基础知识](azure-stack-manage-basics.md)

[快速入门：使用 Azure Stack 集线器管理门户](azure-stack-manage-portals.md)
