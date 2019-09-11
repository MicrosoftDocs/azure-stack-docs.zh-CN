---
title: Azure Stack 1908 发行说明 |Microsoft Docs
description: 了解 Azure Stack 集成系统的1908更新，包括新功能、已知问题和更新下载位置。
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
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
monikerRange: azs-1908
ms.openlocfilehash: 935a422aee68bb107e5bfe8b79a21d22ce1fffc8
ms.sourcegitcommit: 305536bfd49319455ca3ca270fe3644b1796bad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876615"
---
# <a name="azure-stack-1908-update"></a>Azure Stack 1908 更新

适用范围：*Azure Stack 集成系统*

本文介绍1908更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="previous-release-notes"></a>以前的发行说明

从1908版开始，早期版本的发行说明在左侧的目录中将不再可见。 若要访问旧版本的发行说明，请选择另一篇文章（例如[Azure Stack 概述](azure-stack-overview.md)），然后从左侧目录顶部的版本选择器中选择1905、1906、1907或1908。 对于早期版本的发行说明，请参阅[存档的发行说明](#archived-release-notes)部分。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1908 更新内部版本号为**1.1908.0.20**。

### <a name="update-type"></a>更新类型

对于1908，Azure Stack 运行的基础操作系统已更新为 Windows Server 2019。 这可以实现核心基本的增强功能，并能够在不久的将来为 Azure Stack 提供附加功能。

Azure Stack 1908 更新生成类型已**满**。 因此，1908更新的运行时比快速更新（如1906和1907）长。 完整更新的确切运行时间通常取决于 Azure Stack 实例包含的节点数目、租户工作负荷在系统上使用的容量、系统的网络连接（如果已连接到 Internet），以及系统的硬件配置。 在内部测试中，1908更新具有以下所需的运行时：4个节点-42 小时，8个节点-50 小时，12个节点-60 小时，16个节点-70 小时。 持续时间比预期值长的更新运行时并不常见，因此，如果更新失败，则不需要 Azure Stack 运算符执行操作。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack 中管理更新](azure-stack-updates.md)。

- 确切的更新运行时通常取决于您的系统上使用的容量、租户工作负荷、系统网络连接（如果已连接到 internet）以及系统硬件配置。
- 持续时间比预期长的运行时并不常见，因此，如果更新失败，则不需要 Azure Stack 运算符执行操作。
- 此运行时近似值特定于1908更新，不应与其他 Azure Stack 更新进行比较。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 对于1908，请注意，Azure Stack 运行的基础操作系统已更新为 Windows Server 2019。 这可以实现核心基本的增强功能，并能够在不久的将来为 Azure Stack 提供附加功能。
- Azure Stack 基础结构的所有组件现在在 FIPS 140-2 模式下运行。
- Azure Stack 运算符现在可以删除门户用户数据。 有关详细信息，请参阅[从 Azure Stack 中清除门户用户数据](azure-stack-portal-clear.md)。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->
- 对 Azure Stack 静态加密数据的改进，将机密保存到物理节点的硬件受信任的平台模块（TPM）中。

### <a name="changes"></a>更改

- 硬件提供程序将同时发布 OEM 扩展包2.1 或更高版本，Azure Stack 1908 版。 OEM 扩展包2.1 或更高版本是 Azure Stack 版本1908的必备组件。 有关如何下载 OEM 扩展包2.1 或更高版本的详细信息，请与系统的硬件提供商联系，并参阅[OEM 更新](azure-stack-update-oem.md#oem-contact-information)一文。  

### <a name="fixes"></a>修复项

- 修复了与未来 Azure Stack OEM 更新兼容的问题，以及使用客户用户映像的 VM 部署问题。 此问题已在1907修补程序[KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)中发现和修复。  
- 修复了在 Test-azurestack for Fabric 环形运行状况中，OEM 固件更新和更正后的 misdiagnosis 的问题。 此问题已在1907修补程序[KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)中发现和修复。
- 修复了 OEM 固件更新过程的问题。 此问题已在1907修补程序[KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)中发现和修复。


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](azure-stack-release-notes-security-updates.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](azure-stack-release-notes-known-issues-1908.md)
- [安全更新](azure-stack-release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从[Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1908 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 在将 Azure Stack 更新为1908之前，请务必安装最新 Azure Stack 修补程序1907。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1908-update"></a>先决条件：在应用1908更新之前

1908版本的 Azure Stack 必须应用于带有以下修补程序的1907版本：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1907.12.44](https://support.microsoft.com/help/4517473)

Azure Stack 1908 更新需要从系统的硬件提供程序**AZURE STACK OEM 版本2.1 或更高版本**。 OEM 更新包括 Azure Stack 系统硬件的驱动程序和固件更新。 有关应用 OEM 更新的详细信息，请参阅[Apply Azure Stack 原始设备制造商更新](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>成功应用1908更新后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- 没有可用于1908的修补程序。

## <a name="automatic-update-notifications"></a>自动更新通知

可从基础结构网络访问 internet 的系统将在操作员门户中看到 "**更新可用**" 消息。 无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

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
