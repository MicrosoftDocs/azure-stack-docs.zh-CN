---
title: 什么是 Azure Stack？ | Microsoft Docs
description: 了解如何使用 Azure Stack 在数据中心内运行 Azure 服务。
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
ms.date: 03/29/2019
ms.author: justinha
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 05/14/2019
ms.openlocfilehash: 1aa6f494320843cf1debe2c1887f353bf79a7303
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268356"
---
# <a name="azure-stack-overview"></a>Azure Stack 概述

Azure Stack 是 Azure 的一个扩展，可用于在本地环境中运行应用程序，以及在数据中心内交付 Azure 服务。 借助一致性的云平台，组织可以根据业务要求而不是技术局限性自信地做出技术决策。

## <a name="why-use-azure-stack"></a>为何要使用 Azure Stack？

Azure 为开发人员提供多功能的平台用于生成新型应用程序。 但是，某些基于云的应用程序面临着延迟、间歇性连接和法规监管等方面的阻碍。 Azure 和 Azure Stack 为面向客户的应用程序和内部业务线应用程序开启了新的混合云用例：

- **边缘解决方案和断开连接的解决方案**。 在 Azure Stack 本地处理数据，然后在 Azure 中聚合以作进一步分析，并在两者之间使用共同的应用程序逻辑，以此满足延迟和连接要求。 甚至可以在断开 Internet 连接且不与 Azure 建立连接的情况下部署 Azure Stack。 示例环境包括工厂车间、游轮和矿井。

- **满足各种法规要求的云应用程序**。 可在 Azure 中开发和部署应用程序，并能够完全灵活地在 Azure Stack 本地进行部署，以满足法规或政策要求，无需更改任何代码。 应用示例包括全球审计、财务报告、外汇交易、在线游戏和费用报告。

- **本地云应用程序模型**。 使用 Azure 服务、容器、无服务器体系结构和微服务体系结构来更新和扩展现有应用程序或生成新的应用程序。 在云中 Azure 和本地 Azure Stack 之间使用一致的 DevOps 流程，以加速任务关键型核心应用程序的现代化。

Azure Stack 通过以下功能来实现这些使用方案：

- 集成式交付体验，通过受信任硬件合作伙伴提供的定制 Azure Stack 集成式系统快速开始正常运营。 交付后，Azure Stack 可轻松集成到数据中心，并通过 System Center Operations Manager 管理包或 Nagios 扩展提供监视功能。

- 使用适用于 Azure 和 Azure Stack 混合环境的 Azure Active Directory (Azure AD) 和适用于离线部署的 Active Directory 联合身份验证服务 (AD FS) 进行灵活的标识管理。 

- Azure 一致性应用程序开发环境，可以最大程度地提高开发人员的工作效率，并支持在整个混合环境中使用通用的 DevOps 方法。

- 使用混合云计算能力从本地交付 Azure 服务。 在整个 Azure 和 Azure Stack 中采用通用的操作实践，使用与 Azure 相同的管理体验和工具来部署和操作 Azure IaaS 与 PaaS 服务。 Microsoft 将持续的 Azure 创新传送到 Azure Stack 中，包括新的 Azure 服务，对现有服务和其他 Azure 应用商店应用程序和映像的更新。

## <a name="azure-stack-architecture"></a>Azure Stack 体系结构
Azure Stack 集成式系统包括受信任的硬件合作伙伴制造的、由 4-16 台服务器构成的机架，将直接交付到你的数据中心。 交付后，解决方案提供商将与你合作部署该集成式系统，并确保 Azure Stack 解决方案符合你的业务要求。 你需要确保已准备好所有必要的电源和散热设备、边界连接，并满足其他必要的数据中心集成要求，以此准备好自己的数据中心。 

> 有关 Azure Stack 数据中心集成体验的详细信息，请参阅 [Azure Stack 数据中心集成](azure-stack-customer-journey.md)。

