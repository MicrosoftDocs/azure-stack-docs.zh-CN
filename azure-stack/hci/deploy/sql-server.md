---
title: 'Azure Stack HCI (AKS 上部署 SQL Server-HCI) '
description: 本主题提供有关如何在 Azure Stack HCI 上部署 SQL Server 的指导。
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/11/2021
ms.openlocfilehash: 433a9b8b7bf6f57cac936b24d7654b94b8e8c050
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186800"
---
# <a name="deploy-sql-server-on-azure-stack-hci"></a>Azure Stack HCI 上部署 SQL Server

>适用于： Azure Stack HCI，版本 20H2; (所有受支持的版本的 SQL Server) 

本主题提供有关如何在 Azure Stack HCI 操作系统上计划、配置和部署 SQL Server 的指导。 操作系统是一种超聚合基础结构， (HCI) 群集解决方案，该解决方案在混合本地环境中托管虚拟化 Windows 和 Linux 工作负荷及其存储。

## <a name="solution-overview"></a>解决方案概述
Azure Stack HCI 提供高度可用、经济高效、灵活的平台，可运行 SQL Server 和存储空间直通。 Azure Stack HCI 可对大数据执行联机事务处理 (OLTP) 工作负荷、数据仓库和 BI 以及 AI 和高级分析。

对于任务关键型数据库，平台的灵活性尤其重要。 可以在使用 Windows Server 或 Linux 的虚拟机 (Vm) 上运行 SQL Server，这允许你根据需要整合多个数据库工作负荷并将更多 Vm 添加到 Azure Stack HCI 环境。 利用 Azure Stack HCI，还可以将 SQL Server 与 Azure Site Recovery 集成，以便为组织的可靠和安全的数据提供基于云的迁移、还原和保护解决方案。

## <a name="deploy-sql-server"></a>部署 SQL Server
本部分详细介绍了如何获取 Azure Stack HCI 上的 SQL Server 硬件，以及如何使用 Windows 管理中心来管理服务器上的操作系统。 包括有关设置 SQL Server、监视和性能优化以及使用高可用性 (HA) 和 Azure 混合服务的信息。

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>步骤1：从 Azure Stack HCI 目录获取硬件
首先，需要购买硬件。 要执行此操作，最简单的方法是在 [AZURE STACK HCI 目录](https://hcicatalog.azurewebsites.net) 中查找首选的 Microsoft 硬件合作伙伴，并使用预装的 Azure Stack HCI 操作系统购买集成系统。 在目录中，可以进行筛选，以查看针对此类型的工作负荷进行了优化的供应商硬件。

否则，你将需要在你自己的硬件上部署 Azure Stack HCI 操作系统。 有关 Azure Stack HCI 部署选项和安装 Windows 管理中心的详细信息，请参阅 [部署 AZURE STACK HCI 操作系统](./operating-system.md)。

接下来，使用 Windows 管理中心 [创建 AZURE STACK HCI 群集](./create-cluster.md)。

### <a name="step-2-install-sql-server-on-azure-stack-hci"></a>步骤2：在 Azure Stack HCI 上安装 SQL Server
你可以在运行 Windows Server 或 Linux 的 Vm 上安装 SQL Server，具体取决于你的要求。

有关安装 SQL Server 的说明，请参阅：
- [SQL Server 适用于 Windows 的安装指南](/sql/database-engine/install-windows/install-sql-server?preserve-view=true&view=sql-server-ver15)。
- [Linux 上的 SQL Server 的安装指南](/sql/linux/sql-server-linux-setup?preserve-view=true&view=sql-server-ver15)。

### <a name="step-3-monitor-and-performance-tune-sql-server"></a>步骤3：监视和性能优化 SQL Server
Microsoft 提供了一套综合的工具，用于监视 SQL Server 中的事件和优化物理数据库设计。 工具的选择取决于要执行的监视或优化类型。

若要确保 Azure Stack HCI 上 SQL Server 实例的性能和运行状况，请参阅 [性能监视和优化工具](/sql/relational-databases/performance/performance-monitoring-and-tuning-tools?preserve-view=true&view=sql-server-ver15)。

若要优化 SQL Server 2017 和 SQL Server 2016，请参阅 [SQL Server 2017 和2016与高性能工作负荷的建议更新和配置选项](https://support.microsoft.com/help/4465518/recommended-updates-and-configurations-for-sql-server)。

### <a name="step-4-use-sql-server-high-availability-features"></a>步骤4：使用 SQL Server 高可用性功能
Azure Stack HCI 利用 [Windows Server 故障转移群集与 SQL Server](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) (WSFC) 来支持在发生硬件故障时在 vm 中运行的 SQL Server。 SQL Server 还提供了 [Always On 可用性组](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) (AG) ，以提供用于帮助实现应用程序和软件故障的数据库级高可用性。 除了 WSFC 和 AG 外，Azure Stack HCI 还可以 (FCI) 使用 [Always On 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server) ，该实例基于用于共享存储的 [存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 技术。

这些选项都适用于适用于仲裁控制的 Microsoft Azure [云见证](/windows-server/failover-clustering/deploy-cloud-witness) 。 对于放置在不同物理节点上的 Vm，建议使用 WSFC 中的群集 [AntiAffinity](/windows-server/failover-clustering/cluster-affinity) 规则，以便在配置 Always On 可用性组时，在发生主机故障时，为 SQL Server 维护运行时间。

### <a name="step-5-set-up-azure-hybrid-services"></a>步骤5：设置 Azure 混合服务
可以使用多个 Azure 混合服务来帮助保持 SQL Server 的数据和应用程序的安全。 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 是一种灾难恢复即服务 (DRaaS) 。 若要详细了解如何使用此服务来保护应用程序的 SQL Server 后端，以帮助使工作负载保持联机，请参阅 [为 SQL Server 设置灾难恢复](/azure/site-recovery/site-recovery-sql)。

[Azure 备份](https://azure.microsoft.com/services/backup/) 允许你定义备份策略来保护企业工作负荷，并支持 SQL Server 一致性来备份和还原。 有关如何备份本地 SQL 数据的详细信息，请参阅 [Install Azure 备份服务器](/azure/backup/backup-azure-microsoft-azure-backup)。

或者，你可以使用 SQL Server 中的 [SQL Server 托管备份](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?preserve-view=true&view=sql-server-ver15) 功能来管理 Azure Blob 存储备份。

有关将此选项用于非现场存档的详细信息，请参阅： 

- [教程：将 Azure Blob 存储服务用于 SQL Server 2016](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016?preserve-view=true&view=sql-server-ver15)
- [快速入门：将 SQL 备份和还原到 Azure Blob 存储服务](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service?preserve-view=true&tabs=SSMS&view=sql-server-ver15)

除了这些备份方案之外，还可以设置 SQL Server 提供的其他数据库服务，包括 [Azure 数据工厂](/azure/machine-learning/team-data-science-process/move-sql-azure-adf) 和 [azure 功能包，以便 Integration Services (SSIS) ](/sql/integration-services/azure-feature-pack-for-integration-services-ssis?preserve-view=true&view=sql-server-ver15)。

## <a name="next-steps"></a>后续步骤
有关使用 SQL Server 的详细信息，请参阅：
- [教程：数据库引擎入门](/sql/relational-databases/tutorial-getting-started-with-the-database-engine?preserve-view=true&view=sql-server-ver15)