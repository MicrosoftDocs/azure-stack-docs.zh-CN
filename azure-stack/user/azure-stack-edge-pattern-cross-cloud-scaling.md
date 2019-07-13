---
title: 跨云与 Azure Stack 的智能边缘的缩放模式 |Microsoft Docs
description: 了解如何使用 Azure Stack 的智能边缘的跨云缩放模式
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
ms.openlocfilehash: 2b97be7ecfffe83e560d32fbaac6480c8c17bb88
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856372"
---
# <a name="cross-cloud-scaling-pattern"></a>跨云缩放模式

自动将资源添加到现有应用程序以适应增加的负载。

## <a name="context-and-problem"></a>上下文和问题

您的应用程序不能增加容量来满足意外增加的需求。 这会导致不在高峰使用情况到达应用程序的用户。 应用可以固定的数量的用户提供服务。

全球的企业需要安全、 可靠且可用的基于云的应用程序。 会议会增加的需求，并且使用正确的基础结构来支持这种需求是关键。 企业绞尽脑汁，希望使用业务数据的安全性、 存储和实时可用性平衡成本和维护。

您不能在公有云中运行应用程序。 但是，要让企业在本地环境中保持用于处理应用需求高峰的容量，在经济上似乎不切实际。 使用此模式时，可以使用你的本地解决方案使用公有云的灵活性。

## <a name="solution"></a>解决方案

跨云缩放模式扩展位于公有云资源的本地云应用。 该模式触发的增加或减少的需求，并分别添加或删除资源在云中。 这些资源提供冗余、 快速的可用性和异地符合路由。

![跨云缩放模式](media/azure-stack-edge-pattern-cross-cloud-scaling/cross-cloud-scaling.png)

> 注意:  
> 此模式仅适用于无状态应用程序。

跨云缩放模式由以下组件构成。

**流量管理器**  
\* * 在关系图中，这是外部的公共云组，但它需要能够协调流量在本地数据中心和公有云。 均衡器提供了通过监视终结点并提供故障转移重新分发时所需的应用程序高可用性。

**域名系统 (DNS)**  
域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。

### <a name="cloud"></a>云

**托管的生成服务器**  
一个环境，用于承载生成管道。

**应用程序资源**  
需要能够缩小和扩大，例如 VM ScaleSets 和容器应用程序资源。

**自定义域名**  
使用路由请求 glob 的自定义域名称。

**公共 IP 地址**  
公共 IP 地址用于将通过流量管理器的传入流量路由到公共云应用程序资源终结点。  

### <a name="local-cloud"></a>本地云

**托管的生成服务器**  
一个环境，用于承载生成管道。

**应用程序资源**  
需要能够缩小和扩大，例如 VM ScaleSets 和容器应用程序资源。

**自定义域名**  
使用路由请求 glob 的自定义域名称。

**公共 IP 地址**  
公共 IP 地址用于将通过流量管理器的传入流量路由到公共云应用程序资源终结点。 

## <a name="issues-and-considerations"></a>问题和注意事项


在决定如何实现此模式时，请考虑以下几点：

### <a name="scalability-considerations"></a>可伸缩性注意事项

跨云缩放的关键组件是提供按需缩放需求中规定证明一致、 可靠的服务的公用和本地云基础结构之间的功能。

### <a name="availability-considerations"></a>可用性注意事项

确定通过本地硬件配置和软件部署来配置本地部署的应用，以实现高可用性。

### <a name="manageability-considerations"></a>可管理性注意事项

无缝管理和环境之间熟悉的界面，可确保跨云模式。

#### <a name="when-to-use-this-pattern"></a>何时使用此模式

使用此模式：

-   当需要提高应用程序容量，意外的需求或周期性需求的需求。

-   当您不想要投入将仅在峰值期间使用的资源。 为你使用的部分付费。

不建议使用此模式时：

-   您的解决方案需要通过 internet 连接的用户。

-   您的公司有本地法规，要求来自现场调用在发起连接。

-   你的网络体验将限制对性能的缩放比例的正则瓶颈。

-   你的环境从 Internet 断开连接，并且不能连接到公共云。

## <a name="example"></a>示例

了解如何创建可提供手动触发过程的跨云解决方案，以通过流量管理器使用自动缩放功能从 Azure Stack 托管的 Web 应用切换到 Azure 托管的 Web 应用，以此确保云实用工具在承受负载时保持灵活性和可伸缩性。

[使用 Azure 创建跨云缩放解决方案](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-cloud-burst)

## <a name="next-steps"></a>后续步骤

了解有关[适用于 Azure Stack 的混合云设计模式](azure-stack-edge-pattern-overview.md)
