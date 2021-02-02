---
title: Azure Stack 1907 发行说明 | Microsoft Docs
description: 了解 Azure Stack 集成系统1907的更新
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9d5aeb68190f8e2b13ec2a49c3237b59ee33bfff
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248021"
---
# <a name="azure-stack-updates-1907-release-notes"></a>Azure Stack 更新：1907发行说明

本文介绍 Azure Stack 更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。

若要访问不同版本的发行说明，请使用左侧目录上方的版本选择器下拉列表。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

> [!IMPORTANT]  
> 如果 Azure Stack 实例落后于两个以上的更新，则认为它不符合。 必须[至少更新到最低支持版本才能获得支持](../azure-stack-servicing-policy.md#keep-your-system-under-support)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues-1907.md)
- [安全更新](../release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](../release-notes-checklist.md)

有关对更新和更新过程进行故障排除的帮助，请参阅[对 Azure Stack 的修补和更新问题进行故障排除](../azure-stack-updates-troubleshoot.md)。

## <a name="1907-build-reference"></a>1907 内部版本参考

Azure Stack 1907 更新内部版本号为 **1.1907.0.20**。

### <a name="update-type"></a>更新类型

Azure Stack 1907 更新内部版本类型为“快速”。 有关更新内部版本类型的详细信息，请参阅[管理 Azure Stack 中的更新](../azure-stack-updates.md)一文。 根据内部测试，完成 1907 更新所需的预期时间约为 13 个小时。

- 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件配置。
- 运行时间超过预期并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。
- 此运行时近似值特定于 1907 更新，不应与其他 Azure Stack 更新进行比较。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 正式推出 Azure Stack 诊断日志收集服务，以加速和改善诊断日志的收集。 Azure Stack 诊断日志收集服务提供了一种简单的方法来收集和共享与 Microsoft 客户支持服务 (CSS) 的诊断日志。 此诊断日志收集服务可在 Azure Stack 管理员门户中提供新的用户体验，让操作员设置在引发特定的关键警报时自动将诊断日志上传到存储 Blob，或按需执行相同的操作。 有关详细信息，请参阅[诊断日志收集](../azure-stack-diagnostic-log-collection-overview.md)一文。

- 正式推出 Azure Stack 网络基础结构验证作为 Azure Stack 验证工具 **Test-AzureStack** 的一部分。 Azure Stack 网络基础结构将成为 **Test-AzureStack** 的一部分，可识别 Azure Stack 的网络基础结构是否发生故障。 此测试绕过 Azure Stack 软件定义的网络来检查网络基础结构的连接。 它会演示如何从公共 VIP 连接到配置的 DNS 转发器、NTP 服务器和标识终结点。 此外，在使用 Azure AD 作为标识提供者时，它会检查与 Azure 的连接；使用 ADFS 时，它会检查与联合服务器的连接。 有关详细信息，请参阅 [Azure Stack 验证工具](../azure-stack-diagnostic-test.md)一文。

- 添加了内部机密轮换过程，以便在系统更新期间轮换内部 SQL TLS 证书。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- “Azure Stack 更新”边栏选项卡现在会显示活动更新的“最后一个步骤已完成”时间。 转到“更新”边栏选项卡，然后单击某个正在运行的更新，即可查看此信息。 “最后一个步骤已完成”随后将显示在“更新运行详细信息”部分。 

- 改进了 **Start-AzureStack** 和 **Stop-AzureStack** 操作员操作。 Azure Stack 的启动时间平均已减少 50%。 Azure Stack 的关闭时间平均已减少 30%。 当缩放单元中的节点数目增加时，平均启动和关闭时间保持不变。

- 改进了已断开连接的市场工具的错误处理方式。 如果在使用 **Export-AzSOfflineMarketplaceItem** 时下载失败或部分成功，系统将显示详细的错误消息，其中包含有关错误和缓解步骤（如果有）的更详细信息。

- 改进了从大型页 Blob/快照创建托管磁盘的性能。 以前在创建大型磁盘时会触发超时。  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- 改进了关闭节点之前的虚拟磁盘运行状况检查，以避免发生意外的虚拟磁盘分离。

- 改进了管理员操作内部日志的存储方式。 这可以尽量减少内部日志进程使用的内存和存储，从而改进管理员操作期间的性能和可靠性。 你还可能会注意到，管理员门户中的更新边栏选项卡页加载时间有所改善。 作为此项改进的一部分，系统将不再提供超过 6 个月的更新日志。 如果需要这些更新的日志，请务必先针对超过 6 个月的所有已运行更新[下载摘要](../azure-stack-apply-updates.md)，然后执行 1907 更新。

### <a name="changes"></a>更改

- Azure Stack 版本 1907 包含警告警报，指示操作员在更新到版本 1908 之前，先将其系统的 OEM 包更新为版本 2.1 或更高版本。 有关如何应用 Azure Stack OEM 更新的详细信息，请参阅[应用 Azure Stack 原始设备制造商更新](../azure-stack-update-oem.md)。

- 已添加新的出站规则 (HTTPS) 来启用 Azure Stack 诊断日志收集服务的通信。 有关详细信息，请参阅 [Azure Stack 数据中心集成 - 发布终结点](../azure-stack-integrate-endpoints.md#ports-and-urls-outbound)。

- 现在，如果外部存储位置耗尽了容量，基础结构备份服务将会删除部分上传的备份。

- 基础结构备份不再包含域服务数据的备份。 这仅适用于使用 Azure Active Directory 作为标识提供者的系统。

- 我们现在会验证引入到“计算”->“VM 映像”边栏选项卡中的映像是否为页 Blob 类型。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- 修复了在资源管理器模板中将发布者、套餐和 SKU 视为区分大小写的问题：除非映像参数的大小写与发布者、套餐和 SKU 的大小写相同，否则不会提取该映像进行部署。

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- 修复了因存储服务元数据备份期间超时而导致备份失败并显示 **PartialSucceeded** 错误消息的问题。  

- 修复了删除用户订阅导致孤立资源的问题。

- 修复了在创建套餐时不保存说明字段的问题。

- 修复了具有“只读”权限的用户能够创建、编辑和删除资源的问题。 现在，只有在获得“参与者”权限之后，用户才能创建资源。 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- 修复了由于 WMI 提供程序主机锁定 DLL 文件而导致更新失败的问题。

- 修复了更新服务中使得可用更新无法显示在更新磁贴或资源提供程序中的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4511282](https://support.microsoft.com/help/4511282/) 中予以修复。

- 修复了由于配置不当而导致管理平面变得不正常，从而导致更新失败的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4512794](https://support.microsoft.com/help/4512794/) 中予以修复。

- 修复了导致用户无法从市场完成第三方映像部署的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4511259](https://support.microsoft.com/help/4511259/) 中予以修复。

- 修复了用户映像管理器服务崩溃可能导致无法从托管映像创建 VM 的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4512794](https://support.microsoft.com/help/4512794/) 中予以修复

- 修复了由于应用程序网关缓存未按预期刷新而导致 VM CRUD 操作失败的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4513119](https://support.microsoft.com/en-us/help/4513119/) 中予以修复

- 修复了运行状况资源提供程序中的一个问题，该问题会影响管理员门户中区域和警报边栏选项卡的可用性。 此问题是在 1906 中发现的，已在修补程序 [KB4512794](https://support.microsoft.com/help/4512794) 中予以修复。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](../release-notes-security-updates.md)。

## <a name="download-the-update"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1907 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1907 之前，请务必先安装 1906 的最新 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1907-update"></a>应用 1907 更新之前

必须在包含以下修补程序的版本 1906 中应用 Azure Stack 版本 1907：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>成功应用 1907 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](../azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1907.17.54](https://support.microsoft.com/help/4523826)