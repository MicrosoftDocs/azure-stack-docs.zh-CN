---
title: Azure Stack 概述 |Microsoft Docs
description: 概述 Azure Stack 的定义，以及如何使你能够在数据中心内运行 Azure 服务。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/16/2019
ms.author: justinha
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 05/14/2019
ms.openlocfilehash: cd6cfeca83fc94c1a25ef446b43bdee50689acab
ms.sourcegitcommit: acebda8a42ac8ecdeba490fc1738e9041479dab0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72814023"
---
# <a name="azure-stack-overview"></a>Azure Stack 概述

Azure Stack 是 Azure 的扩展，提供了一种方法，可在本地环境中运行应用，并在数据中心提供 Azure 服务。 使用一致的云平台，组织可根据业务需求满怀信心地做出技术决策，而不是基于技术限制做出业务决策。

## <a name="why-use-azure-stack"></a>为什么使用 Azure Stack？

Azure 为开发人员提供了一个丰富的平台，用于构建新式应用程序。 但是，某些基于云的应用面临一些障碍，如延迟、间歇连接和法规。 Azure 和 Azure Stack 为面向客户的和内部业务线应用的新的混合云用例提供了：

- **边缘解决方案和断开连接的解决方案**。 通过在 Azure Stack 中本地处理数据，然后将数据聚合在 Azure 中以进行进一步分析来解决延迟和连接要求，同时在这两种情况下使用常见的应用逻辑。 你甚至可以部署 Azure Stack 从 internet 断开连接，而无需连接到 Azure。 请考虑工厂地面、巡航和

- **满足各种法规要求的云应用**。 在 Azure 中开发和部署应用程序，可以完全灵活地在本地部署 Azure Stack 以满足法规或策略要求。 不需要更改代码。 应用示例包括全球审核、财务报表、外国汇兑交易、在线游戏和费用报告。

- **本地云应用模型**。 使用 Azure 服务、容器、无服务器和微服务体系结构来更新和扩展现有应用程序，或构建新应用程序。 在云中的 Azure 中使用一致的 DevOps 流程，并 Azure Stack 本地以加速核心任务关键型应用程序的应用现代化。

Azure Stack 通过提供以下功能来启用这些使用方案：

- 集成的交付体验，可通过受信任的硬件合作伙伴提供的专门 Azure Stack 集成系统快速启动和运行。 送达后，Azure Stack 通过 System Center Operations Manager 管理包或 Nagios 扩展轻松地集成到数据中心。

- 使用 Azure 的 Azure Active Directory （Azure AD）和 Azure Stack 混合环境灵活地进行标识管理，并为断开连接的部署利用 Active Directory 联合身份验证服务（AD FS）。

- 用于实现开发人员工作效率并跨混合环境启用常见 DevOps 方法的 Azure 一致性应用开发环境。

- 使用混合云计算功能从本地传送 Azure 服务。 在 Azure 中采用常见操作做法，并 Azure Stack 使用与 Azure 相同的管理体验和工具来部署和运行 Azure IaaS （基础结构即服务）和 PaaS （平台即服务）服务。 Microsoft 为 Azure Stack 提供持续的 Azure 创新，包括新的 Azure 服务、现有服务的更新以及其他 Azure Marketplace 应用和映像。

## <a name="azure-stack-architecture"></a>Azure Stack 体系结构

Azure Stack 集成系统包含在由受信任的硬件合作伙伴构建并直接传递到你的数据中心的4-16 服务器机架中。 交付后，解决方案提供商将与你一起部署集成系统，并确保 Azure Stack 解决方案满足你的业务要求。 确保所有必需的电源和冷却、边界连接和其他所需的数据中心集成要求都已准备就绪，从而准备好数据中心。

> 有关 Azure Stack datacenter 集成体验的详细信息，请参阅[Azure Stack 数据中心集成](azure-stack-customer-journey.md)。

