---
title: Azure Stack 1904 发行说明 |Microsoft Docs
description: 了解有关 Azure Stack 集成系统，包括最新内容、 1904年更新的已知的问题，以及在何处下载的更新和已存档的 Azure Stack 发行说明。
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
ms.reviewer: ''
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: 770d3bc462750816e55f1c224496d9dde01f5a36
ms.sourcegitcommit: 4e0b450c91c6515794b663a39f9a4b8b49999918
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66411634"
---
# <a name="azure-stack-1904-update"></a>Azure Stack 1904 更新

*适用于：Azure Stack 集成系统*

本文介绍了 1904年更新包的内容。 此更新包括新增功能改进和修复对于此版本的 Azure Stack。 本文包含以下信息：

- [新增功能的说明，改进、 修复程序和安全更新](azure-stack-release-notes-1904.md#whats-in-this-update)
- [更新规划](azure-stack-release-notes-1904.md#update-planning)

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="archived-release-notes"></a>已存档的发行说明

您可以看到[较旧版本的 Azure Stack 发行 TechNet 库中的说明](http://aka.ms/azsarchivedrelnotes)。 这些存档的发行说明提供仅供参考，并不表示对这些版本的支持。 进一步的帮助，请联系 Microsoft 客户支持服务。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1904 更新内部版本号是**1.1904.0.36**。

## <a name="whats-in-this-update"></a>什么是此更新中

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 1904 更新负载包含哪些主机 Azure Stack 不包括基础操作系统的 Azure Stack 组件的更新。 这样就可以实现限定范围的特定更新。 因此，预期时间它花费的 1904年更新完成小于 （大约 16 个小时，但确切的时间而异）。 运行时此下降是类似于 1903年更新。 但是，后续更新可能包含操作系统，这意味着不同的运行时的更新。 将来的更新会根据包含的有效负载，提供有关完成更新预期所需时间的指导。

- 重大改进具有已对 1904年中软件定义网络 (SDN) 堆栈。 这些改进增加的总体服务和 Azure Stack 中的 SDN 堆栈的可靠性。

- 如果当前登录的用户不具有所需的权限，从而使仪表板，以正确加载在管理员门户中，添加一条通知。 它还包含指向介绍哪些帐户有适当的权限，具体取决于在部署过程中使用的标识提供程序的文档的链接。

- 添加了对 VM 复原能力和解析的方案中的所有 Vm 都脱机如果包含 VM 配置文件的存储卷处于脱机状态的运行时间的改进。

<!-- 1901,2,3 related hotfix -->
- 添加了的优化的 Vm 数同时撤走主机并放置在带宽消耗，地址 VM 限电或封锁在网络负载较重时的上一个上限。 更新系统时，此更改会增加 VM 正常运行时间。

<!-- 1901,2,3 related hotfix -->
- 改进的资源限制时系统的运行强度来保护对内部进程耗尽平台的资源，从而导致失败的操作，在门户中。

- 改进了筛选功能使操作员能够同时应用多个筛选器。 仅可以按排序**名称**新的用户界面中的列。

- 删除产品/服务、 计划、 配额和订阅的过程改进。 现在可以成功删除产品/服务、 配额、 计划和订阅管理员门户中你想要删除的对象是否没有依赖关系。 有关详细信息，请参阅[此文章](azure-stack-delete-offer.md)。  

<!-- this applies to bug 3725384 and bug #4225643 -->
- 改进了通过过滤掉不必要的事件并提供配置参数来选择所需的严重性级别为转发消息的 syslog 消息量。 有关如何配置严重级别的详细信息，请参阅[Azure Stack 数据中心集成-syslog 转发](azure-stack-integrate-security.md)。

- 在 Azure Stack 基础结构会消耗额外的 12 GB + (4 GB * 数量的 Azure Stack 主机) 从 1904 update 及更高版本。 这意味着，在 4 节点戳将有 28 GB 额外的容量消耗 (12 GB + 4 GB * 4) 在 Azure Stack 管理员门户的容量屏幕中反映出来。 到 1904年版本更新应会成功，即使其他内存消耗将 Azure Stack 戳放超出容量。 如果 Azure Stack 戳基于内存使用量在更新完成后，将看到专用于反映将此状态下的，修正措施以释放一些虚拟机与一个警报。

<!--this applied to Bug 1473487 -->
- 添加到了新功能**Get-azurestacklog** cmdlet 通过包含一个附加参数来`-OutputSASUri`。 现在可以从你的环境中收集 Azure Stack 日志并将其存储在指定的 Azure 存储 blob 容器。 有关详细信息，请参阅[Azure Stack 诊断](azure-stack-diagnostics.md#examples)。

- 添加新的内存签入**Test-azurestack** `UpdateReadiness`进行检查以查看是否有足够的内存可用于更新已成功完成的堆栈上的组。

<!-- Bug/Task 4311058 -->
- 对改进**Test-azurestack**评估 Service Fabric 运行状况。

<!-- feature: 2976966 -->
- 改进硬件更新，从而减少了完成驱动器固件花费的时间更新为 2-4 小时。 更新引擎动态确定更新的哪些部分需要执行，根据在包中的内容。

<!-- Feature 3906611 -->
- 若要防止影响可用性的破坏性的基础结构角色实例操作的添加可靠的操作，预检查。

<!-- Feature 3780326 -->
- 为幂等性的基础结构备份操作的改进计划。

<!--Bug/Task 3139609 -->
- Azure Stack 日志收集的改进。 这些改进减少检索的日志所需的时间。 此外， [Get-azurestacklog](azure-stack-diagnostics.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system) cmdlet 将无法再生成 OEM 角色的默认日志。 必须执行[Invoke AzureStackOnDemandLog](azure-stack-diagnostics.md#invoke-azurestackondemandlog) cmdlet，指定要检索的 OEM 日志的角色。 有关详细信息，请参阅[Azure Stack 诊断](azure-stack-diagnostics.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system)。

- Azure Stack 现在监视数据中心与 ADFS 集成为提供的联合身份验证数据 URL。 这将在 customer ADFS 实例或场的机密轮换期间提高可靠性。

### <a name="changes"></a>更改

<!-- Feature 3906611 -->
- 删除 Azure Stack 运营商如何关闭的情况下在管理员门户中的基础结构角色实例的选项。 重新启动功能可确保在重新启动基础结构角色实例之前的干净关闭尝试。 对于高级方案，API 和 PowerShell 功能仍然可用。

<!-- Feature ## 4199257 -->
- 没有新的 Marketplace 管理体验，使用单独的 Marketplace 映像和资源提供程序的屏幕。 现在，请**资源提供程序**窗口为空，但在将来的版本新 PaaS 服务产品/服务将显示和管理在**资源提供程序**窗口。

<!-- Feature ## 4199257 -->
- 对运算符门户中的更新体验的更改。 没有对资源提供程序更新的新网格。 更新资源提供程序的功能尚不可用。

<!-- Task ## 3748423  -->
- 对运营商门户中的更新安装体验的更改。 若要帮助 Azure Stack 运算符做出适当的响应更新问题，该门户现在提供更具体的建议基于运行状况的缩放单位中，通过运行自动派生**Test-azurestack**和分析结果。 根据结果，则会通知操作员执行两种操作之一：

  - "Soft"的警告性警报显示在门户中，用于读取"的最新更新的需要注意。 Microsoft 建议在正常营业时间内打开服务请求。 Test-azurestack 是执行，并且基于更新过程的一部分，我们在输出上生成最合适的警报。 在这种情况下，Test-azurestack 已通过。

  - "硬"的严重警报显示在门户中显示为"最新的更新失败。 Microsoft 建议尽可能快地打开服务请求。 Test-azurestack 是执行，并且基于更新过程的一部分，我们在输出上生成最合适的警报。 在这种情况下，Test-azurestack 也失败。"

- 更新的 Azure Linux 代理版本 2.2.38.0。 此项支持可让客户在 Azure 与 Azure Stack 之间保持一致的 Linux 映像。

- 运营商门户中的日志对更新的更改。 请求来检索成功更新日志将不再可用。 失败的更新日志，因为它们是可操作诊断，是仍可供下载。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了在其中 syslog 配置未保留通过更新循环，导致 syslog 客户端会丢失其配置以及要停止正在转发的 syslog 消息。 现在将保留 Syslog 配置。

- 在阻止解除分配的 Vm 的 CRP 中修复了问题。 以前，如果包含多个大型的托管的磁盘 VM，解除分配 VM 可能已失败并出现超时错误。

- 已修复的问题会影响到缩放单位存储访问的 Windows Defender 引擎。

- 修复了用户门户问题 blob 存储帐户的访问策略窗口无法加载。

- 修复了在管理员和用户门户，在其中显示有关全局 Azure 门户中的错误通知。

- 修复了在其中选择一个用户门户问题**反馈**磁贴导致空的浏览器选项卡中打开。

- 修复在更改已绑定到网络的 IP 配置的静态 IP 地址适配器附加到 VM 实例，导致错误消息显示了门户问题。

- 中的尝试修复了用户门户问题**附加网络接口**到现有 VM 通过**网络**窗口导致操作失败并显示错误消息。

- 修复了在其中 Azure Stack 不支持将 4 个以上的网络接口 (Nic) 附加到 VM 实例。

- 修复了门户中的添加入站的安全规则和选择问题**服务标记**作为源，显示不是适用于 Azure Stack 的几个选项。

- 修复了的问题所在网络安全组 (Nsg) 无效 Azure Stack 中全球 Azure 的方式相同。

- 修复了在 Marketplace 管理隐藏所有下载的产品，如果注册过期或被删除。

- 修复了在哪些颁发**集 AzureRmVirtualNetworkGatewayConnection**在 PowerShell 中的现有虚拟网络网关连接到的命令失败，错误消息**无效共享配置密钥...** .

- 修复了导致网络资源提供程序 (NRP) 与网络控制器，从而导致所请求的重复资源同步问题。 在某些情况下，这会导致父资源处于错误状态。

- 修复了在其中用户参与者角色分配到订阅，但未显式提供的读取权限，如果生成了错误的问题的读取 **...客户端somelogonaccount@domain.com使用对象 ID {GUID} 不会执行操作...** 尝试将更改保存到资源时。

- 修复了在其中 marketplace 管理屏幕处于空如果脱机联合工具用来上传的图像，并且其中任何一个文件缺少 URI(s) 的图标。

- 修复了阻止未能从正在删除在 marketplace 管理下载的产品的问题。

### <a name="security-updates"></a>安全更新

Azure Stack 的此更新不包括对基础操作系统承载 Azure Stack 的安全更新。 有关信息，请参阅[Azure Stack 的安全更新。](azure-stack-release-notes-security-updates-1904.md)

## <a name="update-planning"></a>更新规划

更新前，请务必查看以下信息：

- [已知问题](azure-stack-release-notes-known-issues-1904.md)
- [安全更新](azure-stack-release-notes-security-updates-1904.md)
- [活动之前和之后应用更新的清单](azure-stack-release-notes-checklist.md)

> [!NOTE]
> 请确保使用最新版[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner) 工具执行规划和选型工作负荷。 最新版本包含 bug 修复，并提供与每个 Azure Stack 更新一起发布的新功能。

## <a name="download-the-update"></a>下载更新

可以从 Azure Stack 1904 更新包下载[Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 请确保为 1904年更新 Azure Stack 之前为 1903年安装最新的 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1904-update"></a>应用 1904年之前更新

Azure Stack 的 1904年版本必须应用于 1903年发布与以下修补程序：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1903.2.39](https://support.microsoft.com/help/4500638)

### <a name="after-successfully-applying-the-1904-update"></a>已成功应用 1904年后更新

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们[服务策略](azure-stack-servicing-policy.md)。

- [Azure Stack hotfix 1.1904.4.45](https://support.microsoft.com/help/4505688)

## <a name="automatic-update-notifications"></a>自动更新通知

与系统，可从基础结构网络访问 internet 的客户将看到**可用更新**运营商门户中的消息。 如果没有 internet 连接的系统可以下载并导入包含相应的.xml 的.zip 文件。

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
- 填写调查，以提供[发行说明在反馈](https://forms.microsoft.com)。
