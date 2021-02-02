---
title: Azure Stack 1809 更新 | Microsoft Docs
description: 了解 Azure Stack 集成系统 1809 更新的新增功能，包括已知问题和更新下载位置。
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
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 02/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5829e1b1b02636eeb1632f3a8f2fc3bcd2b38699
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247956"
---
# <a name="azure-stack-1809-update"></a>Azure Stack 1809 更新

*适用于：Azure Stack 集成系统*

本文介绍 1809 更新包的内容。 此更新包包含此版 Azure Stack 的改进、修复和已知问题。 本文还包含一个用于下载更新的链接。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1809 更新内部版本号为 **1.1809.0.90**。  

### <a name="new-features"></a>新增功能

此更新包含对 Azure Stack 的以下改进：

- 在此版本中，Azure Stack 集成系统支持 4-16 个节点的配置。 可以使用 [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) 来帮助规划 Azure Stack 容量与配置。

- <!--  2712869   | IS  ASDK -->  **Azure Stack syslog 客户端（正式版）** 此客户端允许将与 Azure Stack 基础结构相关的审核、警报和安全日志转发到 Azure Stack 外部的 syslog 服务器或安全信息与事件管理 (SIEM) 软件。 syslog 客户端现在支持指定 syslog 服务器侦听的端口。

   此版本意味着 syslog 客户端已正式发布，可以用于生产环境。

   有关详细信息，请参阅 [Azure Stack Syslog 转发](../azure-stack-integrate-security.md)。

