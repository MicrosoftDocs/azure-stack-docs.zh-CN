---
title: Azure Stack 集线器诊断日志收集概述
description: 介绍 Azure Stack 中心帮助 + 支持（包括按需和主动日志收集）中的诊断日志收集。
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 8f97ecd20e7ef8db69033268baf96060e1315751
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520426"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Azure Stack 集线器诊断日志收集概述 

Azure Stack 中心是 Windows 组件和本地 Azure 服务彼此交互的大集合。 所有这些组件和服务都生成各自的日志集。 为了使 Microsoft 客户支持服务（CSS）能够有效地诊断问题，我们为诊断日志收集提供了无缝体验。 

"帮助和支持" 中的诊断日志收集帮助操作员快速收集和共享包含 Microsoft 客户支持服务（CSS）的诊断日志，这是一个简单的用户界面，无需 PowerShell。 即使其他基础结构服务出现故障，也会收集日志。  
 
如果管理员门户或 "帮助和支持" 边栏选项卡不可用，则建议使用这种日志收集方法，只[使用特权终结点（PEP）](azure-stack-get-azurestacklog.md) 。 

>[!NOTE]
>必须注册 Azure Stack 集线器，并使 internet 连接能够使用诊断日志收集。 如果未注册 Azure Stack 集线器，则[使用特权终结点（PEP）](azure-stack-get-azurestacklog.md)共享日志。 

![诊断日志收集选项的屏幕截图](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>集合选项和数据处理

诊断日志收集功能提供了两个用于发送日志的选项。 下表说明每个选项以及如何在每种情况下处理数据。 

### <a name="send-logs-proactively"></a>主动发送日志

[主动日志收集](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)可简化和简化诊断日志收集，因此客户可以在打开支持案例之前将日志发送给 Microsoft。 诊断日志是从 Azure Stack 中心主动上传以便进行分析。 仅当引发[系统运行状况警报](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts)时才收集这些日志，并且仅在支持案例的上下文中由 CSS 访问。


#### <a name="how-the-data-is-handled"></a>如何处理数据

仅根据 Azure Stack 集线器系统运行状况警报，由 Microsoft 同意定期自动收集日志。 你还可以在 Microsoft 管理和控制的 Azure 存储帐户中确认并同意这些日志的上传和保留。 

数据仅用于排除系统运行状况警报的目的，而不能用于市场营销、广告或任何其他商业目的。 数据最多可保留90天，Microsoft 收集的任何数据都会按照我们的[标准隐私做法](https://privacy.microsoft.com/)进行处理。

以前使用您的同意收集的任何数据都不会受到您的权限的吊销的影响。

使用**前瞻性日志**收集收集的日志将上传到由 Microsoft 管理和控制的 Azure 存储帐户。 Microsoft 可能会在支持案例的上下文中访问这些日志，并改善 Azure Stack 集线器的运行状况。 |

### <a name="send-logs-now"></a>立即发送日志

[发送日志现在](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)是一个手动选项，仅当你（作为客户）启动集合时，通常在打开支持案例之前，仅当你（客户）启动集合时，才从 Azure Stack 中心上载诊断日志。 

Azure Stack 操作员可以使用管理员门户或 PowerShell 将诊断日志按需发送到 Microsoft 客户支持服务（CSS）。 如果 Azure Stack 集线器已连接到 Azure，则建议使用[管理员门户中的 "发送日志](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)"，因为这是直接将日志发送到 Microsoft 的最简单方法。 如果该门户不可用，则操作员应改为[使用 PowerShell 立即发送日志](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)。 

如果从 internet 断开连接或只想要在本地保存日志，请使用[get-azurestacklog](azure-stack-get-azurestacklog.md)方法发送日志。 以下流程图显示了在每种情况下用于发送诊断日志的选项。 

![流程图显示了如何立即将日志发送给 Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>如何处理数据

通过从 Azure Stack 中心发起诊断日志收集，你确认并同意上载这些日志，并将其保留在 Microsoft 管理并控制的 Azure 存储帐户中。 Microsoft CSS 可以立即通过支持案例访问这些日志，而无需与客户进行日志收集。 

数据仅用于排除系统运行状况警报的目的，而不能用于市场营销、广告或任何其他商业目的。 数据最多可保留90天，Microsoft 收集的任何数据都会按照我们的[标准隐私做法](https://privacy.microsoft.com/)进行处理。 

使用发送日志收集的日志现在会上传到 Microsoft 托管和受控的存储。 Microsoft 在支持案例的上下文中对这些日志进行访问，并改善 Azure Stack 集线器的运行状况。 

## <a name="bandwidth-considerations"></a>带宽注意事项

诊断日志集合的平均大小取决于它是主动运行还是手动运行。 **主动日志收集**的平均大小约为 2 GB。 **发送日志**的集合大小现在取决于收集的小时数。

下表列出了对 Azure 进行有限连接或按流量计费的环境的注意事项。


| 网络连接 | 影响 |
|--------------------|--------|
| 低带宽/高延迟连接 | 日志上传需要较长的时间才能完成 | 
| 共享连接 | 上传还可能会影响共享网络连接的其他应用程序/用户 |
| 计量连接 | 你的 ISP 可能会额外收取额外的网络使用量 | 

## <a name="see-also"></a>另请参阅

[Azure Stack 中心日志和客户数据处理](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

