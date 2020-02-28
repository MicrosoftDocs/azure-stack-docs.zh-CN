---
title: Azure Stack 集线器概述
description: 概述 Azure Stack 集线器的定义，以及如何使你能够在数据中心内运行 Azure 服务。
author: justinha
ms.topic: overview
ms.date: 11/08/2019
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 11/08/2019
ms.openlocfilehash: afa7f02f451192640172665e18213d125e24b574
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698447"
---
# <a name="azure-stack-hub-overview"></a>Azure Stack 集线器概述

Azure Stack 中心是 Azure 的扩展，它提供了一种方法，可在本地环境中运行应用，并在数据中心提供 Azure 服务。 使用一致的云平台，组织可根据业务需求满怀信心地做出技术决策，而不是基于技术限制做出业务决策。

## <a name="why-use-azure-stack-hub"></a>为什么要使用 Azure Stack 中心？

Azure 为开发人员提供了一个丰富的平台，用于构建新式应用程序。 但是，某些基于云的应用面临一些障碍，如延迟、间歇连接和法规。 Azure 和 Azure Stack 中心为面向客户的客户和内部业务线应用，解锁新的混合云用例：

- **边缘解决方案和断开连接的解决方案**。 通过在 Azure Stack 集线器中本地处理数据，然后在 Azure 中将数据聚合在 Azure 中以进行进一步分析，从而满足延迟和连接要求，这两者都具有常见的应用逻辑。 甚至可以部署从 internet 断开连接的 Azure Stack 集线器，无需连接到 Azure。 请考虑工厂地面、巡航和

- **满足各种法规要求的云应用**。 在 Azure 中开发和部署应用程序，可以完全灵活地在本地部署 Azure Stack 集线器，以满足法规或策略要求。 不需要更改代码。 应用示例包括全球审核、财务报表、外国汇兑交易、在线游戏和费用报告。

- **本地云应用模型**。 使用 Azure 服务、容器、无服务器和微服务体系结构来更新和扩展现有应用程序，或构建新应用程序。 在云中的 Azure 中使用一致的 DevOps 流程，并在本地 Azure Stack 中心，加速核心任务关键型应用的应用现代化。

## <a name="azure-stack-hub-architecture"></a>Azure Stack 中心体系结构

Azure Stack 集线器集成系统包含在由受信任的硬件合作伙伴构建并直接传递到你的数据中心的4-16 服务器机架中。 交付后，解决方案提供商将与你一起部署集成系统，并确保 Azure Stack 集线器解决方案满足你的业务要求。 你可以通过确保所有必需的电源和冷却、边界连接和其他所需的数据中心集成要求准备就绪，来准备你的数据中心。

> 有关 Azure Stack 中心数据中心集成体验的详细信息，请参阅[Azure Stack 中心数据中心集成](azure-stack-customer-journey.md)。

Azure Stack 集线器构建在行业标准硬件上，并使用已用于管理 Azure 订阅的相同工具进行管理。 因此，无论是否连接到 Azure，都可以应用一致的 DevOps 进程。

利用 Azure Stack 集线器体系结构，你可以在边缘为远程位置或间歇连接提供 Azure 服务，与 internet 断开连接。 你可以创建混合解决方案，用于在 Azure Stack 中心本地处理数据，然后在 Azure 中将其聚合起来，以便进行额外的处理和分析。 最后，由于 Azure Stack 集线器是在本地安装的，因此你可以满足特定的法规或策略要求，并灵活地在本地部署云应用，而无需更改任何代码。

## <a name="deployment-options"></a>部署选项

Azure Stack 集线器集成系统通过 Microsoft 和硬件合作伙伴的合作关系提供，创建了一个解决方案，提供了云进度的创新和计算管理简易性。 由于 Azure Stack 集线器作为集成的硬件和软件系统提供，因此您可以灵活地控制您的需求，并且能够从云创新。 

Azure Stack 集线器集成系统的大小范围是从4-16 服务器（称为*缩放单位*）。 硬件合作伙伴和 Microsoft 共同支持集成系统。 下图显示了一个缩放单位的示例。 

![显示 Azure Stack 集线器集成系统的图示](./media/azure-stack-overview/azure-stack-integrated-system.png)。 

<!---add info and image on regions, etc--->

### <a name="connection-models"></a>连接模型

你可以选择部署**连接**到 internet （和到 Azure）或与其**断开**连接 Azure Stack 集线器。 

> 有关详细信息，请参阅[连接](azure-stack-connected-deployment.md)和[断开](azure-stack-disconnected-deployment.md)连接部署模型的注意事项。

### <a name="identity-provider"></a>标识提供者 

Azure Stack 集线器使用 Azure Active Directory （Azure AD）或 Active Directory 联合身份验证服务（AD FS）。 Azure AD 是 Microsoft 的基于云的多租户标识提供者。 大多数混合方案都使用与 internet 连接的部署，Azure AD 作为标识存储。

对于 Azure Stack 集线器的断开连接部署，需要使用 AD FS。 Azure Stack 中心资源提供程序和其他应用的工作方式与 AD FS 或 Azure AD 相同。 Azure Stack 中心包含自己的 Active Directory 实例和 Active Directory 图形 API。


## <a name="how-is-azure-stack-hub-managed"></a>如何管理 Azure Stack 中心？

Azure Stack 集线器使用与 Azure 相同的操作模型。 Azure Stack 中心运营商可以向租户用户提供各种服务和应用，类似于 Microsoft 向租户用户提供 Azure 服务的方式。 

