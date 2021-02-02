---
title: Azure Stack 1908 发行说明 | Microsoft Docs
description: 了解 Azure Stack 1908 集成系统的更新
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
ms.openlocfilehash: 6716b31431106e0af43785177376e4980b864f81
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248023"
---
# <a name="azure-stack-updates-1908-release-notes"></a>Azure Stack 更新：1908发行说明

*适用于：Azure Stack 集成系统*

本文介绍 Azure Stack 更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。

若要访问不同版本的发行说明，请使用左侧目录上方的版本选择器下拉列表。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

> [!IMPORTANT]  
> 如果 Azure Stack 实例落后于两个以上的更新，则认为它不符合。 必须[至少更新到最低支持版本才能获得支持](../azure-stack-servicing-policy.md#keep-your-system-under-support)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues-1908.md)
- [安全更新](../release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](../release-notes-checklist.md)

有关对更新和更新过程进行故障排除的帮助，请参阅[对 Azure Stack 的修补和更新问题进行故障排除](../azure-stack-updates-troubleshoot.md)。

## <a name="1908-build-reference"></a>1908 内部版本参考

Azure Stack 1908 更新内部版本号为 **1.1908.4.33**。

### <a name="update-type"></a>更新类型

对于 1908，运行 Azure Stack 的底层操作系统已更新为 Windows Server 2019。 这可以实现核心基础增强，并在不久的将来将更多功能引入 Azure Stack。

Azure Stack 1908 更新内部版本类型为“完整”。 因此，1908 更新的运行时间比快速更新（例如 1906 和 1907）更久。 完整更新的确切运行时间通常取决于 Azure Stack 实例包含的节点数目、租户工作负荷在系统上使用的容量、系统的网络连接（如果已连接到 Internet），以及系统的硬件配置。 在我们的内部测试中，1908 更新的预期运行时间如下：4 个节点 - 42 小时，8 个节点 - 50 小时，12 个节点 - 60 小时，16 个节点 - 70 小时。 更新运行时间超过预期值并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack 中管理更新](../azure-stack-updates.md)。

- 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件配置。
- 运行时间超过预期并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。
- 此运行时近似值特定于 1908 更新，不应与其他 Azure Stack 更新进行比较。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 对于 1908，请注意，运行 Azure Stack 的底层操作系统已更新为 Windows Server 2019。 这可以实现核心基础增强，并在不久的将来将更多功能引入 Azure Stack。
- Azure Stack 基础结构的所有组件现在都以 FIPS 140-2 模式运行。
- Azure Stack 操作员现在可以删除门户用户数据。 有关详细信息，请参阅[从 Azure Stack 中清除门户用户数据](../azure-stack-portal-clear.md)。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->
- Azure Stack 的静态数据加密已得到改进，可将机密持久保存到物理节点的硬件受信任平台模块 (TPM)。

### <a name="changes"></a>更改

- 硬件提供商将在 Azure Stack 版本 1908 的同一发布时间发布 OEM 扩展包 2.1 或更高版本。 必须安装 OEM 扩展包 2.1 或更高版本才能使用 Azure Stack 版本 1908。 有关如何下载 OEM 扩展包 2.1 或更高版本的详细信息，请与系统的硬件提供商联系，并参阅 [OEM 更新](../azure-stack-update-oem.md#oem-contact-information)一文。  

### <a name="fixes"></a>修复项

- 修复了与将来的 Azure Stack OEM 更新兼容的问题，以及使用客户用户映像进行 VM 部署的问题。 此问题是在 1907 中发现的，已在修补程序 [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44) 中予以修复  
- 修复了 OEM 固件更新的问题，并更正了 Fabric Ring Health 的 Test-AzureStack 中的诊断错误。 此问题是在 1907 中发现的，已在修补程序 [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35) 中予以修复
- 修复了 OEM 固件更新过程的问题。 此问题是在 1907 中发现的，已在修补程序 [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37) 中予以修复

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](../release-notes-security-updates.md)。

## <a name="download-the-update"></a><a name="download-the-update-1908"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1908 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1908 之前，请务必先安装 1907 的最新 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1908-update"></a>先决条件：应用 1908 更新之前

必须在包含以下修补程序的版本 1907 中应用 Azure Stack 版本 1908：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1907.17.54](https://support.microsoft.com/help/4523826)

Azure Stack 1908 更新需要系统硬件提供商提供的 **Azure Stack OEM 2.1 或更高版本**。 OEM 更新包括 Azure Stack 系统硬件的驱动程序和固件更新。 有关应用 OEM 更新的详细信息，请参阅[应用 Azure Stack 原始设备制造商更新](../azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>成功应用 1908 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](../azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1908.6.37](https://support.microsoft.com/help/4527372)
