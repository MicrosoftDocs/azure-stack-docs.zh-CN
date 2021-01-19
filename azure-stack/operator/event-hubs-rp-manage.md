---
title: 如何管理 Azure Stack Hub 上的事件中心
description: 了解如何管理 Azure Stack Hub 上的事件中心资源提供程序。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 9cf3339398516688865f7aab3a696562f4974699
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571325"
---
# <a name="how-to-manage-event-hubs-on-azure-stack-hub"></a>如何管理 Azure Stack Hub 上的事件中心

事件中心管理体验允许你控制服务并直观显示其状态和警报。 

## <a name="overview"></a>概述

使用以下步骤访问事件中心管理页面：

1. 登录到 Azure Stack Hub 管理员门户。
2. 在左侧窗格中，选择“所有服务”。
3. 搜索“事件中心”，然后选择该服务。 如果找不到事件中心服务，则必须先[安装](event-hubs-rp-install.md)资源提供程序。
4. 随即显示事件中心管理概述页面。 可在左侧窗格中找到四个部分：
   - **概述**：提供特定管理区域的概貌以及对这些区域的访问。
   - **警报**：显示事件中心的所有严重警报和警告性警报。 有关详细信息，请参阅[警报](#alerts)部分。
   - **配额**：允许创建、更新和删除配额。 有关详细信息，请参阅[配额](#quotas)部分。
   - **事件中心群集**：列出所有已配置的群集。 有关详细信息，请参阅[事件中心群集](#event-hubs-clusters)部分。

   [![管理事件中心](media/event-hubs-rp-manage/1-manage-event-hubs.png)](media/event-hubs-rp-manage/1-manage-event-hubs.png#lightbox)

## <a name="quotas"></a>配额

在主页上选择“配额”将列出正在使用的配额，包括指定配额的关联计划。 
 
[![管理事件中心 - 配额](media/event-hubs-rp-manage/3-quotas.png)](media/event-hubs-rp-manage/3-quotas.png#lightbox)

有关为事件中心定义的配额类型的详细信息，请参阅[配额类型](azure-stack-quota-types.md#event-hubs-quota-types)

## <a name="alerts"></a>警报

事件中心资源提供程序支持以下警报：
   
| Category | 警报 | 类型 | 条件 |
|----------|-------|------|-----------|
| 性能 | | | |
| | EventHub-CpuUsage | 警告 | 过去 6 小时内，事件中心群集的平均 CPU 使用率百分比大于 50%。 |
| | EventHub-MemoryUsage | 警告 | 过去 6 小时内，事件中心群集的平均可用内存空间百分比小于 50%。 |
| | EventHub-DiskUsage | 警告 | 过去 6 小时内，事件中心群集的平均数据磁盘(E:) 使用率百分比大于 50%。 |
| 使用情况/配额 | | | |
| | EventHub-QuotaExceeded | 警告 | 过去 6 小时内发生了超出配额错误。 |
| | EventHub-NamespaceCreditUsage | 警告 | 过去 6 小时内，命名空间的额度使用量总和大于 10000.0。 |
| 服务已降级 | | | |
| | EventHub-InternalServerError | 警告 | 过去 6 小时内发生了内部服务器错误。 |
| | EventHub-ServerBusy | 警告 | 过去 6 小时内发生了服务器繁忙错误。 |
| 客户端 | | | |
| | EventHub-ClientError | 警告 | 过去 6 小时内发生了客户端错误。 |
| 资源 | | | |
| | EventHub-PendingDeletingResources | 警告 | 过去 6 小时内，等待删除的资源总和大于 100。 |
| | EventHub-ProvisioningQueueLength | 警告 | 过去 6 小时内，平均预配队列长度大于 30。 |

在主页上选择“警报”将列出发出的警报：

[![管理事件中心 - 警报摘要](media/event-hubs-rp-manage/2-alerts-summary.png)](media/event-hubs-rp-manage/2-alerts-summary.png#lightbox)

从列表中选择某个警报时，将在右侧显示“警报详细信息”面板：

[![管理事件中心 - 警报详细信息](media/event-hubs-rp-manage/2-alerts-detail.png)](media/event-hubs-rp-manage/2-alerts-detail.png#lightbox)

有关 Azure Stack Hub 监视功能（包括警报）的详细信息，请参阅[监视运行状况和警报](azure-stack-monitor-health.md)。 有关收集日志的详细信息，请参阅 [Azure Stack 诊断日志收集概述](./diagnostic-log-collection.md)。

## <a name="event-hubs-clusters"></a>事件中心群集

在主页上选择“事件中心群集”将显示可用用户群集的列表。 该列表包括每个群集的以下信息：

- 高级配置信息。
- 服务运行状况。
- 备份状态。

[![管理事件中心 - 用户资源](media/event-hubs-rp-manage/4-user-resources.png)](media/event-hubs-rp-manage/4-user-resources.png#lightbox)

在“运行状况”或“备份”下选择某个链接时，将分别显示有关事件中心运行状况和备份状态的详细信息。 “名称”下的链接显示群集的更多详细信息，包括：
- 状态和配置信息。
- 群集的服务限制列表。

[![管理事件中心 - 用户群集](media/event-hubs-rp-manage/4-user-clusters.png)](media/event-hubs-rp-manage/4-user-clusters.png#lightbox)

服务限制是用于定义事件中心的操作边界的配置参数。 可用的服务限制类似于为 Azure 事件中心专用层提供的服务限制。 通过选择“配置值”下的链接，可以更改分配的值。

> [!IMPORTANT]
> 在更改服务限制之前，应花时间分析所有影响。 服务限制更改可能会影响使用和生成事件的解决方案的行为。 更改还有可能影响 Azure Stack 容量的资源消耗。

## <a name="next-steps"></a>后续步骤

有关以下内容的详细信息：

- 为事件中心定义的配额类型，请参阅[配额类型](azure-stack-quota-types.md#event-hubs-quota-types)。
- Azure Stack Hub 监视功能（包括警报），请参阅[监视运行状况和警报](azure-stack-monitor-health.md)。 
- Azure Stack Hub 日志收集，请参阅 [Azure Stack 诊断日志收集概述](./diagnostic-log-collection.md)。
