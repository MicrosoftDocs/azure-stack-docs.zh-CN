---
title: 什么是 Azure Stack？ | Microsoft Docs
description: 了解 Azure Stack 如何使你能够在你的数据中心中运行 Azure 服务。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 05/14/2019
ms.openlocfilehash: b98be2ae02e65fea9356f35f2d2554e57dfb5628
ms.sourcegitcommit: 1655b2ef4d01d69ceeb52bc16f922bdc19cb968d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/15/2019
ms.locfileid: "65706284"
---
# <a name="azure-stack-overview"></a>Azure Stack 概述

Azure Stack 是 Azure 提供了一种方法在本地环境中运行应用程序和数据中心交付 Azure 服务的扩展。 使用一致的云平台，组织可以自信地作出技术决策基于业务需求，而不是由于技术限制的业务决策。

## <a name="why-use-azure-stack"></a>为何使用 Azure Stack？

Azure 提供了丰富的平台，开发人员可以构建现代应用程序。 但是，某些基于云的应用程序也面临例如延迟、 间歇性连接性和法规障碍。 Azure 和 Azure Stack 解锁面向客户的和内部业务线应用程序的新混合云用例：

- **边缘解决方案和断开连接的解决方案**。 通过在 Azure Stack 本地处理数据，然后它在 Azure 中汇集以作进一步分析，这两个常见的应用程序逻辑与满足延迟和连接要求。 甚至可以部署 Azure Stack 断开到 Azure 而无需连接 internet 的连接。 可以看作是生产车间，巡航出厂和我的轴作为示例。

- **满足各种法规要求的云应用程序**。 开发和部署应用程序在 Azure 中，能够完全灵活地在本地部署与 Azure Stack，以满足法规或政策要求，不需要更改代码。 应用程序示例包括全局审核、 财务报告、 外汇交易、 在线游戏和开支报告。

- **本地云应用程序模型**。 使用 Azure 服务、 容器、 无服务器和微服务体系结构来更新和扩展现有应用程序或生成新的。 使用一致的 DevOps 流程跨在云中的 Azure 和 Azure Stack 在本地核心任务关键型应用程序的应用程序现代化提高速度。

Azure Stack，从而使这些应用场景：

- 集成的提供的经验来启动和运行快速的定制 Azure Stack 集成系统的受信任的硬件合作伙伴。 传送之后 Azure Stack 可轻松集成到 System Center Operations Manager 管理包或 Nagios 扩展通过监视的数据中心。

- 对于 Azure 和 Azure Stack 的混合环境，使用 Azure Active Directory (Azure AD) 和利用 Active Directory 联合身份验证服务 (AD FS) 进行灵活的标识管理断开连接部署。 

- Azure 一致的应用程序开发环境，从而最大化开发人员工作效率并启用常见 DevOps 方法在混合环境。

- 从 azure 服务交付使用的本地混合云计算能力。 跨 Azure 和 Azure Stack 部署和运行 Azure IaaS 和 PaaS 服务作为 Azure 中使用的相同管理体验和工具采用常规操作实践。 Microsoft 将持续的 Azure 创新传送到 Azure Stack 中，包括新的 Azure 服务，对现有服务和其他 Azure 应用商店应用程序和映像的更新。

## <a name="azure-stack-architecture"></a>Azure Stack 体系结构
Azure Stack 集成的系统组成的 4-16 服务器生成的机架中受信任的硬件合作伙伴，并直接转到你的数据中心传递。 传送之后解决方案提供程序会使用您要部署集成的系统并确保 Azure Stack 解决方案满足业务需求。 将需要通过确保所有所需的功率和冷却准备你的数据中心，边界连接和其他所需的数据中心集成要求就位。 

> 有关 Azure Stack 数据中心集成体验的详细信息，请参阅[Azure Stack 数据中心集成](azure-stack-customer-journey.md)。

基于行业标准硬件并使用相同的工具已将用于管理 Azure 订阅进行管理的 azure Stack。 因此，无论您连接到 Azure，或不可以将应用一致的 DevOps 流程。 

