---
title: Azure Stack 1906 发行说明 | Microsoft Docs
description: 了解 Azure Stack 集成系统的 1906 更新，包括新增功能、已知问题和更新下载位置。
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
ms.date: 09/05/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/15/2019
monikerRange: azs-1906
ms.openlocfilehash: ae447df40d29b92f06f345ba54b77ff883e6ff07
ms.sourcegitcommit: e2aec63cacfdc830a20a02ee40e715e3c5dfdf22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386217"
---
# <a name="azure-stack-1906-update"></a>Azure Stack 1906 更新

适用范围：*Azure Stack 集成系统*

本文介绍 1906 更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1906 更新内部版本号为 **1.1906.0.30**。

### <a name="update-type"></a>更新类型

Azure Stack 1906 更新内部版本类型为“快速”。 有关更新内部版本类型的详细信息，请参阅[管理 Azure Stack 中的更新](azure-stack-updates.md)一文。 无论 Azure Stack 环境中有多少个物理节点，完成 1906 更新的预估时间都大约为 10 小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。 运行时间超过预期值并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。 此运行时近似值特定于 1906 更新，不应与其他 Azure Stack 更新进行比较。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- 在特权终结点 (PEP) 中添加 **Set-TLSPolicy** cmdlet，以在所有终结点上强制实施 TLS 1.2。 有关详细信息，请参阅 [Azure Stack 安全控制](azure-stack-security-configuration.md)。

- 在特权终结点 (PEP) 中添加 **Get-TLSPolicy** cmdlet，以检索应用的 TLS 策略。 有关详细信息，请参阅 [Azure Stack 安全控制](azure-stack-security-configuration.md)。

- 添加了内部机密轮换过程，以便在系统更新期间轮换内部 TLS 证书。

- 添加了一项保护措施，以便在机密过期的重大警报遭到忽略时，通过强制实施内部机密轮换来防止内部机密过期。 在日常运营过程中不应依赖此措施。 应在维护时段规划机密轮换。 有关详细信息，请参阅 [Azure Stack 机密轮换](azure-stack-rotate-secrets.md)。

- 使用 AD FS 的 Azure Stack 部署现在支持 Visual Studio Code。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 特权终结点中的 **Get-GraphApplication** cmdlet 现在显示当前使用的证书的指纹。 使用 AD FS 部署 Azure Stack 时，这可以改善服务主体的证书管理。

- 添加了运行状况监视规则来验证 AD Graph 和 AD FS 的可用性，包括引发警报的功能。

- 改善了基础结构备份服务移到另一个实例时的备份资源提供程序可靠性。

- 优化了外部机密轮换过程的性能，提供了统一的执行时间以简化维护时段的计划。

- **Test-AzureStack** cmdlet 现在会报告即将过期的内部机密（关键警报）。

- 特权终结点中的 **Register-CustomAdfs** cmdlet 有新参数可用，可让你在设置 AD FS 联合信任时跳过证书吊销列表的检查。

- 版本 1906 引入了更高的更新进度可见性，让你确保更新不会暂停。 操作员可在“更新”边栏选项卡中看到更多的更新步骤总数。 你还可能会发现，与以前的更新相比，现在有更多的更新步骤同时进行。

#### <a name="networking-updates"></a>网络更新

- 将 DHCP 响应程序中设置的租约时间更新为与 Azure 一致。

- 改善了在发生资源部署失败的情况时资源提供程序的重试率。

- 从负载均衡器和公共 IP 中删除了**标准** SKU 选项，因为目前不支持该选项。

### <a name="changes"></a>更改

- 创建存储帐户的体验现在与 Azure 一致。

- 更改了内部机密过期的警报触发器：
  - 警告警报现在会在机密过期之前的 90 天引发。
  - 关键警报现在会在机密过期之前的 30 天引发。

- 更新了基础结构备份资源提供程序中的字符串以使用一致的术语。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了托管磁盘的 VM 大小调整因“内部操作错误”而失败的问题。

- 修复了以下问题：失败的用户映像创建使管理映像的服务处于不正常状态；这会导致无法删除失败的映像，并且无法创建新映像。 此问题也已在 1905 修补程序中得到修复。

- 现在，内部机密即将过期的活动警报在内部机密轮换成功执行之后会自动关闭。

- 修复了以下问题：如果更新运行超过 99 个小时，更新历史记录选项卡中的更新持续时间会去掉第一位数。

- “更新”边栏选项卡包含针对失败更新的“继续”选项。

- 在管理员和用户门户中修复了以下市场问题：Docker 扩展未正确从搜索中返回，并且无法采取进一步的措施，因为 Azure Stack 不提供此类措施。

- 修复了模板部署 UI 中的以下问题：如果模板名称以下划线束“_”开头，则不会填充参数。

- 修复了虚拟机规模集创建体验提供基于 CentOS 的7.2 作为部署选项的问题。 Azure Stack 不提供 CentOS 7.2。 我们现在提供 Centos 7.5 作为部署选项

- 你现在可以从 "**虚拟机规模集**" 边栏选项卡中删除规模集。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](azure-stack-release-notes-security-updates.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](azure-stack-release-notes-known-issues-1906.md)
- [安全更新](azure-stack-release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1906 更新包。

## <a name="hotfixes"></a>修补程序

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

## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。 无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>已存档的发行说明

可查看 [TechNet 库中 Azure Stack 发行说明的早期版本](https://aka.ms/azsarchivedrelnotes)。 提供这些已存档的发行说明仅供参考，并不意味着支持这些版本。 有关 Azure Stack 支持的信息, 请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。 若要获得更多帮助，请与 Microsoft 客户支持服务联系。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
