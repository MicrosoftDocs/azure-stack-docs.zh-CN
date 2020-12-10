---
title: Azure Stack HCI 数据收集
description: 本主题介绍与 Azure Stack HCI 收集的诊断数据相关的设计和策略。
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: 91f578bcb0dc3a9d2debd086e6ed95cb591de90d
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97053059"
---
# <a name="azure-stack-hci-data-collection"></a>Azure Stack HCI 数据收集

> 适用于：Azure Stack HCI，版本 20H2

本主题介绍收集的必要数据，以保持 Azure Stack HCI 安全、最新，以及在2020年12月公开上市 (GA) 。

Microsoft 提供 Azure Stack HCI 需要下述数据。 每日收集一次此数据，并且可以在事件日志中查看数据收集事件。 Azure Stack HCI 收集使群集保持最新、安全和正常运行所需的最小数据。

   > [!IMPORTANT]
   > 下面所述的 Azure Stack HCI 收集的数据独立于 Windows 诊断数据，可针对各种级别的集合进行配置。 在 Azure Stack HCI 中，Windows 诊断数据收集的默认设置是) 的安全 (，这意味着除非管理员更改诊断数据设置，否则不会发送 Windows 诊断数据。 有关详细信息，请参阅[配置组织中的 Windows 诊断数据](/windows/privacy/configure-windows-diagnostic-data-in-your-organization)。 Microsoft 是与 Azure Stack HCI 连接的任何 Windows 诊断数据的独立控制器。 Microsoft 将根据 [Microsoft 隐私声明](https://privacy.microsoft.com/privacystatement)处理 Windows 诊断数据。

## <a name="data-collection-and-residency"></a>数据收集和常驻

此 Azure Stack HCI 数据： 

- 在将产品注册到 Azure 之前，不会将其发送给 Microsoft。 取消注册 Azure Stack HCI 后，此数据收集将停止。
- 记录到 Test-azurestack-HCI/分析事件通道。 
- 采用 JSON 格式，因此系统管理员可以检查和分析要发送的数据。
- 存储在安全的 Microsoft 运营数据中心的美国中。

## <a name="data-retention"></a>数据保留

Azure Stack HCI 收集此数据后，会将其保留90天。 聚合的、取消识别的数据可能会保留更长的时间。

## <a name="what-data-is-collected"></a>收集什么数据？

Azure Stack HCI 收集：

- 有关服务器的信息，如操作系统版本、处理器型号、处理器核心数、内存大小、群集标识符和硬件 ID 的哈希
- 安装 Azure Stack HCI 服务器功能的列表 (例如 BitLocker) 
- 计算 Azure Stack HCI 操作系统的可靠性所需的信息
- 计算运行状况收集数据的可靠性所需的信息
- 从事件日志中收集的用于特定错误的信息，如更新下载失败
- 有关计算存储可靠性的信息
- 用于计算物理磁盘可靠性的信息
- 有关计算卷加密可靠性的信息
- 用于计算存储空间修复的可靠性和性能的信息
- 用于验证 Azure Stack HCI 操作系统安全性的信息
- 用于计算 Azure Stack HCI 操作系统的防病毒/反恶意软件状态的可靠性的信息
- 用于关联网络组件可靠性的信息
- 要关联网络性能的信息
- 与更新和安装的可靠性关联的信息
- 用于测量 Hyper-v 可靠性的信息
- 用于测量/关联群集组件可靠性的信息
- 跟踪群集感知更新是否成功 (CAU) 功能的信息
- 用于测量/关联灾难恢复功能的可靠性的信息
- 描述应用于 Azure Stack HCI 服务器的 SMB 带宽限制的信息

## <a name="view-this-data"></a>查看此数据

1. 使用以下 PowerShell 命令启用分析日志：

   ```PowerShell
   wevtutil sl Microsoft-AzureStack-HCI/Analytic /e:True
   ```

2. 查看日志以查看收集的数据：

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest
   ```

3. 设置要导出的数据的格式：

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest `
   | Where-Object Id -eq 802 `
   | ForEach-Object { 
       [pscustomobject] @{
           TimeCreated = $_.TimeCreated 
           EventName=$_.Properties[0].Value 
           Value=$_.Properties[1].Value 
       } 
   }
   ```
 
输出应类似于：

```shell
TimeCreated            EventName                                                  Value
-----------            ---------                                                  -----
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.Core                   {"OEMName":"Microsoft Corporation"...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ProductFeatures        {"InstalledFeatures":["Server-Core...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.OSReliability          {"DailyDirtyRestarts":0,"WeeklyDir...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.DiagnosticHealth       {"DailySuccessfulDiagnosticUploads...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ErrorSummary           {"ErrorSummary":[{"EventName":"Win...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.VolumeSummary          {"VolumeCount":2,"HealthyVolumeCou...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.DiskSummary            {"DiskCount":33,"Summary":[]}
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.BitlockerVolumeSummary {"BitlockerVolumeCount":0,"Summary...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageErrors          {"ErrorSummary":[{"EventName":"Sto...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageRepairSummary   {"DailyRepairStartCount":0,"Weekly...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.TrustedPlatformModule  {"Manufacturer":"MSFT","Manufactur...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.MicrosoftDefender      {"AMEngineVersion":"1.1.17600.5","...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkInfo            {"NetworkDirect":true,"NetworkDire...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkAdapterSummary  {"NetworkAdapterGroup":[{"DriverNa...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.OSDeploy               {"OSInstallType":0}
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.ClusterProperties      {"Id":"fd2fc061-b924-4d61-a45b-3b3...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.DisasterRecovery       {"IsDisasterRecoveryEnabled":false...
```
