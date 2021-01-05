---
title: 全球 Azure、Azure Stack Hub 与 Azure Stack HCI 之间的差异
titleSuffix: Azure Stack Hub
description: 了解全球 Azure、Azure Stack Hub 与 Azure Stack HCI 之间的差异。
author: PatAltimore
ms.topic: overview
ms.date: 07/10/2020
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 4fc890225801fe35ec4ab72e84777aa56d597035
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868703"
---
# <a name="differences-between-global-azure-azure-stack-hub-and-azure-stack-hci"></a>全局 Azure、Azure Stack Hub 和 Azure Stack HCI 之间的差异

Microsoft 在一个 Azure 生态系统中提供 Azure 和 Azure Stack Hub 系列的服务。 使用与 Azure 资源管理器相同的应用程序模型、自助服务门户和 Api，提供基于云的功能，无论你的企业使用的是全球 Azure 还是本地资源。

本文介绍全局 Azure、Azure Stack 中心和 Azure Stack HCI 功能之间的差异。 它提供了常见的方案建议，帮助你在为组织交付基于 Microsoft 云的服务时做出最佳选择。

![Azure 生态系统概述](./media/compare-azure-azure-stack/azure-family-updated.png)

## <a name="global-azure"></a>全球 Azure

Microsoft Azure 是一组不断扩展的云服务，可帮助您的组织满足您的业务挑战。 它允许你自由使用偏好的工具和框架，在大规模全球网络中构建、管理和部署应用。

全球 Azure 提供在全球54地区提供的100多个服务。 有关全球 Azure 服务的最新列表，请参阅 [*可用产品（按区域*](https://azure.microsoft.com/regions/services)）。 Azure 中提供的服务按类别列出，同时会指出是否已推出正式版，或者以预览版提供。

有关全球 Azure 服务的详细信息，请参阅 [Azure 入门](/azure/?panel=get-started1&pivot=get-started)。

## <a name="azure-stack-hub"></a>Azure Stack Hub

Azure Stack Hub 是 Azure 的扩展，它将云计算的敏捷性和创新引入到了本地环境。 可以使用部署在本地的 Azure Stack Hub 来提供 Azure 一致性服务，这些服务可以连接到 Internet（和 Azure），也可以位于未连接 Internet 的离线环境中。 Azure Stack 集线器使用与全局 Azure 相同的基础技术，其中包括基础结构即服务 (IaaS) 的核心组件、软件即服务 (SaaS) ，以及可选的平台即服务 (PaaS) 功能。 这些功能包括：

- 适用于 Windows 和 Linux 的 Azure VM
- Azure Web 应用和 Functions
- Azure Key Vault
- Azure Resource Manager
- Azure 市场
- 容器
- 管理工具（计划、套餐、RBAC 等）

Azure Stack Hub 的 PaaS 功能是可选的，因为 Azure Stack Hub 不是由 Microsoft 操作，而是由客户操作。 这意味着，如果你已准备好从最终用户抽离底层基础结构和流程，则可以向最终用户提供任何 PaaS 服务。 但是，Azure Stack Hub 包含多个可选的 PaaS 服务提供程序，包括应用服务、SQL 数据库和 MySQL 数据库。 这些产品以资源提供程序的形式交付，因此已经是多租户性质、会通过标准 Azure Stack Hub 更新不断更新、显示在 Azure Stack Hub 门户中，并与 Azure Stack Hub 妥善集成。

除上述资源提供程序以外，还有其他 PaaS 服务可供使用，经测试它们可以用作在 IaaS 中运行的[基于 Azure 资源管理器模板的解决方案](https://github.com/Azure/AzureStack-QuickStart-Templates)。 Azure Stack Hub 操作员可将其作为 PaaS 服务提供给用户，这些服务包括：

- Service Fabric
- Kubernetes 容器服务
- 以太坊区块链
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack-hub"></a>Azure Stack Hub 的示例用例

- 财务建模
- 临床诊断和理赔数据
- IoT 设备分析
- 零售分类优化
- 供应链优化
- 工业 IoT
- 预见性维护
- 智能城市
- 公民参与

若要详细了解 Azure Stack Hub，请参阅[什么是 Azure Stack Hub](azure-stack-overview.md)。

## <a name="azure-stack-hci"></a>Azure Stack HCI

[Azure Stack HCI](../hci/overview.md) 是超融合的群集，可在本地环境使用经过验证的硬件来运行虚拟化 Windows 和 Linux 工作负载，并轻松连接到 Azure 以实现基于云的备份、恢复和监视。 最初基于 Windows Server 2019 的 Azure Stack HCI 现在作为 Azure 服务提供，并内置了基于订阅的许可模式和混合功能。 尽管 Azure Stack HCI 基于与 Windows Server 相同的核心操作系统组件，但它是一个全新的产品系列，专注于成为最佳的虚拟化主机。

Azure Stack HCI 使用 OEM 合作伙伴提供的经 Microsoft 验证的硬件来确保最佳性能和可靠性。 这些解决方案包括对 NVMe 驱动器、永久性内存和远程直接内存访问 (RDMA) 网络等技术的支持。

### <a name="example-use-cases-for-azure-stack-hci"></a>Azure Stack HCI 的示例用例

- 远程或分支机构系统
- 数据中心整合
- 虚拟桌面基础结构
- 业务关键型基础结构
- 低成本存储
- 云中的高可用性和灾难恢复
- 虚拟化企业应用，如 SQL Server

请访问 [Azure Stack HCI 网站](https://azure.microsoft.com/overview/azure-stack/hci/)，以查看 Microsoft 合作伙伴目前提供的 70 多种 Azure Stack HCI 解决方案。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 管理基础知识](azure-stack-manage-basics.md)

[快速入门：使用 Azure Stack Hub 管理门户](azure-stack-manage-portals.md)
