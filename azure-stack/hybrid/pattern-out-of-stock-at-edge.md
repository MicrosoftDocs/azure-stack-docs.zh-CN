---
title: 用于在边缘使用 Azure 和 Azure Stack 边缘实现脱销检测的模式。
description: 了解如何使用 Azure 和 Azure Stack Edge 服务实现脱销检测。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 144163b415a5d5aaa914b2c36ab036b587acd999
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77688808"
---
# <a name="out-of-stock-detection-at-the-edge-pattern"></a>边缘模式的脱销检测

此模式说明了如何使用 Azure Stack 边缘或 Azure IoT Edge 设备和网络照相机来确定货位是否有库存物品。

## <a name="context-and-problem"></a>上下文和问题

物理零售商店会失去销售，因为当客户查找物品时，它不会出现在货位上。 但是，该项可能已在商店的背面，而不是 restocked。 商店希望更有效地利用其员工，并在需要重新安排物品时自动获得通知。

## <a name="solution"></a>解决方案

解决方案示例使用边缘设备（例如每个商店中的 Azure Stack 边缘），这会在商店中有效处理照相机中的数据。 此优化的设计允许商店仅向云中发送相关事件和映像。 该设计节省了带宽和存储空间，并确保了客户隐私。 从每个照相机中读取帧时，ML 模型会处理图像并返回任何库存区。 在本地 web 应用上显示图像和脱销区域。 此数据可以发送到时序见解环境，以在 Power BI 中显示见解。

![边缘解决方案体系结构脱销](media/pattern-out-of-stock-at-edge/solution-architecture.png)

解决方案的工作原理如下：
1. 图像通过 HTTP 或 RTSP 从网络摄像机中捕获。
2. 图像将被重设大小并发送到推理驱动程序，该驱动程序与 ML 模型进行通信，以确定是否有任何股票图像。
3. ML 模型会返回任何库存区。
4. 推断驱动程序将原始映像上传到 blob （如果已指定），并将该模型的结果发送到 Azure IoT 中心，并将边界盒处理器发送到设备。
5. 边界框处理器向图像添加边界框，并在内存中数据库中缓存图像路径。
6. Web 应用查询图像并按接收的顺序显示这些图像。
7. 来自 IoT 中心的消息在时序见解中进行聚合。
8. Power BI 使用时序见解中的数据显示一段时间内脱销项的交互式报表。


## <a name="components"></a>组件

此解决方案使用以下组件：

| 层 | 组件 | 说明 |
|----------|-----------|-------------|
| 本地硬件 | 网络照相机 | 需要一个网络摄像机，其中使用 HTTP 或 RTSP 源来提供用于推理的映像。 |
| Azure | Azure IoT 中心 | [Azure IoT 中心](/azure/iot-hub/)处理边缘设备的设备预配和消息传递。 |
|  | Azure Time Series Insights | [Azure 时序见解](/azure/time-series-insights/)存储来自 IoT 中心的消息以进行可视化。 |
|  | Power BI | [Microsoft Power BI](https://powerbi.microsoft.com/)提供了面向业务的脱销事件的报告。 Power BI 提供了一个易于使用的仪表板界面，用于查看来自 Azure 流分析的输出。 |
| Azure Stack 边缘或<br>Azure IoT Edge 设备 | Azure IoT Edge | [Azure IoT Edge](/azure/iot-edge/)协调本地容器的运行时并处理设备管理和更新。|
| | Azure 项目脑电波 | 在 Azure Stack 边缘设备上，[项目脑电波](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)使用现场可编程的入口阵列（fpga）来加速 ML 推断。|

## <a name="issues-and-considerations"></a>问题和注意事项

决定如何实现此解决方案时，请注意以下几点：

### <a name="scalability"></a>可伸缩性 

大多数机器学习模型只能在特定数量的帧上运行（具体取决于所提供的硬件）。 确定摄像机的最佳采样率，以确保 ML 管道不会备份。 不同类型的硬件将处理不同数量的相机和帧速率。

### <a name="availability"></a>可用性

如果边缘设备丢失连接，则需要考虑可能会发生的情况。 请考虑时序见解和 Power BI 仪表板中可能丢失的数据。 所提供的示例解决方案并未设计为具有高可用性。

### <a name="manageability"></a>可管理性

此解决方案可跨多个设备和位置，这可能会变得困难。 Azure 的 IoT 服务可自动使新的位置和设备联机，并使其保持最新状态。 还必须遵循适当的数据管理过程。

### <a name="security"></a>安全性

此模式处理潜在的敏感数据。 请确保密钥定期旋转，并正确设置 Azure 存储帐户和本地共享上的权限。 

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：
- 此模式使用多个 IoT 相关服务，包括[Azure IoT Edge](/azure/iot-edge/)、 [azure IoT 中心](/azure/iot-hub/)和[azure 时序见解](/azure/time-series-insights/)。
- 若要了解有关 Microsoft Project 脑电波的详细信息，请参阅[博客公告](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)和签出[Azure 加速机器学习与 Project 脑电波视频](https://www.youtube.com/watch?v=DJfMobMjCX0)。
- 有关最佳实践的详细信息，请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，并回答其他问题。
- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续学习[用于分析解决方案部署指南的分层数据](https://aka.ms/edgeinferencingdeploy)。 部署指南提供了有关部署和测试其组件的分步说明。