Azure Stack 是基于行业标准硬件构建的，并使用已用于管理 Azure 订阅的相同工具进行管理。 因此，无论是否连接到 Azure，都可以应用一致的 DevOps 进程。

利用 Azure Stack 的体系结构，你可以在边缘提供适用于远程位置的 Azure 服务，或从 internet 断开连接的间歇性连接。 你可以创建混合解决方案，用于在 Azure Stack 中本地处理数据，然后将数据聚合到 Azure 中，以便进行额外的处理和分析。 最后，由于 Azure Stack 安装在本地，因此你可以满足特定的法规或策略要求，并灵活地在本地部署云应用，而无需更改任何代码。

## <a name="deployment-options"></a>部署选项

### <a name="production-or-evaluation-environments"></a>生产或评估环境

Azure Stack 提供了两种部署选项来满足你的需求，Azure Stack 用于生产的集成系统，以及用于评估 Azure Stack 的 Azure Stack 开发工具包（ASDK）：

- **Azure Stack 集成系统**： Azure Stack 集成系统通过 Microsoft 和硬件合作伙伴的合作关系提供，并创建可提供云进度的创新和计算管理简易性的解决方案。 由于 Azure Stack 是作为集成的硬件和软件系统提供的，因此你可以灵活地控制你的需求，并且能够从云创新。 Azure Stack 集成系统的大小介于4-16 个节点之间，由硬件合作伙伴和 Microsoft 共同支持。 使用 Azure Stack 集成系统创建新方案并为生产工作负荷部署新解决方案。

- **Azure Stack 开发工具包（ASDK）** ： [ASDK](../asdk/asdk-what-is.md)是一 Azure Stack 种免费的单节点部署，可用于评估和了解 Azure Stack。 你还可以将 ASDK 用作开发人员环境，以使用与 Azure 一致的 Api 和工具来生成应用。 不过，ASDK 不应用作生产环境，并且与完全集成系统生产部署相比，具有以下限制：

    - ASDK 只能与单个 Azure AD 或 AD FS 标识提供程序相关联。
    - 由于 Azure Stack 组件部署在单台主机计算机上，因此，租户资源有有限的可用物理资源。 此配置不用于缩放或性能评估。
    - 由于单一主机和 NIC 部署要求，网络方案受到限制。

### <a name="connection-models"></a>连接模型

你可以选择部署 Azure Stack**连接**到 internet （和 Azure）或与其**断开**连接。 此选择定义可用于标识存储（Azure AD 或 AD FS）和计费模型的选项（使用基于计费或基于容量的计费来支付）。

> 有关详细信息，请参阅[连接](azure-stack-connected-deployment.md)和[断开](azure-stack-disconnected-deployment.md)连接部署模型的注意事项。

### <a name="identity-provider"></a>标识提供者 

Azure Stack 使用 Azure AD 或活动 AD FS 来提供标识。 Azure AD 是 Microsoft 的基于云的多租户标识提供者。 大多数混合方案都使用与 internet 连接的部署，Azure AD 作为标识存储。

对于断开连接的 Azure Stack 部署，需要使用 AD FS。 Azure Stack 的资源提供程序和其他应用的工作方式与 AD FS 或 Azure AD 相同。 Azure Stack 包括其自己的 Active Directory 实例和 Active Directory 图形 API。

> [!IMPORTANT]
> 部署后，无法更改标识提供者。 若要使用其他标识提供程序，需要重新部署 Azure Stack。 有关详细信息，请参阅[Azure Stack 的标识概述](azure-stack-identity-overview.md)Azure Stack 标识注意事项。

## <a name="how-is-azure-stack-managed"></a>Azure Stack 如何管理？

可以通过管理员门户、用户门户或[PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)来管理 Azure Stack。 每个 Azure Stack 门户由 Azure 资源管理器的单独实例提供支持。 **Azure Stack 运算符**使用管理员门户来管理 Azure Stack，并执行一些操作，例如创建租户产品并维护集成系统的运行状况和监视状态。 用户门户为虚拟机（Vm）、存储帐户和 web 应用等云资源提供自助服务体验。

