---
title: Azure Stack 中心概述 |Microsoft Docs
description: 概述 Azure Stack 集线器的定义，以及如何使你能够在数据中心内运行 Azure 服务。
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
ms.openlocfilehash: 9461b7c306eac0c2a1467378a49d4e91e234a629
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595423"
---
# <a name="azure-stack-hub-overview"></a>Azure Stack 集线器概述

Azure Stack 中心是 Azure 的扩展，它提供了一种方法，可在本地环境中运行应用，并在数据中心提供 Azure 服务。 借助一致性的云平台，组织可以根据业务要求而不是技术局限性自信地做出技术决策。

## <a name="why-use-azure-stack-hub-"></a>为什么要使用 Azure Stack 中心？

Azure 为开发人员提供了一个丰富的平台，用于构建新式应用程序。 但是，某些基于云的应用面临一些障碍，如延迟、间歇连接和法规。 Azure 和 Azure Stack 中心为面向客户的客户和内部业务线应用，解锁新的混合云用例：

- **边缘解决方案和断开连接的解决方案**。 通过在 Azure Stack 集线器中本地处理数据，然后在 Azure 中将数据聚合在 Azure 中以进行进一步分析，从而满足延迟和连接要求，这两者都具有常见的应用逻辑。 甚至可以部署从 internet 断开连接的 Azure Stack 集线器，无需连接到 Azure。 示例环境包括工厂车间、游轮和矿井。

- **满足各种法规要求的云应用**。 在 Azure 中开发和部署应用程序，可以完全灵活地在本地部署 Azure Stack 集线器，以满足法规或策略要求。 不需要更改代码。 应用示例包括全球审核、财务报表、外国汇兑交易、在线游戏和费用报告。

- **本地云应用模型**。 使用 Azure 服务、容器、无服务器和微服务体系结构来更新和扩展现有应用程序，或构建新应用程序。 在云中的 Azure 中使用一致的 DevOps 流程，并在本地 Azure Stack 中心，加速核心任务关键型应用的应用现代化。

Azure Stack 中心通过提供以下功能来启用这些使用方案：

- 集成的交付体验，可通过受信任的硬件合作伙伴提供的专门构建 Azure Stack 集线器集成系统快速启动和运行。 交付后，Azure Stack 集线器通过 System Center Operations Manager 管理包或 Nagios 扩展轻松地集成到数据中心。

- 使用 Azure 的 Azure Active Directory （Azure AD）和 Azure Stack 集线器混合环境灵活地进行标识管理，并为断开连接的部署利用 Active Directory 联合身份验证服务（AD FS）。

- 用于实现开发人员工作效率并跨混合环境启用常见 DevOps 方法的 Azure 一致性应用开发环境。

- 使用混合云计算能力从本地交付 Azure 服务。 跨 Azure 和 Azure Stack 中心采用常见的操作做法，以使用与 Azure 相同的管理体验和工具来部署和运行 Azure IaaS （基础结构即服务）和 PaaS （平台即服务）服务。 Microsoft 为 Azure Stack 中心提供持续的 Azure 创新，包括新的 Azure 服务、现有服务的更新以及其他 Azure Marketplace 应用和映像。

## <a name="azure-stack-hub-architecture"></a>Azure Stack 中心体系结构

Azure Stack 集线器集成系统包含在由受信任的硬件合作伙伴构建并直接传递到你的数据中心的4-16 服务器机架中。 交付后，解决方案提供商将与你一起部署集成系统，并确保 Azure Stack 集线器解决方案满足你的业务要求。 确保所有必需的电源和冷却、边界连接和其他所需的数据中心集成要求都已准备就绪，从而准备好数据中心。

> 有关 Azure Stack 中心数据中心集成体验的详细信息，请参阅[Azure Stack 中心数据中心集成](azure-stack-customer-journey.md)。

Azure Stack 集线器构建在行业标准硬件上，并使用已用于管理 Azure 订阅的相同工具进行管理。 因此，无论是否连接到 Azure，都可以应用一致的 DevOps 进程。

