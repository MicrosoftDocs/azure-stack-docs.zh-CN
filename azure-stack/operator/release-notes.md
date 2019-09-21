---
title: Azure Stack 发行说明 |Microsoft Docs
description: 了解 Azure Stack 集成系统的更新，包括新功能以及下载更新的位置。
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 896169a39f450b0a0fb26699867638afb02f20a7
ms.sourcegitcommit: 2c37ac8e88de19430080128bac1b70e33557d354
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71164260"
---
# <a name="azure-stack-updates-release-notes"></a>Azure Stack 更新：发行说明

适用范围：*Azure Stack 集成系统*

本文介绍 Azure Stack 更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。

若要访问不同版本的发行说明，请使用左侧目录上方的 "版本选择器" 下拉列表。

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> 如果 Azure Stack 实例超过两个更新，则会将其视为不符合。 必须[至少更新到支持的最低版本](azure-stack-servicing-policy.md#keep-your-system-under-support)。 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908生成引用

Azure Stack 1908 更新内部版本号为**1.1908.0.20**。

### <a name="update-type-1908"></a>更新类型

对于1908，Azure Stack 运行的基础操作系统已更新为 Windows Server 2019。 这可以实现核心基本的增强功能，并能够在不久的将来为 Azure Stack 提供附加功能。

Azure Stack 1908 更新生成类型已**满**。 因此，1908更新的运行时比快速更新（如1906和1907）长。 完整更新的确切运行时间通常取决于 Azure Stack 实例包含的节点数目、租户工作负荷在系统上使用的容量、系统的网络连接（如果已连接到 Internet），以及系统的硬件配置。 在内部测试中，1908更新具有以下所需的运行时：4个节点-42 小时，8个节点-50 小时，12个节点-60 小时，16个节点-70 小时。 持续时间比预期值长的更新运行时并不常见，因此，如果更新失败，则不需要 Azure Stack 运算符执行操作。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack 中管理更新](azure-stack-updates.md)。

- 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件配置。
- 运行时间超过预期并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。
- 此运行时近似值特定于1908更新，不应与其他 Azure Stack 更新进行比较。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1908"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 对于1908，请注意，Azure Stack 运行的基础操作系统已更新为 Windows Server 2019。 这可以实现核心基本的增强功能，并能够在不久的将来为 Azure Stack 提供附加功能。
- Azure Stack 基础结构的所有组件现在在 FIPS 140-2 模式下运行。
- Azure Stack 运算符现在可以删除门户用户数据。 有关详细信息，请参阅[从 Azure Stack 中清除门户用户数据](azure-stack-portal-clear.md)。

### <a name="improvements-1908"></a>措施

<!-- Changes and product improvements with tangible customer-facing value. -->
- 对 Azure Stack 静态加密数据的改进，将机密保存到物理节点的硬件受信任的平台模块（TPM）中。

### <a name="changes-1908"></a>变化

