---
title: 立即发送 Azure Stack 集线器诊断日志
description: 了解如何使用管理员门户或 PowerShell 脚本在 Azure Stack 中心内收集诊断日志。
author: justinha
ms.topic: article
ms.date: 03/30/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/30/2020
ms.openlocfilehash: 2a46c031be648eedf42e204496ed2a123c926dd9
ms.sourcegitcommit: b65952127f39c263b162aad990e4d5b265570a7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80402720"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>立即发送 Azure Stack 集线器诊断日志

Azure Stack 操作员可以在请求支持之前，使用管理员门户或 PowerShell 将诊断日志按需发送到 Microsoft 客户支持服务（CSS）。 如果 Azure Stack 中心连接到 Azure，则建议管理员门户中的 "**立即发送日志**" 选项，因为这是将日志直接发送到 Microsoft 的最简单方法。 如果该门户不可用，则操作员应改[为使用 AzureStackDiagnosticLog 发送日志](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)。 

如果从 internet 断开连接或只想要在本地保存日志，请使用[get-azurestacklog](azure-stack-get-azurestacklog.md)方法发送日志。 以下流程图显示了在每种情况下用于发送诊断日志的选项。 

![流程图显示了如何立即将日志发送给 Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>作为根据需要收集日志的替代方法，你可以通过[主动收集诊断日志](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)来简化故障排除过程。 如果需要调查系统运行状况条件，则在使用 CSS 打开事例之前，会自动上载日志以进行分析。 如果启用了 "主动日志收集"，则 "**帮助和支持**" 将显示 "日志收集正在进行"。 如果单击 "**立即发送日志**" 以从特定时间收集日志，同时主动日志收集正在进行，则在主动日志收集完成后，按需收集便会开始。

指定日志收集的开始时间和结束时间，并单击 "**收集并上传**"。 

![立即发送日志的选项的屏幕截图](media/azure-stack-help-and-support/send-logs-now.png)

## <a name="next-steps"></a>后续步骤

[使用特权终结点（PEP）发送 Azure Stack 集线器诊断日志](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)
