---
title: Azure Stack Hub 概述
description: Azure Stack Hub 的概述，以及如何使用它在数据中心运行 Azure 服务。
author: justinha
ms.topic: overview
ms.date: 04/20/2020
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 11/08/2019
ms.custom: conteperfq4
ms.openlocfilehash: 5acbe557c58d33785e0c227c2014264fbcd06a06
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488410"
---
# <a name="azure-stack-hub-overview"></a>Azure Stack Hub 概述

Azure Stack Hub 是 Azure 的一个扩展，可用于在本地环境中运行应用，以及在数据中心内交付 Azure 服务。 借助一致性的云平台，组织可以根据业务要求而不是技术局限性自信地做出技术决策。

## <a name="why-use-azure-stack-hub"></a>为何要使用 Azure Stack Hub？

Azure 为开发人员提供多功能的平台用于生成新型应用。 但是，某些基于云的应用面临着延迟、间歇性连接和法规监管等方面的阻碍。 Azure 和 Azure Stack Hub 为面向客户的应用和内部业务线应用程序开启了新的混合云用例：

- **边缘解决方案和断开连接的解决方案**。 在 Azure Stack Hub 本地处理数据，然后在 Azure 中聚合以作进一步分析，并在两者之间使用共同的应用逻辑，以此满足延迟和连接要求。 甚至可以在断开 Internet 连接且不与 Azure 建立连接的情况下部署 Azure Stack Hub。 示例环境包括工厂车间、游轮和矿井。

- **满足各种法规要求的云应用**。 可在 Azure 中开发和部署应用，并能够完全灵活地在 Azure Stack Hub 本地进行部署，以满足法规或政策要求。 无需更改任何代码。 应用示例包括全球审计、财务报告、外汇交易、在线游戏和费用报告。

- **本地云应用模型**。 使用 Azure 服务、容器、无服务器体系结构和微服务体系结构来更新和扩展现有应用或生成新的应用。 在云中 Azure 和本地 Azure Stack Hub 之间使用一致的 DevOps 流程，以加速任务关键型核心应用的现代化。

## <a name="azure-stack-hub-architecture"></a>Azure Stack Hub 体系结构

Azure Stack Hub 集成式系统包括受信任的硬件合作伙伴制造的、由 4-16 台服务器构成的机架，将直接交付到你的数据中心。 交付后，解决方案提供商将与你合作部署该集成式系统，并确保 Azure Stack Hub 解决方案符合你的业务要求。 可以准备数据中心：确保已准备好所有必要的电源和散热设备、边界连接，并满足其他必要的数据中心集成要求。

> 有关 Azure Stack Hub 数据中心集成体验的详细信息，请参阅 [Azure Stack Hub 数据中心集成](azure-stack-customer-journey.md)。

Azure Stack Hub 建立在行业标准的硬件基础之上，使用管理 Azure 订阅所用的相同工具进行管理。 因此，无论是否已连接到 Azure，都能够应用一致的 DevOps 流程。

Azure Stack Hub 体系结构允许在远程位置的边缘，或者在间歇性连接、与 Internet 断开连接的情况下提供 Azure 服务。 可以创建混合解决方案用于处理 Azure Stack Hub 本地的数据，然后在 Azure 中聚合这些数据，以进行额外的处理和分析。 最后，由于 Azure Stack Hub 安装在本地，你可以满足具体的法规或政策要求，并可以在本地灵活部署云应用，而无需更改任何代码。

## <a name="deployment-options"></a>部署选项

Azure Stack 集线器集成系统通过 Microsoft 和硬件合作伙伴的合作关系提供，创建了一个解决方案，提供了云进度的创新和计算管理简易性。 由于 Azure Stack Hub 以集成式硬件和软件系统的形式提供，因此你可以获得所需的灵活性和控制度，以及云中的创新能力。

Azure Stack Hub 集成系统的大小范围为 4-16 台服务器（称为“缩放单元”）**。 硬件合作伙伴和 Microsoft 共同支持集成系统。 下图显示了缩放单元示例。 

![显示 Azure Stack Hub 集成系统的关系图](./media/azure-stack-overview/azure-stack-integrated-system.svg)上获取。 

<!---add info and image on regions, etc--->

### <a name="connection-models"></a>连接模型

可以选择在**已连接**到 Internet（和 Azure）时或者与之**断开连接**时部署 Azure Stack Hub。 

> 有关详细信息，请参阅有关[联网](azure-stack-connected-deployment.md)和[离线](azure-stack-disconnected-deployment.md)部署模型的注意事项。

### <a name="identity-provider"></a>标识提供者 

Azure Stack Hub 使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS)。 Azure AD 是 Microsoft 的基于云的多租户标识提供者。 使用 Internet 联网部署的大多数混合方案都使用 Azure AD 作为标识存储。

对于 Azure Stack Hub 的离线部署，需要使用 AD FS。 Azure Stack Hub 资源提供程序和其他应用以类似方式使用 AD FS 或 Azure AD。 Azure Stack Hub 包含自身的 Active Directory 实例，另外还包含 Active Directory Graph API。


## <a name="how-is-azure-stack-hub-managed"></a>如何管理 Azure Stack Hub？

Azure Stack Hub 使用与 Azure 相同的操作模型。 Azure Stack Hub 操作员可以向租户用户提供各种服务和应用，类似于 Microsoft 向租户用户提供 Azure 服务。 

