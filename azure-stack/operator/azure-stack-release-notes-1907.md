---
title: Azure Stack 1907 发行说明 |Microsoft Docs
description: 了解 Azure Stack 集成系统的1907更新, 包括新功能、已知问题和更新下载位置。
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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 961233bea7c2581e20cb5cae875b9dab9f617758
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497240"
---
# <a name="azure-stack-1907-update"></a>Azure Stack 1907 更新

适用对象：*Azure Stack 集成系统*

本文介绍1907更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1907 更新内部版本号为**1.1907.0.20**。

### <a name="update-type"></a>更新类型

Azure Stack 1907 更新生成类型为**Express**。 有关更新生成类型的详细信息, 请参阅[Azure Stack 文章中的管理更新](azure-stack-updates.md)。 基于内部测试, 完成1907更新所需的预期时间大约为13小时。 
 - 确切的更新运行时将依赖于您的系统上使用的容量、租户工作负荷、系统网络连接 (如果已连接到 internet) 以及系统硬件配置。 
 - 持续时间比预期值长的运行时并不常见, 因此, 如果更新失败, 则不需要 Azure Stack 运算符执行操作。 
 - 此运行时近似值特定于1907更新, 不应与其他 Azure Stack 更新进行比较。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- Azure Stack 诊断日志收集服务的公开发行版, 以便于和改善诊断日志收集。 Azure Stack 诊断日志收集服务提供了一种简单的方法来收集和共享与 Microsoft 客户支持服务 (CSS) 的诊断日志。 Azure Stack 诊断日志收集服务在 Azure Stack 管理员门户中提供了新的用户体验, 使操作员能够在引发某些关键警报时将诊断日志自动上传到存储 blob, 或者执行按需操作。 有关详细信息, 请参阅[诊断日志收集](azure-stack-diagnostic-log-collection-overview.md)一文。

- 添加了内部机密旋转过程, 以便在系统更新过程中根据需要轮替内部 SQL TLS 证书。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- "Azure Stack 更新" 边栏选项卡现在为活动更新显示 "上一步完成" 时间。 转到 "更新" 边栏选项卡, 并单击正在运行的更新可查看此选项。 "上一步完成" 在 "更新运行详细信息" 部分中可用。

- **Test-azurestack**和**test-azurestack**运算符操作的改进。 开始 Azure Stack 的时间平均降低了 50%。 Azure Stack 的关闭时间平均降低了 30%。 平均启动和关闭时间与缩放单位中的节点数相同。 

- 改善了断开连接的 Marketplace 工具的错误处理。 如果在使用**AzSOfflineMarketplaceItem**时下载失败或部分成功, 则会显示详细的错误消息, 其中包含有关错误和缓解步骤的详细信息 (如果有)。

- 提高了从大型页面 blob/快照创建托管磁盘的性能。 以前, 它在创建大磁盘时触发了超时。  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- 改善了虚拟磁盘运行状况检查, 然后关闭节点, 以避免意外的虚拟磁盘分离。

- 改善了用于管理员操作的内部日志的存储。 这会使内部日志进程的内存和存储消耗降到最低, 从而提高了管理员操作期间的性能和可靠性。 客户可能还会注意到在管理员门户中的更新边栏选项卡上的页面加载时间已提升。 作为此改进的一部分, 超过6个月的更新日志将不再出现在系统中。 如果需要这些更新的日志, 请确保在执行1907更新之前下载所有超过6个月的更新的[摘要](https://docs.microsoft.com/en-us/azure-stack/operator/azure-stack-apply-updates)。

### <a name="changes"></a>更改

- 添加了新的出站规则 (HTTPS) 以启用 Azure Stack 诊断日志收集服务的通信。 有关详细信息, 请参阅[Azure Stack 数据中心集成-发布终结点](azure-stack-integrate-endpoints.md#ports-and-urls-outbound)。

- 如果外部存储位置的容量不足, 则基础结构备份服务将删除部分上载的备份。

- 基础结构备份不再包含域服务数据的备份。 这仅适用于使用 Azure Active Directory 作为标识提供者的系统。

- 现在, 我们将验证正在引入 > 的映像是否已 VM 映像边栏选项卡的类型为页 blob。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- 修复了发布者、产品/服务和 SKU 在资源管理器模板中被视为区分大小写的问题: 没有为部署提取映像, 除非映像参数与发布者、产品/服务和 SKU 的大小写相同。

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- 修复了由于备份存储服务元数据期间出现超时, 导致备份失败并出现**PartialSucceeded**错误消息的问题。  

- 修复了删除用户订阅导致孤立资源的问题。

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- 修复了由于 WMI 提供程序主机锁定的 DLL 文件而导致更新失败的问题。

-   修复了更新服务中阻止可用更新在更新磁贴或资源提供程序中显示的问题。 此问题已在1906修补程序[KB4511282](https://support.microsoft.com/help/4511282/)中发现和修复。

- 修复了一个问题, 此问题可能会导致更新失败, 因为管理平面由于配置错误而变得不正常。 此问题已在1906修补程序[KB4512794](https://support.microsoft.com/help/4512794/)中发现和修复。

- 修复了阻止用户完成从 marketplace 部署第三方映像的问题。 此问题已在1906中发现并修复了修补程序[KB4511259](https://support.microsoft.com/help/4511259/)。

- 修复了可能导致从托管映像创建 VM 失败的问题, 因为用户映像管理器服务崩溃。 此问题已在1906中发现并与[KB4512794](https://support.microsoft.com/help/4512794/)修复。

- 解决了由于 appgateway 缓存未按预期刷新而导致 VM CRUD 操作失败的问题。 此问题已在1906中发现并与[KB4513119](https://support.microsoft.com/en-us/help/4513119/)修复。

-   修复了运行状况资源提供程序中的问题, 该提供程序在管理员门户中影响了区域和警报 blade 的可用性。 此问题已在1906中发现并与[KB4512794](https://support.microsoft.com/help/4512794)修复。


## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息, 请参阅[Azure Stack 安全更新](azure-stack-release-notes-security-updates-1907.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](azure-stack-release-notes-known-issues-1907.md)
- [安全更新](azure-stack-release-notes-security-updates-1907.md)
- [应用更新之前和之后的活动清单](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从[Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1907 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 在将 Azure Stack 更新为1907之前, 请务必安装最新 Azure Stack 修补程序1906。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1907-update"></a>在应用1907更新之前

1907版本的 Azure Stack 必须应用于带有以下修补程序的1906版本:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1906.11.52](https://support.microsoft.com/help/4513119)

### <a name="after-successfully-applying-the-1907-update"></a>成功应用1907更新后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息, 请参阅[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- 没有可用于1907的修补程序。

## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。 无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>已存档的发行说明

可[在 TechNet 库中查看 Azure Stack 发行说明的早期版本](https://aka.ms/azsarchivedrelnotes)。 这些存档的发行说明仅供参考目的提供, 并不意味着对这些版本的支持。 有关 Azure Stack 支持的信息, 请参阅[Azure Stack 服务策略](azure-stack-servicing-policy.md)。 若要获得更多帮助, 请与 Microsoft 客户支持服务联系。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。
