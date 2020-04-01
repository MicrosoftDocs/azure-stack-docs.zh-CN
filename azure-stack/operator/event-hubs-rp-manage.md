---
title: 如何在 Azure Stack 集线器上管理事件中心
description: 了解如何在 Azure Stack 集线器上管理事件中心资源提供程序。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e9f500da4cab68688a6dd33374cfbc1cc166828d
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424033"
---
# <a name="how-to-manage-event-hubs-on-azure-stack-hub"></a>如何在 Azure Stack 集线器上管理事件中心

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

事件中心管理体验允许您控制服务并可视化服务的状态和警报。 

## <a name="overview"></a>概述

使用以下步骤来访问 "事件中心管理" 页：

1. 登录到 Azure Stack 中心管理员门户。
2. 从左侧窗格中选择 "**所有服务**"。
3. 搜索 "事件中心"，然后选择服务。 如果找不到事件中心服务，则必须先[安装](event-hubs-rp-install.md)资源提供程序。
4. 显示 "事件中心管理概述" 页。 你将在左窗格中找到四个部分：
   - **概述**：提供对特定管理区域的一般视图和访问权限。
   - **警报**：显示事件中心的所有严重警报和警告警报。 有关详细信息，请参阅[警报部分](#alerts)。
   - **配额**：允许你创建、更新和删除配额。 有关详细信息，请参阅 "[配额" 部分](#quotas)。
   - **事件中心群集**：显示已配置的所有群集的列表。 有关详细信息，请参阅[事件中心群集](#event-hubs-clusters)部分。

   [![管理事件中心](media/event-hubs-rp-manage/1-manage-event-hubs.png)](media/event-hubs-rp-manage/1-manage-event-hubs.png#lightbox)

## <a name="quotas"></a>配额

在主页上选择 "**配额**" 将显示正在使用的配额列表，包括指定配额的关联计划。 
 
[![管理事件中心-配额](media/event-hubs-rp-manage/3-quotas.png)](media/event-hubs-rp-manage/3-quotas.png#lightbox)

有关为事件中心定义的配额类型的详细信息，请参阅[配额类型](azure-stack-quota-types.md#event-hubs-quota-types)

## <a name="alerts"></a>警报

事件中心资源提供程序支持以下警报：
   
| 类别 | 警报 | 类型 | 条件 |
|----------|-------|------|-----------|
| 性能 | | | |
| | EventHub-CpuUsage | 警告 | 在过去6小时内，事件中心群集的平均 CPU 使用率百分比大于50%。 |
| | EventHub-MemoryUsage | 警告 | % Data Disk （E：）的平均值过去6小时内的事件中心群集使用情况大于50%。 |
| | EventHub-DiskUsage | 警告 | 在过去6小时内，事件中心群集的可用内存空间百分比的平均值小于50%。 |
| 使用情况/配额 | | | |
| | EventHub-QuotaExceeded | 警告 | 过去六个小时内发生了配额错误。 |
| | EventHub-NamespaceCreditUsage | 警告 | 过去六个小时内命名空间信用使用的总和大于10000.0。 |
| 服务已降级 | | | |
| | EventHub-InternalServerError | 警告 | 过去六个小时内发生内部服务器错误。 |
| | EventHub-服务器繁忙 | 警告 | 过去六个小时内出现服务器忙错误。 |
| Client | | | |
| | EventHub-关于 microsoft.relay | 警告 | 过去六个小时内发生客户端错误。 |
| 资源 | | | |
| | EventHub-PendingDeletingResources | 警告 | 过去6小时内的挂起删除资源的总和大于100。 |
| | EventHub-ProvisioningQueueLength | 警告 | 预配队列长度在过去六个小时内的平均数量大于30。 |

在主页上选择 "**警报**" 将显示发出的警报的列表：

[![管理事件中心-警报摘要](media/event-hubs-rp-manage/2-alerts-summary.png)](media/event-hubs-rp-manage/2-alerts-summary.png#lightbox)

从列表中选择警报后，会在右侧显示 "**警报详细信息**" 面板：

[![管理事件中心-警报详细信息](media/event-hubs-rp-manage/2-alerts-detail.png)](media/event-hubs-rp-manage/2-alerts-detail.png#lightbox)

有关 Azure Stack 集线器监视功能（包括警报）的详细信息，请参阅[监视运行状况和警报](azure-stack-monitor-health.md)。 有关收集日志的详细信息，请参阅[Azure Stack 诊断日志集合概述](azure-stack-diagnostic-log-collection-overview.md)。

## <a name="event-hubs-clusters"></a>事件中心群集

在主页上选择 "**事件中心群集**" 将显示可用用户群集的列表。 此列表包括每个分类的以下各项：

- 高级配置信息。
- 服务运行状况。
- 备份状态。

[![管理事件中心-用户资源](media/event-hubs-rp-manage/4-user-resources.png)](media/event-hubs-rp-manage/4-user-resources.png#lightbox)

选择 "**运行状况**" 或 "**备份**" 下的链接将分别显示事件中心运行状况的状态和备份状态的详细信息。 "**名称**" 下的链接显示群集的详细信息，包括：
- 状态和配置信息。
- 群集的服务限制列表。

[![管理事件中心-用户群集](media/event-hubs-rp-manage/4-user-clusters.png)](media/event-hubs-rp-manage/4-user-clusters.png#lightbox)

服务限制是定义事件中心的操作边界的配置参数。 可用的服务限制类似于 Azure 事件中心专用层提供的服务限制。 通过选择 "**配置" 值**下的链接，可以更改分配的值。

> [!IMPORTANT]
> 在更改服务限制之前，你应花时间分析全部含义。 服务限制更改可能会影响使用和生成事件的解决方案的行为。 更改还可能会影响 Azure Stack 容量的资源消耗。

## <a name="next-steps"></a>后续步骤

有关详细信息，请查看：

- 为事件中心定义的配额类型，请查阅[配额类型](azure-stack-quota-types.md#event-hubs-quota-types)。
- Azure Stack 集线器监视功能（包括警报），请参阅[监视运行状况和警报](azure-stack-monitor-health.md)。 
- Azure Stack 中心日志集合，请参阅[Azure Stack 诊断日志集合概述](azure-stack-diagnostic-log-collection-overview.md)。













