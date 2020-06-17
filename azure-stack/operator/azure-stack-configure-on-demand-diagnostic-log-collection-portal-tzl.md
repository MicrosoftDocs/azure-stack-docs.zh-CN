---
title: 立即发送 Azure Stack Hub 诊断日志
description: 了解如何使用管理员门户或 PowerShell 脚本在 Azure Stack Hub 中按需收集诊断日志。
author: justinha
ms.topic: article
ms.date: 03/30/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/30/2020
ms.openlocfilehash: 2c741440deb92be81497b34a7e485d4628501100
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819397"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>立即发送 Azure Stack Hub 诊断日志

::: moniker range=">= azs-2002"

Azure Stack 操作员可以在请求支持之前，使用管理员门户或 PowerShell 将诊断日志按需发送到 Microsoft 支持部门。 如果 Azure Stack 中心连接到 Azure，则建议管理员门户中的 "**立即发送日志**" 选项，因为这是将日志直接发送到 Microsoft 的最简单方法。 如果门户不可用，则操作员应改为[使用 Send-AzureStackDiagnosticLog 立即发送日志](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)。 

如果已断开与 Internet 的连接，或者只想在本地保存日志，请使用 [Get-AzureStackLog](azure-stack-get-azurestacklog.md) 方法发送日志。 以下流程图显示了每种情况下用于发送诊断日志的选项。 

![流程图，显示如何将日志立即发送到 Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>可以通过[主动收集诊断日志](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)来简化故障排除过程，这是一种替代按需收集日志的方法。 如果需要调查系统运行状况条件，则在使用 Microsoft 支持部门打开事例之前，会自动上载日志以进行分析。 如果启用了主动收集日志功能，则当日志收集正在进行时，会显示“帮助和支持”。 如果在进行主动日志收集时单击“立即发送日志”从特定时间收集日志，则会在主动日志收集完成后开始按需收集。

指定日志收集的开始时间和结束时间，然后单击“收集并上传”。 

![“立即发送日志”选项的屏幕截图](media/azure-stack-help-and-support/send-logs-now.png)


::: moniker-end
::: moniker range="<= azs-1910"
## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>使用“帮助和支持”按需收集诊断日志

若要解决某个问题，Microsoft 支持部门可能会请求 Azure Stack 中心操作员从上一周的特定时间范围内收集诊断日志。 在这种情况下，Microsoft 支持部门会向操作员提供用于上传集合的 SAS URL。 
使用以下步骤，通过 Microsoft 支持部门中的 SAS URL 配置按需日志收集：

1. 打开“帮助和支持概览”，单击“立即收集日志”。  
1. 从过去七天选择 1-4 小时滑动窗口。 
1. 选择本地时区。
1. 输入 Microsoft 支持部门提供的 SAS URL。

   ![按需日志收集的屏幕截图](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>如果启用了诊断日志自动收集，“帮助和支持”会显示何时在进行日志收集。 如果单击“立即收集日志”以收集特定时间的日志，而此时系统正在进行自动日志收集，则当自动日志收集完成后，按需收集就会开始。 


::: moniker-end


## <a name="next-steps"></a>后续步骤

[使用特权终结点 (PEP) 发送 Azure Stack Hub 诊断日志](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)