- 现在可以在 Azure 上的资源组之间[移动注册资源](../azure-stack-registration.md#move-a-registration-resource)，而无需重新注册。 只要新旧订阅都映射到相同的 CSP 合作伙伴 ID，云解决方案商 (CSP) 也可以在订阅之间移动注册资源。 这不影响现有的客户租户映射。 

- 增加了为每个网络接口分配多个 IP 地址的支持。  如需更多详细信息，请参阅[使用 PowerShell 将多个 IP 地址分配到虚拟机](https://docs.microsoft.com/azure/virtual-network/virtual-network-multiple-ip-addresses-powershell)。

### <a name="fixed-issues"></a>已修复的问题

<!-- TBD - IS ASDK -->
- 在门户中，报告免费/已用容量的内存图表现在是准确的。 可以更可靠地预测可创建的 VM 数量。

<!-- TBD - IS ASDK --> 
- 修复了以下问题：在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- 以下托管磁盘问题已在 1809 中修复，同时已在 1808 [Azure Stack 修补程序 1.1808.9.117](https://support.microsoft.com/help/4481066/) 中修复： 

   <!--  2966665 IS, ASDK --> 
  - 解决了将 SSD 数据磁盘附加到高级大小托管磁盘虚拟机 (DS、DSv2、Fs、Fs_V2) 失败，并出现错误：  *未能更新虚拟机的磁盘 Vmname 错误：无法执行请求的操作，因为 VM 大小不支持存储帐户类型 Premium_LRS Standard_DS/Ds_V2/fs/Fs_v2)*。 
   
  - 使用 **createOption** 创建托管磁盘 VM： **附加** 失败，出现以下错误： *长时间运行操作失败，状态为 "失败"。其他信息： "发生内部执行错误"。*
    ErrorCode： InternalExecutionError ErrorMessage：发生了内部执行错误。
   
    现已修复此问题。

- <!-- 2702741 -  IS, ASDK --> 修复了在发出“停止-解除分配”命令后，无法保证系统会保留使用动态分配方法部署的公共 IP 的问题。 它们现在已保留。

- <!-- 3078022 - IS, ASDK --> 如果 VM 在 1808 之前已停止-解除分配，则该 VM 在 1808 更新之后无法重新分配。  此问题已在 1809 中解决。 处于这种状态且无法启动的实例可以在已应用此修复的 1809 中启动。 此修复还可以防止该问题反复发生。

### <a name="changes"></a>更改

<!-- 2635202 - IS, ASDK -->
- 基础结构备份服务已从[公共基础结构网络](https://docs.microsoft.com/azure/azure-stack/azure-stack-network)转移到[公共 VIP 网络](https://docs.microsoft.com/azure/azure-stack/azure-stack-network#public-vip-network)。 客户需确保该服务有权访问公共 VIP 网络中的备份存储位置。  

> [!IMPORTANT]  
> 如果防火墙不允许从公共 VIP 网络连接到文件服务器，此项更改会导致基础结构备份因“错误 53: 找不到网络路径”而失败。 这是一项中断性更改，没有合理的解决方法。 根据客户反馈，Microsoft 将在修补程序中还原此更改。 请查看[更新后的步骤部分](#post-update-steps)，以获取有关 1809 可用的修补程序的详细信息。 发布修补程序后，请确保只在网络策略不允许公共 VIP 网络访问基础结构资源的情况下，才在更新到 1809 之后应用该修补程序。 在 1811 中，此项更改将应用到所有系统。 如果在 1809 中应用该修补程序，则无需采取进一步的措施。  

### <a name="common-vulnerabilities-and-exposures"></a>常见漏洞和风险

此更新安装以下安全更新：  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

有关这些漏洞的详细信息，请单击上述链接，或者查看 Microsoft 知识库文章 [4457131](https://support.microsoft.com/help/4457131) 和 [4462917](https://support.microsoft.com/help/4462917)。

### <a name="prerequisites"></a>必备条件

- 在应用 1809 之前，请安装 1808 的最新 Azure Stack 修补程序。 有关详细信息，请参阅 [KB 4481066-Azure Stack 修补程序 Azure Stack 修补程序 1.1808.9.117](https://support.microsoft.com/help/4481066/)。 虽然 Microsoft 建议使用最新的修补程序，但安装1809所需的最低版本为1.1808.5.110。

  > [!TIP]  
  > 订阅以下 *rr* 或 *Atom* 馈送以跟上 Azure Stack 修补程序：
  > - RR https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss 
  > - 动 https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom


- 在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](../azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看活动警报，并解决所有需要采取措施的警报。  

  ```powershell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```

- 如果 Azure Stack 由 System Center Operations Manager (SCOM) 进行管理，请确保在应用1809之前将 Microsoft Azure Stack 的管理包更新为版本1.0.3.11。

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 在 1809 更新后运行 [Test-AzureStack](../azure-stack-diagnostic-test.md) 时，会显示基板管理控制器 (BMC) 中的一条警告消息。 可以放心地忽略此警告。

- <!-- 2468613 - IS --> 在此更新的安装过程中，你可能会看到 *FaultType UserAccounts* 的标题为 "错误" 的警报。  可以放心地忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

- <!-- 2489559 - IS --> 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md#plan-for-updates)。

- <!-- 3139614 | IS --> 如果已从 OEM 应用 Azure Stack 更新，则“有可用的更新”通知可能不会显示在 Azure Stack 管理员门户中。 若要安装 Microsoft 更新，请遵照[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)中的说明手动下载并导入该更新。

### <a name="post-update-steps"></a>更新后步骤

> [!Important]  
> 让 Azure Stack 部署准备好使用后续更新包启用的扩展主机。 遵照[为 Azure Stack 准备扩展主机](../azure-stack-extension-host-prepare.md)中的指导准备系统。

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请查看以下知识库文章，以及我们的[服务策略](../azure-stack-servicing-policy.md)。  
- [KB 4481548-Azure Stack 修补程序 Azure Stack 修补程序1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

- Azure Stack 技术文档重点介绍了最新版本。 由于版本之间的门户更改，你在使用 Azure Stack 门户时看到的内容可能与在文档中看到的内容不同。

<!-- 2930718 - IS ASDK --> 
-  在管理员门户中访问用户订阅详细信息时，在关闭边栏选项卡并单击“最新”以后，用户订阅名称不显示。

<!-- 3060156 - IS ASDK --> 
-  在管理员门户和用户门户中，单击门户设置并选择“删除所有设置和专用仪表板”的操作不正常。 此时会显示错误通知。 

<!-- 2930799 - IS ASDK --> 
-  在管理员门户和用户门户中的“所有服务”下，资产“DDoS 防护计划”未正确列出。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示错误，指出门户无法创建此市场项。 

<!-- 2930820 - IS ASDK --> 
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。 

<!-- 2967387 - IS, ASDK --> 
-  登录 Azure Stack 管理员门户或用户门户时使用的帐户显示为“未标识的用户”。 如果帐户未指定“名字”或“姓氏”，则会显示此消息。 若要解决此问题，请编辑用户帐户，提供名或姓。 然后必须注销，再重新登录门户。  

<!--  2873083 - IS ASDK --> 
-  使用门户创建 (VMSS) 的虚拟机规模集时，当你使用 Internet Explorer 时， *实例大小* 下拉列表将不会正确加载。 若要解决此问题，请在通过门户创建 VMSS 时使用其他浏览器。  

<!-- 2931230 - IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!--2760466 - IS  ASDK --> 
- 安装运行此版本的新 Azure Stack 环境时，指示“需要激活”的警报可能不显示。 必须先[激活](../azure-stack-registration.md)，然后才能使用市场联合。  

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 订阅类型为 **计数订阅** 和 **消耗订阅**。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- TBD - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- TBD - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。


### <a name="health-and-monitoring"></a>运行状况和监视

<!-- TBD - IS -->
- 你可能会看到以下警报在 Azure Stack 系统上重复出现，然后消失：
   - *基础结构角色实例不可用*
   - *缩放单元节点已脱机*
   
  运行 [Test-AzureStack](../azure-stack-diagnostic-test.md) cmdlet 来验证基础结构角色实例和缩放单元节点的运行状况。 如果 [Test-AzureStack](../azure-stack-diagnostic-test.md) 未检测到问题，则可以忽略这些警报。 如果检测到问题，则可以尝试使用管理门户或 PowerShell 启动基础结构角色实例或节点。

  此问题已在最新的 [1809 修补程序版本](https://support.microsoft.com/help/4481548/)中修复，因此如果遇到此问题，请务必安装此修补程序。 

<!-- 1264761 - IS ASDK -->  
- 可能会看到包含以下详细信息的“运行状况控制器”组件的警报：  

   警报 #1：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  警报 #2：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器故障扫描仪不可用。 这可能会影响运行状况报告和指标。

  可以放心忽略这两条警报，它们在一段时间后会自动关闭。  


<!-- 2812138 | IS --> 
- 可能会看到具有以下详细信息的 **存储** 组件警报：

   - 名称: 存储服务内部通信错误  
   - 严重性：严重  
   - 组件: 存储  
   - 说明: 将请求发送到以下节点时发生存储服务内部通信错误。  

    可以放心地忽略此警报，但需手动关闭它。

<!-- 2368581 - IS, ASDK --> 
- 如果 Azure Stack 操作员收到内存不足的警报，并且租户虚拟机无法部署并出现“Fabric VM 创建错误”，则可能表示 Azure Stack 模组的可用内存不足。 请使用 [Azure Stack 容量规划工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)来充分了解可供工作负荷使用的容量。

### <a name="compute"></a>计算

- 创建 [Dv2 系列 VM](../../user/azure-stack-vm-considerations.md#vm-sizes) 时，可以通过 D11-14v2 VM 分别创建 4 个、8 个、16 个和 32 个数据磁盘。 不过，“创建 VM”窗格会显示 8 个、16 个、32 个和 64 个数据磁盘。

<!-- 3235634 - IS, ASDK -->
- 若要部署大小包含 **v2** 后缀的 VM（例如 **Standard_A2_v2**），请将后缀指定为 **Standard_A2_v2**（小写 v）。 请勿使用 **Standard_A2_V2**（大写 V）。 这适用于全球 Azure，在 Azure Stack 上有不一致的问题。

<!-- 3099544 - IS, ASDK --> 
- 使用 Azure Stack 门户创建新的虚拟机 (VM) 并选择 VM 大小时，“美元/月”列在显示时会出现“不可用”消息。 此列不应显示；Azure Stack 不支持显示 VM 定价列。

<!-- 2869209 - IS, ASDK --> 
- 使用 [**Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0&preserve-view=true) 时，必须使用 **-OsUri** 参数作为存储帐户 URI（在其中上传磁盘）。 如果使用磁盘的本地路径，则 cmdlet 会失败，并出现以下错误： *长时间运行操作失败，状态为 "失败*"。 

<!--  2795678 - IS, ASDK --> 
- 通过门户创建“高级”VM 大小（DS、Ds_v2、FS、FSv2）的虚拟机 (VM) 时，该 VM 在标准存储帐户中创建。 在标准存储帐户中创建不影响功能、IOPS 或计费。 

   你可以放心地忽略该警告，指出： *你选择使用的磁盘大小支持高级磁盘。这可能会影响操作系统的性能，不建议这样做。请考虑改用高级存储 (SSD) 。*

<!-- 2967447 - IS, ASDK --> 
- 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一 OS，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。  

<!-- 2724873 - IS --> 
- 使用 PowerShell cmdlet **Start-AzsScaleUnitNode** 或 **Stop-AzsScaleunitNode** 管理缩放单元时，首次尝试启动或停止缩放单元可能会失败。 如果 cmdlet 在第一次运行时失败，请再次运行 cmdlet。 第二次运行应该能够成功地完成操作。 

<!-- TBD - IS ASDK --> 
- 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

<!-- 1662991 IS ASDK --> 
- Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  

<!-- 2724961- IS ASDK --> 
- 在订阅设置中注册 **Microsoft.Insight** 资源提供程序并创建支持来宾 OS 诊断的 Windows VM 时，VM 概述页中的“CPU 百分比”图表不显示指标数据。

   若要查找指标数据（例如 VM 的“CPU 百分比”图表），请转到“指标”窗口并查看所有受支持的 Windows VM 来宾指标。

<!-- 3507629 - IS, ASDK --> 
- 托管磁盘创建了两个新的[计算配额类型](../azure-stack-quota-types.md#compute-quota-types)来限制可以预配的托管磁盘的最大容量。 默认情况下将为每个托管磁盘配额类型分配 2048 GiB。 不过，你可能会遇到以下问题：

   - 对于在 1808 更新之前创建的配额，托管磁盘配额在管理门户中将显示为值 0，虽然分配了 2048 GiB。 你可以根据实际需求增大或减小该值，新设置的配额值将替代 2048 GiB 默认值。
   - 如果将配额值更新为 0，则它等效于默认值 2048 GiB。 作为一种解决方法，请将配额值设置为 1。

<!-- TBD - IS ASDK --> 
- 应用 1809 更新后，在部署带托管磁盘的 VM 时可能会遇到以下问题：

  - 如果订阅是在 1808 更新之前创建的，则部署具有托管磁盘的 VM 可能会失败并出现内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
     1. 在租户门户中转到“订阅”，找到相应订阅。 依次单击“资源提供程序”、“Microsoft.Compute”、“重新注册”。
     2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“AzureStack-DiskRP-Client”角色是否已列出。
  - 如果已配置多租户环境，在与来宾目录相关联的订阅中部署 VM 可能会失败并出现内部错误消息。 若要解决错误，请执行[此文章](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory)中的步骤来重新配置每个来宾目录。

- 如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。 若要解决此问题，请在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。

### <a name="networking"></a>网络  

<!-- 1766332 - IS ASDK --> 
- 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

<!-- 1902460 - IS ASDK --> 
-  Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

<!-- 16309153 - IS ASDK --> 
- 在使用 *自动* 的 DNS 服务器设置创建的虚拟网络上，更改为自定义 DNS 服务器失败。 更新的设置不推送到该 Vnet 中的 VM。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack 机密轮换期间，有一个时段（两到五分钟）会无法访问公共 IP 地址。

<!-- 2664148 - IS ASDK --> 
-   租户通过 S2S VPN 隧道访问其虚拟机时，可能会遇到这样的情况：如果在创建网关后向本地网络网关添加本地子网，连接尝试会失败。 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>应用服务

<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。


### <a name="usage"></a>使用情况  

<!-- TBD - IS ASDK --> 
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>下载更新

可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1809 更新包。
  

## <a name="next-steps"></a>后续步骤

- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](../azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](../azure-stack-monitor-update.md)。  
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。  