- 硬件提供程序将同时发布 OEM 扩展包2.1 或更高版本，Azure Stack 1908 版。 OEM 扩展包2.1 或更高版本是 Azure Stack 版本1908的必备组件。 有关如何下载 OEM 扩展包2.1 或更高版本的详细信息，请与系统的硬件提供商联系，并参阅[OEM 更新](azure-stack-update-oem.md#oem-contact-information)一文。  

### <a name="fixes-1908"></a>纠正

- 修复了与未来 Azure Stack OEM 更新兼容的问题，以及使用客户用户映像的 VM 部署问题。 此问题已在1907修补程序[KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)中发现和修复。  
- 修复了在 Test-azurestack for Fabric 环形运行状况中，OEM 固件更新和更正后的 misdiagnosis 的问题。 此问题已在1907修补程序[KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)中发现和修复。
- 修复了 OEM 固件更新过程的问题。 此问题已在1907修补程序[KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)中发现和修复。


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates-1908"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](release-notes-security-updates.md)。

## <a name="update-planning-1908"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

## <a name="download-the-update-1908"></a>下载更新

可以从[Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1908 更新包。

## <a name="hotfixes-1908"></a>修复

Azure Stack 定期发布修补程序。 在将 Azure Stack 更新为1908之前，请务必安装最新 Azure Stack 修补程序1907。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1908-update"></a>先决条件：在应用1908更新之前

1908版本的 Azure Stack 必须应用于带有以下修补程序的1907版本：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1907.12.44](https://support.microsoft.com/help/4517473)

Azure Stack 1908 更新需要从系统的硬件提供程序**AZURE STACK OEM 版本2.1 或更高版本**。 OEM 更新包括 Azure Stack 系统硬件的驱动程序和固件更新。 有关应用 OEM 更新的详细信息，请参阅[Apply Azure Stack 原始设备制造商更新](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>成功应用1908更新后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- 没有可用于1908的修补程序。
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907生成引用

Azure Stack 1907 更新内部版本号为 **1.1907.0.20**。

### <a name="update-type-1907"></a>更新类型

Azure Stack 1907 更新内部版本类型为“快速”。 有关更新内部版本类型的详细信息，请参阅[管理 Azure Stack 中的更新](azure-stack-updates.md)一文。 根据内部测试，完成 1907 更新所需的预期时间约为 13 个小时。

- 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件配置。
- 运行时间超过预期并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。
- 此运行时近似值特定于 1907 更新，不应与其他 Azure Stack 更新进行比较。

## <a name="whats-in-this-update-1907"></a>此更新中的内容

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1907"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 正式推出 Azure Stack 诊断日志收集服务，以加速和改善诊断日志的收集。 Azure Stack 诊断日志收集服务提供了一种简单的方法来收集和共享与 Microsoft 客户支持服务（CSS）的诊断日志。 此诊断日志收集服务可在 Azure Stack 管理员门户中提供新的用户体验，让操作员设置在引发特定的关键警报时自动将诊断日志上传到存储 Blob，或按需执行相同的操作。 有关详细信息，请参阅[诊断日志收集](azure-stack-diagnostic-log-collection-overview.md)一文。

- 正式推出 Azure Stack 网络基础结构验证作为 Azure Stack 验证工具 **Test-AzureStack** 的一部分。 Azure Stack 网络基础结构将成为 **Test-AzureStack** 的一部分，可识别 Azure Stack 的网络基础结构是否发生故障。 此测试绕过 Azure Stack 软件定义的网络来检查网络基础结构的连接。 它会演示如何从公共 VIP 连接到配置的 DNS 转发器、NTP 服务器和标识终结点。 此外，在使用 Azure AD 作为标识提供者时，它会检查与 Azure 的连接；使用 ADFS 时，它会检查与联合服务器的连接。 有关详细信息，请参阅 [Azure Stack 验证工具](azure-stack-diagnostic-test.md)一文。

- 添加了内部机密轮换过程，以便在系统更新期间轮换内部 SQL TLS 证书。

### <a name="improvements-1907"></a>措施

<!-- Changes and product improvements with tangible customer-facing value. -->

- “Azure Stack 更新”边栏选项卡现在会显示活动更新的“最后一个步骤已完成”时间。 转到“更新”边栏选项卡，然后单击某个正在运行的更新，即可查看此信息。 “最后一个步骤已完成”随后将显示在“更新运行详细信息”部分。

- 改进了 **Start-AzureStack** 和 **Stop-AzureStack** 操作员操作。 Azure Stack 的启动时间平均已减少 50%。 Azure Stack 的关闭时间平均已减少 30%。 当缩放单元中的节点数目增加时，平均启动和关闭时间保持不变。

- 改进了已断开连接的市场工具的错误处理方式。 如果在使用 **Export-AzSOfflineMarketplaceItem** 时下载失败或部分成功，系统将显示详细的错误消息，其中包含有关错误和缓解步骤（如果有）的更详细信息。

- 改进了从大型页 Blob/快照创建托管磁盘的性能。 以前在创建大型磁盘时会触发超时。  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- 改进了关闭节点之前的虚拟磁盘运行状况检查，以避免发生意外的虚拟磁盘分离。

- 改进了管理员操作内部日志的存储方式。 这可以尽量减少内部日志进程使用的内存和存储，从而改进管理员操作期间的性能和可靠性。 你还可能会注意到，管理员门户中的更新边栏选项卡页加载时间有所改善。 作为此项改进的一部分，系统将不再提供超过 6 个月的更新日志。 如果需要这些更新的日志，请务必先针对超过 6 个月的所有已运行更新[下载摘要](azure-stack-apply-updates.md)，然后执行 1907 更新。

### <a name="changes-1907"></a>变化

- Azure Stack 版本1907包含警告警报，该警报指示操作员在更新到版本1908之前，确保操作员将其系统的 OEM 包更新到2.1 或更高版本。 有关如何应用 Azure Stack OEM 更新的详细信息，请参阅[应用 Azure Stack 原始设备制造商更新](azure-stack-update-oem.md)。

- 已添加新的出站规则 (HTTPS) 来启用 Azure Stack 诊断日志收集服务的通信。 有关详细信息，请参阅 [Azure Stack 数据中心集成 - 发布终结点](azure-stack-integrate-endpoints.md#ports-and-urls-outbound)。

- 现在，如果外部存储位置耗尽了容量，基础结构备份服务将会删除部分上传的备份。

- 基础结构备份不再包含域服务数据的备份。 这仅适用于使用 Azure Active Directory 作为标识提供者的系统。

- 我们现在会验证引入到“计算”->“VM 映像”边栏选项卡中的映像是否为页 Blob 类型。

### <a name="fixes-1907"></a>纠正

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

## <a name="security-updates-1907"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](release-notes-security-updates.md)。

## <a name="update-planning-1907"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

## <a name="download-the-update-1907"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1907 更新包。

## <a name="hotfixes-1907"></a>修复

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1907 之前，请务必先安装 1906 的最新 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1907-update"></a>应用 1907 更新之前

必须在包含以下修补程序的版本 1906 中应用 Azure Stack 版本 1907：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1906.13.56](https://support.microsoft.com/help/4520375)

### <a name="after-successfully-applying-the-1907-update"></a>成功应用 1907 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1907.15.50](https://support.microsoft.com/help/4520315)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>1906生成引用

Azure Stack 1906 更新内部版本号为 **1.1906.0.30**。

### <a name="update-type-1906"></a>更新类型

Azure Stack 1906 更新内部版本类型为“快速”。 有关更新内部版本类型的详细信息，请参阅[管理 Azure Stack 中的更新](azure-stack-updates.md)一文。 无论 Azure Stack 环境中有多少个物理节点，完成 1906 更新的预估时间都大约为 10 小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。 运行时间超过预期值并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。 此运行时近似值特定于 1906 更新，不应与其他 Azure Stack 更新进行比较。

## <a name="whats-in-this-update-1906"></a>此更新中的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- 在特权终结点 (PEP) 中添加 **Set-TLSPolicy** cmdlet，以在所有终结点上强制实施 TLS 1.2。 有关详细信息，请参阅 [Azure Stack 安全控制](azure-stack-security-configuration.md)。

- 在特权终结点 (PEP) 中添加 **Get-TLSPolicy** cmdlet，以检索应用的 TLS 策略。 有关详细信息，请参阅 [Azure Stack 安全控制](azure-stack-security-configuration.md)。

- 添加了内部机密轮换过程，以便在系统更新期间轮换内部 TLS 证书。

- 添加了一项保护措施，以便在机密过期的重大警报遭到忽略时，通过强制实施内部机密轮换来防止内部机密过期。 在日常运营过程中不应依赖此措施。 应在维护时段规划机密轮换。 有关详细信息，请参阅 [Azure Stack 机密轮换](azure-stack-rotate-secrets.md)。

- 使用 AD FS 的 Azure Stack 部署现在支持 Visual Studio Code。

### <a name="improvements-1906"></a>措施

<!-- Changes and product improvements with tangible customer-facing value. -->

- 特权终结点中的 **Get-GraphApplication** cmdlet 现在显示当前使用的证书的指纹。 使用 AD FS 部署 Azure Stack 时，这可以改善服务主体的证书管理。

- 添加了运行状况监视规则来验证 AD Graph 和 AD FS 的可用性，包括引发警报的功能。

- 改善了基础结构备份服务移到另一个实例时的备份资源提供程序可靠性。

- 优化了外部机密轮换过程的性能，提供了统一的执行时间以简化维护时段的计划。

- **Test-AzureStack** cmdlet 现在会报告即将过期的内部机密（关键警报）。

- 特权终结点中的 **Register-CustomAdfs** cmdlet 有新参数可用，可让你在设置 AD FS 联合信任时跳过证书吊销列表的检查。

- 版本 1906 引入了更高的更新进度可见性，让你确保更新不会暂停。 操作员可在“更新”边栏选项卡中看到更多的更新步骤总数。 你还可能会发现，与以前的更新相比，现在有更多的更新步骤同时进行。

#### <a name="networking-updates-1906"></a>网络更新

- 将 DHCP 响应程序中设置的租约时间更新为与 Azure 一致。

- 改善了在发生资源部署失败的情况时资源提供程序的重试率。

- 从负载均衡器和公共 IP 中删除了**标准** SKU 选项，因为目前不支持该选项。

### <a name="changes-1906"></a>变化

- 创建存储帐户的体验现在与 Azure 一致。

- 更改了内部机密过期的警报触发器：
  - 警告警报现在会在机密过期之前的 90 天引发。
  - 关键警报现在会在机密过期之前的 30 天引发。

- 更新了基础结构备份资源提供程序中的字符串以使用一致的术语。

### <a name="fixes-1906"></a>纠正

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了托管磁盘的 VM 大小调整因“内部操作错误”而失败的问题。

- 修复了以下问题：失败的用户映像创建使管理映像的服务处于不正常状态；这会导致无法删除失败的映像，并且无法创建新映像。 此问题也已在 1905 修补程序中得到修复。

- 现在，内部机密即将过期的活动警报在内部机密轮换成功执行之后会自动关闭。

- 修复了以下问题：如果更新运行超过 99 个小时，更新历史记录选项卡中的更新持续时间会去掉第一位数。

- “更新”边栏选项卡包含针对失败更新的“继续”选项。

- 在管理员和用户门户中修复了以下市场问题：Docker 扩展未正确从搜索中返回，并且无法采取进一步的措施，因为 Azure Stack 不提供此类措施。

- 修复了模板部署 UI 中的以下问题：如果模板名称以下划线束“_”开头，则不会填充参数。

- 修复了虚拟机规模集创建体验提供基于 CentOS 的 7.2 作为部署选项的问题。 Azure Stack 不提供 CentOS 7.2。 我们现在提供 Centos 7.5 作为部署选项

- 现在可以从“虚拟机规模集”边栏选项卡中删除规模集。

## <a name="security-updates-1906"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](release-notes-security-updates.md)。

## <a name="update-planning-1906"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

## <a name="download-the-update-1906"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1906 更新包。

## <a name="hotfixes-1906"></a>修复

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1906 之前，请务必先安装 1905 的最新 Azure Stack 修补程序。 更新后，安装[适用于 1906 的任何修补程序](#after-successfully-applying-the-1906-update)。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1906-update"></a>应用 1906 更新之前

必须在包含以下修补程序的版本 1905 中应用 Azure Stack 版本 1906：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>成功应用 1906 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1906.13.56](https://support.microsoft.com/help/4520375)
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-build-reference"></a>1905生成引用

Azure Stack 1905 更新内部版本号为 **1.1905.0.40**。

### <a name="update-type-1905"></a>更新类型

Azure Stack 1905 更新内部版本类型为“完整”。 因此，1905 更新的运行时间比快速更新（例如 1903 和 1904 ）更久。 完整更新的确切运行时间通常取决于 Azure Stack 实例包含的节点数目、租户工作负荷在系统上使用的容量、系统的网络连接（如果已连接到 Internet），以及系统的硬件配置。 在我们的内部测试中，1905 更新的预期运行时间如下：4 个节点 - 35 小时，8 个节点 - 45 小时，12 个节点 - 55 小时，16 个节点 - 70 小时。 1905 的运行时间超过预期值并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。 有关更新内部版本类型的详细信息，请参阅[在 Azure Stack 中管理更新](azure-stack-updates.md)。

## <a name="whats-in-this-update-1905"></a>此更新中的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Azure Stack 中的更新引擎可通过此更新来更新缩放单元节点的固件。 这需要从硬件合作伙伴获取合规的更新包。 请联系硬件合作伙伴了解他们是否提供该包。

- 现在支持 Windows Server 2019，可通过 Azure Stack 市场来与其联合。
Windows Server 2019 现可通过此更新成功地在 2016 主机上启动。

- 使用新的 [Azure 帐户 Visual Studio Code 扩展](../user/azure-stack-dev-start-vscode-azure.md)，开发人员可以通过登录和查看订阅以及许多其他服务来以 Azure Stack 作为目标。 Azure 帐户扩展同时适用于 Azure Active Directory (Azure AD) 和 AD FS 环境，并且只需要对 Visual Studio Code 用户设置进行少量更改。 Visual Studio Code 需要向服务主体授予权限才能在此环境中运行。 为此，请导入标识脚本并运行 [Azure Stack 中的多租户](../operator/azure-stack-enable-multitenancy.md)中指定的 cmdlet。 这需要更新主目录，并为每个目录注册来宾租户目录。 更新到 1905 或更高版本后，若要更新包含 Visual Studio Code 服务主体的主目录租户，将显示警报。 

### <a name="improvements-1905"></a>措施

<!-- Changes and product improvements with tangible customer-facing value. -->
- 在 Azure Stack 上强制实施 TLS 1.2 的过程中，以下扩展已更新到这些版本：

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  请立即下载这些扩展版本，以便在将来的版本中强制实施 TLS 1.2 时，能够成功完成新的扩展部署。 请始终设置 **autoUpgradeMinorVersion=true**，以自动执行扩展的次要版本更新（例如，1.8 更新为 1.9）。

- Azure Stack 门户中有新的“帮助和支持概述”，可让操作员轻松检查其支持选项、获取专家帮助，以及详细了解 Azure Stack。 在集成系统上，创建支持请求会预先选择 Azure Stack 服务。 我们强烈建议客户使用此体验来提交票证，而不要使用全局 Azure 门户。 有关详细信息，请参阅 [Azure Stack 帮助和支持](azure-stack-help-and-support-overview.md)。

- 加入了多个 Azure Active Directory （通过[此过程](azure-stack-enable-multitenancy.md)）时，可能会在进行某些更新时或对 Azure AD 服务主体授权的更改导致权限丢失时，忽略重新运行脚本的操作。 这可能会导致各种问题，例如无法访问某些功能，或者更为分立、难以追溯到原始问题的失败。 为了防止此问题，1905 导入了新的功能用于检查这些权限并在发现某些配置问题时创建警报。 此验证每小时运行一次，并显示为了修复问题所要采取的补救措施。 所有租户处于正常状态后，警报将会关闭。

- 提高了服务故障转移期间基础结构备份操作的可靠性。

- 提供了新版 [Azure Stack Nagios 插件](azure-stack-integrate-monitor.md#integrate-with-nagios)，该插件使用 [Azure Active Directory 身份验证库](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) 进行身份验证。 此插件还支持 Azure Stack Azure AD 和 Active Directory 联合身份验证服务（AD FS）部署。 有关详细信息，请参阅 [Nagios 插件交换](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)网站。

- 已发布新的混合配置文件 **2019-03-01-Hybrid**，可支持 Azure Stack 中的所有最新功能。 Azure PowerShell 和 Azure CLI 都支持 **2019-03-01-Hybrid** 配置文件。 .NET、Ruby、Node.js、Go 和 Python SDK 已发布可支持 **2019-03-01-Hybrid** 配置文件的包。 其各自的文档和一些示例也已更新以反映更改。

- [Node.js SDK](https://www.npmjs.com/search?q=2019-03-01-hybrid) 现在支持 API 配置文件。 支持 **2019-03-01-Hybrid** 配置文件的包已发布。

- 1905 Azure Stack 更新添加了两个新的基础结构角色，可改善平台的可靠性与支持能力：

  - **基础结构环**：将来，基础结构环将托管现有基础结构角色（例如 xrp）的容器化版本，这些角色目前需要自身的指定基础结构 VM。 这会改善平台的可靠性，并减少 Azure Stack 所需的基础结构 VM 数目。 因而这可以减少 Azure Stack 基础结构角色将来的总体资源消耗量。
  - **支持环**：将来，支持环将用于处理客户的增强支持方案。  

  此外，我们添加了额外的域控制器 VM 实例来改善此角色的可用性。

  这些更改在以下方面会增加 Azure Stack 基础结构的资源消耗量：
  
    | Azure Stack SKU | 增加计算消耗量 | 增加内存消耗量 |
    | -- | -- | -- |
    |4 个节点|22 个 vCPU|28 GB|
    |8 个节点|38 个 vCPU|44 GB|
    |12 个节点|54 个 vCPU|60 GB|
    |16 个节点|70 个 vCPU|76 GB|
  
### <a name="changes-1905"></a>变化

- 为了在计划内和计划外维护方案期间提高可靠性和可用性，Azure Stack 为域服务添加了额外的基础结构角色实例。

- 通过此项更新，系统可在修复和添加节点操作期间验证硬件，以确保在缩放单元中使用同构的缩放单元节点。

- 如果计划的备份无法完成且超过定义的保留期，基础结构备份控制器将确保至少保留一个成功的备份。 

### <a name="fixes-1905"></a>纠正

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 已修复以下问题：在重启缩放单元中的节点之后，出现“计算主机代理”警告。

- 已修复管理员门户中的市场管理问题，这些问题使得系统在应用了筛选条件时显示不正确的结果，以及在发布者筛选器中显示重复的发布者名称。 此外提高了性能，可以更快显示结果。

- 已修复可用备份边栏选项卡中的问题：系统在完成对外部存储位置的上传操作之前列出新的可用备份。 现在，只有在成功上传到存储位置后，可用备份才会显示在列表中。 

<!-- ICM: 114819337; Task: 4408136 -->
- 已修复备份操作期间的修复密钥检索问题。 

<!-- Bug: 4525587 -->
- 已修复 OEM 更新在操作员门户中将版本显示为“未定义”的问题。

### <a name="security-updates-1905"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](release-notes-security-updates.md)。

## <a name="update-planning-1905"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

## <a name="download-the-update-1905"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1905 更新包。 使用下载程序工具时，请务必使用最新版本，并且不是下载目录中的缓存副本。

## <a name="hotfixes-1905"></a>修复

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1905 之前，请务必先安装 1904 的最新 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1905-update"></a>应用 1905 更新之前

必须在包含以下修补程序的版本 1904 中应用 Azure Stack 版本 1905：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>成功应用 1905 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1905.3.48](https://support.microsoft.com/help/4510078)
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。 无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>存档

若要访问较早版本的存档发行说明，请使用左侧目录上方的 "版本选择器" 下拉列表，然后选择要查看的版本。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904存档的发行说明
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903存档的发行说明
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902存档的发行说明
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901存档的发行说明
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811存档的发行说明
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809存档的发行说明
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808存档的发行说明
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807存档的发行说明
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805存档的发行说明
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804存档的发行说明
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803存档的发行说明
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802存档的发行说明
::: moniker-end

::: moniker range="<azs-1905"
你可以[在 TechNet 库上访问 Azure Stack 发行说明的旧版本](https://aka.ms/azsarchivedrelnotes)。 这些存档文档仅供参考目的提供，并不表示支持这些版本。 有关 Azure Stack 支持的信息, 请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。 若要获得更多帮助，请与 Microsoft 客户支持服务联系。
::: moniker-end