> 有关使用管理员门户管理 Azure Stack 的详细信息，请参阅使用[Azure Stack 管理门户快速入门](azure-stack-manage-portals.md)。

作为 Azure Stack 操作员，你可以提供各种服务和应用，如[vm](azure-stack-tutorial-tenant-vm.md)、 [web 应用](azure-stack-app-service-overview.md)、高可用[SQL Server](azure-stack-tutorial-sql.md)和[MySQL Server](azure-stack-tutorial-mysql.md)数据库。 你还可以使用[Azure Stack 快速入门 Azure 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates)来部署 SharePoint、Exchange 等。

使用管理员门户，你可以[配置 Azure Stack 向](service-plan-offer-subscription-overview.md)使用计划、配额、产品和订阅的租户提供服务。 租户用户可以订阅多个产品/服务。 套餐可以包含一个或多个计划，计划可以包含一个或多个服务。 操作员还管理容量并响应警报。

配置 Azure Stack 时， **Azure Stack 用户**使用该操作员提供的服务。 用户可以预配、监视和管理他们订阅的服务，例如 web 应用、存储和 Vm。

> 若要了解有关管理 Azure Stack 的详细信息，包括使用哪些帐户、典型的操作员责任、告诉用户的内容以及如何获取帮助，请查看[Azure Stack 管理基础知识](azure-stack-manage-basics.md)。

## <a name="resource-providers"></a>资源提供程序

资源提供程序是一种 web 服务，构成所有 Azure Stack IaaS 和 PaaS 服务的基础。 Azure 资源管理器依赖于不同的资源提供程序来提供对服务的访问。 每个资源提供程序都可帮助你配置和控制其各自的资源。 服务管理员还可以添加新的自定义资源提供程序。

### <a name="foundational-resource-providers"></a>基础资源提供程序

有三个基础 IaaS 资源提供程序：