利用 Azure Stack 集线器体系结构，你可以在边缘为远程位置或间歇连接提供 Azure 服务，与 internet 断开连接。 你可以创建混合解决方案，用于在 Azure Stack 中心本地处理数据，然后在 Azure 中将其聚合起来，以便进行额外的处理和分析。 最后，由于 Azure Stack 集线器是在本地安装的，因此你可以满足特定的法规或策略要求，并灵活地在本地部署云应用，而无需更改任何代码。

## <a name="deployment-options"></a>部署选项

### <a name="production-or-evaluation-environments"></a>生产或评估环境

Azure Stack 中心提供两种部署选项，可满足你的需求、Azure Stack 用于生产的中心集成系统，以及用于评估 Azure Stack 中心的 Azure Stack 中心开发工具包（ASDK）：

- **Azure Stack 集线器集成系统**：通过 Microsoft 和硬件合作伙伴的合作关系提供 Azure Stack 中心集成系统，从而创建一个解决方案，该解决方案可提供云进度的创新和计算管理简易性。 由于 Azure Stack 集线器作为集成的硬件和软件系统提供，因此您可以灵活地控制您的需求，并且能够从云创新。 Azure Stack 集线器集成系统的大小介于4-16 个节点之间，由硬件合作伙伴和 Microsoft 共同支持。 使用 Azure Stack 集线器集成系统创建新方案并为生产工作负荷部署新的解决方案。

- **Azure Stack 集线器开发工具包（ASDK）** ： [ASDK](../asdk/asdk-what-is.md)是一种免费 Azure Stack 的单节点部署，可用于评估和了解 Azure Stack 中心。 还可将 ASDK 用作开发人员环境，以使用与 Azure 一致的 API 和工具来构建应用。 但是，ASDK 不可用作生产环境，与完全集成式系统生产部署相比，它存在以下限制：

    - ASDK 只能与单个 Azure AD 或 AD FS 标识提供程序相关联。
    - 由于 Azure Stack 集线器组件部署在单台主机计算机上，因此，租户资源有有限的可用物理资源。 此配置不用于缩放或性能评估。
    - 由于只有一台主机和 NIC 部署要求方面的原因，网络方案会受到限制。

### <a name="connection-models"></a>连接模型

你可以选择部署**连接**到 internet （和到 Azure）或与其**断开**连接 Azure Stack 集线器。 此选择定义可用于标识存储（Azure AD 或 AD FS）和计费模型的选项（使用基于计费或基于容量的计费来支付）。

> 有关详细信息，请参阅有关[联网](azure-stack-connected-deployment.md)和[离线](azure-stack-disconnected-deployment.md)部署模型的注意事项。

### <a name="identity-provider"></a>标识提供者 

Azure Stack 中心使用 Azure AD 或活动 AD FS 来提供标识。 Azure AD 是 Microsoft 的基于云的多租户标识提供者。 使用 Internet 联网部署的大多数混合方案都使用 Azure AD 作为标识存储。

对于 Azure Stack 集线器的断开连接部署，需要使用 AD FS。 Azure Stack 中心资源提供程序和其他应用的工作方式与 AD FS 或 Azure AD 相同。 Azure Stack 中心包含自己的 Active Directory 实例和 Active Directory 图形 API。

> [!IMPORTANT]
> 部署后，无法更改标识提供者。 若要使用其他标识提供程序，需要重新部署 Azure Stack 中心。 有关详细信息，请参阅[Azure Stack 中心的标识概述](azure-stack-identity-overview.md)Azure Stack 集线器标识注意事项。

## <a name="how-is-azure-stack-hub-managed"></a>如何管理 Azure Stack 中心？

可以通过管理员门户、用户门户或[PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)来管理 Azure Stack 中心。 Azure Stack 中心门户分别由 Azure 资源管理器的单独实例支持。 **Azure Stack 中心操作员**使用管理员门户来管理 Azure Stack 中心，并执行一些操作，例如创建租户产品并维护集成系统的运行状况和监视状态。 用户门户为虚拟机（Vm）、存储帐户和 web 应用等云资源提供自助服务体验。

