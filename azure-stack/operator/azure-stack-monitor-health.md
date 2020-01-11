---
title: 监视 Azure Stack 集线器中的运行状况和警报 |Microsoft Docs
description: 了解如何监视 Azure Stack 集线器中的运行状况和警报。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: d539ea7c2be9cbd124ce3286adda2eff8cd1a7ca
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882294"
---
# <a name="monitor-health-and-alerts-in-azure-stack-hub"></a>监视 Azure Stack 集线器中的运行状况和警报

Azure Stack 中心包含基础结构监视功能，可帮助你查看 Azure Stack 中心区域的运行状况和警报。 "**区域管理**" 磁贴列出了 Azure Stack 集线器的所有已部署区域。 默认情况下，它在默认提供商订阅的管理员门户中处于固定状态。 该磁贴显示了每个区域的活动关键警报和警告警报的数目。 磁贴是 Azure Stack 集线器的运行状况和警报功能的入口点。

![Azure Stack 中心管理员门户中的 "区域管理" 磁贴](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack-hub"></a>了解 Azure Stack 集线器中的运行状况

运行状况资源提供程序管理运行状况和警报。 Azure Stack 中心基础结构组件在 Azure Stack 中心部署和配置期间向运行状况资源提供程序注册。 此注册可显示每个组件的运行状况和警报。 Azure Stack 集线器中的运行状况是一个简单的概念。 如果某个组件的已注册实例的警报存在，则该组件的运行状况状态将反映最差的活动警报严重性： warning 或严重。

## <a name="alert-severity-definition"></a>警报严重性定义

Azure Stack 中心引发仅有两个严重性的警报： "**警告**" 和 "**严重**"。

- **警告**  
  操作员可以按计划方式处理警告警报。 此警报通常不会影响用户工作负荷。

- **严重**  
  操作员应使用紧急警报来解决关键警报。 这些警报表明 Azure Stack 集线器用户当前影响或即将影响的问题。


## <a name="view-and-manage-component-health-state"></a>查看和管理组件运行状态

你可以通过 REST API 和 PowerShell 在管理员门户中查看组件的运行状况状态。

若要在门户中查看运行状况状态，请在 "**区域管理**" 磁贴中单击要查看的区域。 可以查看基础结构角色和资源提供程序的运行状况状态。

![基础结构角色列表](media/azure-stack-monitor-health/image2.png)

可以单击资源提供程序或基础结构角色来查看更多详细信息。

> [!WARNING]  
> 如果单击某个基础结构角色，然后单击该角色实例，则可以选择**启动**、**重新启动**或**关闭**。 将更新应用于集成系统时，请勿使用这些操作。 此外，不要在 Azure Stack 开发工具包（ASDK）环境**中使用这些**选项。 这些选项仅适用于集成系统环境，其中每个基础结构角色有多个角色实例。 重新启动 ASDK 中的角色实例（特别是 AzS-Azs-xrp01）会导致系统不稳定。 如需故障排除帮助，请将你的问题发布到[Azure Stack 中心论坛](https://aka.ms/azurestackforum)。
>

## <a name="view-alerts"></a>查看警报

可以直接从 "**区域管理**" 边栏选项卡中获取每个 Azure Stack 中心区域的活动警报的列表。 默认配置中的第一个磁贴是 "**警报**" 磁贴，显示区域的严重和警告警报摘要。 可以将 "警报" 磁贴固定到此边栏选项卡上的任何其他磁贴，以实现快速访问。

!["警报" 磁贴在 Azure Stack 中心管理员门户中显示警告](media/azure-stack-monitor-health/image3.png)

 若要查看区域的所有活动警报的列表，请选择 "**警报**" 磁贴的顶部部分。 若要查看警报的筛选列表（"严重" 或 "警告"），请选择磁贴中的 "**严重**" 或 "**警告**" 行项。

"**警报**" 边栏选项卡支持对状态（"活动" 或 "已关闭" 和 "严重" 或 "警告"）进行筛选。 默认视图显示所有活动警报。 在七天后，将从系统中删除所有已关闭的警报。

>[!Note]
>如果警报在一天内仍处于活动状态但尚未更新，则可以运行[test-azurestack](azure-stack-diagnostic-test.md)并在未报告问题时关闭警报。

!["筛选器" 窗格，按 Azure Stack 中心管理员门户中的 "严重" 或 "警告" 状态筛选](media/azure-stack-monitor-health/alert-view.png)

"**查看 API** " 操作显示用于生成列表视图的 REST API。 此操作提供了一种快速熟悉可用于查询警报的 REST API 语法的方法。 可以在自动化中使用此 API，或将其与现有的数据中心监视、报告和票证解决方案集成。

你可以单击特定警报以查看警报详细信息。 警报详细信息显示与警报关联的所有字段，并对受影响的组件和警报源启用快速导航。 例如，如果某个基础结构角色实例脱机或无法访问，则会出现以下警报。  

![Azure Stack 中心管理员门户中的 "警报详细信息" 边栏选项卡](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>修复警报

可以在某些警报中选择 "**修复**"。

如果选择此选项，则 "**修复**" 操作会执行特定于警报的步骤以尝试解决问题。 选择后，**修复**操作的状态将作为门户通知提供。

![正在进行修复警报操作](media/azure-stack-monitor-health/repair-in-progress.png)

"**修复**" 操作将报告成功完成，或在同一 "门户通知" 边栏选项卡中完成此操作。  如果警报的修复操作失败，可以从警报详细信息中重新运行**修复**操作。 如果修复操作已成功完成，请**不要**重新运行**修复**操作。

![修复操作已成功完成](media/azure-stack-monitor-health/repair-completed.png)

基础结构角色实例重新联机后，此警报将自动关闭。 许多（但不是每个警报）会在解决基础问题时自动关闭。 如果 Azure Stack 集线器解决该问题，则 "提供修复操作" 按钮的警报将自动关闭。 对于所有其他警报，请在执行更正步骤后选择 "**关闭警报**"。 如果问题仍然存在，Azure Stack 集线器会生成新的警报。 如果解决该问题，则该警报将保持关闭状态，并且不需要执行更多步骤。

## <a name="next-steps"></a>后续步骤

[管理 Azure Stack 集线器中的更新](azure-stack-updates.md)

[Azure Stack 中心的区域管理](azure-stack-region-management.md)
