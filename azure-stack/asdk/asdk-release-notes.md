---
title: Microsoft Azure Stack 开发工具包发行说明 |Microsoft Docs
description: Azure Stack 开发工具包的改进、修复和已知问题。
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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: a2b000f60c5867e557ef5b0621f994ea7ec23913
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493783"
---
# <a name="asdk-release-notes"></a>ASDK 发行说明

本文介绍了 Azure Stack 开发工具包 (ASDK) 中的更改、修复和已知问题。 如果不确定所运行的版本，可以[使用门户检查版本](../operator/azure-stack-updates.md#determine-the-current-version)。

请订阅 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS 源](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，随时了解 ASDK 的新增功能。

## <a name="build-11907020"></a>生成1.1907.0.20

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-release-notes-1907.md#whats-in-this-update)。

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 使用某些 Marketplace 映像创建虚拟机资源时, 可能无法完成部署。 作为一种解决方法, 你可以在 "**摘要**" 页中单击 "**下载模板和参数**" 链接, 然后单击**模板**边栏选项卡中的 "**部署**" 按钮。
- 有关此版本中已修复的 Azure Stack 问题的列表, 请参阅 Azure Stack 发行说明中的[此部分](../operator/azure-stack-release-notes-1907.md#fixes)。
- 如需已知问题的列表，请参阅[此文](../operator/azure-stack-release-notes-known-issues-1907.md)。
- 请注意，[发布的 Azure Stack 修补程序](../operator/azure-stack-release-notes-1907.md#hotfixes)不适用于 Azure Stack ASDK。

## <a name="build-11906030"></a>生成1.1906.0.30

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-release-notes-1906.md#whats-in-this-update)。

### <a name="changes"></a>更改

- 添加了**AZS SRNG01**支持环 VM, 该 VM 承载 Azure Stack 的日志收集服务。 有关详细信息, 请参阅[虚拟机角色](asdk-architecture.md)。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 使用某些 Marketplace 映像创建虚拟机资源时, 可能无法完成部署。 作为一种解决方法, 你可以在 "**摘要**" 页中单击 "**下载模板和参数**" 链接, 然后单击**模板**边栏选项卡中的 "**部署**" 按钮。
- 有关此版本中已修复的 Azure Stack 问题的列表, 请参阅 Azure Stack 发行说明中的[此部分](../operator/azure-stack-release-notes-1906.md#fixes)。
- 如需已知问题的列表，请参阅[此文](../operator/azure-stack-release-notes-known-issues-1906.md)。
- 请注意，[发布的 Azure Stack 修补程序](../operator/azure-stack-release-notes-1906.md#hotfixes)不适用于 Azure Stack ASDK。

## <a name="build-11905040"></a>生成1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-release-notes-1905.md#whats-in-this-update)。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 解决了必须编辑**Hbase-runner.psm1** PowerShell 脚本才能成功[注册 ASDK](asdk-register.md)的问题。
- 如需此版本中已修复的其他 Azure Stack 问题的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-release-notes-1905.md#fixes)。
- 如需已知问题的列表，请参阅[此文](../operator/azure-stack-release-notes-known-issues-1905.md)。
- 请注意，[发布的 Azure Stack 修补程序](../operator/azure-stack-release-notes-1905.md#hotfixes)不适用于 Azure Stack ASDK。

## <a name="build-11904036"></a>内部版本 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>新增功能

- 如需此版本中新功能的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-release-notes-1904.md#whats-in-this-update)。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 由于运行注册脚本时服务主体超时, 若要成功[注册 ASDK](asdk-register.md) , 必须编辑**registerwithazure.psm1** PowerShell 脚本。 请执行以下操作：

  1. 在 ASDK 主计算机上, 使用提升的权限在编辑器中打开文件**C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** 。
  2. 在第1249行的末尾`-TimeoutInSeconds 1800`添加一个参数。 这是必需的, 因为运行注册脚本时服务主体超时。 第1249行现在应如下所示:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- 修复了版本1902中标识的 VPN 连接问题。

- 如需此版本中已修复的其他 Azure Stack 问题的列表，请参阅 Azure Stack 发行说明的[此部分](../operator/azure-stack-release-notes-1904.md#fixes)。
- 如需已知问题的列表，请参阅[此文](../operator/azure-stack-release-notes-known-issues-1904.md)。
- 请注意，[发布的 Azure Stack 修补程序](../operator/azure-stack-release-notes-1904.md#hotfixes)不适用于 Azure Stack ASDK。