Azure Stack 建立在行业标准的硬件基础之上，使用管理 Azure 订阅所用的相同工具进行管理。 因此，无论是否已连接到 Azure，都能够应用一致的 DevOps 流程。 

Azure Stack 体系结构允许在远程位置的边缘，或者在间歇性连接、与 Internet 断开连接的情况下提供 Azure 服务。 可以创建混合解决方案用于处理 Azure Stack 本地的数据，然后在 Azure 中聚合这些数据，以进行额外的处理和分析。 最后，由于 Azure Stack 安装在本地，你可以满足具体的法规或政策要求，并可以在本地灵活部署云应用程序，而无需更改任何代码。 

## <a name="deployment-options"></a>部署选项

### <a name="production-or-evaluation-environments"></a>生产或评估环境
Azure Stack 提供两个部署选项来满足需求 - 用于生产的 Azure Stack 集成式系统，以及用于评估 Azure Stack 的 Azure Stack 开发工具包 (ASDK)：

- **Azure Stack 集成系统**。 Azure Stack 集成的系统提供通过 Microsoft 和硬件合作伙伴，创建解决方案的合作关系提供兼顾云时代的创新和计算简化管理。 由于 Azure Stack 作为集成的硬件和软件系统提供，因此你拥有你需要的灵活性和控制，以及从云中创新的能力。 Azure Stack 集成系统的大小范围从 4 到 16 节点，并由硬件合作伙伴和 Microsoft 共同提供支持。 使用 Azure Stack 集成系统可为生产工作负荷创建新方案和部署新解决方案。

- **Azure Stack 开发工具包**。 [Azure Stack 开发工具包 (ASDK)](../asdk/asdk-what-is.md) 是 Azure Stack 的免费单节点部署，可用于评估和了解 Azure Stack。 还可将 ASDK 用作开发人员环境，以使用与 Azure 一致的 API 和工具来构建应用。 但是，ASDK 不可用作生产环境，与完全集成式系统生产部署相比，它存在以下限制：

    - ASDK 只能与单个 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 标识提供者相关联。
    - 由于 Azure Stack 组件部署在一台主机上，可用作租户资源的物理资源有限。 此配置不适用于缩放或性能评估。
    - 由于只有一台主机和 NIC 部署要求方面的原因，网络方案会受到限制。

### <a name="connection-models"></a>连接模型
可以选择在**已连接**到 Internet（和 Azure）时或者与之**断开连接**时部署 Azure Stack。 此选项定义了哪些选项是可用于标识存储 (Azure AD 或 AD FS) 和计费模型 (才支付你使用基于计费或基于容量的计费)。

> 有关详细信息，请参阅有关[联网](azure-stack-connected-deployment.md)和[离线](azure-stack-disconnected-deployment.md)部署模型的注意事项。 

### <a name="identity-provider"></a>标识提供程序 
Azure Stack 使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 提供的标识。 Azure AD 是 Microsoft 的基于云的多租户标识提供者。 使用 Internet 联网部署的大多数混合方案都使用 Azure AD 作为标识存储。 

对于 Azure Stack 的断开连接部署，您需要使用 Active Directory 联合身份验证服务 (AD FS)。 Azure Stack 资源提供程序和其他应用程序工作原理类似与 AD FS 或 Azure AD。 Azure Stack 包含自身的 Active Directory 实例，另外还包含 Active Directory 图形 API。

> [!IMPORTANT]
> 部署后，不能更改标识提供程序。 若要使用不同的标识提供程序，需要重新部署 Azure Stack。

> 可以在 [Azure Stack 的标识概述](azure-stack-identity-overview.md)中详细了解 Azure Stack 标识注意事项。

## <a name="how-is-azure-stack-managed"></a>如何管理 Azure Stack？
你可以使用管理门户中，用户门户中，管理 Azure Stack 或[PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)。 每个 Azure Stack 门户由 Azure 资源管理器的单独实例提供支持。 **Azure Stack 操作员**可以使用管理门户来管理 Azure Stack，以及执行如下所述的操作：创建租户套餐，保持集成式系统的运行状况及监视其状态。 用户门户 （也称为租户门户） 提供的虚拟机、 存储帐户和 web 应用等云资源使用自助服务体验。 

