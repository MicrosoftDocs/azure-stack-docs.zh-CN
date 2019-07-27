---
title: Azure Stack 1905 发行说明 |Microsoft Docs
description: 了解 Azure Stack 集成系统的1905更新, 包括新功能、已知问题和更新下载位置。
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
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 68b83ac38da20f341242618a1cfa62a9838a0ada
ms.sourcegitcommit: c2690b2dd36918ff3e47e359cac926128bb83101
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68544084"
---
# <a name="azure-stack-1905-update"></a>Azure Stack 1905 更新

适用对象：*Azure Stack 集成系统*

本文介绍1905更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。 本文包含以下信息：

- [新增功能、增强功能、修复和安全更新的说明](#whats-in-this-update)
- [更新规划](#update-planning)

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1905 更新内部版本号为**1.1905.0.40**。

### <a name="update-type"></a>更新类型

Azure Stack 1905 更新生成类型已**满**。 因此, 1905 更新的运行时比快速更新 (如1903和 1904) 长。 完全更新的确切运行时通常取决于 Azure Stack 实例包含的节点数、租户工作负荷在系统上使用的容量、系统的网络连接 (如果已连接到 internet) 和系统硬件configuration. 在内部测试中, 1905 更新具有以下所需的运行时:4个节点-35 小时, 8 个节点-45 小时, 12 个节点-55 小时, 16 个节点-70 小时。 1905运行时持续时间比预期值长的情况并不常见, 因此, 如果更新失败, 则不需要 Azure Stack 运算符执行操作。 有关更新生成类型的详细信息, 请参阅[管理 Azure Stack 中的更新](azure-stack-updates.md)。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- 通过此更新, Azure Stack 中的更新引擎可以更新缩放单元节点的固件。 这需要硬件伙伴提供兼容的更新包。 有关可用性的详细信息, 请联系你的硬件合作伙伴。

- Windows Server 2019 现在受支持, 并可通过 Azure Stack Marketplace 进行供稿。
使用此更新, 现在可以在2016主机上成功激活 Windows Server 2019。

- 使用新的[Azure 帐户 Visual Studio Code 扩展](../user/azure-stack-dev-start-vscode-azure.md), 开发人员可以通过登录和查看订阅以及许多其他服务来以 Azure Stack 为目标。 Azure 帐户扩展适用于 Azure Active Directory (Azure AD) 和 AD FS 环境, 只需要在 Visual Studio Code 用户设置中进行少量更改。 Visual Studio Code 要求为服务主体提供权限以便在此环境中运行。 为此, 请导入标识脚本并运行 Azure Stack 中的[多租户](../operator/azure-stack-enable-multitenancy.md)中指定的 cmdlet。 这需要更新主目录, 并为每个目录注册来宾租户目录。 更新到1905或更高版本后, 将显示一个警报, 以更新包含 Visual Studio Code 服务主体的主目录租户。 

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->
- 在 Azure Stack 上强制执行 TLS 1.2, 以下扩展已更新为以下版本:

  - microsoft.customscriptextension-arm-1.9.3
  - iaasdiagnostics-1.12.2。2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  请立即下载这些版本的扩展, 以便在将来的版本中强制实施 TLS 1.2 时, 扩展的新部署不会失败。 始终将**autoUpgradeMinorVersion**设置为 true, 以便自动执行对扩展的次要版本更新 (例如, 1.8 到 1.9)。

- Azure Stack 门户中的新**帮助和支持概述**使操作员能够更轻松地查看其支持选项、获取专家帮助并了解有关 Azure Stack 的详细信息。 在集成系统上, 创建支持请求将预先 Azure Stack 服务。 我们强烈建议客户使用此体验来提交票证, 而不是使用全局 Azure 门户。 有关详细信息, 请参阅[Azure Stack 帮助和支持](azure-stack-help-and-support-overview.md)。

- 如果有多个 Azure Active Directory 载入 (通过[此过程](azure-stack-enable-multitenancy.md)), 则在发生某些更新或对 Azure AD 服务主体授权的更改导致权限丢失时, 可能会忽略脚本的重新运行。 这可能会导致各种问题 (从阻止的访问某些功能) 到更不连续的故障, 从而难以追溯到原始问题。 为了防止出现这种情况, 1905 引入了一项新功能, 该功能可检查这些权限, 并在发现某些配置问题时创建警报。 每隔一小时运行一次此验证, 并显示修复问题所需的修正操作。 所有租户都处于正常状态后, 警报将关闭。

- 提高了服务故障转移过程中的基础结构备份操作的可靠性。

- 提供了一个新版本的[Azure Stack Nagios 插件](azure-stack-integrate-monitor.md#integrate-with-nagios), 该插件使用[Azure Active Directory 身份验证库](/azure/active-directory/develop/active-directory-authentication-libraries)(ADAL) 进行身份验证。 此插件还支持 Azure Stack Azure AD 和 Active Directory 联合身份验证服务 (AD FS) 部署。 有关详细信息, 请参阅[Nagios 插件 exchange](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)站点。

- 新的混合配置文件**2019-03-01-** 已发布支持 Azure Stack 中所有最新功能的混合。 Azure PowerShell 和 Azure CLI 都支持**2019-03-01 混合**配置文件。 .NET、Ruby、node.js、中转和 Python Sdk 都发布了支持**2019-03-01 混合**配置文件的包。 各自的文档和一些示例已更新, 以反映所做的更改。

- [NODE.JS SDK](https://www.npmjs.com/search?q=2019-03-01-hybrid)现在支持 API 配置文件。 将发布支持**2019-03-01 混合**配置文件的包。

- 1905 Azure Stack 更新添加了两个新的基础结构角色, 以改善平台的可靠性和可支持性:

  - **基础结构循环**:将来, 基础结构环将托管现有基础结构角色的容器化版本 (例如 xrp), 当前需要其自己的指定基础结构 Vm。 这将提高平台的可靠性, 减少 Azure Stack 需要的基础结构 Vm 的数量。 这样一来, 以后就可以减少 Azure Stack 基础结构角色的总体资源消耗。
  - **支持环**:将来, 支持环将用于处理客户的增强支持方案。  

  此外, 我们还添加了一个额外的域控制器 VM 实例, 以提高此角色的可用性。

  这些更改将通过以下方式增加 Azure Stack 基础结构的资源消耗:
  
    | Azure Stack SKU | 计算使用量增加 | 内存使用量增加 |
    | -- | -- | -- |
    |4节点|22 vCPU|28 GB|
    |8个节点|38 vCPU|44 GB|
    |12个节点|54 vCPU|60 GB|
    |16个节点|70 vCPU|76 GB|
  
### <a name="changes"></a>更改

- 为提高计划内和计划外维护方案的可靠性和可用性, Azure Stack 为域服务添加了一个额外的基础结构角色实例。

- 在此更新中, 在修复和添加节点操作期间, 会对硬件进行验证, 以确保在一个缩放单位内存在相同的缩放单位节点。

- 如果计划的备份没有完成并且超过了定义的保留期, 则基础结构备份控制器将确保至少保留一次成功备份。 

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了在缩放单元中重启节点后出现**计算主机代理**警告的问题。

- 修复了在应用筛选器时显示错误结果的管理门户中的 marketplace 管理问题, 并在发布服务器筛选器中显示了重复的发布服务器名称。 同时提高了性能, 从而更快显示结果。

- 修复了 "可用备份" 边栏选项卡中列出了新的可用备份, 并在其完成上传到外部存储位置之前的问题。 现在, 可用备份会在成功上传到存储位置后显示在列表中。 

<!-- ICM: 114819337; Task: 4408136 -->
- 修复了在备份操作期间检索恢复密钥的问题。 

<!-- Bug: 4525587 -->
- 修复了在操作员门户中将版本显示为 "未定义" 的 OEM 更新问题。

### <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息, 请参阅[Azure Stack 安全更新](azure-stack-release-notes-security-updates-1905.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](azure-stack-release-notes-known-issues-1905.md)
- [安全更新](azure-stack-release-notes-security-updates-1905.md)
- [应用更新之前和之后的活动清单](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从[Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1905 更新包。 使用下载程序工具时, 请确保使用最新版本, 而不是下载目录中的缓存副本。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 在将 Azure Stack 更新为1905之前, 请务必安装最新 Azure Stack 修补程序1904。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1905-update"></a>在应用1905更新之前

1905版本的 Azure Stack 必须应用于带有以下修补程序的1904版本:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>成功应用1905更新后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息, 请参阅[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1905.3.48](https://support.microsoft.com/help/4510078)

## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。 无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>已存档的发行说明

可[在 TechNet 库中查看 Azure Stack 发行说明的早期版本](http://aka.ms/azsarchivedrelnotes)。 这些存档的发行说明仅供参考目的提供, 并不意味着对这些版本的支持。 有关 Azure Stack 支持的信息, 请参阅[Azure Stack 服务策略](azure-stack-servicing-policy.md)。 若要获得更多帮助, 请与 Microsoft 客户支持服务联系。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  

