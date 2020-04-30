---
title: Azure Stack 中心帮助和支持
description: 获取 Microsoft Azure Stack 集线器支持。
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 20d20e5e53a8826ea0514e894bacbfeeeda6c540
ms.sourcegitcommit: 3fd4a38dc8446e0cdb97d51a0abce96280e2f7b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580129"
---
# <a name="azure-stack-hub-help-and-support"></a>Azure Stack 中心帮助和支持

::: moniker range=">= azs-2002"

Azure Stack 中心操作员可以使用 "**帮助 + 支持**" 来收集诊断日志，并将其发送给 Microsoft 进行故障排除。 可以从管理员门户访问 Azure Stack 中心门户中的 "**帮助 + 支持**"。 它包含一些资源，帮助操作员了解 Azure Stack 的详细信息，查看其支持选项，并获得专家帮助。  

![如何在 Azure Stack 中心管理员门户中访问帮助和支持](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>帮助资源

操作员可以使用 "**帮助 + 支持**" 来详细了解 Azure Stack 中心、检查其支持选项，并获得专家帮助。

### <a name="things-to-try-first"></a>首先要尝试的操作

在 "**帮助 + 支持**" 顶部，你应该首先尝试操作，如阅读新的概念、了解帐单的工作方式，或查看哪些支持选项可用。

![Azure Stack 集线器中的自助服务支持](media/azure-stack-help-and-support/get-support-tiles.png)

- **文档**。 [Azure Stack 中心操作员文档](index.yml)包含有关如何提供 Azure Stack 中心服务的概念、操作方法主题和教程。 这些服务包括虚拟机、SQL 数据库、web 应用等。

- **了解计费**。 获取有关[用量和计费](azure-stack-billing-and-chargeback.md)的提示。

- **支持选项**。 Azure Stack 中心操作员可以从一系列可满足任何企业需求的[Azure 支持选项](https://aka.ms/azstacksupport)中进行选择。

### <a name="get-expert-help"></a>获取专家帮助

对于集成系统，Microsoft 和我们的原始设备制造商（OEM）硬件合作伙伴之间存在协调的升级和解决过程。

如果有云服务问题，通过 Microsoft 客户支持服务（CSS）提供支持。 可在管理员门户的右上角选择 "**帮助**" （问号），然后选择 "**帮助 + 支持**" 以打开 "**帮助 + 支持概述**" 并提交新的支持请求。 创建支持请求将预先 Azure Stack 集线器服务。 我们强烈建议客户使用此体验来提交票证，而不是使用全局 Azure 门户。

如果部署、修补和更新、硬件（包括现场可更换部件）和任何硬件品牌软件（如硬件生命周期主机上运行的软件）存在问题，请首先联系你的 OEM 硬件供应商。 至于其他问题，请联系 Microsoft CSS。

![获取集成系统的专家帮助](media/azure-stack-help-and-support/get-support-integrated.png)

对于 Azure Stack 开发工具包（ASDK），你可以在[Azure Stack 中心 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。

选择管理员门户右上角的 "**帮助**" （问号），然后选择 "**帮助 + 支持**"，打开 "**帮助 + 支持概述**"，其中包含指向论坛的链接。 我们会定期关注 MSDN 论坛。 由于 ASDK 是一个评估环境，因此没有通过 Microsoft CSS 提供的官方支持。

还可以转到 MSDN 论坛来探讨问题，或参加在线培训以提升自己的技能。

![获取有关 Azure Stack 中心的专家帮助](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Azure Stack 中心提高速度

这套教程是根据运行的是 ASDK 或集成系统而自定义的，可帮助你快速熟悉环境。

![获取 Azure Stack 中心的支持教程](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>诊断日志收集

可以通过两种方式将诊断日志发送到 Microsoft：

- [主动发送日志](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)：如果启用，则日志收集由特定的运行状况警报触发。
- [立即发送日志](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)：可以手动选择特定的滑动窗口作为日志收集的时间范围。

![诊断日志收集选项](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>诊断日志收集

从 1907 版开始，在“帮助和支持”**** 中提供了两种新方法来收集日志：

- **自动收集**：如果启用，则日志收集由特定的运行状况警报触发。
- **立即收集日志**：可从过去7天内选择1-4 小时滑动窗口。

![诊断日志收集选项](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

集成系统可以与 Microsoft 客户支持服务（CSS）共享诊断日志。 由于 Azure Stack 开发工具包（ASDK）是一个评估环境，因此它不受 CSS 支持。 有关详细信息，请参阅[Azure Stack 集线器诊断日志集合概述](azure-stack-diagnostic-log-collection-overview.md)。

## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Azure Stack 集线器的早期版本的帮助和支持（1905版）

上一 Azure Stack 中心版本还提供了一个链接，用于将重定向到[Azure Stack 中心操作员文档](https://aka.ms/adminportaldocs)的**帮助 + 支持**。

![获取支持教程](media/azure-stack-help-and-support/get-support-previous.png)

如果有云服务问题，通过 Microsoft 客户支持服务（CSS）提供支持。 你可以选择管理员门户右上角的 "**帮助**" （问号），选择 "**帮助和支持**"，然后选择 "**新建支持请求**" 以使用 CSS 直接提交新的支持请求。

对于集成系统，Microsoft 和我们的 OEM 合作伙伴之间存在协调的升级和解决过程。 如果有云服务问题，则支持通过 Microsoft CSS 提供。

如果部署、修补和更新、硬件（包括现场可更换部件）和任何硬件品牌软件（如硬件生命周期主机上运行的软件）存在问题，请首先联系你的 OEM 硬件供应商。 至于其他问题，请联系 Microsoft CSS。

对于 ASDK，你可以在[Azure Stack 中心 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。

选择管理员门户右上角的 "**帮助**" （问号），然后选择 "**新建支持请求**" 以从 Azure Stack 中心社区中的专家那里获取帮助。 由于 ASDK 是一个评估环境，因此没有通过 Microsoft CSS 提供的官方支持。

::: moniker-end

## <a name="next-steps"></a>后续步骤

- 了解[诊断日志收集](azure-stack-diagnostic-log-collection-overview-tzl.md)。
- 了解如何[查找你的云 ID](azure-stack-find-cloud-id.md)。
- 了解[Azure Stack 集线器的故障排除](azure-stack-troubleshooting.md)。
