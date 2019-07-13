---
title: 使用 Azure Stack 的智能边缘的 DevOps 模式 |Microsoft Docs
description: 了解如何使用 Azure Stack 的智能边缘的 DevOps 模式
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 76437cd37733984d3230d4c40ccc82c7e6ede2b9
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856383"
---
# <a name="devops-pattern"></a>DevOps 模式

从单个位置的代码并部署到多个目标可能是在本地数据中心、 私有云或公有云的开发、 测试和生产环境中。

## <a name="context-and-problem"></a>上下文和问题

应用程序部署连续性、 安全性和可靠性是至关重要的组织和开发团队的关键。

应用通常需要重构的代码以在每个目标环境中运行。 这意味着，应用程序不是完全可移植。 它必须是更新、 测试和验证流过每个环境。 例如，在开发环境中编写的代码必须是重新编写以便在测试环境中，然后重写时最后进入生产环境中。 此外，此代码将专门绑定到主机。 这会增加成本和维护您的应用程序的复杂性。 每个版本的应用程序绑定到每个环境。 增加了的复杂性和重复增加安全性和代码质量的风险。 此外，代码不能轻松地重新部署删除失败的还原主机或部署其他主机来处理需求增加时。

## <a name="solution"></a>解决方案

DevOps 模式，可生成、 测试和部署在多个云运行的应用。 此模式将的持续集成和持续交付实践结合在一起。 通过持续集成代码是生成和测试每次团队成员将更改提交到版本控制。 持续交付会自动从生成到生产环境中每个步骤。 在一起，这些进程创建支持跨各种环境部署发布过程。 使用此模式时，可以草稿代码，然后将相同的代码部署到内部部署环境、 不同私有云和公有云。 在环境中的差异需要对配置文件的更改，而不是对代码的更改。

![DevOps 模式](media/azure-stack-edge-pattern-hybrid-ci-cd/hybrid-ci-cd.png)

使用一组一致的跨本地、 私有云和公有云环境的开发工具，您可以实现一种做法的持续集成和持续交付。 应用和服务部署使用 DevOps 模式是可互换的并可以运行任何这些位置，利用本地和公有云特性和功能中。

使用 DevOps 发布管道可帮助你：

-   启动新的生成基于代码提交到单个存储库。

-   自动将新生成的代码部署到公有云，对于用户验收测试。

-   你的代码通过测试后，会自动将部署到私有云。

## <a name="issues-and-considerations"></a>问题和注意事项

DevOps 模式旨在确保无论目标环境的部署的一致性。 但是，功能在不同跨云和本地环境。 请注意以下几点：

-   函数、 终结点、 服务和其他资源在您的部署中有目标的部署位置？

-   在跨云可访问的位置中存储配置项目？

-   部署参数将在所有目标环境中工作？

-   是特定于资源的属性在所有目标云中可用？

有关详细信息，请参阅[开发 Azure 资源管理器模板的云一致性](https://docs.microsoft.com/azure/azure-resource-manager/templates-cloud-consistency)。

此外，当决定如何实现此模式时考虑以下几点：

### <a name="scalability-considerations"></a>可伸缩性注意事项

部署自动化系统是 DevOps 模式中的控件点。 实现可能存在差异。 正确的服务器大小的选择取决于所需工作负荷的大小。 Vm 的成本高于缩放容器。 但是，若要使用容器进行缩放，则必须配合容器运行生成过程。

### <a name="availability-considerations"></a>可用性注意事项

DevPattern 的上下文中的可用性是指能够恢复任何状态信息与您的工作流，如测试结果、 代码依赖项或其他项目相关联。 若要评估可用性要求，请考虑两个常见指标：

-   恢复时间目标 (RTO) 指定了多长时间可以转而无需系统。

-   恢复点目标 (RPO) 指示您可以承受丢失如果服务中断的数据量会影响系统。

在实践中，RTO 和 RPO 涉及到冗余和备份。 在全局 Azure 云中，可用性不是硬件恢复的问题 —，它是 Azure 的一部分 — 但而确保维持 DevOps 系统的状态。 在 Azure Stack 硬件恢复可能不需要考虑。

设计用于部署自动化的系统时的另一个主要考虑因素是访问控制和将服务部署到云环境所需的权限的适当管理。 需要创建、 删除或修改部署哪些权限？ 例如，一组权限通常需要在 Azure 和另一个资源组中部署服务中创建的资源组。

### <a name="manageability-considerations"></a>可管理性注意事项

基于 DevOps 模式任何系统的设计必须考虑自动化，日志记录，并跨产品组合为每个服务发出警报。 使用共享的服务、 应用程序团队或两者，并跟踪安全策略和管理以及。

部署生产环境和开发/测试环境中在 Azure 或 Azure Stack 上的单独的资源组。 然后可以监视每个环境的资源，并汇总计费成本按资源组。 还可以删除作为集的资源，这适用于测试部署。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

使用此模式：

-   可以在一个环境，以满足您的开发人员需要中开发代码和到你的解决方案则可能很难开发新代码将部署到特定的环境。

-   可以使用的代码和您的开发人员想要我们只要能够理解的持续集成和持续交付过程 DevOps 模式中的工具。

不推荐使用此模式：

-   如果你不能自动处理基础结构，预配资源、 配置、 标识和安全任务。

-   如果团队没有对混合云资源以实现持续集成/持续开发 (CI/CD) 方法的访问。

## <a name="example"></a>示例

了解如何使用混合持续集成/持续交付 (CI/CD) 管道将应用程序部署到 Azure 和 Azure Stack。

[将应用部署到 Azure 和 Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline)

## <a name="next-steps"></a>后续步骤

了解有关[适用于 Azure Stack 的混合云设计模式](azure-stack-edge-pattern-overview.md)
