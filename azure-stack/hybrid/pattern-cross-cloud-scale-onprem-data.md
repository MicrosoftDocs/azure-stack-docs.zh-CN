---
title: Azure Stack 中心中的跨云缩放（本地数据）模式
description: 了解如何构建使用 Azure 中的本地数据和 Azure Stack 集线器的可伸缩跨云应用。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: edbb608fbf8e5288f29572bfe4cca98ffb3cb8fc
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80812384"
---
# <a name="cross-cloud-scaling-on-premises-data-pattern"></a>跨云缩放（本地数据）模式

了解如何构建跨 Azure 和 Azure Stack 集线器的混合应用。 此模式还演示如何使用单个本地数据源来实现合规性。

## <a name="context-and-problem"></a>上下文和问题

许多组织收集和存储大量敏感的客户数据。 由于公司法规或政府政策，它们经常被阻止将敏感数据存储在公有云中。 这些组织还希望利用公有云的可伸缩性。 公有云可以处理季节性的流量高峰，使客户能够在需要时将费用花费在他们真正需要的硬件上。

## <a name="solution"></a>解决方案

此解决方案利用私有云的合规性优势，并将这些优势与公有云的可伸缩性相结合。 Azure 和 Azure Stack Hub 混合云为开发人员提供一致的体验。 这种一致性使他们能够将他们的技能应用到公有云和本地环境。

解决方案部署指南允许你将相同的 web 应用部署到公有和私有云。 你还可以访问在私有云上托管的非 internet 可路由网络。 监视 web 应用的负载。 当流量大幅增加时，某个程序会处理 DNS 记录，以将流量重定向到公有云。 当流量不再高发时，将会更新 DNS 记录，以将流量定向回到私有云。

