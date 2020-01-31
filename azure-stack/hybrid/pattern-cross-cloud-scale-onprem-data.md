---
title: 用于构建应用的模式，该应用可在 Azure 和 Azure Stack 集线器上使用本地数据缩放跨云。
description: 了解如何使用 Azure 和 Azure Stack 中心来构建使用本地数据的可伸缩跨云应用。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 51224171848b6109a7cd3d8eb2d5fa1fdcb70973
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875770"
---
# <a name="cross-cloud-scaling-on-premises-data-pattern"></a>跨云缩放（本地数据）模式

了解如何构建跨 Azure 和 Azure Stack 集线器的混合应用程序。 此模式还说明了如何使用单个本地数据源以实现符合性。

## <a name="context-and-problem"></a>上下文和问题

许多组织收集和存储大量的客户敏感数据。 由于公司法规或政府政策，它们经常被阻止将敏感数据存储在公有云中。 这些组织还希望利用公有云的可伸缩性。 公有云可以处理流量中的季节性高峰，使客户能够在需要时为所需的确切硬件付费。

## <a name="solution"></a>解决方案

此解决方案利用私有云的符合性优点，将它们与公有云的可伸缩性相结合。 Azure 和 Azure Stack 集线器混合云为开发人员提供了一致的体验。 这种一致性允许用户将其技能应用到公有云和本地环境。

解决方案部署指南允许你将相同的 web 应用程序部署到公有和私有云。 你还可以访问托管在私有云上的非 internet 可路由网络。 监视 web 应用程序的负载。 随着流量的大幅增加，程序会操作 DNS 记录，将流量重定向到公有云。 当流量不再重要时，会更新 DNS 记录，以将流量定向回私有云。