> 有关使用管理员门户管理 Azure Stack 集线器的详细信息，请参阅使用[Azure Stack 中心管理门户快速入门](azure-stack-manage-portals.md)。

作为 Azure Stack 中心运营商，你可以提供各种服务和应用，如[vm](azure-stack-tutorial-tenant-vm.md)、 [web 应用](azure-stack-app-service-overview.md)、高可用[SQL Server](azure-stack-tutorial-sql.md)和[MySQL Server](azure-stack-tutorial-mysql.md)数据库。 你还可以使用[Azure Stack 集线器快速入门 Azure 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates)来部署 SharePoint、Exchange 等。

使用管理员门户，你可以[将 Azure Stack 中心配置为](service-plan-offer-subscription-overview.md)使用计划、配额、产品和订阅向租户提供服务。 租户用户可以订阅多个套餐。 套餐可以包含一个或多个计划，计划可以包含一个或多个服务。 操作员还可以管理容量以及对警报做出响应。

配置 Azure Stack 集线器时， **Azure Stack 中心用户**使用操作员提供的服务。 用户可以预配、监视和管理他们订阅的服务，例如 web 应用、存储和 Vm。

> 若要了解有关管理 Azure Stack 中心的详细信息，包括要使用的帐户、操作方法、要告诉用户的信息以及如何获取帮助，请查看[Azure Stack 中心管理基础知识](azure-stack-manage-basics.md)。

## <a name="resource-providers"></a>资源提供程序

资源提供程序是一种 web 服务，构成所有 Azure Stack 中心 IaaS 和 PaaS 服务的基础。 Azure 资源管理器依靠不同的资源提供程序提供对服务的访问。 每个资源提供程序可帮助你配置和控制其相应资源。 服务管理员还可以添加新的自定义资源提供程序。

### <a name="foundational-resource-providers"></a>基础资源提供程序

有三个基础 IaaS 资源提供程序：

