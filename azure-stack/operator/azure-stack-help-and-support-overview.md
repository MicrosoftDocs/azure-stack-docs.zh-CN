---
title: Microsoft Azure Stack 帮助和支持概述 |Microsoft Docs
description: 获取 Microsoft Azure Stack 支持。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/24/2019
ms.openlocfilehash: ff7911f64cf7c1413e623cd84cebe3d26585a3e5
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493861"
---
# <a name="microsoft-azure-stack-help-and-support"></a>Microsoft Azure Stack 帮助和支持

Azure Stack 门户中的 "**帮助 + 支持**" 包含帮助操作员了解有关 Azure Stack 的详细信息, 请查看其支持选项, 并获得专家帮助。 从1907版本开始, 操作员还可以使用 "帮助 + 支持" 来收集诊断日志以便进行故障排除。  

## <a name="help-resources"></a>帮助资源 

操作员还可以使用 "**帮助 + 支持**" 来详细了解 Azure Stack、检查其支持选项, 并获得专家帮助。 

### <a name="things-to-try-first"></a>首先尝试的任务

"**帮助 + 支持**" 顶部的链接是指向你可能首先尝试的操作的链接, 例如了解新概念、了解计费的工作方式, 或查看可用的支持选项。 

![自助服务支持](media/azure-stack-help-and-support/get-support-tiles.png)

- **文档**。 [Azure Stack 操作员文档](index.yml)包括概念、操作方法主题和教程, 它们演示了如何提供 Azure Stack 服务, 例如虚拟机、SQL 数据库、web 应用等。 

- **了解帐单**。 获取有关[使用情况和计费](azure-stack-billing-and-chargeback.md)的提示。

- **支持选项**。 Azure Stack 操作员可以从一系列可满足任何企业需求的[Azure 支持选项](https://aka.ms/azstacksupport)中进行选择。 

### <a name="get-expert-help"></a>获取专家帮助 

对于集成系统，Microsoft 和其原始设备制造商 (OEM) 硬件合作伙伴之间已经建立了协作的问题升级和解决流程。

如果存在云服务问题，请通过 Microsoft 客户支持服务 (CSS) 寻求支持。 你可以单击管理员门户右上角的 "**帮助**" (问号), 然后单击 "**帮助 + 支持**", 打开 "**帮助 + 支持概述**" 并提交新的支持请求。 创建支持请求将预先 Azure Stack 服务。 我们强烈建议客户使用此体验来提交票证, 而不是使用公用 Azure 门户。 

如果存在部署问题、修补和更新问题、硬件（包括现场可更换部件）问题，以及任何硬件品牌软件（例如在硬件生命周期主机上运行的软件）问题，请首先联系 OEM 硬件供应商。 至于其他问题，请联系 Microsoft CSS。

![获取集成系统的专家帮助](media/azure-stack-help-and-support/get-support-integrated.png)

对于 ASDK, 你可以在[AZURE STACK MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。 

你可以单击管理员门户右上角的 "**帮助**" (问号), 然后单击 "**帮助 + 支持**", 打开 "**帮助 + 支持概述**", 其中包含了论坛的链接。 将定期监视 MSDN 论坛。  
由于开发工具包是一个评估环境，因此我们不会通过 Microsoft CSS 提供官方支持。

![获取有关 ASDK 的专家帮助](media/azure-stack-help-and-support/get-support-asdk.png)

你还可以与 MSDN 论坛联系, 讨论问题, 或者采取在线培训和提高自己的技能。 

![获取专家帮助](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack"></a>利用 Azure Stack 提高速度

这一组教程的自定义取决于您运行的是 ASDK 系统还是集成系统, 以便快速掌握您的环境。 

![获取支持教程](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>诊断日志集合

从1907版开始, 在 "**帮助和支持**" 中提供了两种新方法来收集日志:

- **自动收集**:如果启用, 则日志收集由特定的运行状况警报触发 
- **立即收集日志**:可以从过去七天选择1-4 小时滑动窗口

![诊断日志收集选项的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

集成系统可以与 Microsoft 客户支持服务 (CSS) 共享诊断日志。 由于 Azure Stack 开发工具包 (ASDK) 是一个评估环境, 因此它不受 CSS 支持。 有关详细信息, 请参阅[Azure Stack 诊断日志收集概述](azure-stack-diagnostic-log-collection-overview.md)"。



## <a name="help-and-support-for-earlier-releases-azure-stack-pre-1905"></a>更早版本的帮助和支持 Azure Stack (1905 之前)

以前的 Azure Stack 版本还提供了指向 "**帮助 + 支持**" 的链接, 该链接将重定向到[Azure Stack 操作员文档](https://aka.ms/adminportaldocs)。

![获取支持教程](media/azure-stack-help-and-support/get-support-previous.png)

如果存在云服务问题，请通过 Microsoft 客户支持服务 (CSS) 寻求支持。 你可以单击管理员门户右上角的 "**帮助**" (问号), 单击 "**帮助和支持**", 然后单击 "**新建支持请求**" 以使用 CSS 直接提交新的支持请求。

对于集成系统, Microsoft 和我们的 OEM 合作伙伴之间存在协调的升级和解决过程。 如果有云服务问题, 则支持通过 Microsoft CSS 提供。 

如果存在部署问题、修补和更新问题、硬件（包括现场可更换部件）问题，以及任何硬件品牌软件（例如在硬件生命周期主机上运行的软件）问题，请首先联系 OEM 硬件供应商。 至于其他问题，请联系 Microsoft CSS。

对于开发工具包, 可以在[AZURE STACK MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。 你可以单击管理员门户右上角的 "**帮助**" (问号), 然后单击 "**新建支持请求**" 以从 Azure Stack 社区中的专家那里获取帮助。
由于开发工具包是一个评估环境，因此我们不会通过 Microsoft CSS 提供官方支持。

## <a name="next-steps"></a>后续步骤

- 了解[故障排除 Azure Stack](azure-stack-troubleshooting.md)