Azure Stack 体系结构，可提供远程位置或间歇性连接，从 internet 断开连接的上边缘的 Azure 服务。 可以创建混合解决方案处理本地 Azure Stack 中的数据，然后聚合它在 Azure 中进行其他处理和分析。 最后，由于 Azure Stack 是安装在本地，你可以满足特定的法规或策略要求的灵活性的云应用程序在本地部署而无需更改任何代码。 

## <a name="deployment-options"></a>部署选项

### <a name="production-or-evaluation-environments"></a>生产或评估环境
Azure Stack 提供两个部署选项，以满足您的需求，生产环境中使用的 Azure Stack 集成系统和 Azure Stack 开发工具包 (ASDK) 用于评估 Azure Stack 中：

- **Azure Stack 集成系统**。 Azure Stack 集成的系统提供通过 Microsoft 和硬件合作伙伴，创建解决方案的合作关系提供兼顾云时代的创新和计算简化管理。 由于 Azure Stack 作为集成的硬件和软件系统提供，因此你拥有你需要的灵活性和控制，以及从云中创新的能力。 Azure Stack 集成系统的大小范围从 4 到 16 节点，并由硬件合作伙伴和 Microsoft 共同提供支持。 使用 Azure Stack 集成系统可为生产工作负荷创建新方案和部署新解决方案。

- **Azure Stack 开发工具包**。 [Azure Stack 开发工具包 (ASDK)](../asdk/asdk-what-is.md)是免费的单节点部署的可用于评估和了解 Azure Stack 的 Azure Stack。 还可将 ASDK 用作开发人员环境，以使用与 Azure 一致的 API 和工具来构建应用。 但是，ASDK 并不旨在用作生产环境，并具有以下限制与完整的集成的系统生产部署相比：

    - ASDK 只能与单个 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 标识提供者相关联。
    - 由于 Azure Stack 组件部署在一台主机上，可用作租户资源的物理资源有限。 此配置不适用于缩放或性能评估。
    - 由于只有一台主机和 NIC 部署要求方面的原因，网络方案会受到限制。

### <a name="connection-models"></a>连接模型
您可以选择或者部署 Azure Stack**连接**到 internet （和 Azure） 或**断开连接**从它。 此选项定义了哪些选项是可用于标识存储 (Azure AD 或 AD FS) 和计费模型 (才支付你使用基于计费或基于容量的计费)。

> 有关详细信息，请参阅有关的注意事项[连接](azure-stack-connected-deployment.md)并[断开连接](azure-stack-disconnected-deployment.md)部署模型。 

### <a name="identity-provider"></a>标识提供程序 
Azure Stack 使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 提供的标识。 Azure AD 是 Microsoft 的基于云的多租户标识提供者。 使用连接到 internet 的部署使用 Azure AD 作为标识存储的大多数混合方案。 

对于 Azure Stack 的断开连接部署，您需要使用 Active Directory 联合身份验证服务 (AD FS)。 Azure Stack 资源提供程序和其他应用程序工作原理类似与 AD FS 或 Azure AD。 Azure Stack 包含自身的 Active Directory 实例，另外还包含 Active Directory 图形 API。

> [!IMPORTANT]
> 部署后，不能更改标识提供程序。 若要使用不同的标识提供程序，需要重新部署 Azure Stack。

> 了解在 Azure Stack 标识注意事项[的 Azure Stack 的标识概述](azure-stack-identity-overview.md)。

## <a name="how-is-azure-stack-managed"></a>如何管理 Azure Stack？
你可以使用管理门户中，用户门户中，管理 Azure Stack 或[PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)。 每个 Azure Stack 门户由 Azure 资源管理器的单独实例提供支持。 **Azure Stack 操作员**使用管理门户来管理 Azure Stack 和执行操作，例如创建租户产品/服务，并维护集成系统的运行状况和监视状态。 用户门户 （也称为租户门户） 提供的虚拟机、 存储帐户和 web 应用等云资源使用自助服务体验。 

