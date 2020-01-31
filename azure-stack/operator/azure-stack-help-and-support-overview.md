---
title: Microsoft Azure Stack 中心帮助和支持概述
description: 获取 Microsoft Azure Stack 集线器支持。
author: justinha
ms.topic: article
ms.date: 07/24/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/24/2019
ms.openlocfilehash: 621c0abb4f2e50c72e5a024cc3c1e634e73fe0c2
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882229"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Microsoft Azure Stack 中心帮助和支持

Azure Stack 中心门户中的 "**帮助 + 支持**" 提供了一些资源，可帮助操作员了解有关 Azure Stack 中心的详细信息，查看其支持选项，并获得专家帮助。 从1907版本开始，操作员还可以使用 "帮助 + 支持" 来收集诊断日志以便进行故障排除。  

## <a name="help-resources"></a>帮助资源 

操作员还可以使用 "**帮助 + 支持**" 来详细了解 Azure Stack 中心、检查其支持选项，并获得专家帮助。 

### <a name="things-to-try-first"></a>首先尝试的任务

"**帮助 + 支持**" 顶部的链接是指向你可能首先尝试的操作的链接，例如了解新概念、了解计费的工作方式，或查看可用的支持选项。 

![自助服务支持](media/azure-stack-help-and-support/get-support-tiles.png)

- **文档**。 [Azure Stack 中心操作员文档](index.yml)包含的概念、操作方法主题和教程介绍了如何提供 Azure Stack 中心服务，例如虚拟机、SQL 数据库、web 应用等。 

- **了解帐单**。 获取有关[使用情况和计费](azure-stack-billing-and-chargeback.md)的提示。

- **支持选项**。 Azure Stack 中心操作员可以从一系列可满足任何企业需求的[Azure 支持选项](https://aka.ms/azstacksupport)中进行选择。 

### <a name="get-expert-help"></a>获取专家帮助 

对于集成系统，Microsoft 和我们的原始设备制造商（OEM）硬件合作伙伴之间存在协调的升级和解决过程。

如果有云服务问题，则通过 Microsoft 客户支持服务（CSS）提供支持。 你可以单击管理员门户右上角的 "**帮助**" （问号），然后单击 "**帮助 + 支持**"，打开 "**帮助 + 支持概述**" 并提交新的支持请求。 创建支持请求将预先 Azure Stack 集线器服务。 我们强烈建议客户使用此体验来提交票证，而不是使用公用 Azure 门户。 

如果部署、修补和更新、硬件（包括现场可更换部件）和任何硬件品牌软件（如硬件生命周期主机上运行的软件）存在问题，请首先联系你的 OEM 硬件供应商。 对于其他任何内容，请联系 Microsoft CSS。

![获取集成系统的专家帮助](media/azure-stack-help-and-support/get-support-integrated.png)

对于 ASDK，你可以在[Azure Stack 中心 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。 

你可以单击管理员门户右上角的 "**帮助**" （问号），然后单击 "**帮助 + 支持**"，打开 "**帮助 + 支持概述**"，其中包含了论坛的链接。 将定期监视 MSDN 论坛。  
由于开发工具包是一个评估环境，因此没有通过 Microsoft CSS 提供的官方支持。

![获取有关 ASDK 的专家帮助](media/azure-stack-help-and-support/get-support-asdk.png)

你还可以与 MSDN 论坛联系，讨论问题，或者采取在线培训和提高自己的技能。 

![获取专家帮助](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Azure Stack 中心提高速度

这一组教程的自定义取决于您运行的是 ASDK 系统还是集成系统，以便快速掌握您的环境。 

![获取支持教程](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>诊断日志集合

从1907版开始，在 "**帮助和支持**" 中提供了两种新方法来收集日志：

- **自动收集**：如果启用，则日志收集由特定的运行状况警报触发 
- **立即收集日志**：可从过去七天选择1-4 小时滑动窗口

![诊断日志收集选项的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

集成系统可以与 Microsoft 客户支持服务（CSS）共享诊断日志。 由于 Azure Stack 开发工具包（ASDK）是一个评估环境，因此它不受 CSS 支持。 有关详细信息，请参阅[Azure Stack 集线器诊断日志集合概述](azure-stack-diagnostic-log-collection-overview.md)。



## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Azure Stack 集线器的早期版本的帮助和支持（1905版）

上一 Azure Stack 中心版本还提供了一个链接，用于将重定向到[Azure Stack 中心操作员文档](https://aka.ms/adminportaldocs)的**帮助 + 支持**。

![获取支持教程](media/azure-stack-help-and-support/get-support-previous.png)

如果有云服务问题，则通过 Microsoft 客户支持服务（CSS）提供支持。 你可以单击管理员门户右上角的 "**帮助**" （问号），单击 "**帮助和支持**"，然后单击 "**新建支持请求**" 以使用 CSS 直接提交新的支持请求。

对于集成系统，Microsoft 和我们的 OEM 合作伙伴之间存在协调的升级和解决过程。 如果有云服务问题，则支持通过 Microsoft CSS 提供。 

如果部署、修补和更新、硬件（包括现场可更换部件）和任何硬件品牌软件（如硬件生命周期主机上运行的软件）存在问题，请首先联系你的 OEM 硬件供应商。 对于其他任何内容，请联系 Microsoft CSS。

对于开发工具包，可以在[Azure Stack 中心 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。 你可以单击管理员门户右上角的 "**帮助**" （问号），然后单击 "**新建支持请求**" 以从 Azure Stack 中心社区中的专家那里获取帮助。
由于开发工具包是一个评估环境，因此没有通过 Microsoft CSS 提供的官方支持。

## <a name="next-steps"></a>后续步骤

- 了解[Azure Stack 集线器疑难解答](azure-stack-troubleshooting.md)