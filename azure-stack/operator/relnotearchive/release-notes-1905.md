---
title: Azure Stack 1905 发行说明 | Microsoft Docs
description: 了解 Azure Stack 集成系统的1905更新
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
ROBOTS: NOINDEX
ms.openlocfilehash: 0f26f27cea7fabaaec9c6fd1507294f9bcbe3372
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248028"
---
# <a name="azure-stack-1905-update"></a>Azure Stack 1905 更新

*适用于：Azure Stack 集成系统*

本文介绍 1905 更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。 本文介绍了以下信息：

- [新增功能、增强功能、修复和安全更新的说明](#whats-in-this-update)
- [更新规划](#update-planning)

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1905 更新内部版本号为 **1.1905.0.40**。

### <a name="update-type"></a>更新类型

Azure Stack 1905 更新内部版本类型为“完整”。 因此，1905 更新的运行时间比快速更新（例如 1903 和 1904 ）更久。 完整更新的确切运行时间通常取决于 Azure Stack 实例包含的节点数目、租户工作负荷在系统上使用的容量、系统的网络连接（如果已连接到 Internet），以及系统的硬件配置。 1905更新具有以下所需的内部测试运行时：4个节点-35 小时、8个节点-45 小时、12个节点-55 小时、16个节点-70 小时。 1905 的运行时间超过预期值并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。 有关更新内部版本类型的详细信息，请参阅[在 Azure Stack 中管理更新](../azure-stack-updates.md)。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Azure Stack 中的更新引擎可通过此更新来更新缩放单元节点的固件。 这需要从硬件合作伙伴获取合规的更新包。 请联系硬件合作伙伴了解他们是否提供该包。

- 现在支持 Windows Server 2019，可通过 Azure Stack 市场来与其联合。
Windows Server 2019 现可通过此更新成功地在 2016 主机上启动。

- 使用新的 [Azure 帐户 Visual Studio Code 扩展](../../user/azure-stack-dev-start-vscode-azure.md)，开发人员可以通过登录和查看订阅以及许多其他服务来以 Azure Stack 作为目标。 Azure 帐户扩展同时适用于 Azure Active Directory (Azure AD) 和 AD FS 环境，并且只需要对 Visual Studio Code 用户设置进行少量更改。 Visual Studio Code 需要向服务主体授予权限才能在此环境中运行。 为此，请导入标识脚本并运行 [Azure Stack 中的多租户](../azure-stack-enable-multitenancy.md)中指定的 cmdlet。 这需要更新主目录，并为每个目录注册来宾租户目录。 更新到 1905 或更高版本后，若要更新包含 Visual Studio Code 服务主体的主目录租户，将显示警报。 

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->
- 在 Azure Stack 上强制实施 TLS 1.2 的过程中，以下扩展已更新到这些版本：

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  请立即下载这些扩展版本，以便在将来的版本中强制实施 TLS 1.2 时，能够成功完成新的扩展部署。 请始终设置 **autoUpgradeMinorVersion=true**，以自动执行扩展的次要版本更新（例如，1.8 更新为 1.9）。

- Azure Stack 门户中有新的“帮助和支持概述”，可让操作员轻松检查其支持选项、获取专家帮助，以及详细了解 Azure Stack。 在集成系统上，创建支持请求会预先选择 Azure Stack 服务。 我们强烈建议客户使用此体验来提交票证，而不要使用全局 Azure 门户。 有关详细信息，请参阅 [Azure Stack 帮助和支持](../azure-stack-help-and-support-overview.md)。

- 加入了多个 Azure Active Directory （通过[此过程](../azure-stack-enable-multitenancy.md)）时，可能会在进行某些更新时或对 Azure AD 服务主体授权的更改导致权限丢失时，忽略重新运行脚本的操作。 这可能会导致各种问题，例如无法访问某些功能，或者更为分立、难以追溯到原始问题的失败。 为了防止此问题，1905 导入了新的功能用于检查这些权限并在发现某些配置问题时创建警报。 此验证每小时运行一次，并显示为了修复问题所要采取的补救措施。 所有租户处于正常状态后，警报将会关闭。

- 提高了服务故障转移期间基础结构备份操作的可靠性。

- 提供了新版 [Azure Stack Nagios 插件](../azure-stack-integrate-monitor.md#integrate-with-nagios)，该插件使用 [Azure Active Directory 身份验证库](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) 进行身份验证。 该插件现在还支持 Azure Stack 的 Azure AD 和 Active Directory 联合身份验证服务 (AD FS) 部署。 有关详细信息，请参阅 [Nagios 插件交换](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)网站。

- 已发布新的混合配置文件 **2019-03-01-Hybrid**，可支持 Azure Stack 中的所有最新功能。 Azure PowerShell 和 Azure CLI 都支持 **2019-03-01-Hybrid** 配置文件。 .NET、Ruby、Node.js、Go 和 Python SDK 已发布可支持 **2019-03-01-Hybrid** 配置文件的包。 其各自的文档和一些示例也已更新以反映更改。

- [Node.js SDK](https://www.npmjs.com/search?q=2019-03-01-hybrid) 现在支持 API 配置文件。 支持 **2019-03-01-Hybrid** 配置文件的包已发布。

- 1905 Azure Stack 更新添加了两个新的基础结构角色，可改善平台的可靠性与支持能力：

  - **基础结构循环**：未来，基础结构环将托管现有基础结构角色的容器化版本（例如 xrp），当前需要其自己的指定基础结构 vm。 这会改善平台的可靠性，并减少 Azure Stack 所需的基础结构 VM 数目。 因而这可以减少 Azure Stack 基础结构角色将来的总体资源消耗量。
  - **支持环**：将来，支持环将用于处理客户的增强支持方案。  

  此外，我们添加了额外的域控制器 VM 实例来改善此角色的可用性。

  这些更改在以下方面会增加 Azure Stack 基础结构的资源消耗量：
  
    | Azure Stack SKU | 增加计算消耗量 | 增加内存消耗量 |
    | -- | -- | -- |
    |4 个节点|22 个 vCPU|28 GB|
    |8 个节点|38 个 vCPU|44 GB|
    |12 个节点|54 个 vCPU|60 GB|
    |16 个节点|70 个 vCPU|76 GB|
  
### <a name="changes"></a>更改

- 为了在计划内和计划外维护方案期间提高可靠性和可用性，Azure Stack 为域服务添加了额外的基础结构角色实例。

- 通过此项更新，系统可在修复和添加节点操作期间验证硬件，以确保在缩放单元中使用同构的缩放单元节点。

- 如果计划的备份无法完成且超过定义的保留期，基础结构备份控制器将确保至少保留一个成功的备份。 

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 已修复以下问题：在重启缩放单元中的节点之后，出现“计算主机代理”警告。

- 已修复管理员门户中的市场管理问题，这些问题使得系统在应用了筛选条件时显示不正确的结果，以及在发布者筛选器中显示重复的发布者名称。 此外提高了性能，可以更快显示结果。

- 已修复可用备份边栏选项卡中的问题：系统在完成对外部存储位置的上传操作之前列出新的可用备份。 现在，只有在成功上传到存储位置后，可用备份才会显示在列表中。 

<!-- ICM: 114819337; Task: 4408136 -->
- 已修复备份操作期间的修复密钥检索问题。 

<!-- Bug: 4525587 -->
- 已修复 OEM 更新在操作员门户中将版本显示为“未定义”的问题。

### <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](../release-notes-security-updates.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues-1905.md)
- [安全更新](../release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](../release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1905 更新包。 使用下载程序工具时，请务必使用最新版本，并且不是下载目录中的缓存副本。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1905 之前，请务必先安装 1904 的最新 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1905-update"></a>应用 1905 更新之前

必须在包含以下修补程序的版本 1904 中应用 Azure Stack 版本 1905：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>成功应用 1905 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](../azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1905.3.48](https://support.microsoft.com/help/4510078)

## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。 无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](../azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](../azure-stack-monitor-update.md)。  

