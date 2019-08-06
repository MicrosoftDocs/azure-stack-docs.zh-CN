---
title: 使用 Azure Stack 上的 Azure Monitor | Microsoft Docs
description: 了解如何使用 Azure Stack 上的 Azure Monitor。
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
ms.date: 08/02/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 329ef95f7902f3fe6dfcdd71b599675fdf7c6286
ms.sourcegitcommit: 0e0d010c4e010f2fd6799471db8bf71652d8d4e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68806931"
---
# <a name="use-azure-monitor-on-azure-stack"></a>使用 Azure Stack 上的 Azure Monitor

适用对象：*Azure Stack 集成系统*

本文概述了 Azure Stack 中的 Azure Monitor 服务。 它讨论了 Azure Monitor 的运行以及有关如何使用 Azure Stack 中的 Azure Monitor 的其他信息。 

有关 Azure Monitor 的概述, 请参阅全球 Azure 文章[Azure Stack 上的 Azure Monitor 入门](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)。

![Azure Stack Monitor 边栏选项卡](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure 监视器是一款平台服务，提供一个中心位置来让用户监视 Azure 资源。 通过 Azure Monitor 直观显示、查询、路由和存档来自 Azure 内部资源的指标和日志并对其采取其他操作。 可以通过使用 Azure Stack 管理门户、Monitor PowerShell Cmdlet、跨平台 CLI 或 Azure Monitor REST API 来处理此数据。 有关 Azure Stack 支持的具体连接，请参阅[如何使用 Azure Stack 中的监视数据](azure-stack-metrics-monitor.md)。

> [!Note]
> 指标和诊断日志不可用于 Azure Stack 开发工具包。

## <a name="prerequisites-for-azure-monitor-on-azure-stack"></a>Azure Stack 上的 Azure Monitor 的先决条件

在你的订阅的套餐资源提供程序设置上注册 **Microsoft.insights** 资源提供程序。 你可以验证该资源提供程序在与你的订阅关联的套餐中是否可用：

1. 打开 Azure Stack 管理门户。
2. 选择“套餐”。
3. 选择与订阅关联的套餐。
4. 在“设置”下，选择“资源提供程序”。 
5. 在列表中找到 **Microsoft.Insights**，并验证状态是否为“已注册”。

## <a name="overview-of-azure-monitor-on-azure-stack"></a>Azure Stack 上的 Azure Monitor 概述

与 Azure 上的 Azure Monitor 一样，Azure Stack 上的 Azure Monitor 针对大多数服务提供基本级别的基础结构指标和日志。

## <a name="azure-monitor-sources-compute-subset"></a>Azure Monitor 源：计算子集

![Azure Stack 上的 Azure Monitor 源 - 计算子集](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

Azure Stack 中的 **Microsoft.Compute** 资源提供程序包括：
 - 虚拟机 
 - 虚拟机规模集

### <a name="application---diagnostics-logs-app-logs-and-metrics"></a>应用程序 - 诊断日志、应用日志和指标

应用可以在运行有 **Microsoft.Compute** 资源提供程序的 VM 的 OS 中运行。 这些应用和 VM 会发布其自己的日志和指标集。 Azure Monitor 依赖于 Azure 诊断扩展（Windows 或 Linux）来收集大多数应用级指标和日志。

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
> Azure Stack 上的 Linux 诊断扩展不受支持。

### <a name="host-and-guest-vm-metrics"></a>宿主和来宾 VM 指标

前面列出的计算资源具有专用的宿主 VM 和来宾 OS。 宿主 VM 和来宾 OS 是 Hyper-V 虚拟机监控程序中的根 VM 和来宾 VM 的等效项。 可以收集宿主 VM 和来宾 OS 的指标。 还可以收集来宾 OS 的诊断日志。 [Azure Stack 上的 Azure Monitor 支持的指标](azure-stack-metrics-supported.md)中列出了 Azure Stack 上的宿主和来宾 VM 指标中的可收集指标。 

### <a name="activity-log"></a>活动日志

可以在活动日志中搜索有关 Azure Stack 基础结构看到的计算资源的信息。 日志包含多种信息，例如创建或销毁资源的时间。 Azure Stack 上的活动日志与 Azure 是一致的。 有关详细信息，请参阅 [Azure 上的活动日志概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)的说明。 


## <a name="azure-monitor-sources-everything-else"></a>Azure Monitor 源：所有其他项

![Azure Monitor Azure Stack 源-其他所有内容](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>资源 - 指标和诊断日志

可收集的指标和诊断日志因资源类型而异。 支持的指标中提供了 Azure Stack 上的每种资源的可收集指标的列表。 有关详细信息，请参阅 [Azure Stack 上的 Azure Monitor 支持的指标](azure-stack-metrics-supported.md)。

### <a name="activity-log"></a>活动日志

对于计算资源，活动日志是相同的。 

### <a name="uses-for-monitoring-data"></a>用于监视数据

**存储和存档**  

某些监视数据已存储并且在设定的时间段内在 Azure Monitor 中可用。 
 - 指标存储 90 天。 
 - 活动日志条目存储 90 天。 
 - 诊断日志不存储。
 - 将数据存档到存储帐户可以保留更长时间。

**查询**  

可以使用 Azure 监视器 REST API、跨平台命令行接口 (CLI) 命令、PowerShell cmdlet 或 .NET SDK 访问系统或 Azure 存储中的数据。 

**可视化**

以图形和图表形式将监视数据可视化可以帮助你更快地查明趋势，其速度远非单纯查看数据可比。 

可视化方法包括：
 - 使用 Azure Stack 用户和管理员门户。
 - 将数据路由到 Microsoft Power BI。
 - 将数据路由到第三方可视化工具，可以使用实时传送视频流，也可以让工具从 Azure 存储中的存档读取。

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>访问 Azure Stack 上的 Azure Monitor 的方法

您可以使用以下方法之一来处理数据跟踪、路由和检索。 并非所有方法都适用于所有操作或数据类型。 

 - [Azure Stack 门户](azure-stack-use-portal.md)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [跨平台的命令行接口 (CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

> [!Important]  
> 查看 VM 的性能图形时, 如果遇到 "找**不到资源**" 错误, 请确保已在与 vm 关联的订阅上注册了 "Microsoft 见解"。

## <a name="next-steps"></a>后续步骤

在[使用 Azure Stack 中的监视数据](azure-stack-metrics-monitor.md)一文中详细了解如何使用 Azure Stack 上的监视数据。
