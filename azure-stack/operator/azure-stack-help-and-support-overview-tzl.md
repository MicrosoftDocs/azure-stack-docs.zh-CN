---
title: Microsoft Azure Stack 中心帮助和支持概述
description: 获取 Microsoft Azure Stack 集线器支持。
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 2026593dff049906d4c176ac10324f9b74206830
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81624918"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Microsoft Azure Stack 中心帮助和支持


::: moniker range=">= azs-2002"

Azure Stack 中心操作员可以使用 "**帮助 + 支持**" 来收集诊断日志，并将其发送给 Microsoft 进行故障排除。 可以从管理员门户访问 Azure Stack 中心门户中的 "**帮助 + 支持**"。 它包含一些资源，帮助操作员了解 Azure Stack 的详细信息，查看其支持选项，并获得专家帮助。  

![如何在管理员门户中访问帮助和支持的屏幕截图](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>帮助资源 

操作员可以使用 "**帮助 + 支持**" 来详细了解 Azure Stack 中心、检查其支持选项，并获得专家帮助。 

### <a name="things-to-try-first"></a>首先要尝试的操作

“帮助 + 支持”的顶部提供了可先试用项目的链接，例如研读新概念、了解计费的工作原理，或查看有哪些可用的支持选项。**** 

![自助支持](media/azure-stack-help-and-support/get-support-tiles.png)

- **文档**。 [Azure Stack 中心操作员文档](index.yml)包括概念、操作方法主题和教程，它们演示了如何提供 Azure Stack 中心服务，例如虚拟机、SQL 数据库、web 应用等。 

- **了解计费**。 获取有关[用量和计费](azure-stack-billing-and-chargeback.md)的提示。

- **支持选项**。 Azure Stack 中心操作员可以从一系列可满足任何企业需求的[Azure 支持选项](https://aka.ms/azstacksupport)中进行选择。 


### <a name="get-expert-help"></a>获取专家帮助 

对于集成系统，Microsoft 和其原始设备制造商 (OEM) 硬件合作伙伴之间已经建立了协作的问题升级和解决流程。

如果存在云服务问题，请通过 Microsoft 客户支持服务 (CSS) 寻求支持。 
可以单击管理员门户右上角的“帮助”（问号），然后单击“帮助 + 支持”打开“帮助 + 支持概述”，并提交新的支持请求。************ 创建支持请求将预先 Azure Stack 集线器服务。 我们强烈建议客户使用此体验来提交票证，而不是使用全局 Azure 门户。 

如果存在部署问题、修补和更新问题、硬件（包括现场可更换部件）问题，以及任何硬件品牌软件（例如在硬件生命周期主机上运行的软件）问题，请首先联系 OEM 硬件供应商。 
至于其他问题，请联系 Microsoft CSS。

![获取集成系统的专家帮助](media/azure-stack-help-and-support/get-support-integrated.png)

对于 ASDK，你可以在[Azure Stack 中心 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。 

你可以单击管理员门户右上角的 "**帮助**" （问号），然后单击 "**帮助 + 支持**"，打开 "**帮助 + 支持概述**"，其中包含了论坛的链接。 我们会定期关注 MSDN 论坛。  
由于开发工具包是一个评估环境，因此我们不会通过 Microsoft CSS 提供官方支持。

还可以转到 MSDN 论坛来探讨问题，或参加在线培训以提升自己的技能。 

![获取专家帮助](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Azure Stack 中心提高速度

这套教程是根据运行的是 ASDK 或集成系统而自定义的，可帮助你快速熟悉环境。 

![获取支持教程](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>诊断日志收集

可以通过两种方式将诊断日志发送到 Microsoft： 

- [主动发送日志](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)：如果启用，则日志收集由特定的运行状况警报触发 
- [立即发送日志](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)：可以手动选择特定的滑动窗口作为日志收集的时间范围

![诊断日志收集选项的屏幕截图](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>诊断日志收集

从 1907 版开始，在“帮助和支持”**** 中提供了两种新方法来收集日志：

- **自动收集**：如果启用，则日志收集由特定的运行状况警报触发 
- **立即收集日志**：可从过去七天选择1-4 小时滑动窗口

![诊断日志收集选项的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

集成系统可以与 Microsoft 客户支持服务（CSS）共享诊断日志。 由于 Azure Stack 开发工具包 (ASDK) 是一个评估环境，因此它不受 CSS 支持。 有关详细信息，请参阅[Azure Stack 集线器诊断日志集合概述](azure-stack-diagnostic-log-collection-overview.md)。



## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Azure Stack 集线器的早期版本的帮助和支持（1905版）

上一 Azure Stack 中心版本还提供了一个链接，用于将重定向到[Azure Stack 中心操作员文档](https://aka.ms/adminportaldocs)的**帮助 + 支持**。

![获取支持教程](media/azure-stack-help-and-support/get-support-previous.png)

如果存在云服务问题，请通过 Microsoft 客户支持服务 (CSS) 寻求支持。 
可以单击管理员门户右上角的“帮助”（问号），单击“帮助和支持”，然后单击“新建支持请求”以直接向 CSS 提交新的支持请求。************

对于集成系统，Microsoft 和我们的 OEM 合作伙伴之间存在协调的升级和解决过程。 
如果有云服务问题，则支持通过 Microsoft CSS 提供。 

如果存在部署问题、修补和更新问题、硬件（包括现场可更换部件）问题，以及任何硬件品牌软件（例如在硬件生命周期主机上运行的软件）问题，请首先联系 OEM 硬件供应商。 
至于其他问题，请联系 Microsoft CSS。

对于开发工具包，可以在[Azure Stack 中心 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。 
你可以单击管理员门户右上角的 "**帮助**" （问号），然后单击 "**新建支持请求**" 以从 Azure Stack 中心社区中的专家那里获取帮助。
由于开发工具包是一个评估环境，因此我们不会通过 Microsoft CSS 提供官方支持。

::: moniker-end


## <a name="next-steps"></a>后续步骤

- 了解[诊断日志收集](azure-stack-diagnostic-log-collection-overview-tzl.md)
- 了解如何[查找你的云 ID](azure-stack-find-cloud-id.md)
- 了解[Azure Stack 集线器的疑难解答](azure-stack-troubleshooting.md)
