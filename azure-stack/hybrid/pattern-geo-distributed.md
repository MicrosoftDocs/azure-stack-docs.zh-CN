---
title: 使用 Azure 和 Azure Stack 集线器的智能边缘的地理分布式应用模式。
description: 了解使用 Azure 和 Azure Stack 集线器的智能边缘的地理分布式应用模式。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 37d4b517eec69ebab72aa3d82733829466b44fda
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875668"
---
# <a name="geo-distributed-pattern"></a>地理分布模式

了解如何在多个区域提供应用终结点，并根据位置和合规性需求来路由用户流量。

## <a name="context-and-problem"></a>上下文和问题

具有遍布各地的地区的企业会尽力安全且准确地分发和启用对数据的访问，同时确保每个用户、位置和设备的所需级别的安全性、符合性和性能。

## <a name="solution"></a>解决方案

Azure Stack 中心地理流量路由模式或异地分布式应用程序允许根据各种指标将流量定向到特定终结点。 使用基于地理的路由和终结点配置创建流量管理器会将流量路由到基于区域要求、公司和国际法规和数据需求的终结点。

![地理分布模式](media/pattern-geo-distributed/geo-distribution.png)

## <a name="components"></a>组件

**流量管理器**  
在此图中，流量管理器位于公有云外部，但需要能够协调本地数据中心和公有云中的流量。 均衡器将流量路由到地理位置。

**域名系统（DNS）**  
域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。

### <a name="public-cloud"></a>公有云

**云终结点**  
公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用程序资源终结点。  

### <a name="local-clouds"></a>本地云

**本地终结点**  
公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用程序资源终结点。

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，请考虑以下几点：

### <a name="scalability"></a>可伸缩性

该模式处理地理流量路由，而不是进行缩放以满足流量增加。 但是，可以将此模式与其他 Azure 和本地解决方案结合起来。 例如，可以将此模式与跨云缩放模式结合使用。

### <a name="availability"></a>可用性

确保通过本地硬件配置和软件部署为高可用性配置本地部署的应用。

### <a name="manageability"></a>可管理性

该模式确保了环境之间的无缝管理和熟悉的界面。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

- 我的组织有需要自定义区域安全和分发策略的国际分支。
- 我组织中的每个办公室都拉取了员工、业务和设施数据，需要按本地法规和时区报告活动。
- 只要对单个区域内和跨区域的多个应用部署进行应用的水平扩展，即可达到高缩放性要求以处理极高的负载要求。
- 即使单个区域发生故障，应用程序也必须具有高可用性和对客户端请求的响应能力。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：
- 请参阅[Azure 流量管理器概述](/azure/traffic-manager/traffic-manager-overview)，了解有关此基于 DNS 的流量负载均衡器工作原理的详细信息。
- 有关最佳实践的详细信息，请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，并回答其他问题。
- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续阅读[异地分布式应用解决方案部署指南](solution-deployment-guide-geo-distributed.md)。 部署指南提供了有关部署和测试其组件的分步说明。 了解如何根据各种指标使用地理分布式应用模式将流量定向到特定终结点。 使用基于地理的路由和终结点配置创建流量管理器配置文件，可确保将信息路由到基于区域要求、公司和国际法规和数据需求的终结点。