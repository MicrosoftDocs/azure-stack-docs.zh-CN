---
title: Azure Stack 1904 发行说明 | Microsoft Docs
description: 了解 Azure Stack 集成系统的1904更新, 其中包括新的、已知问题, 以及下载更新和存档 Azure Stack 发行说明的位置。
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
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: 71ad5812472534ae4a7e3dc34feabf7a1a02faa0
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494053"
---
# <a name="azure-stack-1904-update"></a>Azure Stack 1904 更新

适用对象：*Azure Stack 集成系统*

本文介绍 1904 更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。 本文包含以下信息：

- [新增功能、增强功能、修复和安全更新的说明](azure-stack-release-notes-1904.md#whats-in-this-update)
- [更新规划](azure-stack-release-notes-1904.md#update-planning)

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="archived-release-notes"></a>已存档的发行说明

可[在 TechNet 库中查看 Azure Stack 发行说明的早期版本](http://aka.ms/azsarchivedrelnotes)。 这些存档的发行说明仅供参考目的提供, 并不意味着对这些版本的支持。 若要获得更多帮助, 请与 Microsoft 客户支持服务联系。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1904 更新内部版本号为 **1.1904.0.36**。

### <a name="update-type"></a>更新类型

Azure Stack 1904 更新生成类型为**Express**。 有关更新生成类型的详细信息, 请参阅[Azure Stack 文章中的管理更新](azure-stack-updates.md)。 完成1904更新所需的预期时间大约为16小时, 但精确时间可能会有所不同。 此运行时近似值特定于1904更新, 不应与其他 Azure Stack 更新进行比较。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 1904 已对软件定义的网络 (SDN) 堆栈做了重大改进。 这些改进提高了 SDN 堆栈在 Azure Stack 中的整体服务能力和可靠性。

- 管理员门户中添加了通知，当目前登录的用户没有所需权限时会显示该通知，因此可让仪表板正常加载。 通知中还提供了说明哪些帐户具有相应权限（取决于部署期间使用的标识提供者）的文档的链接。

- 改进了 VM 的复原能力和运行时间，可解决当包含 VM 配置文件的存储卷脱机时所有 VM 都脱机的问题。

<!-- 1901,2,3 related hotfix -->
- 已将并发逐出并有消耗带宽上限的 VM 数目优化，以解决网络负载过重时的 VM 节电或停机问题。 此项更改增大了系统更新时 VM 的运行时间。

<!-- 1901,2,3 related hotfix -->
- 改进了遇到以下情况时的资源限制：系统大规模运行以避免内部进程耗尽平台资源，从而导致门户中的操作失败。

- 改进了可让操作员同时应用多个筛选器的筛选功能。 在新的用户界面中，只能按“名称”列进行排序。

- 改进了删除套餐、计划、配额和订阅的过程。 现在，如果你要删除的对象没有任何依赖项，则可以成功地从管理员门户中删除套餐、配额、计划和订阅。 有关详细信息，请参阅[此文章](azure-stack-delete-offer.md)。  

<!-- this applies to bug 3725384 and bug #4225643 -->
- 改进了 syslog 消息量，方法是筛选掉不必要的事件，并提供配置参数以针对转发的消息选择所需的严重性级别。 有关如何配置严重性级别的详细信息，请参阅 [Azure Stack 数据中心集成 - syslog 转发](azure-stack-integrate-security.md)。

<!--this applied to Bug 1473487 -->
- 已通过整合附加参数 `-OutputSASUri`，将一个新的功能添加到 **Get-AzureStackLog** cmdlet。 现在，可以从环境中收集 Azure Stack 日志，并将其存储在指定的 Azure 存储 Blob 容器中。 有关详细信息，请参阅 [Azure Stack 诊断](azure-stack-configure-on-demand-diagnostic-log-collection.md#examples)。

- 已在 **Test-AzureStack** `UpdateReadiness` 组中添加了新的内存检查，用于检查堆栈上是否有足够的内存，使更新能够成功完成。

<!-- Bug/Task 4311058 -->
- 改进了用于评估 Service Fabric 运行状况的 **Test-AzureStack**。

<!-- feature: 2976966 -->
- 改进了硬件更新，将完成驱动器固件更新所需的时间缩减为 2 到 4 个小时。 更新引擎根据包中的内容，动态确定需要执行更新的哪些部分。

<!-- Feature 3906611 -->
- 添加了可靠的操作预先检查，以防止中断性的基础结构角色实例操作影响可用性。

<!-- Feature 3780326 -->
- 改进了基础结构备份操作计划的幂等性。

<!--Bug/Task 3139609 -->
- 改进了 Azure Stack 日志收集。 这些改进缩减了检索日志集所需的时间。 此外，[Get-AzureStackLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system) cmdlet 不再为 OEM 角色生成默认日志。 必须执行 [Invoke-AzureStackOnDemandLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#invoke-azurestackondemandlog) cmdlet，并指定要检索其 OEM 日志的角色。 有关详细信息，请参阅 [Azure Stack 诊断](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system)。

- Azure Stack 现在会监视为数据中心与 ADFS 集成而提供的联合数据 URL。 这提高了客户 ADFS 实例或场的秘密旋转期间的可靠性。

### <a name="changes"></a>更改

<!-- Feature 3906611 -->
- 删除了可让 Azure Stack 操作员在管理员门户中关闭基础结构角色实例的选项。 重启功能可确保系统在重启基础结构角色实例之前先尝试干净关闭。 对于高级方案，API 和 PowerShell 功能仍然可用。

<!-- Feature ## 4199257 -->
- 提供了新的市场管理体验，市场映像和资源提供程序有各自的屏幕。 现在，“资源提供程序”窗口是空的，但将来发布的新 PaaS 服务套餐将会显示，并可在“资源提供程序”窗口中进行管理。

<!-- Feature ## 4199257 -->
- 更改了操作员门户中的更新体验。 资源提供程序更新有新的网格。 目前尚未推出更新资源提供程序的功能。

<!-- Task ## 3748423  -->
- 更改了操作员门户中的更新安装体验。 为了帮助 Azure Stack 操作员对更新问题做出适当的响应，门户现在会根据缩放单元的运行状况来提供更具体的建议（通过运行 **Test-AzureStack** 并分析结果来自动派生）。 它会根据结果告知操作员采取以下两项措施之一：

  - 门户中会显示一条“软性”警告警报，指出“最近的更新需要引以注意。 Microsoft 建议在正常营业时间提出服务请求。 在更新过程中，会执行 Test-AzureStack，我们会根据输出生成最适当的警报。 在此情况下，Test-AzureStack 将会通过。”

  - 门户中会显示一条“硬性”关键警报，指出“最近的更新失败。 Microsoft 建议尽快提出服务请求。 在更新过程中，会执行 Test-AzureStack，我们会根据输出生成最适当的警报。 在此情况下，Test-AzureStack 也会失败。”

- 已更新 Azure Linux Agent 版本 2.2.38.0。 此项支持可让客户在 Azure 与 Azure Stack 之间保持一致的 Linux 映像。

- 操作员门户中更新日志的更改。 检索成功更新日志的请求不再可用。 更新日志失败, 因为它们可用于诊断, 仍可供下载。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了以下问题：syslog 配置不会在整个更新周期中保留，导致 syslog 客户端丢失其配置，并停止转发 syslog 消息。 现在会保留 Syslog 配置。

- 修复了 CRP 中阻止解除分配 VM 的问题。 以前，如果 VM 包含多个大型托管磁盘，则解除分配 VM 可能由于超时错误而失败。

- 修复了 Windows Defender 引擎影响访问缩放单元存储的问题。

- 修复了以下用户门户问题：无法加载 Blob 存储帐户的“访问策略”窗口。

- 解决了管理员和用户门户中的问题, 其中显示了有关全局 Azure 门户的错误通知。

- 修复了以下用户门户问题：选择“反馈”磁贴会导致打开空的浏览器标签页。

- 修复了以下用户门户问题：更改附加到 VM 实例的网络适配器的绑定 IP 配置的静态 IP 地址会导致显示错误消息。

- 修复了以下用户门户问题：尝试通过“网络”窗口**将网络接口附加到**现有 VM 会导致操作失败并出现错误消息。

- 修复了以下问题：Azure Stack 不支持将 4 个以上的网络接口 (NIC) 附加到一个 VM 实例。

- 修复了以下门户问题：添加入站安全规则并选择“服务标记”作为源会显示多个无法供 Azure Stack 使用的选项。

- 解决了网络安全组 (Nsg) 在 Azure Stack 中的工作方式与与全局 Azure 相同的问题。

- 修复了以下市场管理问题：如果注册过期或被删除，则会隐藏所有已下载的产品。

- 修复了以下问题：在 PowerShell 中对现有的虚拟网络网关连接发出 **Set-AzureRmVirtualNetworkGatewayConnection** 命令失败，并出现错误消息“配置的共享密钥无效...”。

- 修复了导致网络资源提供程序 (NRP) 无法与网络控制器同步，从而导致请求重复资源的问题。 在某些情况下，这会导致父资源一直处于错误状态。

- 修复了以下问题：如果为某个用户分配了订阅的参与者角色，但未显式为其授予给定的读取权限，则在尝试保存对资源所做的更改时，会生成一条错误，指出“...对象 ID 为 {GUID} 的客户端 'somelogonaccount@domain.com' 未获得执行操作的授权...”。

- 修复了以下问题：如果使用脱机联合工具上传映像，并且其中的任一映像缺少图标 URI，则市场管理屏幕是空的。

- 修复了一个问题, 该问题阻止无法从 marketplace 管理中删除的产品。

### <a name="security-updates"></a>安全更新

Azure Stack 的此更新不包括托管 Azure Stack 的底层操作系统的安全更新。 有关信息，请参阅 [Azure Stack 安全更新](azure-stack-release-notes-security-updates-1904.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](azure-stack-release-notes-known-issues-1904.md)
- [安全更新](azure-stack-release-notes-security-updates-1904.md)
- [应用更新之前和之后的活动清单](azure-stack-release-notes-checklist.md)

> [!NOTE]
> 请确保使用最新版本的 [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) 工具来执行工作负荷规划和大小调整。 最新版本包含 bug 修复，并提供与每个 Azure Stack 更新一起发布的新功能。

## <a name="download-the-update"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1904 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1904 之前，请务必先安装 1903 的最新 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1904-update"></a>应用 1904 更新之前

必须在包含以下修补程序的版本 1903 中应用 Azure Stack 版本 1904：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1903.2.39](https://support.microsoft.com/help/4500638)

### <a name="after-successfully-applying-the-1904-update"></a>成功应用 1904 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

- [Azure Stack 修补程序1.1904.4.45](https://support.microsoft.com/help/4505688)

## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。 无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  

