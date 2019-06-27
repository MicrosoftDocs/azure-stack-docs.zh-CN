---
title: Azure Stack 1905 发行说明 |Microsoft Docs
description: 了解有关 Azure Stack 集成系统，包括最新内容、 1905年更新的已知的问题，以及下载更新的位置。
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
ms.openlocfilehash: 6f178d0208f5111a7ae60d23e1d914fcf8e3aba5
ms.sourcegitcommit: c9d11be7d27c73797bdf279d4fcabb7a22451541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2019
ms.locfileid: "67397272"
---
# <a name="azure-stack-1905-update"></a>Azure Stack 1905 更新

*适用于：Azure Stack 集成系统*

本文介绍 1905年更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。 本文包含以下信息：

- [新增功能、增强功能、修复和安全更新的说明](#whats-in-this-update)
- [更新规划](#update-planning)

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1905 更新内部版本号是**1.1905.0.40**。

### <a name="update-type"></a>更新类型

Azure Stack 1905 更新生成类型是**完整**。 因此，1905年更新具有更长的运行时版本低于 1903年和 1904年等快速更新。 执行完全更新的确切运行时通常取决于节点数，将 Azure Stack 实例包含，租户工作负荷，系统的网络连接 （如果连接到 internet），你的系统上使用的容量和系统硬件配置。 1905 更新已在我们的内部测试以下预期的运行时：4 个节点的 35 小时、 8 个节点-45 小时、 12 个节点-55 小时、 16 个节点的 70 多个小时。 1905 运行时持续时间比预期这些值并不少见，不需要执行操作由 Azure Stack 操作员除非更新失败。 有关更新的详细信息生成类型，请参阅[管理 Azure Stack 中的更新](azure-stack-updates.md)。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- 利用此更新，在 Azure Stack 中的更新引擎可以更新固件的缩放单位节点。 这要求从硬件合作伙伴的合规性更新包。 联系你的硬件合作伙伴可用性有关的详细信息。

- Windows Server 2019 现已通过 Azure Stack Marketplace 支持的和可用于聚合。
利用此更新，Windows Server 2019 可以现在已成功激活 2016年主机上。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->
- 为强制实施 Azure Stack 上的 TLS 1.2 的一部分，对这些版本已更新以下扩展：

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  请下载这些版本的扩展立即，以便在将来的版本中强制实施 TLS 1.2，扩展的新部署不会失败。 始终设置**autoUpgradeMinorVersion = true** ，以便自动执行次要版本更新到扩展 (例如，1.8 到 1.9)。

- 一个新**帮助和支持概述**中 Azure Stack 门户轻松地运算符以查看其支持选项、 获得专家帮助和了解有关 Azure Stack 的详细信息。 在集成系统上创建支持请求将预先选择 Azure Stack 服务。 我们强烈建议客户使用这种体验来提交票证，而不是无需使用全局 Azure 门户。 有关详细信息，请参阅[Azure Stack 帮助和支持](azure-stack-help-and-support-overview.md)。

- 当多个 Azure Active 目录会加入 (通过[此过程](azure-stack-enable-multitenancy.md))，则可以忽略了某些更新发生时，或对 AAD 服务主体授权的更改而导致发生权限为时重新运行脚本缺少。 这会导致各种问题，从被阻止访问某些功能，到多个离散故障很难进行跟踪回原来的问题。 若要防止此情况，1905年引入了一项新功能，这些权限检查时发现某些配置问题时创建警报。 此验证运行每隔一小时，并显示解决该问题所需的修正操作。 所有租户都都处于正常状态后，将关闭警报。

- 改进的基础结构服务在故障转移期间的备份操作的可靠性。

- 新版[Azure Stack Nagios 插件](azure-stack-integrate-monitor.md#integrate-with-nagios)是使用可用[Azure Active Directory 身份验证库](/azure/active-directory/develop/active-directory-authentication-libraries)(ADAL) 进行身份验证。 该插件现在还支持 Azure Stack 的 Azure Active Directory (AAD) 和 Active Directory 联合身份验证服务 (ADFS) 部署。 有关详细信息，请参阅[Nagios 插件 exchange](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)站点。

- 新的混合配置文件**2019年-03-01-混合**发布，在 Azure Stack 中支持所有最新功能。 Azure PowerShell 和 Azure CLI 支持**2019年-03-01-混合**配置文件。 .NET、 Ruby、 Node.js、 Go 和 Python Sdk 已发布的支持包**2019年-03-01-混合**配置文件。 已更新相应的文档和一些示例，以反映所做的更改。

- [Node.js SDK](https://www.npmjs.com/search?q=2019-03-01-hybrid)现在支持 API 配置文件。 包的支持**2019年-03-01-混合**发布配置文件。

- 1905 Azure Stack 更新时添加了两个新的基础结构角色，以改进平台可靠性和可支持性：

  - **基础结构环**:将来，基础结构环将托管现有基础结构角色 – 例如，xrp-目前都需要自己指定的基础结构 Vm 的容器化的的版本。 这将提高平台可靠性并降低基础结构 Vm 的 Azure Stack 需要数。 随后，这减少了总体资源消耗的 Azure Stack 基础结构角色在将来。
  - **支持环**:在将来支持环将用于处理为客户增强的支持方案。  

  此外，我们添加了额外的域控制器 VM 的更佳的可用性，此角色的实例。

  这些更改将按以下方式增加 Azure Stack 基础结构的资源消耗：
  
    | Azure Stack SKU | 增加计算消耗 | 增加内存消耗 |
    | -- | -- | -- |
    |4 个节点|22 个 vCPU|28 GB|
    |8 个节点|38 个 vCPU|44 GB|
    |12 个节点|54 vCPU|60 GB|
    |16 个节点|70 个 vCPU|76 GB|
  
- 现在是适用于 Visual Studio Code 的 Azure Stack 扩展。 使用**Azure 帐户**扩展，开发人员可以通过 Azure Stack，通过中的日志记录并查看订阅，以及许多其他服务。 Azure 帐户扩展适用于 AAD 环境以及 ADFS 环境，并只需稍微进行更改的用户设置中的 Visual Studio Code 进行输入 Azure Stack 元数据值。 有关详细信息[请参阅此处的文档](../user/azure-stack-dev-start-vscode-azure.md)。

### <a name="changes"></a>更改

- 若要在计划内和计划外维护方案过程中提高可靠性和可用性，Azure Stack，请添加域服务的其他基础结构角色实例。

- 与此更新，请在修复过程并添加节点操作硬件进行验证以确保同构的缩放单位节点缩放单位内的。

- 如果计划的备份无法完成，超出定义的保留期，基础结构备份控制器将确保至少一个成功的备份保留。 

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了在其中**计算主机代理**后重新启动缩放单位中的某个节点出现警告。

- 在 marketplace 管理在管理员门户中显示了不正确的结果，如果将筛选器已应用，并且发布者筛选器中显示重复的发布者名称的修复的问题。 此外做了性能改进，更快显示结果。

- 中的可用备份边栏选项卡列出新的可用备份之前已完成上的传到外部存储位置的已修复的问题。 现在可用的备份后将显示在列表中已成功上载到的存储位置。 

<!-- ICM: 114819337; Task: 4408136 -->
- 在备份操作过程中检索恢复密钥已修复的问题。 

<!-- Bug: 4525587 -->
- 已修复的问题 OEM 更新为未定义运营商门户中显示的版本。

### <a name="security-updates"></a>安全更新

Azure Stack 的此更新中的安全更新的信息，请参阅[Azure Stack 的安全更新](azure-stack-release-notes-security-updates-1905.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](azure-stack-release-notes-known-issues-1905.md)
- [安全更新](azure-stack-release-notes-security-updates-1905.md)
- [应用更新之前和之后的活动清单](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从 Azure Stack 1905 更新包下载[Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)。 使用程序下载程序工具时，请务必使用最新版本并不是从下载目录的缓存的副本。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 请确保为 1904年到 1905年更新 Azure Stack 之前安装的最新的 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1905-update"></a>应用 1905年之前更新

Azure Stack 的 1905年版本必须应用于 1904年发布与以下修补程序：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hotfix 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>已成功应用 1905年后更新

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。  无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>已存档的发行说明

您可以看到[较旧版本的 Azure Stack 发行 TechNet 库中的说明](http://aka.ms/azsarchivedrelnotes)。 这些存档的发行说明提供仅供参考，并不表示对这些版本的支持。 有关 Azure Stack 支持的信息，请参阅[Azure Stack 服务策略](azure-stack-servicing-policy.md)。 进一步的帮助，请联系 Microsoft 客户支持服务。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  

