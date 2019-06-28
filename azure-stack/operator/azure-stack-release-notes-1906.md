---
title: Azure Stack 1906 发行说明 |Microsoft Docs
description: 了解有关 Azure Stack 集成系统，包括最新内容、 1906年更新的已知的问题，以及下载更新的位置。
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
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: db1611557d07ec31ec53edbb5e8356b520256be5
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419864"
---
# <a name="azure-stack-1906-update"></a>Azure Stack 1906 更新

*适用于：Azure Stack 集成系统*

本文介绍 1906年更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1906 更新内部版本号是**1.1906.0.30**。

### <a name="update-type"></a>更新类型

Azure Stack 1906 更新生成类型是**Express**。 有关更新的详细信息生成类型，请参阅[管理 Azure Stack 中的更新](azure-stack-updates.md)一文。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- 添加**集 TLSPolicy**特权终结点 (PEP) 来强制所有终结点上的 TLS 1.2 中的 cmdlet。 有关详细信息，请参阅[Azure Stack 的安全控制](azure-stack-security-configuration.md)。

- 添加**Get TLSPolicy**特权终结点 (PEP) 来检索应用的 TLS 策略中的 cmdlet。 有关详细信息，请参阅[Azure Stack 的安全控制](azure-stack-security-configuration.md)。

- 添加内部机密轮换过程在系统更新过程轮换内部所需的 TLS 证书。

- 添加了一种安全措施以防止通过忽略上即将到期的机密的严重警报的情况下强制内部机密轮换内部机密到期。 这应不依赖于作为常规操作过程。 应在维护时段内规划机密轮换。 有关详细信息，请参阅[Azure Stack 机密轮换](azure-stack-rotate-secrets.md)。

- 使用 Azure Stack 部署中使用 AD FS 现在支持 visual Studio Code。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- **Get GraphApplication**特权终结点中的 cmdlet 现在将显示当前所用证书的指纹。 使用 AD FS 部署 Azure Stack 时，这提高了服务主体的证书管理。

- 已添加新的运行状况监视规则来验证 AD Graph 和 AD FS 中，包括发出警报的功能的可用性。

- 备份资源提供程序基础结构备份服务将移到另一个实例时的可靠性改进。

- 外部机密轮换过程才能提供统一的执行时间，以方便进行计划的维护时段的性能优化。

- **Test-azurestack** cmdlet 现在会报告上即将过期 （关键警报） 的内部机密。

- 一个新的参数是可用于**寄存器 CustomAdfs** cmdlet 中，正在跳过证书吊销列表检查配置 AD FS 的联合信任时的特权终结点。

#### <a name="networking-updates"></a>网络更新

- 已更新的租用时间设置中 DHCP 响应程序为与 Azure 一致。

- 改进了重试在方案中的资源的部署失败的资源提供程序的速率。

- 删除**标准**SKU 选项从负载均衡器和公共 IP，因为当前不支持。

### <a name="changes"></a>更改

- 创建存储帐户体验现已与 Azure 一致。
  - 机密到期之前 90 天现在引发警告警报。
  - 现在引发关键警报的机密到期前 30 天。

- 更新基础结构备份资源提供程序的一致术语中的字符串。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了在其中调整托管的磁盘 VM 的大小失败，出现**内部操作错误**。

- 修复了在其中管理映像的服务处于错误的状态; 失败的用户映像创建 put这将阻止删除失败的映像和创建新映像。 这也被固定的 1905年修补程序。

- 在内部机密轮换成功执行后立即自动关闭上即将过期的内部机密的活动警报。

- 修复了在其中更新历史记录选项卡中的更新持续时间会剪裁的第一位如果更新已运行超过 99 小时。

- 修复了在**更新**边栏选项卡： 现在包括**恢复**失败的更新的选项。

- 中的管理员和用户无法使门户，在 marketplace 中的 Docker 扩展未正确从返回的搜索，但不是执行进一步的操作中修复的问题，因为它不是 Azure Stack 中提供。

- 模板部署不会填充参数，如果模板名称以下划线 _ 开头的用户界面中修复了问题。

## <a name="security-updates"></a>安全更新

Azure Stack 的此更新中的安全更新的信息，请参阅[Azure Stack 的安全更新](azure-stack-release-notes-security-updates-1906.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](azure-stack-release-notes-known-issues-1906.md)
- [安全更新](azure-stack-release-notes-security-updates-1906.md)
- [应用更新之前和之后的活动清单](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从 Azure Stack 1906 更新包下载[Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 请确保为 1905年到 1906年更新 Azure Stack 之前安装的最新的 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1906-update"></a>应用 1906年之前更新

Azure Stack 的 1906年版本必须应用 1905年版本具有以下修补程序：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>已成功应用 1906年后更新

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- 没有可用于 1906年的修补程序。

## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。  无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>已存档的发行说明

您可以看到[较旧版本的 Azure Stack 发行 TechNet 库中的说明](https://aka.ms/azsarchivedrelnotes)。 这些存档的发行说明提供仅供参考，并不表示对这些版本的支持。 有关 Azure Stack 支持的信息，请参阅[Azure Stack 服务策略](azure-stack-servicing-policy.md)。 进一步的帮助，请联系 Microsoft 客户支持服务。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
