---
title: 使用 Azure 和 Azure Stack 集线器实现混合中继解决方案的模式。
description: 了解如何使用 Azure 和 Azure Stack 中心服务连接到边缘资源或受防火墙保护的设备。
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 9b9defd4a6aec6dab3511425089e7395649d8087
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73638581"
---
# <a name="hybrid-relay-pattern"></a>混合中继模式

了解如何使用 Azure 服务总线中继连接到通过防火墙保护的资源或设备。

## <a name="context-and-problem"></a>上下文和问题

边缘设备通常位于企业防火墙或 NAT 设备的后面。 尽管它们是安全的，但是它们可能无法与公有云或其他企业网络上的边缘设备通信。 可能需要以安全方式向公有云中的用户公开特定的端口和功能。 

## <a name="solution"></a>解决方案

混合中继模式使用 Azure 服务总线中继建立 Websocket 隧道，两个终结点之间无法直接通信。 不在本地但需要连接到本地终结点的设备将连接到公有云中的终结点。 此终结点将通过安全通道上的预定义路由重定向流量。 本地环境中的终结点接收流量，并将流量路由到正确的目标。 

![混合中继解决方案体系结构](media/pattern-hybrid-relay/solution-architecture.png)

解决方案的工作原理如下： 

1. 设备在预定义的端口上连接到 Azure 中的虚拟机（VM）。
2. 流量会转发到 Azure 中的服务总线中继。
3. Azure Stack 集线器上已建立到服务总线中继的长期连接的 VM 接收流量，并将其转发到目标。
4. 本地服务或终结点处理请求。 

## <a name="components"></a>组件

此解决方案使用以下组件：

| 层 | 组件 | 描述 |
|----------|-----------|-------------|
| Azure | Azure VM | Azure VM 为本地资源提供可公开访问的终结点。 |
| | Azure 服务总线中继 | [Azure 服务总线中继](/azure/service-bus-relay/)提供基础结构，用于维护 Azure vm 与 AZURE STACK 中心 vm 之间的隧道和连接。|
| Azure Stack 中心 | 计算 | Azure Stack 中心 VM 提供混合中继隧道的服务器端。 |
| | 存储空间 | 部署到 Azure Stack 集线器中的 AKS 引擎群集提供了一个可缩放的弹性引擎来运行人脸 API 容器。|

## <a name="issues-and-considerations"></a>问题和注意事项

决定如何实现此解决方案时，请注意以下几点：

### <a name="scalability"></a>可伸缩性 

此模式仅允许在客户端和服务器上进行1:1 端口映射。 例如，如果端口80是 Azure 终结点上某个服务的隧道，则它不能用于其他服务。 应相应地计划端口映射。 应适当调整服务总线中继和 Vm 来处理流量。

### <a name="availability"></a>可用性

这些隧道和连接不是冗余的。 若要确保高可用性，你可能想要实现错误检查代码。 另一种方法是将服务总线中继连接的 Vm 放在负载均衡器后面。

### <a name="manageability"></a>可管理性

此解决方案可跨多个设备和位置，这可能会变得困难。 Azure 的 IoT 服务可自动使新的位置和设备联机，并使其保持最新状态。

### <a name="security"></a>“安全”

此模式如下所示，允许自由从边缘访问内部设备上的端口。 考虑将身份验证机制添加到内部设备上的服务，或添加到混合中继终结点前面。 

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：
- 此模式使用 Azure 服务总线中继。 有关详细信息，请参阅[Azure 服务总线中继文档](/azure/service-bus-relay/)。
- 有关最佳实践的详细信息，请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，并回答其他问题。
- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续阅读[混合中继解决方案部署指南](https://aka.ms/hybridrelaydeployment)。 部署指南提供了有关部署和测试其组件的分步说明。