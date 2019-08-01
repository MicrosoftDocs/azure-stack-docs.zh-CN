---
title: Azure Stack 1906 发行说明 |Microsoft Docs
description: 了解 Azure Stack 集成系统的1906更新, 包括新功能、已知问题和更新下载位置。
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
ms.date: 07/15/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/15/2019
ms.openlocfilehash: ffd6436b9e7bf27ef069ecdc44ff2d6efadfc795
ms.sourcegitcommit: d96adbb821175167f6a4c8f3aba305981d7e7c3e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68685562"
---
# <a name="azure-stack-1906-update"></a>Azure Stack 1906 更新

适用对象：*Azure Stack 集成系统*

本文介绍1906更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1906 更新内部版本号为**1.1906.0.30**。

### <a name="update-type"></a>更新类型

Azure Stack 1906 更新生成类型为**Express**。 有关更新生成类型的详细信息, 请参阅[Azure Stack 文章中的管理更新](azure-stack-updates.md)。 完成1906更新所需的预期时间大约为10小时, 而不考虑 Azure Stack 环境中的物理节点数。 确切的更新运行时将依赖于您的系统上使用的容量、租户工作负荷、系统网络连接 (如果已连接到 internet) 以及系统硬件规范。 持续时间比预期值长的运行时并不常见, 因此, 如果更新失败, 则不需要 Azure Stack 运算符执行操作。 此运行时近似值特定于1906更新, 不应与其他 Azure Stack 更新进行比较。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- 在特权终结点 (PEP) 中添加了**TLSPolicy** cmdlet, 以强制所有终结点上的 TLS 1.2。 有关详细信息, 请参阅[Azure Stack 安全控件](azure-stack-security-configuration.md)。

- 在特权终结点 (PEP) 中添加了**TLSPolicy** cmdlet, 以检索应用的 TLS 策略。 有关详细信息, 请参阅[Azure Stack 安全控件](azure-stack-security-configuration.md)。

- 添加了内部机密旋转过程, 以便在系统更新过程中根据需要轮替内部 TLS 证书。

- 添加了一项安全措施, 通过强制实施内部机密循环来防止内部机密过期, 以防对过期机密发出关键警报。 这不应作为常规操作过程进行依赖。 在维护时段内应计划机密旋转。 有关详细信息, 请参阅[Azure Stack 秘密旋转](azure-stack-rotate-secrets.md)。

- Visual Studio Code 现在支持使用 AD FS Azure Stack 部署。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 特权终结点中的**GraphApplication** cmdlet 现在显示当前使用的证书的指纹。 这改善了在 AD FS 中部署 Azure Stack 时服务主体的证书管理。

- 添加了新的运行状况监视规则, 用于验证 AD Graph 和 AD FS 的可用性, 包括能够引发警报。

- 当基础结构备份服务移动到另一个实例时, 对备份资源提供程序的可靠性进行了改进。

- 对外部机密旋转过程进行性能优化, 以便为维护时段的计划提供一致的执行时间。

- **Test-azurestack** cmdlet 现在报告即将过期的内部机密 (关键警报)。

- 新参数适用于特权终结点中的**CustomAdfs** cmdlet, 该 cmdlet 可在配置 AD FS 的联合身份验证信任时跳过证书吊销列表检查。

- 1906版本引入了更新进度的更好的可见性, 因此您可以确保更新不会暂停。 这会导致 "**更新**" 边栏选项卡中对操作员显示的更新步骤总数增加。 你还可能会注意到, 在以前的更新中并行发生了更多的更新步骤。

#### <a name="networking-updates"></a>网络更新

- 已更新 DHCP 响应程序中设置的与 Azure 一致的租约时间。

- 在资源部署失败的情况下, 改进了资源提供程序的重试率。

- 从负载均衡器和公共 IP 中删除了**标准**SKU 选项, 因为当前不支持此选项。

### <a name="changes"></a>更改

- 现在, 创建存储帐户体验与 Azure 一致。

- 更改了内部机密过期警报触发器:
  - 在密码过期之前90天, 现在会发出警告性警报。
  - 关键警报现在会在密码过期前30天引发。

- 更新了基础结构备份资源提供程序中的字符串, 以获得一致的术语。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了调整托管磁盘 VM 的大小时失败, 出现**内部操作错误**。

- 修复了发生故障的用户映像创建导致管理映像的服务处于错误状态的问题;这会阻止删除失败映像并创建新映像。 这在1905修补程序中也是固定的。

- 在成功执行内部机密旋转后, 会自动关闭对过期内部机密发出的活动警报。

- 修复了一个问题: 如果更新的运行时间超过99小时, "更新历史记录" 选项卡中的更新持续时间将修整第一个数字。

- **更新**边栏选项卡包含失败更新的**恢复**选项。

- 在管理员和用户门户中, 修复了 marketplace 中从搜索中错误地返回 Docker 扩展的问题, 但无法采取进一步的操作, 因为它在 Azure Stack 中不可用。

- 修复了模板部署 UI 中未填充参数的问题 (如果模板名称以 "_" 下划线开头)。

- 修复了虚拟机规模集创建体验提供基于 CentOS 的7.2 作为部署选项的问题。 Azure Stack 不提供 CentOS 7.2。 我们现在提供 Centos 7.5 作为部署选项

- 你现在可以从 "**虚拟机规模集**" 边栏选项卡中删除规模集。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息, 请参阅[Azure Stack 安全更新](azure-stack-release-notes-security-updates-1906.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](azure-stack-release-notes-known-issues-1906.md)
- [安全更新](azure-stack-release-notes-security-updates-1906.md)
- [应用更新之前和之后的活动清单](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从[Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1906 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 在将 Azure Stack 更新为1906之前, 请务必安装最新 Azure Stack 修补程序1905。 更新后, 安装任何[适用于1906的修补程序](#after-successfully-applying-the-1906-update)。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1906-update"></a>在应用1906更新之前

1906版本的 Azure Stack 必须应用于带有以下修补程序的1905版本:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>成功应用1906更新后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息, 请参阅[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1906.11.52](https://support.microsoft.com/help/4513119)

## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。  无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

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
