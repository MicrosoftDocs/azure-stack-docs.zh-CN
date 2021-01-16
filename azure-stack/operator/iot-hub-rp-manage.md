---
title: 如何管理 Azure Stack Hub 上的 IoT 中心
description: IoT 中心管理体验和警报
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 3fe1459d28c399269ccdb1d4cffed024bd741000
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255735"
---
# <a name="how-to-manage-iot-hub-on-azure-stack-hub"></a>如何管理 Azure Stack Hub 上的 IoT 中心

通过 IoT 中心管理体验，你可以可视化和管理总体状态、警报和容量。

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

## <a name="iot-hub-management-dashboard"></a>IoT 中心管理仪表板

若要访问 IoT 中心管理仪表板，请执行以下操作：

1. 登录到 Azure Stack Hub 管理员门户，选择左侧的“仪表板”视图，然后选择“IoT 中心”资源提供程序 ：

   [![操作员仪表板](media\iot-hub-rp-manage\dashboard.png)](media\iot-hub-rp-manage-capacity\dashboard.png#lightbox)

2. IoT 中心仪表板提供摘要视图，显示当前警报、在戳记上创建的配额以及订阅中的 IoT 中心群集总数： 

   [![IoT 中心仪表板 - 概览](media\iot-hub-rp-manage\dashboard-rp-iot-hub-overview.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-overview.png#lightbox)

## <a name="alerts"></a>警报

IoT 中心资源提供程序支持以下警报：

|Category|警报|类型|说明|
|-|-|-|-|
|性能|IoT 中心 CPU 使用情况需要引起注意。|警告|在过去 6 小时内，IoT 中心资源提供程序的 CPU 使用量百分比平均超过 75%。|
|性能|IoT 中心内存使用情况需要引起注意。|警告|过去 6 小时内 IoT 中心资源提供程序的剩余内存使用量小于 1024 MB。|
|性能|用于 IoT 中心资源提供程序的磁盘空间不足。|警告|剩余磁盘空间少于10%。|
|资源|创建或更新 IoT 中心资源失败。|警告|IoT 中心资源提供程序创建或更新 IoT 中心故障计数在 15 分钟内不少于 1。|
|服务|IoT 中心资源提供程序日志错误需要引起注意。|警告|IoT 中心资源提供程序每个角色实例的日志失败计数在 15 分钟内超过 3。|
|服务|IoTHub-SU-InternalError|警告|IoTHub SU 内部故障和超时计数过去 30 分钟内不少于 5。|
|服务|IoT 中心数据平面备份失败。|警告|IoT 中心数据平面在过去 15 分钟内出现备份失败。 设备数据将不受保护。|
|服务|IoT 中心数据平面已发生连续备份失败。|警告|IoT 中心数据平面在过去 15 分钟内出现连续的备份故障。 设备数据将不受保护。|
|服务|IoT 中心数据平面未成功备份。|警告|IoT 中心在过去 3 小时内未成功备份。 设备数据将不受保护。|
|服务|IoT 中心数据平面还原失败。|警告|IoT 中心设备信息还原失败。 IotHubPerformanceMetrics 在过去 15 分钟内超过阈值。|
|服务|IoT 中心网关出现故障。|警告|IoT 中心网关出现故障。 设备遥测功能可能会受到影响。|
|服务|IoT 中心容器出现故障。|警告|IoT 中心容器出现故障。 设备身份验证可能失败。 |
|服务|IoT 中心设备管理容器出现故障。|警告|IoT 中心设备管理容器出现故障。 设备孪生、直接方法功能可能会降级。|

监视警报和配额：

1. 选择“警报”以查看警报历史记录： 
 
   [![IoT 中心仪表板 - 警报](media\iot-hub-rp-manage\dashboard-rp-iot-hub-alerts.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-alerts.png#lightbox)  

2. 选择“配额”以查看有效配额列表：  

   > [!NOTE]
   > 在预览期间，禁用“创建”功能并提供无限制的默认配额。 正式发布版将启用“创建”。

   [![中心仪表板 - 配额](media\iot-hub-rp-manage\dashboard-rp-iot-hub-quotas.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-quotas.png#lightbox) 

## <a name="capacity-management"></a>容量管理

作为操作员，你可以采用与任何云操作员相同的方式管理和操作 Azure Stack Hub 实例。 确保软件安装正确、配置正确且安全，并一致有效运行，实现高可用性 (HA)。 在操作 Azure Stack Hub 实例和 IoT 中心资源提供程序时，需要应用容量管理原则。

### <a name="azure-stack-hub-capacity-management"></a>Azure Stack Hub 容量管理

若要确定 IoT 中心所需的容量，需要估计工作负载，主要是设备数量和消息吞吐量。 为了协助规划，我们在 4 节点 Azure Stack Hub 环境中进行了以下测试以供参考：

| 方案 | VM | Vcores | 设备数/中心 | [中心版本](https://azure.microsoft.com/pricing/details/iot-hub) | 中心 | 单位数/中心 | 设备总数 | 总中心单位 | 数百万条消息/天 |
|----------|---------------|------------------|-----------------------|-------------------|-|-|-|-|-|
|默认|5|20|300,000|S2|4|200|1,200,000|800|4,800|
|12 个 VM|12|48|500,000|S2|4|200|2,000,000|800|4,800|
|18 个 VM|18|72|400,000|S3|4|10|1,600,000|40|12,000|

有关详细信息，请参阅 [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md)。

### <a name="iot-hub-capacity-management"></a>IoT 中心容量管理

由于 Azure Stack Hub 部署在资源有限的本地数据中心中，因此在 Azure Stack Hub 上运行的所有服务都将共享相同的资源池并为之竞争。 操作员需要根据业务需求规划和管理容量。 IoT 中心资源提供程序使操作员能够管理服务的容量要求。

IoT 中心只有一个 VM 类型。 在 IoT 中心的部署过程中，系统会在 Azure Stack Hub 上提供一组这样的 VM。 VM 可以支持一定数量的设备和消息吞吐量。 默认设置应满足大多数需求。 但是，如果你需要更多设备或更高的消息吞吐量，可以使用管理员门户、CLI 或 PowerShell 增加容量。 

监视和更改容量设置：

1. 选择左侧的“容量”视图。 你将看到容量状态，包括 IoT 中心群集中预配的 VM 数量及其资源利用率。 显示的节点数是分配给 IoT 中心当前的节点数。 

2. 若要增加容量，请选择 IoT 中心群集名称，更改节点数，然后选择“更新缩放”。 可以通过添加可用资源允许的尽可能多的 VM 来增加容量。

   > [!IMPORTANT]
   > IoT 中心群集横向扩展（从小到大）仅支持预览。 IoT 中心的正式发布 (GA) 版本将支持横向缩减（从大到小）。

   [![中心仪表板 - 容量](media\iot-hub-rp-manage\dashboard-rp-iot-hub-capacity.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-capacity.png#lightbox)


## <a name="next-steps"></a>后续步骤

有关以下内容的详细信息：

Azure Stack Hub 监视功能（包括警报），请参阅[监视运行状况和警报](azure-stack-monitor-health.md)。

Azure Stack Hub 日志收集，请参阅 [Azure Stack 诊断日志收集概述](./diagnostic-log-collection.md)。