![显示 Azure Stack 中心作业角色的图示](./media/azure-stack-overview/azure-stack-job-roles.png)

可以通过管理员门户、用户门户或[PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)来管理 Azure Stack 中心。 Azure Stack 中心门户分别由 Azure 资源管理器的单独实例支持。 **Azure Stack 中心操作员**使用管理员门户来管理 Azure Stack 中心，并执行一些操作，例如创建租户产品并维护集成系统的运行状况和监视状态。 用户门户为虚拟机（Vm）、存储帐户和 web 应用等云资源提供自助服务体验。

> 有关使用管理员门户管理 Azure Stack 集线器的详细信息，请参阅使用[Azure Stack 中心管理门户快速入门](azure-stack-manage-portals.md)。

作为 Azure Stack 中心操作员，你可以提供[vm](azure-stack-tutorial-tenant-vm.md)、 [web 应用](azure-stack-app-service-overview.md)、高可用性[SQL Server](azure-stack-tutorial-sql.md)和[MySQL Server](azure-stack-tutorial-mysql.md)数据库。 你还可以使用[Azure Stack 集线器快速入门 Azure 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates)来部署 SharePoint、Exchange 等。

操作员可以使用[管理员门户](azure-stack-manage-portals.md)或[PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1)来管理 Azure Stack 中心。 可以将 Azure Stack 中心配置为使用计划、配额、产品和订阅向租户[提供服务](service-plan-offer-subscription-overview.md)。 租户用户可以订阅多个产品/服务。 套餐可以包含一个或多个计划，计划可以包含一个或多个服务。 操作员还管理容量并响应警报。

用户使用操作员提供的服务。 用户可以预配、监视和管理他们订阅的服务，例如 web 应用、存储和 Vm。 用户可以通过用户门户或 PowerShell 管理 Azure Stack 中心。

> 若要了解有关管理 Azure Stack 中心的详细信息，包括要使用的帐户、操作方法、要告诉用户的信息以及如何获取帮助，请查看[Azure Stack 中心管理基础知识](azure-stack-manage-basics.md)。

## <a name="resource-providers"></a>资源提供程序

资源提供程序是一种 web 服务，构成所有 Azure Stack 中心 IaaS 和 PaaS 服务的基础。 Azure 资源管理器依赖于不同的资源提供程序来提供对服务的访问。 每个资源提供程序都可帮助你配置和控制其各自的资源。 服务管理员还可以添加新的自定义资源提供程序。

### <a name="foundational-resource-providers"></a>基础资源提供程序

有三个基础 IaaS 资源提供程序：

- **计算**：计算资源提供程序允许 Azure Stack 中心租户创建自己的 vm。 计算资源提供程序包括创建 Vm 和 VM 扩展的能力。 VM 扩展服务有助于提供适用于 Windows 和 Linux Vm 的 IaaS 功能。 例如，你可以使用计算资源提供程序来设置 Linux VM，并在部署过程中运行 Bash 脚本来配置 VM。
- **网络资源提供程序**：网络资源提供程序为私有云提供了一系列软件定义的网络（SDN）和网络功能虚拟化（NFV）功能。 你可以使用网络资源提供程序来创建软件负载均衡器、公共 Ip、网络安全组和虚拟网络等资源。
- **存储资源提供程序**：存储资源提供程序提供了四种 Azure 一致性存储服务： [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage)、[队列](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage)、[表](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)和[Key Vault](https://docs.microsoft.com/azure/key-vault/)帐户管理，提供机密（如密码和证书）的管理和审核。 存储资源提供程序还提供了一个存储云管理服务，用于简化 Azure 一致存储服务的服务提供程序管理。 通过 azure 存储空间，可以灵活地存储和检索大量非结构化数据（例如，包含 Azure Blob 的文档和媒体文件）和基于结构的基于 NoSQL 的数据与 Azure 表。

### <a name="optional-resource-providers"></a>可选资源提供程序

有三个可选的 PaaS 资源提供程序可与 Azure Stack 中心一起部署和使用：

- **应用服务**： [Azure Stack 中心的 Azure App Service](azure-stack-app-service-overview.md)是 Microsoft Azure 可用于 Azure Stack 中心的 PaaS 产品/服务。 此服务使你的内部或外部客户能够为任何平台或设备创建 web、API 和 Azure Functions 应用。
- **SQL Server**：使用[SQL Server 资源提供程序](azure-stack-sql-resource-provider.md)将 SQL 数据库作为 Azure Stack 中心的服务提供。 安装资源提供程序并将其连接到一个或多个 SQL Server 实例之后，你和你的用户可以创建适用于云原生应用的数据库、使用 SQL 的网站，以及使用 SQL 的其他工作负荷。
- **Mysql 服务器**：使用[mysql 服务器资源提供程序](azure-stack-mysql-resource-provider-deploy.md)将 Mysql 数据库作为 Azure Stack 中心服务公开。 MySQL 资源提供程序在 Windows Server 2019 Server Core VM 上以服务的形式运行。

## <a name="next-steps"></a>后续步骤

[比较 Azure Stack 中心项目组合](compare-azure-azure-stack.md)

[管理基础知识](azure-stack-manage-basics.md)

[快速入门：使用 Azure Stack 集线器管理门户](azure-stack-manage-portals.md)

[了解使用情况和计费](azure-stack-usage-reporting.md)。