> 有关管理 Azure Stack 使用管理门户的详细信息，请参阅使用[Azure Stack 管理门户快速入门](azure-stack-manage-portals.md)。

Azure Stack 操作员，您可以提供各种服务和应用程序，如[虚拟机](azure-stack-tutorial-tenant-vm.md)， [web 应用程序](azure-stack-app-service-overview.md)的、 高度可用[SQL Server](azure-stack-tutorial-sql.md)，并[MySQL 服务器](azure-stack-tutorial-mysql.md)数据库。 此外可以使用[Azure Stack 快速入门 Azure 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates)部署 SharePoint、 Exchange、 和的详细信息。 

使用管理门户，你可以[配置 Azure Stack，以提供服务](azure-stack-plan-offer-quota-overview.md)到使用计划、 配额、 产品和订阅的租户。 租户用户可以订阅多个产品/服务。 套餐可以包含一个或多个计划，计划可以包含一个或多个服务。 操作员还可以管理容量以及对警报做出响应。 

Azure Stack 配置时， **Azure Stack 用户**（也称为租户） 使用运算符提供的服务。 用户可以预配、监视和管理他们订阅的服务，例如 Web 应用、存储和虚拟机。

> 若要深入了解如何管理 Azure Stack 中，包括使用 where、 典型操作员职责，哪些帐户需要了解的用户，以及如何获取帮助，请查看[Azure Stack 管理基础知识](azure-stack-manage-basics.md)。

## <a name="resource-providers"></a>资源提供程序 
资源提供程序是 web 服务，构成的基础，所有 Azure Stack IaaS 和 PaaS 服务。 Azure 资源管理器依赖于不同的资源提供程序提供对服务的访问。 每个资源提供程序可帮助你配置和控制其各自的资源。 服务管理员还可以添加新的自定义资源提供程序。 

### <a name="foundational-resource-providers"></a>基础资源提供程序 
有三个基本的 IaaS 资源提供程序： 

