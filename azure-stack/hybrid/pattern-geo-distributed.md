---
title: Azure Stack 中心内的地理分布式应用模式
description: 了解使用 Azure 和 Azure Stack Hub 的智能边缘的地理分散式应用模式。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod2019
ms.openlocfilehash: 1f6243927390c7a520c2607c722664b2d31fc07f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891021"
---
# <a name="geo-distributed-app-pattern"></a>异地分布式应用模式

了解如何在多个区域提供应用终结点，并根据位置和合规性需求来路由用户流量。

## <a name="context-and-problem"></a>上下文和问题

在地理上分散的组织需致力于以安全且准确的方式分发数据并允许对数据进行访问，同时确保按用户、位置和设备跨边界实现所需级别的安全性、符合性和性能。

## <a name="solution"></a>解决方案

Azure Stack 中心地理流量路由模式或异地分布式应用程序，可根据各种指标将流量定向到特定终结点。 使用基于地理位置的路由和终结点配置创建流量管理器即可根据区域要求、企业与国际法规和数据需求将流量路由到终结点。

![地理分散模式](media/pattern-geo-distributed/geo-distribution.png)

## <a name="components"></a>组件

### <a name="outside-the-cloud"></a>云外

#### <a name="traffic-manager"></a>流量管理器

在此图中，流量管理器位于公有云的外部，但它需要能够协调本地数据中心和公有云中的流量。 均衡器将流量路由到各个地理位置。

#### <a name="domain-name-system-dns"></a>域名系统 (DNS)

域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。

### <a name="public-cloud"></a>公有云

#### <a name="cloud-endpoint"></a>云终结点

公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用资源终结点。  

### <a name="local-clouds"></a>本地云

#### <a name="local-endpoint"></a>本地终结点

公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用资源终结点。

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，请考虑以下几点：

### <a name="scalability"></a>可伸缩性

此模式用于处理地理流量路由，而不是通过缩放来应对流量增加的情况。 但是，可以将此模式与其他 Azure 和本地解决方案结合使用。 例如，可以将此模式与跨云缩放模式结合使用。

### <a name="availability"></a>可用性

确定通过本地硬件配置和软件部署来配置本地部署的应用，以实现高可用性。

### <a name="manageability"></a>可管理性

此模式可确保在环境之间提供无缝的管理和熟悉的界面。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

- 我的组织有海外分公司，需要自定义的区域安全性和分发策略。
- 我组织中的每个办公室都拉取了员工、业务和设施数据，需要按本地法规和时区报告活动。
- 通过横向扩展应用程序可满足大规模要求，在单个区域内或跨区域进行多个应用部署，以处理极高的负载要求。
- 即使在单区域服务中断期间，应用程序也必须具有高可用性并且能够响应客户端请求。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：

- 请参阅 [Azure 流量管理器概述](/azure/traffic-manager/traffic-manager-overview)，详细了解这个基于 DNS 的流量负载均衡器的工作原理。
- 请参阅[混合应用设计注意事项](overview-app-design-considerations.md)，了解有关最佳实践的详细信息，并获取任何其他问题的答案。
- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续阅读[地理分散式应用解决方案部署指南](solution-deployment-guide-geo-distributed.md)。 该部署指南逐步说明了如何部署和测试 Azure Stack 的组件。 了解如何根据各种指标使用地理分布式应用模式将流量定向到特定终结点。 使用基于地理位置的路由和终结点配置创建流量管理器配置文件可确保根据区域要求、企业与国际法规和数据需求将信息路由到终结点。