[![使用本地数据模式进行跨云缩放](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)

## <a name="components"></a>组件

此解决方案使用以下组件：

| 层 | 组件 | 说明 |
|----------|-----------|-------------|
| Azure | Azure 应用服务 | [Azure 应用服务](/azure/app-service/)可让你生成和托管 Web 应用、RESTful API 应用和 Azure Functions。 相关应用全都以所选的编程语言编写，无需管理基础结构。 |
| | Azure 虚拟网络| [Azure 虚拟网络 (VNet)](/azure/virtual-network/virtual-networks-overview) 是 Azure 中专用网络的基本构建块。 VNet 使多个 Azure 资源类型（例如虚拟机（VM））能够安全地相互通信、internet 和本地网络。 该解决方案还演示了其他网络组件的用法：<br>-应用和网关子网。<br>-本地网络网关。<br>-作为站点到站点 VPN 网关连接的虚拟网络网关。<br>-一个公共 IP 地址。<br>-点到站点 VPN 连接。<br>-用于托管 DNS 域和提供名称解析的 Azure DNS。 |
| | Azure 流量管理器 | [Azure 流量管理器](/azure/traffic-manager/traffic-manager-overview)是基于 DNS 的流量负载均衡器。 使用它可以控制用户流量在不同数据中心内的服务终结点上的分布。 |
| | Azure Application Insights | [Application Insights](/azure/azure-monitor/app/app-insights-overview)是用于在多个平台上构建和管理应用程序的 web 开发人员的可扩展应用程序性能管理服务。|
| | Azure Functions | [Azure Functions](/azure/azure-functions/)允许在无服务器环境中执行代码，无需先创建 VM 或发布 web 应用。 |
| | Azure 自动缩放 | [自动缩放](/azure/azure-monitor/platform/autoscale-overview)是云服务、vm 和 web 应用的内置功能。 此功能允许应用在需求更改时最大程度地执行。 应用将调整流量峰值，并在指标发生变化时通知你，并根据需要进行缩放。 |
| Azure Stack Hub | IaaS 计算 | Azure Stack 集线器允许使用由 Azure 启用的相同应用模型、自助服务门户和 Api。 Azure Stack 集线器 IaaS 允许使用多种开源技术实现一致的混合云部署。 例如，解决方案示例使用 Windows Server VM 来部署 SQL Server。|
| | Azure 应用服务 | 与 Azure Web 应用一样，该解决方案使用 [Azure Stack Hub 上的 Azure 应用服务](/azure-stack/operator/azure-stack-app-service-overview)来托管 Web 应用。 |
| | 网络 | Azure Stack 中心虚拟网络与 Azure 虚拟网络完全相同。 它使用许多相同的网络组件，包括自定义主机名。
| Azure DevOps Services | 注册 | 快速设置用于生成、测试和部署的持续集成。 有关详细信息，请参阅[注册和登录到 Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)。 |
| | Azure Pipelines | 使用 [Azure Pipelines](/azure/devops/pipelines/agents/agents?view=azure-devops) 实现持续集成/持续交付。 Azure Pipelines 可让你管理托管的生成和发布代理以及定义。 |
| | 代码存储库 | 利用多个代码存储库来简化开发管道。 使用 GitHub、Bitbucket、Dropbox、OneDrive 和 Azure Repos 中现有的代码存储库。 |

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此解决方案时，请考虑以下几点：

### <a name="scalability"></a>可伸缩性

Azure 和 Azure Stack Hub 非常适合支持当今全球分布式业务的需求。

#### <a name="hybrid-cloud-without-the-hassle"></a>无障碍的混合云

Microsoft 在统一的解决方案中为本地资产与 Azure Stack Hub 和 Azure 提供无可比拟的集成。 这种集成消除了管理多点解决方案和混合云提供程序的不便。 使用跨云缩放时，Azure 的强大功能只需单击几下即可。 只需将 Azure Stack 中心连接到 Azure，使用云突发功能即可在 Azure 中根据需要使用数据和应用。

- 消除了构建和维护辅助灾难恢复站点的需求。
- 通过消除磁带备份并在 Azure 中使用最多99年的备份数据，节省时间和资金。
- 将运行中的 Hyper-V、物理（预览版）和 VMware（预览版）工作负荷轻松迁移到 Azure，以利用云的经济效益和弹性。
- 在 Azure 中的本地资产的复制副本上运行计算密集型报表或分析，而不会影响生产工作负荷。
- 根据需要使用更大的计算模板将流量突发到云中，并在 Azure 中运行本地工作负荷。 混合解决方案可在需要时提供所需的强大功能。
- 只需单击几下鼠标，即可在 Azure 中创建多层开发环境–甚至会将实时生产数据复制到开发/测试环境，使其保持近乎实时同步。

#### <a name="economy-of-cross-cloud-scaling-with-azure-stack-hub"></a>通过 Azure Stack Hub 进行跨云缩放的经济效益

云突发的主要优势是经济实惠。 只有在需要这些额外的资源时，才需要支付费用。 不需要花费更多的额外容量，也不能预测需求高峰和波动。

#### <a name="reduce-high-demand-loads-into-the-cloud"></a>降低云中的高需求负载

跨云缩放可用于肩处理负担。 负载是通过将基本应用移至公有云，为业务关键型应用释放本地资源来分布的。 应用可应用到私有云，然后仅在需要时才会向公有云突发以满足要求。

### <a name="availability"></a>可用性

全球部署面临着自己的挑战，如可变连接性和不同地区的政府法规。 开发人员只能开发一个应用，然后根据不同的原因和要求部署该应用。 将你的应用部署到 Azure 公有云，然后在本地部署其他实例或组件。 可以使用 Azure 管理所有实例之间的流量。

### <a name="manageability"></a>可管理性

#### <a name="a-single-consistent-development-approach"></a>单个一致的开发方法

使用 Azure 和 Azure Stack 中心，你可以在组织中使用一组一致的开发工具。 这种一致性可让你更轻松地实现持续集成和持续开发 (CI/CD)。 许多部署在 Azure 或 Azure Stack 中心中的应用和服务都是可互换的，可以在任一位置无缝运行。

混合 CI/CD 管道有助于：

- 根据代码存储库中提交的代码启动新的生成。
- 自动将新生成的代码部署到 Azure 进行用户验收测试。
- 代码通过测试后，可自动部署到 Azure Stack Hub。

### <a name="a-single-consistent-identity-management-solution"></a>单个一致的标识管理解决方案

Azure Stack 集线器适用于 Azure Active Directory （Azure AD）和 Active Directory 联合身份验证服务（ADFS）。 Azure Stack 集线器适用于连接的方案中的 Azure AD。 对于未建立连接的环境，可以使用 ADFS 作为离线解决方案。 服务主体用于授予对应用的访问权限，允许他们通过 Azure 资源管理器部署或配置资源。

### <a name="security"></a>安全性

#### <a name="ensure-compliance-and-data-sovereignty"></a>确保合规性和数据主权

Azure Stack 集线器使你可以在多个国家/地区运行相同的服务，就像使用公有云一样。 在每个国家/地区的数据中心中部署相同的应用可满足数据主权需求。 此功能可确保个人数据保留在每个国家/地区的边界内。

#### <a name="azure-stack-hub---security-posture"></a>Azure Stack Hub - 安全态势

没有稳定、持续的服务流程，就没有良好的安全态势。 出于此原因，Microsoft 投资购买了一个业务流程引擎，它可以在整个基础结构中无缝应用修补程序和更新。

由于与 Azure Stack 中心 OEM 合作伙伴的合作关系，Microsoft 将相同的安全状况扩展到 OEM 特定的组件，如硬件生命周期主机和在其上运行的软件。 这种合作关系可确保 Azure Stack Hub 在整个基础结构上具有一致且稳固的安全态势。 相反，客户可以构建和保护其应用工作负荷。

#### <a name="use-of-service-principals-via-powershell-cli-and-azure-portal"></a>通过 PowerShell、CLI 和 Azure 门户使用服务主体

若要为资源提供对脚本或应用的访问权限，请设置应用的标识，并使用应用自身的凭据进行身份验证。 此标识称为服务主体，可让你：

- 将权限分配给应用标识，这些权限不同于你自己的权限，并且仅限于应用的需求。
- 执行无人参与的脚本时，使用证书进行身份验证。

若要详细了解如何创建服务主体，并使用凭据来访问资源，请参阅[使用应用标识来访问资源](/azure-stack/operator/azure-stack-create-service-principals)。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

- 我的组织正在使用 DevOps 方法，或打算在不久的将来使用某种方法。
- 我想要跨 Azure Stack Hub 实施方案和公有云实施 CI/CD 做法。
- 我想要跨云和本地环境合并 CI/CD 管道。
- 我希望能够使用云和本地服务无缝开发应用程序。
- 我想在云和本地应用程序中利用一致的开发人员技能。
- 我正在使用 Azure，但我的开发人员在本地 Azure Stack 的中心云。
- 我的本地应用在季节、循环或不可预测的波动期间遇到高峰需求。
- 我有本地组件，并希望使用云无缝缩放。
- 我想让我的应用程序尽可能多地在本地运行云伸缩性。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：

- 有关如何使用此模式的概述，请观看在[数据中心和公有云之间动态缩放应用](https://www.youtube.com/watch?v=2lw8zOpJTn0)。
- 请参阅[混合应用设计注意事项](overview-app-design-considerations.md)，了解有关最佳实践的详细信息，并回答你可能遇到的其他问题。
- 此模式使用 Azure Stack 产品系列，包括 Azure Stack Hub。 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续学习[跨云缩放（本地数据）解决方案部署指南](solution-deployment-guide-cross-cloud-scaling-onprem-data.md)。 该部署指南逐步说明了如何部署和测试 Azure Stack 的组件。