- **计算**：计算资源提供程序允许 Azure Stack 租户创建自己的 vm。 计算资源提供程序包括创建 Vm 和 VM 扩展的能力。 VM 扩展服务有助于提供适用于 Windows 和 Linux Vm 的 IaaS 功能。 例如，你可以使用计算资源提供程序来设置 Linux VM，并在部署过程中运行 Bash 脚本来配置 VM。
- **网络资源提供程序**：网络资源提供程序为私有云提供了一系列软件定义的网络（SDN）和网络功能虚拟化（NFV）功能。 你可以使用网络资源提供程序来创建软件负载均衡器、公共 Ip、网络安全组和虚拟网络等资源。
- **存储资源提供程序**：存储资源提供程序提供了四种 Azure 一致性存储服务： [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage)、[队列](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage)、[表](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)和[Key Vault](https://docs.microsoft.com/azure/key-vault/)帐户管理，提供机密管理和审核，例如密码和证书。 存储资源提供程序还提供了一个存储云管理服务，用于简化 Azure 一致存储服务的服务提供程序管理。 通过 azure 存储空间，可以灵活地存储和检索大量非结构化数据（例如，包含 Azure Blob 的文档和媒体文件）和基于结构的基于 NoSQL 的数据与 Azure 表。

### <a name="optional-resource-providers"></a>可选资源提供程序

有三个可选的 PaaS 资源提供程序，可以使用 Azure Stack 进行部署和使用：

- **应用服务**： [Azure Stack 上的 Azure App Service](azure-stack-app-service-overview.md)是 Microsoft Azure Azure Stack 可用的 PaaS 产品/服务。 此服务使你的内部或外部客户能够为任何平台或设备创建 web、API 和 Azure Functions 应用。
- **SQL Server**：使用[SQL Server 资源提供程序](azure-stack-sql-resource-provider.md)将 SQL 数据库作为 Azure Stack 服务提供。 安装资源提供程序并将其连接到一个或多个 SQL Server 实例之后，你和你的用户可以创建适用于云原生应用的数据库、使用 SQL 的网站，以及使用 SQL 的其他工作负荷。
- **Mysql 服务器**：使用[mysql 服务器资源提供程序](azure-stack-mysql-resource-provider-deploy.md)将 mysql 数据库作为 Azure Stack 服务公开。 MySQL 资源提供程序在 Windows Server 2016 Server Core VM 上以服务的形式运行。

## <a name="providing-high-availability"></a>提供高可用性

若要在 Azure 中实现多 VM 生产系统的高可用性，请将 Vm 放置在可跨多个容错域和更新域传播它们的[可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。 在较小的 Azure Stack 范围内，可用性集中的容错域定义为缩放单位中的单个节点。  

尽管 Azure Stack 的基础结构已经能够应对故障，但如果发生硬件故障，基础技术（故障转移群集）仍会对受影响的物理服务器上的 Vm 产生一定的停机时间。 Azure Stack 支持拥有最多三个容错域的可用性集，以便与 Azure 保持一致。

- **容错域**：放置在可用性集中的 vm 将以物理方式彼此隔离，方法是将它们均匀地分布在多个容错域（Azure Stack 节点）上。 如果发生硬件故障，故障容错域中的 Vm 将在其他容错域中重新启动。 它们将保存在与其他 Vm 不同的容错域中，但可能会保留在同一可用性集中。 当硬件重新联机时，将重新平衡 Vm 以保持高可用性。

- **更新域**：更新域是在可用性集中提供高可用性的另一个 Azure 概念。 更新域是一组可同时进行维护的基础硬件逻辑组。 位于同一更新域中的 Vm 将在计划内维护期间一起重新启动。 当租户在可用性集中创建 Vm 时，Azure 平台会自动将 Vm 分布到这些更新域。 在 Azure Stack 中，Vm 在其基础主机更新之前，会在群集中的其他联机主机之间进行实时迁移。 由于在主机更新期间不会出现任何租户停机时间，因此 Azure Stack 上的 "更新域" 功能仅存在于与 Azure 的模板兼容性。 可用性集中的 Vm 会在门户上显示**0**作为其更新域的编号。

## <a name="role-based-access-control"></a>基于角色的访问控制

你可以使用基于角色的访问控制（RBAC）通过在订阅、资源组或单个资源级别分配角色来授予对授权用户、组和服务的系统访问权限。 每个角色定义了用户、组或服务对 Microsoft Azure Stack 资源拥有的访问级别。

Azure Stack RBAC 有三个适用于所有资源类型的基本角色：所有者、参与者和读者。 “所有者”拥有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。 参与者可以创建和管理所有类型的 Azure 资源，但不能向其他人授予访问权限。 读者只能查看现有资源。 其他 RBAC 角色允许对特定的 Azure 资源进行管理。 例如，虚拟机参与者角色允许创建和管理 Vm，但不允许管理 VM 连接到的虚拟网络或子网。

> 有关详细信息，请参阅[管理基于角色的访问控制](azure-stack-manage-permissions.md)。

## <a name="reporting-usage-data"></a>报告使用情况数据

Azure Stack 收集并聚合所有资源提供程序的使用情况数据，并将其传输到 Azure 以供 Azure 商业处理。 Azure Stack 上收集的使用情况数据可通过 REST API 查看。 有一个 Azure 一致性租户 API 以及提供程序和委托的提供程序 Api，可跨所有租户订阅获取使用情况数据。 可以使用这些数据来集成外部工具或服务，以实现计费或费用分摊。 Azure 商业处理使用后，可在 Azure 计费门户中查看。

> 详细了解[如何将使用情况数据报告给 Azure Azure Stack](azure-stack-usage-reporting.md)。

## <a name="next-steps"></a>后续步骤

[比较 Azure Stack 和全球 Azure](compare-azure-azure-stack.md)。

[管理基础知识](azure-stack-manage-basics.md)。

[快速入门：使用 Azure Stack 管理门户](azure-stack-manage-portals.md)。