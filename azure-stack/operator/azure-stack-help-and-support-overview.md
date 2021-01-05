---
title: Azure Stack Hub 帮助和支持
description: 获取 Microsoft Azure Stack Hub 的支持。
author: PatAltimore
ms.topic: article
ms.date: 02/26/2020
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 7c4bb5f75118552d7f8379edac7083f8830082a5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870709"
---
# <a name="azure-stack-hub-help-and-support"></a>Azure Stack Hub 帮助和支持

::: moniker range=">= azs-2002"

Azure Stack Hub 操作员可以使用“帮助 + 支持”收集诊断日志并将其发送到 Microsoft 进行故障排除。 可以从管理员门户访问 Azure Stack Hub 门户中的“帮助 + 支持”。 它提供一些资源，可帮助操作员了解有关 Azure Stack 的详细信息、查看其支持选项并获得专家帮助。  

![如何在 Azure Stack Hub 管理员门户中访问帮助和支持](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>帮助资源

操作员可以通过“帮助 + 支持”详细了解 Azure Stack Hub、查看其支持选项，以及获得专家帮助。

### <a name="things-to-try-first"></a>首先要尝试的操作

“帮助 + 支持”的顶部是应首先尝试的操作，例如阅读新概念、了解计费的工作原理或查看有哪些可用的支持选项。

![Azure Stack Hub 中的自助服务支持](media/azure-stack-help-and-support/get-support-tiles.png)

- **文档** [Azure Stack Hub 操作员文档](index.yml)包含介绍如何提供 Azure Stack Hub 服务的概念、操作指南主题和教程。 这些服务包括虚拟机、SQL 数据库、Web 应用等。

- **了解计费**。 获取有关[用量和计费](azure-stack-billing-and-chargeback.md)的提示。

- **支持选项**。 Azure Stack Hub 操作员有多种可供选择的 [Azure 支持选项](./azure-stack-manage-basics.md)，用于应对各种企业的需求。

### <a name="get-expert-help"></a>获取专家帮助

对于集成系统，Microsoft 和原始设备制造商之间存在协调的升级和解决过程， (OEM) 硬件合作伙伴。

如果有云服务问题，则通过 Microsoft 支持部门提供支持。 可以选择管理员门户右上角的“帮助”（问号），然后选择“帮助 + 支持”，打开“帮助 + 支持”概述，并提交新的支持请求  。 创建支持请求时，系统会预先选择 Azure Stack Hub 服务。 我们强烈建议客户使用此体验来提交票证，而不是使用全局 Azure 门户。

如果存在部署问题、修补和更新问题、硬件（包括现场可更换部件）问题，以及任何硬件品牌软件（例如在硬件生命周期主机上运行的软件）问题，请首先联系 OEM 硬件供应商。 对于其他任何内容，请联系 Microsoft 支持部门。

![获取集成系统的专家帮助](media/azure-stack-help-and-support/get-support-integrated.png)

对于 Azure Stack 开发工具包 (ASDK)，可以在 [Azure Stack Hub MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中咨询与支持相关的问题。

选择管理员门户右上角的“帮助”（问号），然后选择“帮助 + 支持”，打开“帮助 + 支持”概述，其中包含指向论坛的链接  。 我们会定期监视 MSDN 论坛。 由于 ASDK 是一个评估环境，因此没有通过 Microsoft 支持部门提供的官方支持。

还可以转到 MSDN 论坛来探讨问题，或参加在线培训以提升自己的技能。

![获取有关 Azure Stack Hub 的专家帮助](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>快速熟悉 Azure Stack Hub

这套教程是根据运行的是 ASDK 或集成系统而自定义的，可帮助你快速熟悉环境。

![获取有关 Azure Stack Hub 的支持教程](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>诊断日志收集

可以通过两种方式将诊断日志发送到 Microsoft：

- [主动发送日志](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively)：如果启用，日志收集将由特定的运行状况警报触发。
- [立即发送日志](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now)：可以手动选择特定滑动窗口作为日志收集的时间范围。

![此屏幕截图显示了如何开始收集诊断日志。](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>诊断日志收集

从 1907 版开始，在“帮助和支持”中提供了两种新方法来收集日志：

- **自动收集**：如果启用，日志收集将由特定的运行状况警报触发。
- **立即收集日志**：可以从过去七天选择 1-4 小时滑动窗口。

![诊断日志收集选项](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

集成系统可以与 Microsoft 支持部门共享诊断日志。 由于 Azure Stack 开发工具包 (ASDK) 是一个评估环境，因此不支持 Microsoft 支持部门。 有关详细信息，请参阅 [Azure Stack 诊断日志收集概述](azure-stack-diagnostic-log-collection-overview.md)。

## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>旧版 Azure Stack Hub（1905 之前）的帮助和支持

旧版 Azure Stack Hub 也有“帮助 + 支持”链接，此链接重定向到 [Azure Stack Hub 操作员文档](./index.yml)。

![获取支持教程](media/azure-stack-help-and-support/get-support-previous.png)

如果有云服务问题，则通过 Microsoft 支持部门提供支持。 你可以选择管理员门户右上角的 " **帮助** (问号") ，选择 " **帮助和支持**"，然后选择 " **新建支持请求** " 以使用 Microsoft 支持部门直接提交新的支持请求。

对于集成系统，Microsoft 和我们的 OEM 合作伙伴之间存在协调的升级和解决过程。 如果有云服务问题，则通过 Microsoft 支持部门提供支持。

如果存在部署问题、修补和更新问题、硬件（包括现场可更换部件）问题，以及任何硬件品牌软件（例如在硬件生命周期主机上运行的软件）问题，请首先联系 OEM 硬件供应商。 对于其他任何内容，请联系 Microsoft 支持部门。

对于 ASDK，可以在 [Azure Stack Hub MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提出与支持相关的问题。

选择管理员门户右上角的“帮助”（问号），然后选择“新建支持请求”，从 Azure Stack Hub 社区的专家处获取帮助 。 由于 ASDK 是一个评估环境，因此没有通过 Microsoft 支持部门提供的官方支持。

::: moniker-end

## <a name="next-steps"></a>后续步骤

- 了解[诊断日志收集](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)。
- 了解如何[查找云 ID](azure-stack-find-cloud-id.md)。
- 了解如何[排查 Azure Stack Hub 问题](azure-stack-troubleshooting.md)。