> 有关使用管理门户管理 Azure Stack 的详细信息，请参阅 [Azure Stack 管理门户快速入门](azure-stack-manage-portals.md)。

Azure Stack 操作员，您可以提供各种服务和应用程序，如[虚拟机](azure-stack-tutorial-tenant-vm.md)， [web 应用程序](azure-stack-app-service-overview.md)的、 高度可用[SQL Server](azure-stack-tutorial-sql.md)，并[MySQL 服务器](azure-stack-tutorial-mysql.md)数据库。 他们还可以使用 [Azure Stack 快速入门 Azure 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates)来部署 SharePoint、Exchange 等。 

在管理门户中，可以使用计划、配额、套餐和订阅来[配置 Azure Stack，以将服务交付给租户](azure-stack-plan-offer-quota-overview.md)。 租户用户可以订阅多个套餐。 套餐可以包含一个或多个计划，计划可以包含一个或多个服务。 操作员还可以管理容量以及对警报做出响应。 

配置 Azure Stack 后，**Azure Stack 用户**（也称为租户）可以使用操作员提供的服务。 用户可以预配、监视和管理他们订阅的服务，例如 Web 应用、存储和虚拟机。

> 若要详细了解如何管理 Azure Stack，包括在何处使用哪些帐户、典型的操作员职责、要向用户告知哪些信息，以及如何取得帮助，请查看 [Azure Stack 管理基础知识](azure-stack-manage-basics.md)。

## <a name="resource-providers"></a>资源提供程序 
资源提供程序属于 Web 服务，构成了所有 Azure Stack IaaS 和 PaaS 服务的基础。 Azure 资源管理器依靠不同的资源提供程序提供对服务的访问。 每个资源提供程序可帮助你配置和控制其相应资源。 服务管理员还可以添加新的自定义资源提供程序。 

### <a name="foundational-resource-providers"></a>基础资源提供程序 
有三个基础 IaaS 资源提供程序： 

