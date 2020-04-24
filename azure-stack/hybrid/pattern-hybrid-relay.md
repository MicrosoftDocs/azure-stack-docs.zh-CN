---
title: Azure 中的混合中继模式和 Azure Stack 中心
description: 使用 Azure 中的混合中继模式和 Azure Stack 中心连接到受防火墙保护的边缘资源。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: abb4f2b779afb7450c04b98d1d6b02d824446b09
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891002"
---
# <a name="hybrid-relay-pattern"></a>混合中继模式

了解如何使用混合中继模式和 Azure 服务总线中继连接到边缘资源或受防火墙保护的设备。

## <a name="context-and-problem"></a>上下文和问题

边缘设备通常位于企业防火墙或 NAT 设备的后面。 尽管它们是安全的，但是它们可能无法与其他企业网络上的公有云或边缘设备通信。 可能有必要以安全方式向公有云中的用户公开特定的端口和功能。

## <a name="solution"></a>解决方案

混合中继模式使用 Azure 服务总线中继在两个终结点之间建立不能直接进行通信的 Websocket 隧道。 不在本地但需要连接到本地终结点的设备将连接到公有云中的终结点。 此终结点将通过安全通道重定向预定义路由上的流量。 本地环境中的终结点接收流量，并将其路由到正确的目标。

![混合中继模式解决方案体系结构](media/pattern-hybrid-relay/solution-architecture.png)

混合中继模式的工作原理如下：

1. 设备在预定义的端口上连接到 Azure 中的虚拟机（VM）。
2. 流量转发到 Azure 中的服务总线中继。
3. Azure Stack 集线器上的 VM 已建立到服务总线中继的长期连接，接收流量并将其转发到目标。
4. 本地服务或终结点处理请求。

## <a name="components"></a>组件

此解决方案使用以下组件：

| 层 | 组件 | 说明 |
|----------|-----------|-------------|
| Azure | Azure VM | Azure VM 提供本地资源的可公开访问终结点。 |
| | Azure 服务总线中继 | [Azure 服务总线中继](/azure/service-bus-relay/)提供基础结构来维护 Azure VM 与 Azure Stack Hub VM 之间的隧道和连接。|
| Azure Stack Hub | 计算 | Azure Stack Hub VM 提供混合中继隧道的服务器端。 |
| | 存储 | 部署到 Azure Stack 集线器中的 AKS 引擎群集提供了一个可缩放的弹性引擎来运行人脸 API 容器。|

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此解决方案时，请考虑以下几点：

### <a name="scalability"></a>可伸缩性

此模式只允许在客户端与服务器上进行 1:1 端口映射。 例如，如果端口 80 已用于 Azure 终结点上的一个服务的隧道传输，则该端口不可用于另一个服务。 应该相应地规划好端口映射。 服务总线中继和 VM 应该相应地缩放以处理流量。

### <a name="availability"></a>可用性

这些隧道和连接不是冗余的。 若要确保高可用性，可以实现错误检查代码。 另一种做法是在负载均衡器后面使用一个与服务总线中继连接的 VM 池。

### <a name="manageability"></a>可管理性

此解决方案可能跨越许多设备和位置，因而变得不好管理。 Azure 的 IoT 服务可自动使新的位置和设备联机，并使其保持最新状态。

### <a name="security"></a>安全性

这种模式允许从边缘自由访问内部设备上的端口。 请考虑对内部设备上的服务或者混合中继终结点前面的服务添加身份验证机制。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：

- 此模式使用 Azure 服务总线中继。 有关详细信息，请参阅 [Azure 服务总线中继文档](/azure/service-bus-relay/)。
- 有关最佳实践的详细信息，请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，并获取其他任何问题的答案。
- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续阅读[混合中继解决方案部署指南](https://aka.ms/hybridrelaydeployment)。 该部署指南逐步说明了如何部署和测试 Azure Stack 的组件。