---
title: 混合模式，用于实现使用 Azure 和 Azure Stack 中心实现的基于 AI 的 footfall 检测。
description: 了解如何使用 Azure 和 Azure Stack 中心服务来实现基于 AI 的 footfall 检测解决方案，以便分析零售商店流量。
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 10/31/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 10/31/2019
ms.openlocfilehash: a7a7563db3c315c4913287e8f286f07abd633602
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73638350"
---
# <a name="footfall-detection-pattern"></a>Footfall 检测模式

此模式提供了实现基于 AI 的 footfall 检测解决方案的概述，用于分析零售商店中的访问者流量。 该解决方案使用 Azure、Azure Stack 中心和自定义视觉 AI 开发工具包从实际操作中生成见解。

## <a name="context-and-problem"></a>上下文和问题

Contoso 商店希望获得有关客户如何接收其当前产品与商店布局相关的见解。 他们无法在每个部分中放置人员，因此，拥有一组分析人员审查整个商店的摄像机素材是低效的。 此外，其任何商店都没有足够的带宽来将视频从其所有相机流式传输到云以进行分析。 

Contoso 想要找出一种不引人注目的、可通过隐私识别的方式来确定其客户的人口统计、忠诚度和反应，以存储显示器和产品。

## <a name="solution"></a>解决方案

此零售 analytics 模式使用分层方法在边缘推断。 通过使用自定义视觉 AI 开发工具包，只会将具有人脸的图像发送到运行 Azure 认知服务的专用 Azure Stack 中心。 匿名，聚合数据将发送到 Azure，以便在 Power BI 中的所有存储和可视化对象中进行聚合。 结合使用边缘和公有云，Contoso 可以利用新式 AI 技术。 尽管与此同时，仍遵守公司政策并尊重客户的隐私。

[![Footfall 检测解决方案](media/pattern-retail-footfall-detection/solution-architecture.png)](media/pattern-retail-footfall-detection/solution-architecture.png)

下面是该解决方案的工作原理的摘要： 

1. 自定义视觉 AI 开发工具包从 IoT 中心获取配置，该配置将安装 IoT Edge 运行时和 ML 模型。
2. 如果该模型看到某个人，则会拍摄一张图片，并将其上传到 Azure Stack 中心 blob 存储。 
3. Blob 服务在 Azure Stack 集线器上触发 Azure 函数。 
4. Azure 函数会调用容器，其中包含人脸 API 以获取图像中的人口统计数据和情感数据。
5. 数据匿名并发送到 Azure 事件中心。
6. 事件中心将数据推送到流分析。
7. 流分析聚合数据并将其推送到 Power BI。

## <a name="components"></a>组件

此解决方案使用以下组件：

| 层 | 组件 | 描述 |
|----------|-----------|-------------|
| 应用商店内硬件 | [自定义视觉 AI 开发工具包](https://azure.github.io/Vision-AI-DevKit-Pages/) | 使用仅捕获要分析的人员图像的本地 ML 模型提供存储中筛选。 通过 IoT 中心安全地预配和更新。<br><br>|
| Azure | [Azure 事件中心](/azure/event-hubs/) | Azure 事件中心提供可缩放的平台，适用于与 Azure 流分析完美集成的引入匿名数据。 |
|  | [Azure 流分析](/azure/stream-analytics/) | Azure 流分析作业聚合匿名数据，并将其分组到15秒的窗口中进行可视化。 |
|  | [Microsoft Power BI](https://powerbi.microsoft.com/) | Power BI 提供了一个易于使用的仪表板界面，用于查看来自 Azure 流分析的输出。 |
| Azure Stack 中心 | [应用服务](../operator/azure-stack-app-service-overview.md) | 应用服务资源提供程序（RP）为边缘组件提供基。 包括 web 应用/Api 和函数的宿主和管理功能。 |
| | Azure Kubernetes 服务[（AKS）引擎](https://github.com/Azure/aks-engine)群集 | 将 AKS 群集部署到 Azure Stack 集线器中的 AKS RP 提供可缩放的弹性引擎来运行人脸 API 容器。 |
| | Azure 认知服务[人脸 API 容器](/azure/cognitive-services/face/face-how-to-install-containers)| 具有人脸 API 容器的 Azure 认知服务 RP 在 Contoso 的专用网络上提供了人口统计、情感和独特的访问者检测。 |
| | Blob 存储 | 从 AI 开发工具包捕获的图像将上传到 Azure Stack 集线器的 blob 存储。 |
| | Azure Functions | 在 Azure Stack 集线器上运行的 Azure 函数接收来自 blob 存储的输入，并管理与人脸 API 的交互。 它将匿名数据发送到位于 Azure 中的事件中心。<br><br>|

## <a name="issues-and-considerations"></a>问题和注意事项

决定如何实现此解决方案时，请注意以下几点：

### <a name="scalability"></a>可伸缩性 

若要使此解决方案能够跨多个照相机和位置进行扩展，需要确保所有组件都能处理增加的负载。 可能需要执行以下操作：

- 增加流分析流式处理单元数
- 横向扩展人脸 API 部署
- 提高事件中心吞吐量
- 对于极端情况，可能需要从 Azure Functions 迁移到虚拟机。

### <a name="availability"></a>可用性

由于此解决方案是分层的，因此请务必考虑如何处理网络或电源故障。 根据业务需求，实现一种机制，以便在本地缓存映像，然后在连接返回时转发到 Azure Stack 中心。 如果位置足够大，则使用人脸 API 容器将 Data Box Edge 部署到该位置可能是更好的选择。

### <a name="manageability"></a>可管理性

此解决方案可跨多个设备和位置，这可能会变得困难。 可以使用[Azure IoT 服务](/azure/iot-fundamentals/)自动使新的位置和设备联机，并使其保持最新状态。 

### <a name="security"></a>“安全”

此解决方案捕获客户映像，使安全性成为极其重要的考虑因素。 请确保所有存储帐户都使用正确的访问策略进行保护，并定期轮换密钥。 确保存储帐户和事件中心具有符合公司和政府隐私法规的保留策略。 另外，请确保对用户访问级别进行分层。 分层确保用户只能访问其角色所需的数据。

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：
- 请参阅 footfall 检测模式使用的[分层数据模式](https://aka.ms/tiereddatadeploy)。
- 有关使用自定义视觉的详细信息，请参阅[自定义视觉 AI 开发工具包](https://azure.github.io/Vision-AI-DevKit-Pages/)。 

准备好测试解决方案示例时，请继续学习[Footfall 检测部署指南](solution-deployment-guide-retail-footfall-detection.md)。 部署指南提供了有关部署和测试其组件的分步说明。