- **计算**。 Azure Stack 租户可以使用计算资源提供程序创建自己的虚拟机。 计算资源提供程序包括创建虚拟机和虚拟机扩展的功能。 虚拟机扩展服务可帮助提供适用于 Windows 和 Linux 虚拟机的 IaaS 功能。 例如，可以使用计算资源提供程序预配一个 Linux 虚拟机，并在部署期间运行 Bash 脚本来配置该 VM。
- **网络资源提供程序**。 网络资源提供程序为私有云提供了一系列软件定义的网络 (SDN) 和网络功能虚拟化 (NFV) 功能。 可以使用网络资源提供程序创建软件负载均衡器、公共 IP、网络安全组和虚拟网络等资源。
- **存储资源提供程序**。 存储资源提供程序提供了四个 Azure 一致的存储服务： [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage)，[队列](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage)，[表](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)，以及[KeyVault](https://docs.microsoft.com/azure/key-vault/)帐户管理提供了管理和审核的机密，如密码和证书。 存储资源提供程序还提供存储云管理服务，用于简化 Azure 一致性存储服务的服务提供程序管理。 Azure 存储可为存储和检索大量非结构化数据提供弹性，例如 Azure Blob 的文档与媒体文件，以及具有 Azure 表的结构化 NoSQL 数据。 

### <a name="optional-resource-providers"></a>可选的资源提供程序
在 Azure Stack 中可以部署和使用三个可选的 PaaS 资源提供程序： 

- **应用服务**。 [Azure Stack 上的 azure 应用服务](azure-stack-app-service-overview.md)是 Microsoft Azure 的平台-作为-服务 (PaaS) 产品可用于 Azure Stack。 该服务可让你的内部或外部客户为任何平台或设备创建 Web 应用、API 应用和 Azure Functions 应用程序。 
- **SQL Server**。 使用 [SQL Server 资源提供程序](azure-stack-sql-resource-provider.md)将 SQL 数据库作为 Azure Stack 的一项服务提供。 安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，你和你的用户可以创建数据库，云原生应用、 网站，使用 SQL，并使用 SQL 的其他工作负荷。
- **MySQL Server**。 可以使用 [MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)将 MySQL 数据库公开为 Azure Stack 服务。 MySQL 资源提供程序以服务的形式在 Windows Server 2016 Server Core 虚拟机 (VM) 上运行。

## <a name="providing-high-availability"></a>提供高可用性
为了在 Azure 中实现多 VM 生产系统的高可用性，可以将 VM 置于横跨多个容错域和更新域的[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)中。 在较小规模的 Azure Stack 中，可用性集中的容错域定义为缩放单元中的单个节点。  

在发生硬件故障时，虽然 Azure Stack 的基础结构已具备故障还原能力，但基础技术（故障转移群集功能）的局限仍会导致受影响物理服务器上的 VM 出现停机。 为了与 Azure 保持一致，Azure Stack 支持的可用性集最多有三个容错域。

- **容错域**。 置于可用性集中的 VM 在物理上是彼此隔离的，换句话说，会尽可能均衡地让其分散到多个容错域（Azure Stack 节点）中。 如果硬件故障，将在其他容错域中，重新启动故障的容错域中的 Vm，但在同一可用性集中，如有可能从其他 Vm 保留在单独的容错域。 当硬件重新联机时，会对 VM 重新进行均衡操作，以维持高可用性。 
 
- **更新域**。 更新域是另一可以在可用性集中提供高可用性的 Azure 概念。 更新域是可以同时维护的基础硬件逻辑组。 同一个更新域中的 VM 会在计划内维护期间一起重启。 当租户在可用性集内创建 VM 时，Azure 平台会自动将 VM 分布到这些更新域。 在 Azure Stack 中，VM 会先跨群集中的其他联机主机进行实时迁移，然后其基础主机才会进行更新。 由于在主机更新期间不会造成租户停机，因此 Azure Stack 上存在更新域功能只是为了确保与 Azure 实现模板兼容。 

## <a name="role-based-access-control"></a>基于角色的访问控制
可以使用基于角色的访问控制 (RBAC) 向已获授权的用户、组和服务授予系统访问权限：在订阅、资源组或单个资源的级别为其分配角色即可。 每个角色定义了用户、组或服务对 Microsoft Azure Stack 资源拥有的访问级别。

Azure Stack RBAC 有三种适用于所有资源类型的基本角色：所有者、参与者和读者。 “所有者”拥有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。 参与者可以创建和管理所有类型的 Azure 资源，但不能授予对其他人访问权限。 “读取者”只能查看现有资源。 其他 RBAC 角色允许对特定的 Azure 资源进行管理。 例如，“虚拟机参与者”角色允许创建和管理虚拟机，但不允许管理虚拟机连接到的虚拟网络或子网。

> 有关详细信息，请参阅[管理基于角色的访问控制](azure-stack-manage-permissions.md)。 

## <a name="reporting-usage-data"></a>报告使用情况数据
Azure Stack 从所有资源提供程序收集聚合用量数据，并将其传输到 Azure 供 Azure 商业组件进行处理。 可以通过 REST API 查看 Azure Stack 中收集的用量数据。 可以使用 Azure 一致的租户 API 以及提供程序和委派提供程序 API 从所有租户订阅获取使用情况数据。 可以使用这些数据来集成外部工具或服务，以实现计费或费用分摊。 用量经 Azure 商业组件处理后，可以在 Azure 计费门户中查看。

> 详细了解如何[向 Azure 报告 Azure Stack 使用情况数据](azure-stack-usage-reporting.md)。

## <a name="next-steps"></a>后续步骤

[比较 Azure Stack 与全球 Azure](compare-azure-azure-stack.md)

[管理基础知识](azure-stack-manage-basics.md)

[快速入门：使用 Azure Stack 管理门户](azure-stack-manage-portals.md)