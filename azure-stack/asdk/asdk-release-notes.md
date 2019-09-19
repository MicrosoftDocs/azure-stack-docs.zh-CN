---
title: ASDK 发行说明 |Microsoft Docs
description: Azure Stack 开发工具包的改进、修复和已知问题（ASDK）。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 98e43cfe0226e06ca936484a78da5a61915f5797
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101030"
---
# <a name="asdk-release-notes"></a>ASDK 发行说明

本文提供了有关 Azure Stack 开发工具包（ASDK）中的更改、修复和已知问题的信息。 如果你不确定要运行的版本，请[使用门户检查](../operator/azure-stack-updates.md)。

请订阅 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS 源](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，随时了解 ASDK 的新增功能。

::: moniker range="azs-1908"
## <a name="build-11908020"></a>生成1.1908.0.20

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1908)。

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1908) of the Azure Stack release notes. -->
- 如需已知问题的列表，请参阅[此文](/azure-stack/operator/known-issues?view=azs-1908)。
- 请注意，可用的 Azure Stack 修补程序不适用于 ASDK。
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>内部版本 1.1907.0.20

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update-1907)。

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 使用某些 Marketplace 映像创建 VM 资源时，可能无法完成部署。 作为一种解决方法，可以单击“摘要”页中的“下载模板和参数”链接，然后单击“模板”边栏选项卡中的“部署”按钮。
- 如需此版本中已修复的 Azure Stack 问题的列表，请参阅 Azure Stack 发行说明的[此部分](/azure-stack/operator/release-notes?view=azs-1907#fixes-1907)。
- 如需已知问题的列表，请参阅[此文](/azure-stack/operator/known-issues?view=azs-1907)。
- 请注意，[发布的 Azure Stack 修补程序](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-1907)不适用于 Azure Stack ASDK。
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>内部版本 1.1906.0.30

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1906)。

### <a name="changes"></a>更改

- 添加了 **AzS-SRNG01** 支持环 VM，用于托管 Azure Stack 的日志收集服务。 有关详细信息，请参阅[虚拟机角色](asdk-architecture.md)。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 使用某些 Marketplace 映像创建 VM 资源时，可能无法完成部署。 作为一种解决方法，可以单击“摘要”页中的“下载模板和参数”链接，然后单击“模板”边栏选项卡中的“部署”按钮。
- 如需此版本中已修复的 Azure Stack 问题的列表，请参阅 Azure Stack 发行说明的[此部分](/azure-stack/operator/release-notes?view=azs-1906#fixes-1906)。
- 如需已知问题的列表，请参阅[此文](/azure-stack/operator/known-issues?view=azs-1906)。
- 请注意，[发布的 Azure Stack 修补程序](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-1906)不适用于 Azure Stack ASDK。
::: moniker-end

::: moniker range="azs-1905"
## <a name="build-11905040"></a>内部版本 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](/azure-stack/operator/release-notes?view=azs-1905#whats-in-this-update-1905)。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 修复了必须编辑 **RegisterWithAzure.psm1** PowerShell 脚本才能成功[注册 ASDK](asdk-register.md) 的问题。
- 如需此版本中已修复的其他 Azure Stack 问题的列表，请参阅 Azure Stack 发行说明的[此部分](/azure-stack/operator/release-notes?view=azs-1905#fixes-1905)。
- 如需已知问题的列表，请参阅[此文](/azure-stack/operator/known-issues?view=azs-1905)。
- 请注意，[发布的 Azure Stack 修补程序](/azure-stack/operator/release-notes?view=azs-1905#hotfixes-1905)不适用于 Azure Stack ASDK。
::: moniker-end
