---
title: Azure Stack 集线器中的跨云缩放模式
description: 了解如何在 Azure 和 Azure Stack 集线器上构建可伸缩的跨云应用。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: a830f96e97c347cbbcc09a1b17f4836ecb6eb3e6
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891020"
---
# <a name="cross-cloud-scaling-pattern"></a>跨云缩放模式

自动将资源添加到现有应用以适应负载增加。

## <a name="context-and-problem"></a>上下文和问题

应用无法提高容量来满足意外增长的需求。 缺少可伸缩性会导致用户在高峰期无法访问应用。 应用可为固定数目的用户提供服务。

全球企业需要安全、可靠且可用的基于云的应用。 满足需求增加，并使用适当的基础结构来支持这种需求非常重要。 为了在成本和维护以及业务数据安全、存储和实时可用性之间取得平衡，企业费尽了心力。

可能无法在公有云中运行应用。 但是，要让企业在本地环境中保持用于处理应用需求高峰的容量，在经济上似乎不切实际。 通过此模式，可对本地解决方案利用公有云的弹性。

## <a name="solution"></a>解决方案

跨云缩放模式使用公有云资源扩展本地云中的应用。 该模式在需求增大或减小时触发，并相应地在云中添加或删除资源。 这些资源提供冗余、快速可用性和地域合规的路由。

![跨云缩放模式](media/pattern-cross-cloud-scale/cross-cloud-scaling.png)

> [!NOTE]
> 此模式仅适用于应用的无状态组件。

## <a name="components"></a>组件

跨云缩放模式包括以下组件。

### <a name="outside-the-cloud"></a>云外

#### <a name="traffic-manager"></a>流量管理器

在该图中，它位于公有云组的外部，但需要能够协调本地数据中心和公有云中的流量。 均衡器通过监视终结点并在需要时提供故障转移，提供应用的高可用性。

#### <a name="domain-name-system-dns"></a>域名系统 (DNS)

域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。

### <a name="cloud"></a>云

#### <a name="hosted-build-server"></a>托管生成服务器

用于托管生成管道的环境。

#### <a name="app-resources"></a>应用资源

应用资源需要能够缩小和扩大，如虚拟机规模集和容器。

#### <a name="custom-domain-name"></a>自定义域名

使用自定义域名来路由请求 glob。

#### <a name="public-ip-addresses"></a>公共 IP 地址

公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用资源终结点。  

### <a name="local-cloud"></a>本地云

#### <a name="hosted-build-server"></a>托管生成服务器

用于托管生成管道的环境。

#### <a name="app-resources"></a>应用资源

应用资源需要能够进行缩减和横向扩展，如虚拟机规模集和容器。

#### <a name="custom-domain-name"></a>自定义域名

使用自定义域名来路由请求 glob。

#### <a name="public-ip-addresses"></a>公共 IP 地址

公共 IP 地址用于通过流量管理器将传入流量路由到公有云应用资源终结点。

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，请考虑以下几点：

### <a name="scalability"></a>可伸缩性

跨云缩放的关键组件提供按需缩放能力。 必须在公共和本地云基础结构之间进行缩放，并根据需要提供一致、可靠的服务。

### <a name="availability"></a>可用性

确定通过本地硬件配置和软件部署来配置本地部署的应用，以实现高可用性。

### <a name="manageability"></a>可管理性

跨云模式确保在环境之间提供无缝的管理和熟悉的界面。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

使用此模式：

- 需要提高应用程序容量来应对意外的需求或定期需求时。
- 当你不想投入仅在高峰期间使用的资源时。 只想为所用的资源付费时。

对于以下情况不建议使用此模式：

- 解决方案要求用户通过 Internet 进行连接。
- 企业所在地的法规要求发起的连接来自现场。
- 网络遇到使缩放性能受限的一般瓶颈。
- 你的环境已从 internet 断开连接，因此无法访问公有云。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：

- 请参阅 [Azure 流量管理器概述](/azure/traffic-manager/traffic-manager-overview)，详细了解这个基于 DNS 的流量负载均衡器的工作原理。
- 请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，了解有关最佳实践的详细信息，并获取任何其他问题的答案。
- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续阅读[跨云缩放解决方案部署指南](solution-deployment-guide-cross-cloud-scaling.md)。 该部署指南逐步说明了如何部署和测试 Azure Stack 的组件。 了解如何创建跨云解决方案，以提供手动触发的过程，以便从 Azure Stack 中心托管 web 应用切换到 Azure 托管的 web 应用。 此外，介绍了如何通过流量管理器使用自动缩放功能，来确保在承受负载的情况下保持云设施的灵活性和可伸缩性。
