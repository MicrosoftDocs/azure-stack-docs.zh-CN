---
title: Azure Stack 的智能边缘的 DevOps 模式 |Microsoft Docs
description: 了解 Azure Stack 的智能边缘的 DevOps 模式。
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
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: deab520045d50acefb03691b9b127f99676061a0
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277710"
---
# <a name="geo-distributed-pattern"></a>地理分布模式

跨多个区域提供应用终结点。 根据位置和合规性需求来路由用户流量。

## <a name="context-and-problem"></a>上下文和问题

具有遍布各地的地区的企业会尽力安全且准确地分发和启用对数据的访问，同时确保每个用户、位置和设备的所需级别的安全性、符合性和性能。

## <a name="solution"></a>解决方案

Azure Stack 地理流量路由模式或异地分布式应用程序允许根据各种指标将流量定向到特定终结点。 使用基于地理的路由和终结点配置创建流量管理器会将流量路由到基于区域要求、公司和国际法规和数据需求的终结点。

![地理分布模式](media/azure-stack-edge-pattern-geo-distribution/geo-distribution.png)

**流量管理器**  
在该图中，它位于公有云的外部，但需要能够协调本地数据中心和公有云中的流量。 均衡器将流量路由到地理位置。

**域名系统（DNS）**  
域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。

### <a name="public-cloud"></a>公有云

**云终结点**  
公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用程序资源终结点。  

### <a name="local-clouds"></a>本地云

**本地终结点**  
公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用程序资源终结点。

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，应考虑以下几点：

### <a name="scalability-considerations"></a>可伸缩性注意事项

该模式处理地理流量路由，而不是进行缩放以满足流量增加。 但是，可以将此模式与其他 Azure 和本地解决方案结合起来。 例如，可以将此模式与跨云缩放模式结合使用。

### <a name="availability-considerations"></a>可用性注意事项

确定通过本地硬件配置和软件部署来配置本地部署的应用，以实现高可用性。

### <a name="manageability-considerations"></a>可管理性注意事项

该模式确保了环境之间的无缝管理和熟悉的界面。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

-   我的组织有需要自定义区域安全和分发策略的国际分支。

-   我组织中的每个办公室都拉取了员工、业务和设施数据，需要按本地法规和时区报告活动。

-   只要对单个区域内和跨区域的多个应用部署进行应用的水平扩展，即可达到高缩放性要求以处理极高的负载要求。

-   即使单个区域发生故障，应用程序也必须具有高可用性和对客户端请求的响应能力。

## <a name="example"></a>示例

了解如何根据各种指标，使用异地分布式应用模式将流量定向到特定的终结点。 使用基于地理位置的路由和终结点配置创建流量管理器配置文件可确保根据区域要求、企业与国际法规和数据需求将信息路由到终结点。

[使用 Azure 和 Azure Stack 创建异地分布式应用解决方案](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-geo-distributed)

## <a name="next-steps"></a>后续步骤

了解[Azure Stack 的混合云设计模式](azure-stack-edge-pattern-overview.md)
