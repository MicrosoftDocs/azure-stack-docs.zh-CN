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
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 2323f13029d4d356ce54b825bab69da5ca21d6ab
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993582"
---
# <a name="asdk-release-notes"></a>ASDK 发行说明

本文提供了有关 Azure Stack 开发工具包（ASDK）中的更改、修复和已知问题的信息。 如果你不确定要运行的版本，请[使用门户检查](../operator/azure-stack-updates.md)。

订阅[![rss](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [rss 源](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，随时了解 ASDK 的新增功能。

::: moniker range="azs-1910"
## <a name="build-11910058"></a>生成1.1910.0.58

### <a name="new-features"></a>新增功能

- 有关此版本中已修复问题、更改和新功能的列表，请参阅[Azure Stack 发行说明](../operator/release-notes.md)中的相关章节。

### <a name="fixed-and-known-issues"></a>修复的问题和已知问题

- 修复了收集日志并将其存储在 Azure 存储 blob 容器中的问题。 此操作的语法如下所示：

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ``` 

- 修复了一个部署问题：缓慢加载后台处理程序服务会阻止删除某些 Windows 功能，并需要重新启动。
- 有关此版本中 Azure Stack 已知问题的列表，请参阅[已知问题](../operator/known-issues.md)一文。
- 请注意，可用的 Azure Stack 修补程序不适用于 ASDK。
::: moniker-end

::: moniker range="azs-1908"
  
## <a name="build-11908020"></a>生成1.1908.0.20

### <a name="new-features"></a>新增功能

- 有关此版本中新功能的列表，请参阅 Azure Stack 发行说明中的[此部分](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1)。

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>修复的问题和已知问题

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- 有关已知问题的列表，请参阅[此文](/azure-stack/operator/known-issues?view=azs-1908)。
- 请注意，可用的 Azure Stack 修补程序不适用于 ASDK。
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>生成1.1907.0.20

### <a name="new-features"></a>新增功能

- 有关此版本中新功能的列表，请参阅 Azure Stack 发行说明中的[此部分](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update)。

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>修复的问题和已知问题

- 使用某些 Marketplace 映像创建 VM 资源时，可能无法完成部署。 作为一种解决方法，你可以在 "**摘要**" 页中单击 "**下载模板和参数**" 链接，然后单击**模板**边栏选项卡中的 "**部署**" 按钮。
- 有关此版本中已修复的 Azure Stack 问题的列表，请参阅 Azure Stack 发行说明中的[此部分](/azure-stack/operator/release-notes?view=azs-1907#fixes-2)。
- 有关已知问题的列表，请参阅[此文](/azure-stack/operator/known-issues?view=azs-1907)。
- 请注意，[可用的 Azure Stack 修补程序](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2)不适用于 Azure Stack ASDK。
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>生成1.1906.0.30

### <a name="new-features"></a>新增功能

- 有关此版本中新功能的列表，请参阅 Azure Stack 发行说明中的[此部分](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1)。

### <a name="changes"></a>更改

- 添加了**AZS SRNG01**支持环 VM，该 VM 承载 Azure Stack 的日志收集服务。 有关详细信息，请参阅[虚拟机角色](asdk-architecture.md)。

### <a name="fixed-and-known-issues"></a>修复的问题和已知问题

- 使用某些 Marketplace 映像创建 VM 资源时，可能无法完成部署。 作为一种解决方法，你可以在 "**摘要**" 页中单击 "**下载模板和参数**" 链接，然后单击**模板**边栏选项卡中的 "**部署**" 按钮。
- 有关此版本中已修复的 Azure Stack 问题的列表，请参阅 Azure Stack 发行说明中的[此部分](/azure-stack/operator/release-notes?view=azs-1906#fixes-3)。
- 有关已知问题的列表，请参阅[此文](/azure-stack/operator/known-issues?view=azs-1906)。
- 请注意，[可用的 Azure Stack 修补程序](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-3)不适用于 Azure Stack ASDK。
::: moniker-end
