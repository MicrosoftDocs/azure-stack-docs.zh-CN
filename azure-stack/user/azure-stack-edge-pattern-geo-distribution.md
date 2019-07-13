---
title: 使用 Azure Stack 的智能边缘的 DevOps 模式 |Microsoft Docs
description: 了解如何使用 Azure Stack 的智能边缘的 DevOps 模式。
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
ms.openlocfilehash: 07e0c3f0ca52c7079b879050ab36bf6a5f166f92
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856361"
---
# <a name="geo-distributed-pattern"></a>异地分布式模式

跨多个区域提供应用程序终结点。 将根据位置和法规遵从性需求的用户流量路由。

## <a name="context-and-problem"></a>上下文和问题

组织提供广泛的地理区域致力于安全准确地分发并启用对数据的访问，同时确保在边框之间的所需的级别的安全性、 符合性和每个用户、 位置和设备的性能。

## <a name="solution"></a>解决方案

Azure Stack 的地理流量路由模式或 Geo-Distributed 应用，允许流量定向到特定终结点根据各种指标。 创建具有基于地理位置的路由和终结点配置的路由的流量管理器流量到终结点基于区域的要求、 公司和国际法规和数据的需求。

![异地分布式模式](media/azure-stack-edge-pattern-geo-distribution/geo-distribution.png)

**流量管理器**  
在关系图中，这是外部公有云，但它需要能够协调流量在本地数据中心和公有云。 该均衡器将流量路由到地理位置。

**域名系统 (DNS)**  
域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。

### <a name="public-cloud"></a>公有云

**云终结点**  
公共 IP 地址用于将通过流量管理器的传入流量路由到公共云应用程序资源终结点。  

### <a name="local-clouds"></a>本地云

**本地终结点**  
公共 IP 地址用于将通过流量管理器的传入流量路由到公共云应用程序资源终结点。

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，应考虑以下几点：

### <a name="scalability-considerations"></a>可伸缩性注意事项

该模式处理地理流量路由，而不是缩放以满足增加的流量。 但是，您可将此模式与其他 Azure 和本地解决方案。 例如，此模式可以用于跨云缩放模式。

### <a name="availability-considerations"></a>可用性注意事项

确定通过本地硬件配置和软件部署来配置本地部署的应用，以实现高可用性。

### <a name="manageability-considerations"></a>可管理性注意事项

该模式可确保无缝管理和环境之间熟悉的界面。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

-   我的组织有需要自定义区域的安全和通讯组策略的国际分支。

-   我的组织机构的每个拉取员工、 业务和设施的数据，需要报告每个本地法规和时区的活动。

-   只要对单个区域内和跨区域的多个应用部署进行应用的水平扩展，即可达到高缩放性要求以处理极高的负载要求。

-   应用程序必须高度可用且高度可响应甚至发生单区域中断时的客户端请求。

## <a name="example"></a>示例

了解如何根据各种指标，使用异地分布式应用模式将流量定向到特定的终结点。 使用基于地理位置的路由和终结点配置创建流量管理器配置文件可确保根据区域要求、企业与国际法规和数据需求将信息路由到终结点。

[使用 Azure 和 Azure Stack 创建异地分布式应用解决方案](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-geo-distributed)

## <a name="next-steps"></a>后续步骤

了解有关[适用于 Azure Stack 的混合云设计模式](azure-stack-edge-pattern-overview.md)
