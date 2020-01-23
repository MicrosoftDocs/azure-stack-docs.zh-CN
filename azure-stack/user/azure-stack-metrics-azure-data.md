---
title: 在 Azure Stack 中心使用 Azure Monitor |Microsoft Docs
description: 了解如何在 Azure Stack 中心使用 Azure Monitor。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 7627202f24feff4ce4ebf90a97d9c4bf1b5e119b
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536124"
---
# <a name="use-azure-monitor-on-azure-stack-hub"></a>使用 Azure Stack 中心的 Azure Monitor

本文概述了 Azure Stack 中心的 Azure Monitor 服务。 本文讨论 Azure Monitor 的操作以及有关如何在 Azure Stack 中心使用 Azure Monitor 的其他信息。 

有关 Azure Monitor 的概述，请参阅全球 Azure 文章[Azure Stack 集线器上的 Azure Monitor 入门](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)。

![Azure Stack 中心监视器 "边栏选项卡](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor 是一种平台服务，可提供用于监视 Azure 资源的单一源。 Azure Monitor 允许你对来自 Azure 资源的指标和日志进行可视化、查询、路由、存档和执行其他操作。 可以通过使用 Azure Stack 中心管理员门户、监视 PowerShell Cmdlet、跨平台 CLI 或 Azure Monitor REST Api 来处理此数据。 有关 Azure Stack 中心支持的特定连接，请参阅[如何使用 Azure Stack 中心的监视数据](azure-stack-metrics-monitor.md)。

> [!Note]
> 指标和诊断日志不适用于 Azure Stack 开发工具包。

## <a name="prerequisites-for-azure-monitor-on-azure-stack-hub"></a>Azure Stack 中心 Azure Monitor 的先决条件

在订阅的 "产品/服务" 资源提供程序设置上注册**Microsoft insights**资源提供程序。 可以在与订阅相关联的产品/服务中验证资源提供程序是否可用：

1. 打开 Azure Stack 集线器用户门户。
2. 选择 **订阅**。
3. 选择要注册的订阅。
4. 选择 "设置" 下的**资源提供程序** **。** 
5. 在**列表中找到**并验证状态是否为 "已**注册**"。

## <a name="overview-of-azure-monitor-on-azure-stack-hub"></a>Azure Stack 中心的 Azure Monitor 概述

与 Azure 上的 Azure Monitor 一样，Azure Stack 中心 Azure Monitor 为大多数服务提供了基本的基础结构指标和日志。

## <a name="azure-monitor-sources-compute-subset"></a>Azure Monitor 源：计算子集

![Azure Monitor Azure Stack 中心源-计算子集](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

Azure Stack 中心中的**Microsoft. 计算**资源提供程序包括：
 - 虚拟机 
 - 虚拟机规模集

### <a name="application---diagnostics-logs-app-logs-and-metrics"></a>应用程序-诊断日志、应用程序日志和指标

应用可在使用**Microsoft. 计算**资源提供程序运行的 VM 的 OS 中运行。 这些应用和 Vm 会发出自己的日志和指标集。 Azure Monitor 依赖于 Azure 诊断扩展（Windows 或 Linux）来收集大多数应用程序级指标和日志。

度量值的类型包括：
 - 性能计数器
 - 应用日志
 - Windows 事件日志
 - .NET 事件源
 - IIS 日志
 - 基于清单的 ETW
 - 故障转储
 - 客户错误日志

> [!Note]  
> 不支持 Azure Stack 集线器上的 Linux 诊断扩展。

### <a name="host-and-guest-vm-metrics"></a>宿主和来宾 VM 指标

前面列出的计算资源具有专用的主机 VM 和来宾操作系统。 主机 VM 和来宾 OS 是 Hyper-v 虚拟机监控程序中的根 VM 和来宾 VM 的等效项。 可以收集主机 VM 和来宾操作系统的指标。 你还可以收集来宾操作系统的诊断日志。 Azure Stack 集线器上的主机和来宾 VM 指标的可回收指标列表可[通过 Azure Monitor Azure Stack 中心提供支持的指标](azure-stack-metrics-supported.md)。 

### <a name="activity-log"></a>活动日志

可以在活动日志中搜索有关计算资源的信息，如 Azure Stack 中心基础结构所示。 日志包含多种信息，例如创建或销毁资源的时间。 Azure Stack 集线器上的活动日志与 Azure 一致。 有关详细信息，请参阅[Azure 上的活动日志概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)的说明。 


## <a name="azure-monitor-sources-everything-else"></a>Azure monitor 源：其他一切

![Azure Monitor Azure Stack 中心源-其他所有内容](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>资源-指标和诊断日志

可回收指标和诊断日志因资源类型而异。 支持的指标中提供了 Azure Stack 集线器上每个资源的可回收指标列表。 有关详细信息，请参阅[Azure Stack 中心的 Azure Monitor 支持的指标](azure-stack-metrics-supported.md)。

### <a name="activity-log"></a>活动日志

计算资源的活动日志相同。 

### <a name="uses-for-monitoring-data"></a>用于监视数据

**存储和存档**  

某些监视数据已存储并且在设定的时间段内在 Azure Monitor 中可用。 
 - 指标存储 90 天。 
 - 活动日志条目存储 90 天。 
 - 不存储诊断日志。
 - 将数据存档到存储帐户，以便保留更长时间。

**查询**  

可以使用 Azure Monitor REST API、跨平台命令行接口（CLI）命令、PowerShell cmdlet 或 .NET SDK 访问系统或 Azure 存储中的数据。 

**视觉**

以图形和图表形式将监视数据可视化可以帮助你更快地查明趋势，其速度远非单纯查看数据可比。 

可视化方法包括：
 - 使用 Azure Stack 集线器用户和管理员门户。
 - 将数据路由到 Microsoft Power BI。
 - 使用实时流式处理或通过从 Azure 存储中的存档读取该工具，将数据路由到第三方可视化工具。

## <a name="methods-of-accessing-azure-monitor-on-azure-stack-hub"></a>Azure Stack 集线器上访问 Azure monitor 的方法

您可以使用以下方法之一来处理数据跟踪、路由和检索。 并非所有方法都适用于所有操作或数据类型。 

 - [Azure Stack 集线器用户门户](azure-stack-use-portal.md)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [跨平台命令行接口（CLI）](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

> [!Important]  
> 查看 VM 的性能图形时，如果遇到 "找**不到资源**" 错误，请确保已在与 vm 关联的订阅上注册了 "Microsoft 见解"。

## <a name="next-steps"></a>后续步骤

若要详细了解如何监视 Azure Stack 集线器上的数据使用情况，请参阅[使用 Azure Stack 中心的监视数据](azure-stack-metrics-monitor.md)。