- **计算**：计算资源提供程序允许 Azure Stack 中心租户创建自己的 vm。 计算资源提供程序包括创建 Vm 和 VM 扩展的能力。 VM 扩展服务有助于提供适用于 Windows 和 Linux Vm 的 IaaS 功能。 例如，你可以使用计算资源提供程序来设置 Linux VM，并在部署过程中运行 Bash 脚本来配置 VM。
- **网络资源提供程序**：网络资源提供程序为私有云提供了一系列软件定义的网络（SDN）和网络功能虚拟化（NFV）功能。 可以使用网络资源提供程序创建软件负载均衡器、公共 IP、网络安全组和虚拟网络等资源。
- **存储资源提供程序**：存储资源提供程序提供了四种 Azure 一致性存储服务： [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage)、[队列](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage)、[表](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)和[Key Vault](https://docs.microsoft.com/azure/key-vault/)帐户管理，提供机密管理和审核，例如密码和证书。 存储资源提供程序还提供了一个存储云管理服务，用于简化 Azure 一致存储服务的服务提供程序管理。 通过 azure 存储空间，可以灵活地存储和检索大量非结构化数据（例如，包含 Azure Blob 的文档和媒体文件）和基于结构的基于 NoSQL 的数据与 Azure 表。

### <a name="optional-resource-providers"></a>可选的资源提供程序

有三个可选的 PaaS 资源提供程序可与 Azure Stack 中心一起部署和使用：

- **应用服务**： [Azure Stack 中心的 Azure App Service](azure-stack-app-service-overview.md)是 Microsoft Azure 可用于 Azure Stack 中心的 PaaS 产品/服务。 该服务可让你的内部或外部客户为任何平台或设备创建 Web 应用、API 应用和 Azure Functions 应用。
- **SQL Server**：使用[SQL Server 资源提供程序](azure-stack-sql-resource-provider.md)将 SQL 数据库作为 Azure Stack 中心的服务提供。 安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，你和你的用户可以创建云原生应用的数据库、使用 SQL 的网站，以及使用 SQL 的其他工作负荷。
- **Mysql 服务器**：使用[mysql 服务器资源提供程序](azure-stack-mysql-resource-provider-deploy.md)将 Mysql 数据库作为 Azure Stack 中心服务公开。 MySQL 资源提供程序在 Windows Server 2016 Server Core VM 上以服务的形式运行。

## <a name="providing-high-availability"></a>提供高可用性

为了在 Azure 中实现多 VM 生产系统的高可用性，可以将 VM 置于横跨多个容错域和更新域的[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)中。 在较小的 Azure Stack 中心范围内，可用性集中的容错域定义为缩放单位中的单个节点。  

尽管 Azure Stack 中心的基础结构已对故障有弹性，但如果出现硬件故障，基础技术（故障转移群集）仍会对受影响的物理服务器上的 Vm 产生一些停机时间。 Azure Stack 中心支持拥有最多三个容错域的可用性集，以便与 Azure 保持一致。

- **容错域**：放置在可用性集中的 vm 将以物理方式彼此隔离，方法是将它们均匀地分布在多个容错域（Azure Stack 中心节点）上。 如果发生硬件故障，故障容错域中的 Vm 将在其他容错域中重新启动。 它们将保存在与其他 Vm 不同的容错域中，但可能会保留在同一可用性集中。 当硬件重新联机时，会对 VM 重新进行均衡操作，以维持高可用性。

- **更新域**：更新域是在可用性集中提供高可用性的另一个 Azure 概念。 更新域是可以同时维护的基础硬件逻辑组。 同一个更新域中的 VM 会在计划内维护期间一起重启。 当租户在可用性集内创建 VM 时，Azure 平台会自动将 VM 分布到这些更新域。 在 Azure Stack 集线器中，Vm 在其基础主机更新之前，会在群集中的其他联机主机之间进行实时迁移。 由于在主机更新期间不会出现任何租户停机时间，因此，Azure Stack 集线器上的更新域功能只存在于与 Azure 的模板兼容性。 可用性集中的 VM 将显示 **0** 作为其在门户上的更新域编号。

## <a name="role-based-access-control"></a>基于角色的访问控制

可以使用基于角色的访问控制 (RBAC) 向已获授权的用户、组和服务授予系统访问权限：在订阅、资源组或单个资源级别为其分配角色即可。 每个角色定义了用户、组或服务对 Microsoft Azure Stack 中心资源的访问级别。

Azure Stack 中心 RBAC 有三个适用于所有资源类型的基本角色：所有者、参与者和读者。 “所有者”拥有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。 参与者可以创建和管理所有类型的 Azure 资源，但不能将访问权限授予其他用户。 “读取者”只能查看现有资源。 其他 RBAC 角色允许对特定的 Azure 资源进行管理。 例如，虚拟机参与者角色允许创建和管理 Vm，但不允许管理 VM 连接到的虚拟网络或子网。

> 有关详细信息，请参阅[管理基于角色的访问控制](azure-stack-manage-permissions.md)。

## <a name="reporting-usage-data"></a>报告使用情况数据

Azure Stack 中心收集并聚合所有资源提供程序的使用情况数据，并将其传输到 Azure 以供 Azure 商业处理。 Azure Stack 中心收集的使用情况数据可通过 REST API 查看。 有一个 Azure 一致性租户 API 以及提供程序和委托的提供程序 Api，可跨所有租户订阅获取使用情况数据。 可以使用这些数据来集成外部工具或服务，以实现计费或费用分摊。 用量经 Azure 商业组件处理后，可以在 Azure 计费门户中查看。

> 详细了解[如何将 Azure Stack 集线器使用情况数据报告给 Azure](azure-stack-usage-reporting.md)。

## <a name="next-steps"></a>后续步骤

[比较 Azure Stack 中心与全局 Azure](compare-azure-azure-stack.md)。

[管理基础知识](azure-stack-manage-basics.md)。

[快速入门：使用 Azure Stack 集线器管理门户](azure-stack-manage-portals.md)。
