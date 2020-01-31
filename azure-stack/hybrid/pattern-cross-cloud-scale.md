---
title: 用于生成可在 Azure 和 Azure Stack 集线器上跨云缩放的应用程序的模式。
description: 了解如何使用 Azure 和 Azure Stack 中心构建可伸缩的跨云应用程序。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 4bf4a7b868822300c39275b8763798007cdada29
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76877368"
---
# <a name="cross-cloud-scaling-pattern"></a>跨云缩放模式

自动将资源添加到现有应用程序，以适应负载增加的情况。

## <a name="context-and-problem"></a>上下文和问题

应用无法增加容量以满足意外的需求增长。 这种伸缩性缺乏可伸缩性，导致用户在高峰使用时间未到达应用。 应用可为固定数量的用户服务。

全球企业需要安全、可靠且可用的基于云的应用程序。 满足需求增加，并使用适当的基础结构来支持这种需求非常重要。 企业努力通过业务数据安全性、存储和实时可用性来平衡成本和维护。

你可能无法在公有云中运行你的应用程序。 不过，企业不能经济地维护本地环境中所需的容量来处理应用程序的需求峰值。 使用此模式，可以将公有云的弹性与本地解决方案一起使用。

## <a name="solution"></a>解决方案

跨云缩放模式使用公有云资源扩展位于本地云中的应用。 模式由按需增加或减少的方式触发，并分别在云中添加或删除资源。 这些资源提供冗余、快速可用性和与地域兼容的路由。

![跨云缩放模式](media/pattern-cross-cloud-scale/cross-cloud-scaling.png)

> [!NOTE]
> 此模式仅适用于应用的无状态组件。

## <a name="components"></a>组件

跨云缩放模式包括以下组件。

**流量管理器**  

在该图中，它位于公有云组的外部，但需要能够协调本地数据中心和公有云中的流量。 均衡器通过监视终结点并在需要时提供故障转移，为应用程序提供高可用性。

**域名系统（DNS）**  

域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。

### <a name="cloud"></a>云

**托管生成服务器**  
用于承载生成管道的环境。

**应用程序资源**  
应用程序资源需要能够缩小和扩大，例如 VM ScaleSets 和容器。

**自定义域名**  
将自定义域名用于路由请求 glob。

**公共 IP 地址**  
公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用程序资源终结点。  

### <a name="local-cloud"></a>本地云

**托管生成服务器**  
用于承载生成管道的环境。

**应用程序资源**  
应用程序资源需要能够进行缩减和扩展，例如 VM ScaleSets 和容器。

**自定义域名**  
将自定义域名用于路由请求 glob。

**公共 IP 地址**  
公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用程序资源终结点。 

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，请考虑以下几点：

### <a name="scalability"></a>可伸缩性

跨云缩放的关键组件是能够交付按需缩放。 必须在公共和本地云基础结构之间进行缩放，并根据需要提供一致、可靠的服务。

### <a name="availability"></a>可用性

确保通过本地硬件配置和软件部署为高可用性配置本地部署的应用。

### <a name="manageability"></a>可管理性

跨云模式确保了环境之间的无缝管理和熟悉的界面。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

使用此模式：

- 当你需要增加应用程序的容量时，需要根据意外需求或定期需求。
- 如果你不想投入仅在高峰期间使用的资源。 为你使用的部分付费。

不建议在以下情况下使用此模式：

- 解决方案需要用户通过 internet 进行连接。
- 你的业务具有要求发起连接的本地法规，来自现场呼叫。
- 你的网络会经历会限制缩放性能的定期瓶颈。
- 你的环境已从 Internet 断开连接，因此无法访问公有云。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：
- 请参阅[Azure 流量管理器概述](/azure/traffic-manager/traffic-manager-overview)，了解有关此基于 DNS 的流量负载均衡器工作原理的详细信息。
- 有关最佳实践的详细信息，请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，并回答其他问题。
- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续学习[跨云缩放解决方案部署指南](solution-deployment-guide-cross-cloud-scaling.md)。 部署指南提供了有关部署和测试其组件的分步说明。 了解如何创建跨云解决方案，以提供手动触发的过程，以便从 Azure Stack 中心托管的 web 应用程序切换到 Azure 托管的 web 应用。 你还将了解如何通过流量管理器使用自动缩放，以确保在负载下灵活且可缩放的云实用程序。