- **计算**。 计算资源提供程序允许 Azure Stack 租户创建他们自己的虚拟机。 计算资源提供程序包括创建虚拟机以及虚拟机扩展的能力。 虚拟机扩展服务可帮助提供适用于 Windows 和 Linux 虚拟机的 IaaS 功能。 例如，可以使用计算资源提供程序来预配 Linux 虚拟机并在要配置的 VM 部署期间运行 Bash 脚本。
- **网络资源提供程序**。 网络资源提供程序提供了一系列的私有云软件定义网络 (SDN) 和网络功能虚拟化 (NFV) 功能。 网络资源提供程序可用于创建软件负载均衡器、 公共 Ip、 网络安全组和虚拟网络等资源。
- **存储资源提供程序**。 存储资源提供程序提供了四个 Azure 一致的存储服务： [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage)，[队列](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage)，[表](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)，以及[KeyVault](https://docs.microsoft.com/azure/key-vault/)帐户管理提供了管理和审核的机密，如密码和证书。 存储资源提供程序还提供存储云管理服务，用于简化 Azure 一致的存储服务的服务提供程序管理。 Azure 存储可为存储和检索大量非结构化数据提供弹性，例如 Azure Blob 的文档与媒体文件，以及具有 Azure 表的结构化 NoSQL 数据。 

### <a name="optional-resource-providers"></a>可选资源提供程序
有三个可选 PaaS 资源提供程序可以部署和与 Azure Stack 配合使用： 

- **应用服务**。 [Azure Stack 上的 azure 应用服务](azure-stack-app-service-overview.md)是 Microsoft Azure 的平台-作为-服务 (PaaS) 产品可用于 Azure Stack。 该服务可让你的内部或外部客户为任何平台或设备创建 Web 应用、API 应用和 Azure Functions 应用程序。 
- **SQL Server**。 使用[SQL Server 资源提供程序](azure-stack-sql-resource-provider.md)以提供了 SQL 数据库提供作为 Azure Stack 的服务。 安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，你和你的用户可以创建数据库，云原生应用、 网站，使用 SQL，并使用 SQL 的其他工作负荷。
- **MySQL Server**。 使用[MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)作为 Azure Stack 服务公开 MySQL 数据库。 MySQL 资源提供程序以服务的形式在 Windows Server 2016 Server Core 虚拟机 (VM) 上运行。

## <a name="providing-high-availability"></a>提供高可用性
为了在 Azure 中实现多 VM 生产系统的高可用性，可以将 VM 置于横跨多个容错域和更新域的[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)中。 在较小规模的 Azure Stack 中，可用性集中的容错域定义为缩放单元中的单个节点。  

在发生硬件故障时，虽然 Azure Stack 的基础结构已具备故障还原能力，但基础技术（故障转移群集功能）的局限仍会导致受影响物理服务器上的 VM 出现停机。 为了与 Azure 保持一致，Azure Stack 支持的可用性集最多有三个容错域。

- **容错域**。 置于可用性集中的 VM 在物理上是彼此隔离的，换句话说，会尽可能均衡地让其分散到多个容错域（Azure Stack 节点）中。 如果硬件故障，将在其他容错域中，重新启动故障的容错域中的 Vm，但在同一可用性集中，如有可能从其他 Vm 保留在单独的容错域。 当硬件重新联机时，会对 VM 重新进行均衡操作，以维持高可用性。 
 
- **更新域**。 更新域是另一可以在可用性集中提供高可用性的 Azure 概念。 更新域是可以同时维护的基础硬件逻辑组。 同一个更新域中的 VM 会在计划内维护期间一起重启。 当租户在可用性集内创建 VM 时，Azure 平台会自动将 VM 分布到这些更新域。 在 Azure Stack 中，VM 会先跨群集中的其他联机主机进行实时迁移，然后其基础主机才会进行更新。 由于在主机更新期间不会造成租户停机，因此 Azure Stack 上存在更新域功能只是为了确保与 Azure 实现模板兼容。 

## <a name="role-based-access-control"></a>基于角色的访问控制
可以使用角色基于访问控制 (RBAC) 通过分配这些角色的订阅、 资源组或单个资源级别授予对授权的用户、 组和服务的系统访问权限。 每个角色定义了用户、组或服务对 Microsoft Azure Stack 资源拥有的访问级别。

Azure Stack RBAC 有三个适用于所有资源类型的基本角色：所有者、参与者和读者。 “所有者”拥有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。 参与者可以创建和管理所有类型的 Azure 资源，但不能授予对其他人访问权限。 读者只能查看现有资源。 其余的 RBAC 角色允许管理特定的 Azure 资源。 例如，“虚拟机参与者”角色允许创建和管理虚拟机，但不允许管理虚拟机连接到的虚拟网络或子网。

> 请参阅[管理基于角色的访问控制](azure-stack-manage-permissions.md)有关详细信息。 

## <a name="reporting-usage-data"></a>报告使用情况数据
Azure Stack 从所有资源提供程序收集聚合用量数据，并将其传输到 Azure 供 Azure 商业组件进行处理。 可以通过 REST API 查看 Azure Stack 中收集的用量数据。 可以使用 Azure 一致的租户 API 以及提供程序和委派提供程序 API 从所有租户订阅获取使用情况数据。 可以使用这些数据来集成外部工具或服务，以实现计费或费用分摊。 用量经 Azure 商业组件处理后，可以在 Azure 计费门户中查看。

> 详细了解如何[向 Azure 报告 Azure Stack 用量数据](azure-stack-usage-reporting.md)。

## <a name="next-steps"></a>后续步骤

[比较 Azure Stack 与全球 Azure](compare-azure-azure-stack.md)

[管理基础知识](azure-stack-manage-basics.md)

[快速入门： 使用 Azure Stack 管理门户](azure-stack-manage-portals.md)