[通过本地数据模式 ![跨云缩放](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)

## <a name="components"></a>组件

此解决方案使用以下组件：

| 层 | 组件 | Description |
|----------|-----------|-------------|
| Azure | Azure App Service | [Azure App Service](/azure/app-service/)允许你构建和托管 web 应用、RESTful API 应用和 Azure Functions。 采用所选编程语言，无需管理基础结构。 |
| | Azure 虚拟网络| [Azure 虚拟网络（VNet）](/azure/virtual-network/virtual-networks-overview)是 azure 中专用网络的基本构建基块。 VNet 使多个 Azure 资源类型（例如虚拟机（VM））能够安全地相互通信、internet 和本地网络。 此解决方案还演示了如何使用其他网络组件：<br>-应用程序和网关子网<br>-本地网络网关<br>-作为站点到站点 VPN 网关连接的虚拟网络网关<br>-一个公共 IP 地址<br>-点到站点 VPN 连接<br>-用于托管 DNS 域和提供名称解析的 Azure DNS |
| | Azure Traffic Manager | [Azure 流量管理器](/azure/traffic-manager/traffic-manager-overview)是一种基于 DNS 的流量负载均衡器。 它允许你控制不同数据中心内服务终结点的用户流量分布。 |
| | Azure Application Insights | [Application Insights](/azure/azure-monitor/app/app-insights-overview)是一种可扩展的应用程序性能管理服务，适用于在多个平台上构建和管理应用程序的 web 开发人员。|
| | Azure Functions | [Azure Functions](/azure/azure-functions/)允许在无服务器环境中执行代码，无需先创建 VM 或发布 web 应用程序。 |
| | Azure 自动缩放 | [自动缩放](/azure/azure-monitor/platform/autoscale-overview)是云服务、虚拟机和 Web 应用的内置功能。 此功能允许应用程序在需求更改时执行其最佳性能。 应用将调整流量峰值，在指标发生变化时通知你，并根据需要进行缩放。 |
| Azure Stack Hub | IaaS 计算 | Azure Stack 集线器允许使用由 Azure 启用的应用程序模型、自助服务门户和 Api。 Azure Stack 集线器 IaaS 允许使用多种开源技术实现一致的混合云部署。 例如，解决方案示例使用 Windows Server VM 来 SQL Server。|
| | Azure App Service | 与 Azure web 应用一样，该解决方案使用[Azure Stack 中心的 Azure App Service](/azure-stack/operator/azure-stack-app-service-overview)来托管 web 应用。 |
| | 联网 | Azure Stack 中心虚拟网络的工作方式与 Azure 虚拟网络完全相同。 它使用许多相同的网络组件，包括自定义主机名。 
| Azure DevOps Services | 注册 | 快速设置持续集成生成、 测试和部署。 有关详细信息，请参阅[注册和登录到 Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)。 |
| | Azure Pipelines | 使用[Azure Pipelines](/azure/devops/pipelines/agents/agents?view=azure-devops)进行持续集成/持续交付。 Azure Pipelines 允许管理托管的生成和发布代理和定义。 |
| | 代码存储库 | 利用多个代码存储库简化开发管道。 使用 GitHub 中的现有代码存储库、Bitbucket、Dropbox、OneDrive 和 Azure Repos。 |

## <a name="issues-and-considerations"></a>问题和注意事项

决定如何实现此解决方案时，请注意以下几点：

### <a name="scalability"></a>可伸缩性 

Azure 和 Azure Stack 集线器唯一适用于支持当今全球分布式业务的需求。

**无障碍的混合云**

Microsoft 在一个统一的解决方案中提供了与 Azure Stack 中心和 Azure 的本地资产的无与伦比集成。 此集成消除了管理多点解决方案和云提供商混合的麻烦。 使用跨云缩放时，Azure 的强大功能只需单击几下即可。 只需使用云爆发将 Azure Stack 集线器连接到 Azure，数据和应用程序就可以在 Azure 中使用（如果需要）。

- 不再需要构建和维护辅助 DR 站点。
- 通过消除磁带备份并在 Azure 中使用最多99年的备份数据，节省时间和资金。
- 轻松地将运行中的 Hyper-v、物理（预览版）和 VMware （预览版）工作负荷迁移到 Azure 中，以利用云的经济性和弹性。
- 在 Azure 中的本地资产的复制副本上运行计算密集型报表或分析，而不影响生产工作负荷。
- 突发到云中，在 Azure 中运行本地工作负荷，并在需要时提供更大的计算模板。 混合可在需要时提供所需的强大功能。
- 只需单击几下鼠标，即可在 Azure 中创建多层开发环境–甚至会将实时生产数据复制到开发/测试环境，使其保持近乎实时同步。

**跨云缩放与 Azure Stack 中心的经济**

云突发的主要优势是节省成本。 仅当需要对这些资源的需求时，才需要支付额外资源。 无需再花费额外的额外容量，也不需要预测需求高峰和波动。

**将高需求负载减少到云中**

跨云缩放可用于肩处理负担。 通过将基本应用程序移到公有云，并为业务关键型应用程序释放本地资源，来分发负载。 应用程序可以应用于私有云，然后仅在需要时才会向公有云突发以满足要求。


### <a name="availability"></a>可用性

全球部署面临着自身的难题，如可变连接性和不同地区的政府法规。 开发人员可以只开发一个应用程序，然后将其部署到不同需求的不同原因。  将应用程序部署到 Azure 公有云，然后在本地部署其他实例或组件。 你可以使用 Azure 管理所有实例之间的流量。

### <a name="manageability"></a>可管理性

**单个一致的开发方法**

使用 Azure 和 Azure Stack 中心，你可以在组织中使用一组一致的开发工具。 这种一致性使得实现持续集成和持续开发（CI/CD）的做法更容易。 许多部署在 Azure 或 Azure Stack 中心中的应用和服务都是可互换的，可以在任意位置无缝运行。

混合 CI/CD 管道可帮助你：
- 在代码存储库中基于代码提交启动新的生成。
- 自动将新生成的代码部署到 Azure 进行用户验收测试。
- 代码通过测试后，会自动部署到 Azure Stack 中心。

**单个一致的标识管理解决方案**

Azure Stack 集线器适用于 Azure Active Directory 和 Active Directory 联合身份验证服务（ADFS）。 Azure Stack 集线器适用于连接的方案中的 Azure Active Directory。 对于没有连接的环境，可以使用 ADFS 作为断开连接的解决方案。 服务主体用于授予对应用程序的访问权限，允许他们通过 Azure 资源管理器部署或配置资源。 

### <a name="security"></a>安全性

**确保符合性和数据主权**

Azure Stack 集线器使你可以在多个国家/地区运行相同的服务，就像使用公有云一样。 在每个国家/地区的数据中心部署相同的应用程序，允许满足数据主权需求。 此功能可确保个人数据保留在每个国家/地区的边界内。

**Azure Stack 中心-安全状况**

没有可靠的持续维护过程，没有安全状况。 出于此原因，Microsoft 投资了在整个基础结构上无缝应用修补程序和更新的业务流程引擎。

由于与 Azure Stack 中心 OEM 合作伙伴的合作关系，Microsoft 将相同的安全状况扩展到 OEM 特定的组件。 如硬件生命周期主机和在其上运行的软件。 这种合作关系可确保在整个基础结构中 Azure Stack 中心具有统一的稳定安全状况。 相反，客户可以构建和保护其应用程序工作负荷。

**通过 PowerShell、CLI 和 Azure 门户使用服务主体**

若要对脚本或应用提供资源访问权限，请设置应用的标识，并使用其自己的凭据对应用进行身份验证。 此标识称为服务主体，使你能够：

- 将权限分配给应用标识，这些权限不同于你自己的权限，并且仅限于应用的需求。
- 执行无人参与的脚本时，使用证书进行身份验证。 

有关创建服务主体和使用证书凭据的详细信息，请参阅[使用应用标识来访问资源](/azure-stack/operator/azure-stack-create-service-principals)。

## <a name="when-to-use-this-pattern"></a>何时使用此模式 

- 我的组织使用的是 DevOps 的方法，或计划在不久的将来使用。
- 我要跨 Azure Stack 集线器实现和公有云实现 CI/CD 实践。
- 我想要跨云和本地环境合并 CI/CD 管道。
- 我希望能够使用云和本地服务无缝开发应用程序。
- 我想要跨云和本地应用程序利用一致的开发人员技能。
- 我正在使用 Azure，但我的开发人员在本地 Azure Stack 的中心云。
- 我的本地应用程序在季节性、循环或不可预测波动期间遇到高峰需求。
- 我有本地组件，并希望使用云无缝缩放。
- 我需要云可伸缩性，但我希望应用程序尽可能多地在本地运行。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：
- 有关如何使用此模式的概述，请观看在[数据中心和公有云之间动态缩放应用](https://www.youtube.com/watch?v=2lw8zOpJTn0)。
- 有关最佳实践的详细信息，请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，并回答其他问题。
- 此模式使用 Azure Stack 系列的产品，包括 Azure Stack 中心。 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续学习[跨云缩放（本地数据）解决方案部署指南](solution-deployment-guide-cross-cloud-scaling-onprem-data.md)。 部署指南提供了有关部署和测试其组件的分步说明。