![显示 Azure Stack Hub 作业角色的关系图](./media/azure-stack-overview/azure-stack-job-roles.svg)

可以通过管理员门户、用户门户或 [PowerShell](/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1) 来管理 Azure Stack Hub。 每个 Azure Stack Hub 门户由 Azure 资源管理器的单独实例提供支持。 **Azure Stack Hub 操作员**可以使用管理员门户来管理 Azure Stack Hub，以及执行如下所述的操作：创建租户套餐，保持集成式系统的运行状况及监视其状态。 用户门户提供自助服务体验让用户使用云资源，例如虚拟机 (VM)、存储帐户和 Web 应用。

> 有关使用管理门户管理 Azure Stack Hub 的详细信息，请参阅 [Azure Stack Hub 管理员门户快速入门](azure-stack-manage-portals.md)。

作为 Azure Stack 中心操作员，你可以提供 [vm](./tutorial-offer-services.md?view=azs-2002)、 [web 应用](azure-stack-app-service-overview.md)、高可用性 [SQL Server](azure-stack-tutorial-sql.md)和 [MySQL Server](azure-stack-tutorial-mysql.md) 数据库。 他们还可以使用 [Azure Stack Hub 快速入门 Azure 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates)来部署 SharePoint、Exchange 等。

操作员可以通过[管理员门户](azure-stack-manage-portals.md)或 [PowerShell](/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1) 来管理 Azure Stack Hub。 可以使用计划、配额、套餐和订阅来配置 Azure Stack Hub，以向租户[提供服务](service-plan-offer-subscription-overview.md)。 租户用户可以订阅多个套餐。 套餐可以包含一个或多个计划，计划可以包含一个或多个服务。 操作员还可以管理容量以及对警报做出响应。

用户使用操作员提供的服务。 用户可以预配、监视和管理他们订阅的服务，例如 Web 应用、存储和 VM。 用户可以通过用户门户或 PowerShell 来管理 Azure Stack Hub。

> 若要详细了解如何管理 Azure Stack Hub，包括在何处使用哪些帐户、典型的操作员职责、要向用户告知哪些信息，以及如何取得帮助，请查看 [Azure Stack Hub 管理基础知识](azure-stack-manage-basics.md)。

## <a name="resource-providers"></a>资源提供程序

资源提供程序属于 Web 服务，构成了所有 Azure Stack Hub IaaS 和 PaaS 服务的基础。 Azure 资源管理器依靠不同的资源提供程序提供对服务的访问。 每个资源提供程序可帮助你配置和控制其相应资源。 服务管理员还可以添加新的自定义资源提供程序。

### <a name="foundational-resource-providers"></a>基础资源提供程序

有三个基础 IaaS 资源提供程序：

- **计算**：Azure Stack Hub 租户可以通过计算资源提供程序创建自己的 VM。 计算资源提供程序包含用于创建 VM 和 VM 扩展的功能。 VM 扩展服务可帮助为 Windows 与 Linux VM 提供 IaaS 功能。 例如，可以使用计算资源提供程序预配一个 Linux VM，并在部署期间运行 Bash 脚本来配置该 VM。
- **网络资源提供程序**：网络资源提供程序为私有云提供了一系列软件定义的网络 (SDN) 和网络功能虚拟化 (NFV) 功能。 可以使用网络资源提供程序创建软件负载均衡器、公共 IP、网络安全组和虚拟网络等资源。
- **存储资源提供程序**：存储资源提供程序提供了四种 Azure 一致性存储服务： [blob](/azure/storage/common/storage-introduction#blob-storage)、 [队列](/azure/storage/common/storage-introduction#queue-storage)、 [表](/azure/storage/common/storage-introduction#table-storage)和 [Key Vault](/azure/key-vault/) 帐户管理，提供机密（如密码和证书）的管理和审核。 存储资源提供程序还提供存储云管理服务，用于简化 Azure 一致性存储服务的服务提供程序管理。 Azure 存储可为存储和检索大量非结构化数据提供弹性，例如 Azure Blob 的文档与媒体文件，以及具有 Azure 表的结构化 NoSQL 数据。

### <a name="optional-resource-providers"></a>可选的资源提供程序

在 Azure Stack Hub 中可以部署和使用三个可选的 PaaS 资源提供程序：

- **应用服务**： [Azure Stack 中心的 Azure App Service](azure-stack-app-service-overview.md) 是 Microsoft Azure 可用于 Azure Stack 中心的 PaaS 产品/服务。 该服务可让你的内部或外部客户为任何平台或设备创建 Web 应用、API 应用和 Azure Functions 应用。
- **SQL Server**：使用 [SQL Server 资源提供程序](azure-stack-sql-resource-provider.md)将 SQL 数据库作为 Azure Stack Hub 的一项服务提供。 安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，你和你的用户可以创建云原生应用的数据库、使用 SQL 的网站，以及使用 SQL 的其他工作负荷。
- **MySQL Server**：可以使用 [MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)将 MySQL 数据库公开为 Azure Stack Hub 服务。 MySQL 资源提供程序以服务的形式在 Windows Server 2019 Server Core VM 上运行。

## <a name="next-steps"></a>后续步骤

[比较 Azure Stack Hub 项目组合](compare-azure-azure-stack.md)

[管理基础知识](azure-stack-manage-basics.md)

[快速入门：使用 Azure Stack Hub 管理门户](azure-stack-manage-portals.md)

[了解使用情况和计费](azure-stack-usage-reporting.md)。
