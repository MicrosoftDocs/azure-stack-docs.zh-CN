---
title: ASDK 发行说明
description: Azure Stack 开发工具包 (ASDK) 的改进、修复和已知问题。
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/10/2020
ms.openlocfilehash: ea7c991a1d9b16580cb4fbd7f812ec776da6e084
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674296"
---
# <a name="asdk-release-notes"></a>ASDK 发行说明

本文介绍了 Azure Stack 开发工具包 (ASDK) 中的更改、修复和已知问题。 如果不确定所运行的版本，请[使用门户进行查看](../operator/azure-stack-updates.md)。

请订阅 ![RSS](./media/asdk-release-notes/feed-icon-14x14.png) [RSS 源](https://aka.ms/asdkrss)，随时了解 ASDK 的新增功能。

::: moniker range="azs-2008"
## <a name="build-120081388"></a>生成1.2008.13.88

### <a name="new-features"></a>新增功能

- 有关此版本中已修复问题、更改和新功能的列表，请参阅 [Azure Stack 发行说明](../operator/release-notes.md)中的相关章节。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

::: moniker-end

::: moniker range="azs-2005"
## <a name="build-12005040"></a>内部版本 1.2005.0.40

### <a name="new-features"></a>新增功能

- 有关此版本中已修复问题、更改和新功能的列表，请参阅 [Azure Stack 发行说明](../operator/release-notes.md)中的相关章节。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 解密认证密码是一种新选项，用于指定包含解密备份数据所需的私钥的自签名证书 (.pfx) 的密码。 只有在使用证书加密备份时，此密码才是必需的。
- 修复了问题：如果多节点源系统上的原始外部证书密码发生更改，云恢复会失败。 
- 有关此发布中 Azure Stack 已知问题的列表，请参阅[已知问题](../operator/known-issues.md)一文。
- 请注意，可用的 Azure Stack 修补程序不适用于 ASDK。

#### <a name="initial-configuration-fails-in-asdk"></a>初始配置在 ASDK 中失败

- 部署 ASDK 时，可能会收到错误消息：“‘Deployment-Phase0-DeployBareMetal’的状态为‘错误’”和“‘Deployment-InitialSteps’的状态为‘错误’” 。

- 解决方法如下：

1. 在具有行计数器的任何编辑器（例如 PowerShell ISE）中，打开 C:\CloudDeployment\Roles\PhysicalMachines\Tests\BareMetal.Tests.ps1 中的文件。

2. 将第 822 行替换为：

   ```powershell

   PartNumber = if($_.PartNumber) {$_.PartNumber.Trim()} else {""};

   ```  
::: moniker-end

::: moniker range="azs-2002"
## <a name="build-12002035"></a>内部版本 1.2002.0.35

### <a name="new-features"></a>新增功能

- 有关此版本中已修复问题、更改和新功能的列表，请参阅 [Azure Stack 发行说明](../operator/release-notes.md)中的相关章节。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 解密认证密码是一种新选项，用于指定包含解密备份数据所需的私钥的自签名证书 (.pfx) 的密码。 只有在使用证书加密备份时，此密码才是必需的。

- 有关此发布中 Azure Stack 已知问题的列表，请参阅[已知问题](../operator/known-issues.md)一文。

- 请注意，可用的 Azure Stack 修补程序不适用于 ASDK。

#### <a name="sql-vm-provision-fails-in-asdk"></a>ASDK 中的 SQL VM 预配失败

- 适用于：此问题适用于 ASDK 2002。
- 原因：在 ASDK 2002 中创建新 SQL VM 时，可能会收到一条错误消息“扩展存储库中找不到发布者为‘Microsoft.SqlServer.Management’，类型为‘SqlIaaSAgent’，并且类型处理程序版本为‘2.0’的扩展。” 在 Azure Stack Hub 中没有 SqlIaaSAgent 2.0。
::: moniker-end

::: moniker range="azs-1910"
## <a name="build-11910058"></a>内部版本 1.1910.0.58

### <a name="new-features"></a>新增功能

- 有关此版本中已修复问题、更改和新功能的列表，请参阅 [Azure Stack 发行说明](../operator/release-notes.md)中的相关章节。

### <a name="fixed-and-known-issues"></a>修复的和已知的问题

- 修复了收集日志并将其存储在 Azure 存储 Blob 容器中时遇到的问题。 此操作的语法如下所示：

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ``` 

- 修复了一个部署问题：缓慢加载后台处理程序服务会阻止删除某些 Windows 功能，并需要重新启动。
- 有关此发布中 Azure Stack 已知问题的列表，请参阅[已知问题](../operator/known-issues.md)一文。
- 请注意，可用的 Azure Stack 修补程序不适用于 ASDK。
::: moniker-end
