---
title: 如何在 Azure Stack 集线器上管理 IoT 中心
description: IoT 中心管理体验和警报
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: ca410a3ec5b66a3ab3243ae06f82f4cb736af836
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049718"
---
# <a name="how-to-manage-iot-hub-on-azure-stack-hub"></a>如何在 Azure Stack 集线器上管理 IoT 中心

IoT 中心管理体验允许您直观显示和管理总体状态、警报和容量。

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

## <a name="iot-hub-management-dashboard"></a>IoT 中心管理仪表板

访问 IoT 中心管理仪表板：

1. 登录到 Azure Stack 中心管理员门户，在左侧选择 " **仪表板** " 视图，然后选择 " **IoT 中心** 资源提供程序"：

   [![操作员仪表板](media\iot-hub-rp-manage\dashboard.png)](media\iot-hub-rp-manage-capacity\dashboard.png#lightbox)

2. IoT 中心仪表板提供了一个摘要视图，其中显示当前警报、在戳记上创建的配额，以及订阅中 IoT 中心群集的总数： 

   [![iot 中心面板-概述](media\iot-hub-rp-manage\dashboard-rp-iot-hub-overview.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-overview.png#lightbox)

## <a name="alerts"></a>警报

IoT 中心资源提供程序支持以下警报：

|Category|警报|类型|说明|
|-|-|-|-|
|性能|IoT 中心 CPU 使用情况需要注意。|警告|过去6小时内 IoT 中心资源提供程序的平均 CPU 使用率百分比超过75%。|
|性能|IoT 中心内存使用情况需要注意。|警告|最近6小时内 IoT 中心资源提供程序的剩余内存使用率小于 1024 MB。|
|性能|用于 IoT 中心资源提供程序的磁盘空间不足。|警告|剩余磁盘空间少于10%。|
|资源|创建或更新 IoT 中心资源失败。|警告|在15分钟内，IoT 中心资源提供程序创建或更新 IoT 中心失败计数不会小于1。|
|服务|IoT 中心资源提供程序日志错误需要注意。|警告|每个角色实例的 IoT 中心资源提供程序日志失败计数超过15分钟。|
|服务|IoTHub-InternalError|警告|在过去30分钟内，IoTHub SU 内部故障和超时计数不小于5。|
|服务|IoT 中心数据平面备份失败。|警告|IoT 中心数据平面在过去15分钟内发生备份失败。 不会保护设备数据。|
|服务|IoT 中心数据平面连续备份失败。|警告|IoT 中心数据平面在过去15分钟内出现连续的备份失败。 不会保护设备数据。|
|服务|IoT 中心数据平面未成功备份。|警告|IoT 中心在过去3小时内没有成功的备份。 可能无法保护你的设备数据。|
|服务|IoT 中心数据平面还原失败。|警告|IoT 中心设备信息还原失败。 IotHubPerformanceMetrics 超过过去15分钟的阈值。|
|服务|IoT 中心网关出现故障。|警告|IoT 中心网关出现故障。 设备遥测功能可能会受到影响。|
|服务|IoT 中心容器出现故障。|警告|IoT 中心容器出现故障。 设备身份验证可能会失败。 |
|服务|IoT 中心设备管理容器出现故障。|警告|IoT 中心设备管理容器出现故障。 设备克隆，直接方法功能可能会降级。|

监视警报和配额：

1. 选择 " **警报** " 以查看警报历史记录： 
 
   [![iot 中心仪表板-警报](media\iot-hub-rp-manage\dashboard-rp-iot-hub-alerts.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-alerts.png#lightbox)  

2. 选择 " **配额** " 以查看有效配额列表：  

   > [!NOTE]
   > 在预览期间，禁用了 " **创建** " 功能，并提供了不受限制的默认配额。 将为 GA 启用 **创建** 。

   [![iot 中心仪表板-配额](media\iot-hub-rp-manage\dashboard-rp-iot-hub-quotas.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-quotas.png#lightbox) 

## <a name="capacity-management"></a>容量管理

作为操作员，你可以使用与任何云操作员相同的方式来管理和操作 Azure Stack 中心实例。 请确保软件安装正确、配置正确且安全，并 (HA) 、一致且高效地进行高可用性。 需要在 Azure Stack 中心实例和 IoT 中心资源提供程序的操作中应用容量管理原则。

### <a name="azure-stack-hub-capacity-management"></a>Azure Stack 集线器容量管理

若要确定 IoT 中心所需的容量，需要估计工作负荷，主要是设备数和消息吞吐量。 为了帮助进行规划，我们已在4节点 Azure Stack 中心环境中进行了以下测试以供参考：

| 方案 | VM | Vcore | 设备/集线器 | [中心版](https://azure.microsoft.com/pricing/details/iot-hub) | 中心 | 单元/中心 | 设备总数 | 中心单位总数 | 数百万条消息/天 |
|----------|---------------|------------------|-----------------------|-------------------|-|-|-|-|-|
|默认|5|20|300,000|S2|4|200|1200000|800|4,800|
|12个 Vm|12|48|500,000|S2|4|200|2000000|800|4,800|
|18个 Vm|18|72|400,000|S3|4|10|1600000|40|12,000|

有关更多详细信息，请参阅 [Azure Stack 中心 Capacity Planner](azure-stack-capacity-planner.md) 。

### <a name="iot-hub-capacity-management"></a>IoT 中心容量管理

由于 Azure Stack 中心部署在具有有限资源的本地数据中心内，因此在 Azure Stack 中心共享并为同一资源池争用的所有服务。 操作员需要根据业务需求规划和管理容量。 IoT 中心资源提供程序为操作员提供了管理服务的容量需求的能力。

IoT 中心具有单个 VM 类型。 作为 IoT 中心部署的一部分，系统会在 Azure Stack 集线器上预配一组 Vm。 Vm 可支持一定数量的设备和消息吞吐量。 默认设置应满足大多数需求。 但是，如果需要更多设备或更高的消息吞吐量，则可以使用管理员门户、CLI 或 PowerShell 来增加容量。 

监视和更改容量设置：

1. 选择左侧的 **容量** 视图。 你将看到容量状态，包括 IoT 中心群集中预配的 Vm 的数量及其资源利用率。 显示的节点数是当前分配给 IoT 中心的节点数。 

2. 若要增加容量，请选择 IoT 中心群集名称，更改节点数，然后选择 " **更新缩放** "。 可以通过将任意数量的 Vm 添加为可用资源，来增加容量。

   > [!IMPORTANT]
   > 预览版仅支持 IoT 中心群集横向扩展 (较小到较大的) 。  (GA) 版本的 IoT 中心支持 (更大到更小的) 。

   [![iot 中心仪表板-容量](media\iot-hub-rp-manage\dashboard-rp-iot-hub-capacity.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-capacity.png#lightbox)


## <a name="next-steps"></a>后续步骤

有关以下内容的详细信息：

Azure Stack Hub 监视功能（包括警报），请参阅[监视运行状况和警报](azure-stack-monitor-health.md)。

Azure Stack Hub 日志收集，请参阅 [Azure Stack 诊断日志收集概述](azure-stack-diagnostic-log-collection-overview.md)。

