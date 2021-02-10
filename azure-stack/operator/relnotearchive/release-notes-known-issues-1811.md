---
title: Azure Stack 1811 更新 | Microsoft Docs
description: 了解 Azure Stack 集成系统的 1811 更新，包括新增功能、已知问题和更新下载位置。
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 3c852102b386cc0a625f628ed8c75319b112dfbd
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009258"
---
# <a name="azure-stack-1811-update"></a>Azure Stack 1811 更新

*适用于：Azure Stack 集成系统*

本文介绍 1811 更新包的内容。 该更新包包含此版 Azure Stack 的改进、修复和新功能。 本文还描述了此版本中的已知问题，并包含一个用于下载该更新的链接。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1811 更新内部版本号为 **1.1811.0.101**。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1811 之前，请务必先安装 1809 的[最新 Azure Stack 修补程序](#azure-stack-hotfixes)。

### <a name="azure-stack-hotfixes"></a>Azure Stack 修补程序

- **1809**： [KB 4481548 – Azure Stack 修补程序 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**：没有当前可用的修补程序。

## <a name="prerequisites"></a>必备条件

> [!IMPORTANT]
> 在安装 1811 更新期间，必须确保所有管理员门户实例都已关闭。 用户门户可以保持打开，但管理员门户必须关闭。

- 让 Azure Stack 部署准备好使用 Azure Stack 扩展主机。 按照以下指导准备系统：准备用于 [Azure Stack 的扩展主机](../azure-stack-extension-host-prepare.md)。 
 
- 在更新到 1811 之前，请先安装 1809 的[最新 Azure Stack 修补程序](#azure-stack-hotfixes)。

- 在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](../azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看活动警报，并解决所有需要采取措施的警报。  

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    如果不符合扩展主机要求，`Test-AzureStack` 输出会显示以下消息： 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare`

- Azure Stack 1811 更新要求将必需的扩展主机证书正确导入 Azure Stack 环境。 若要继续安装 1811 更新，必须导入扩展主机所需的 SSL 证书。 若要导入证书，请参阅[此部分](../azure-stack-extension-host-prepare.md#import-extension-host-certificates)。

    如果忽略每个警告并仍选择安装 1811 更新，则更新将在大约 1 小时内失败并显示以下消息：   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    正确导入必需的扩展主机证书后，可以从管理员门户恢复 1811 更新。 虽然 Microsoft 建议 Azure Stack 操作员在更新过程中安排维护时段，但由于缺少扩展主机证书而导致的失败不会影响现有的工作负荷或服务。  

    在安装此更新的过程中，配置扩展主机时 Azure Stack 用户门户将不可用。 扩展主机的配置最长可能需要花费 5 小时。 在此期间，你可以检查更新状态，或者使用 [Azure Stack 管理员 PowerShell 或特权终结点](../azure-stack-monitor-update.md)继续执行某个失败的更新安装。

- 如果 Azure Stack 由 System Center Operations Manager (SCOM) 进行管理，请确保在应用1811之前将 Microsoft Azure Stack 的管理包更新为版本1.0.3.11。

## <a name="new-features"></a>新增功能

此更新包含以下适用于 Azure Stack 的新功能和改进：

- 此版本中已启用[扩展主机](../azure-stack-extension-host-prepare.md)。 扩展主机可以简化网络集成，并改进 Azure Stack 的安全态势。

- 添加了通过 Active Directory 联合身份验证服务 (AD FS) 进行设备身份验证的支持（具体而言，是使用 Azure CLI 时）。 有关详细信息，请参阅[在 Azure Stack 中将 API 版本配置文件与 Azure CLI 配合使用](../../user/azure-stack-version-profiles-azurecli2.md)。

- 添加了配合 Active Directory 联合身份验证服务 (AD FS) 使用客户端机密的服务主体支持。 有关详细信息，请参阅[为 AD FS 创建服务主体](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity)。

- 此版本添加了对以下 Azure 存储服务 API 版本的支持： **2017-07-29**、 **2017-11-09**。 还添加了针对以下 Azure 存储资源提供程序 API 版本的支持： **2016-05-01**、 **2016-12-01**、 **2017-06-01** 和 **2017-10-01**。 有关详细信息，请参阅 [Azure Stack 存储：差异和注意事项](../../user/azure-stack-acs-differences.md)。

- 添加了新的特权终结点命令用于更新和删除 ADFS 的服务主体。 有关详细信息，请参阅[为 AD FS 创建服务主体](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity)。

- 添加了可让 Azure Stack 操作员启动、停止和关闭缩放单元节点的新“缩放单元节点”操作。 有关详细信息，请参阅 [Azure Stack 中的缩放单元节点操作](../azure-stack-node-actions.md)。

- 添加了一个显示环境注册详细信息的新区域属性边栏选项卡。 可以通过单击管理员门户中默认仪表板上的“区域管理”磁贴，然后选择“属性”，来查看此信息。

- 添加了新的特权终结点命令，以更新包含用户名和密码的 BMC 凭据用于与物理机进行通信。 有关详细信息，请参阅[更新基板管理控制器 \(BMC) 凭据](../azure-stack-rotate-secrets.md)。

- 添加了通过管理员和用户门户右上角的帮助和支持图标（问号）访问 Azure 路线图的功能，Azure 门户中也有类似的功能。

- 为离线用户添加了改进的市场管理体验。 在离线环境中发布市场项的上传过程已简化成一个步骤，而无需单独上传图像和市场包。 已上传的产品也会显示在市场管理边栏选项卡中。

- 此版本添加了在 [Azure Stack 机密轮换](../azure-stack-rotate-secrets.md)期间只轮换外部证书的功能，缩短了轮换机密所需的维护时段。

- [Azure Stack PowerShell](../azure-stack-powershell-install.md) 已更新到版本 1.6.0。 该更新包含对 Azure Stack 中新的存储相关功能的支持。 有关详细信息，请参阅 [PowerShell 库中的 Azure Stack 管理模块 1.6.0](https://www.powershellgallery.com/packages/AzureStack/1.6.0) 的发行说明。有关更新或安装 Azure Stack PowerShell 的信息，请参阅[安装适用于 Azure Stack 的 PowerShell](../azure-stack-powershell-install.md)。

- 使用 Azure Stack 门户创建虚拟机时，现在默认会启用托管磁盘。 有关需要对托管磁盘执行哪些附加步骤才能避免 VM 创建失败，请参阅[已知问题](#known-issues-post-installation)部分。

- 此版本为 Azure Stack 操作员引入了警报“修复”操作。 1811 中的某些警报在警报中提供一个“修复”按钮用于解决问题。 有关详细信息，请参阅[在 Azure Stack 中监视运行状况和警报](../azure-stack-monitor-health.md)。

- 更新到 Azure Stack 中的更新体验。 更新增强功能包括： 
  - 用于将“更新”与“更新历史记录”拆分开来的选项卡，这样可以更好地跟踪正在进行的更新与已完成的更新。
  - “概要”部分的增强的状态可视化效果，其中的新图标和布局对应于“当前版本和 OEM 版本”以及“上次更新的日期”。
  - “发行说明”列的“查看”链接直接将用户转到特定于该更新的文档，而不是转到常规更新页。
  - “更新历史记录”选项卡，用于确定每项更新的运行时间以及增强的筛选功能。  
  - 连接的 Azure Stack 缩放单元仍会自动接收已发布的 **可用更新**。
  - 未连接的 Azure Stack 缩放单元可以导入更新，就像以前一样。 
  - 从门户下载 JSON 日志的过程没有任何变化。 Azure Stack 操作员会看到表示进度的扩展步骤。

    有关详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。


## <a name="fixed-issues"></a>已修复的问题

<!-- TBD - IS ASDK --> 
- 修复了以下问题：公共 IP 地址使用计量数据针对每条记录显示相同的 **EventDateTime** 值，而不是创建记录时显示的 **TimeDate** 时间戳。 现在，可以使用此数据来执行公共 IP 地址用量的准确计帐。

<!-- 3099544 – IS, ASDK --> 
- 修复了使用 Azure Stack 门户创建新虚拟机 (VM) 时发生的问题。 选择 VM 大小导致 **USD/Month** 列显示 **不可用** 的消息。 现在，此列不再显示；Azure Stack 不支持显示 VM 定价列。

<!-- 2930718 - IS ASDK --> 
- 修复了以下问题：在管理员门户中访问用户订阅详细信息时，在关闭边栏选项卡并单击“最近”后，用户订阅名称不显示。 现在会显示用户订阅名称。

<!-- 3060156 - IS ASDK --> 
- 修复了管理员门户和用户门户中的以下问题：单击门户设置并选择“删除所有设置和专用仪表板”后，结果不符合预期，同时会显示错误通知。 此选项现在会正常工作。

<!-- 2930799 - IS ASDK --> 
- 修复了管理员门户和用户门户中的以下问题：在“所有服务”下，资产“DDoS 防护计划”未正确列出。 它在 Azure Stack 中不可用。 已删除此列表。
 
<!--2760466 – IS  ASDK --> 
- 修复了安装新的 Azure Stack 环境时出现的一个问题，其中指出 **需要激活** 的警报未显示。 现在会显示该警报。

<!--1236441 – IS  ASDK --> 
- 修复了使用 ADFS 时阻止将 RBAC 策略应用到用户组的问题。

<!--3463840 - IS, ASDK --> 
- 修复了由于无法从公共 VIP 网络访问文件服务器而导致基础结构备份失败的问题。 该项修复会将基础结构备份服务移回公共基础结构网络。 如果已应用解决此问题的最新 [Azure Stack 1809 修补程序](#azure-stack-hotfixes)，1811 更新将不进行任何进一步的修改。 

<!-- 2967387 – IS, ASDK --> 
- 修复了以下问题：登录 Azure Stack 管理员门户或用户门户时使用的帐户显示为“未识别的用户”。 如果帐户中未指定“名字”或“姓氏”，则会显示此消息。   

<!--  2873083 - IS ASDK --> 
- 修复了以下问题：使用门户创建虚拟机规模集 (VMSS) 时，如果使用 Internet Explorer，则“实例大小”下拉列表不会正确加载。 现在，此浏览器可正常工作。  

<!-- 3190553 - IS ASDK -->
- 修复了以下问题：生成的干扰性警报指示某个基础结构角色实例不可用或缩放单元节点已脱机。

<!-- 2724961 - IS ASDK -->
- 修复了 VM 概述页无法正常显示 VM 指标图表的问题。 

## <a name="changes"></a>更改

- 1811 中导入了新的方式来查看和编辑计划中的配额。 有关详细信息，请参阅[查看现有配额](../azure-stack-quota-types.md#view-an-existing-quota)。

<!-- 3083238 IS -->
- 此项更新中的安全增强功能会导致目录服务角色的备份大小增加。 有关已更新的外部存储位置大小调整指导，请参阅[基础结构备份文档](../azure-stack-backup-reference.md#storage-location-sizing)。 因为数据传输大小变大，此项更改会导致需要更长时间才能完成备份。 此项更改会影响集成式系统。 

- 在 1811 中，用于检索 BitLocker 恢复密钥的现有 PEP cmdlet 已从 Get-AzsCsvsRecoveryKeys 重命名为 Get-AzsRecoveryKeys。 有关检索 BitLocker 恢复密钥的详细信息，请参阅[有关如何检索密钥的说明](../azure-stack-security-bitlocker.md)。

## <a name="common-vulnerabilities-and-exposures"></a>常见漏洞和风险

此更新安装以下安全更新：  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

有关这些漏洞的详细信息，请单击上述链接，或者查看 Microsoft 知识库文章 [4478877](https://support.microsoft.com/help/4478877)。

## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 在同一特权终结点 (PEP) 会话中运行 **Test-AzureStack** 之后，运行 **Get-AzureStackLog** PowerShell cmdlet 时，**Get-AzureStackLog** 失败。 若要解决此问题，请关闭用于执行 **Test-AzureStack** 的 PEP 会话，然后打开一个新的会话来运行 **Get-AzureStackLog**。

- 在安装 1811 更新期间，请确保所有管理员门户实例都已关闭。 用户门户可以保持打开，但管理员门户必须关闭。

- 运行 [Test-AzureStack](../azure-stack-diagnostic-test.md) 时，如果 **AzsInfraRoleSummary** 或 **AzsPortalApiSummary** 测试失败，系统会提示你结合 `-Repair` 标志运行 **Test-AzureStack**。  如果运行此命令，则会失败并出现以下错误消息：  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  此问题将在将来的版本中得到解决。

- 在安装 1811 更新的过程中，配置扩展主机时 Azure Stack 用户门户将不可用。 扩展主机的配置最长可能需要花费 5 小时。 在此期间，你可以检查更新状态，或者使用 [Azure Stack 管理员 PowerShell 或特权终结点](../azure-stack-monitor-update.md)继续执行某个失败的更新安装。 

- 在安装 1811 更新的过程中，用户门户仪表板可能不可用，并可能会丢失自定义项。 可以在更新完成之后，通过打开门户设置并选择“还原默认设置”，将仪表板还原为默认设置。

- 运行 [Test-AzureStack](../azure-stack-diagnostic-test.md) 时，会显示基板管理控制器 (BMC) 中的一条警告消息。 可以放心地忽略此警告。

- <!-- 2468613 - IS --> 在安装此更新的过程中，可能会看到标题为 `Error – Template for FaultType UserAccounts.New is missing.` 的警报。可以放心忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

- <!-- 3139614 | IS --> 如果已从 OEM 应用 Azure Stack 更新，则“有可用的更新”通知可能不会显示在 Azure Stack 管理员门户中。 若要安装 Microsoft 更新，请遵照[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)中的说明手动下载并导入该更新。

## <a name="post-update-steps"></a>更新后步骤

- 安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅[修补程序](#hotfixes)以及我们的[服务策略](../azure-stack-servicing-policy.md)。  

- 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](../azure-stack-security-bitlocker.md)操作。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

<!-- 2930820 - IS ASDK --> 
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。 

<!-- 2931230 – IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 订阅类型为 **计数订阅** 和 **消耗订阅**。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- TBD - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- TBD - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是[使用 PowerShell 验证权限](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)。

### <a name="health-and-monitoring"></a>运行状况和监视

<!-- 1264761 - IS ASDK -->  
- 可能会看到包含以下详细信息的“运行状况控制器”组件的警报：  

  - 警报 #1：
     - 名称：基础结构角色不正常
     - 严重性：警告
     - 组件：运行状况控制器
     - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  - 警报 #2：
     - 名称：基础结构角色不正常
     - 严重性：警告
     - 组件：运行状况控制器
     - 说明：运行状况控制器故障扫描仪不可用。 这可能会影响运行状况报告和指标。

    可以放心地忽略这两个警报。 它们将在一段时间后自动关闭。  

### <a name="compute"></a>计算

- 创建新的 Windows 虚拟机 (VM) 时，“设置”边栏选项卡要求选择公共入站端口以继续操作。 在 1811 中，这是必需的设置，但不起作用。 这是因为，该功能依赖于 Azure 防火墙，但 Azure Stack 中并未实施该防火墙。 可以选择“无公共入站端口”或任何其他选项来继续创建 VM。 该设置不起作用。

- 创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。 若要解决此问题，请使用以前所用的同一名称重新创建存储帐户。

- 创建 [Dv2 系列 VM](../../user/azure-stack-vm-considerations.md#vm-sizes) 时，可以通过 D11-14v2 VM 分别创建 4 个、8 个、16 个和 32 个数据磁盘。 不过，“创建 VM”窗格会显示 8 个、16 个、32 个和 64 个数据磁盘。

- Azure Stack 上的使用情况记录可能包含意外的大小写，例如：

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   在此示例中，资源组的名称应该是 **AndrewRG**。 可以放心忽略这种不一致情况。

<!-- 3235634 – IS, ASDK -->
- 若要部署大小包含 **v2** 后缀的 VM（例如 **Standard_A2_v2**），请将后缀指定为 **Standard_A2_v2**（小写 v）。 请勿使用 **Standard_A2_V2**（大写 V）。 这适用于全球 Azure，在 Azure Stack 上有不一致的问题。

<!-- 2869209 – IS, ASDK --> 
- 使用 [**Add-AzsPlatformImage** cmdlet](/powershell/module/azs.compute.admin/add-azsplatformimage) 时，必须使用 **-OsUri** 参数作为存储帐户 URI（在其中上传磁盘）。 如果使用磁盘的本地路径，则此 cmdlet 会失败并显示以下错误： 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- 通过门户创建“高级”VM 大小（DS、Ds_v2、FS、FSv2）的虚拟机 (VM) 时，该 VM 在标准存储帐户中创建。 在标准存储帐户中创建不影响功能、IOPS 或计费。 可以放心忽略带有以下字样的警告： 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。  

<!-- 2724873 - IS --> 
- 使用 PowerShell cmdlet **Start-AzsScaleUnitNode** 或 **Stop-AzsScaleunitNode** 管理缩放单元时，首次尝试启动或停止缩放单元可能会失败。 如果 cmdlet 在第一次运行时失败，请再次运行 cmdlet。 第二次运行应该能够成功完成操作。 

<!-- TBD - IS ASDK --> 
- 如果在 VM 部署上预配某个扩展时耗时过长，请让预配超时，而不要尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

<!-- 1662991 IS ASDK --> 
- Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  

<!-- 3507629 - IS, ASDK --> 
- 托管磁盘创建了两个新的[计算配额类型](../azure-stack-quota-types.md#compute-quota-types)来限制可以预配的托管磁盘的最大容量。 默认情况下将为每个托管磁盘配额类型分配 2048 GiB。 不过，你可能会遇到以下问题：

   - 对于在 1808 更新之前创建的配额，托管磁盘配额在管理门户中将显示为值 0，虽然分配了 2048 GiB。 你可以根据实际需求增大或减小该值，新设置的配额值将替代 2048 GiB 默认值。
   - 如果将配额值更新为 0，则它等效于默认值 2048 GiB。 作为一种解决方法，请将配额值设置为 1。

<!-- TBD - IS ASDK --> 
- 应用 1811 更新后，在部署包含托管磁盘的 VM 时可能会遇到以下问题：

   - 如果订阅是在 1808 更新之前创建的，则部署具有托管磁盘的 VM 可能会失败并出现内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。 依次选择“资源提供程序”、“Microsoft.Compute”、“重新注册”。
      2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“AzureStack-DiskRP-Client”角色是否已列出。
   - 如果已配置多租户环境，在与来宾目录相关联的订阅中部署 VM 可能会失败并出现内部错误消息。 若要解决错误，请执行[此文章](../azure-stack-enable-multitenancy.md#register-a-guest-directory)中的步骤来重新配置每个来宾目录。

- 如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。 若要解决此问题，请在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。

### <a name="networking"></a>网络  

<!-- 1766332 - IS ASDK --> 
- 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

<!-- 1902460 - IS ASDK --> 
-  Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网络网关连接之后，系统会拒绝使用同一 IP 地址创建本地网络网关资源的后续尝试。

<!-- 16309153 - IS ASDK --> 
- 在使用“自动”DNS 服务器设置创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack *秘密旋转* 期间，有一段时间无法访问公共 IP 地址两到五分钟。

<!-- 2664148 - IS ASDK --> 
- 租户通过 S2S VPN 隧道访问虚拟机时，可能会遇到这样的情况：如果在创建网关后向本地网络网关添加本地子网，连接尝试会失败。 

- 在 Azure Stack 门户中，对于已附加到 VM 实例的网络适配器，在更改与其绑定的 IP 配置的静态 IP 地址时，会看到一条警告消息，其中指出 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    可以放心忽略此消息；即使 VM 实例未重启，IP 地址也会更改。

- 在门户中的“网络属性”边栏选项卡上，每个网络适配器都有一个“有效安全规则”链接。 选择此链接会打开一个新的边栏选项卡，其中显示了错误消息 `Not Found.`。发生此错误的原因是 Azure Stack 尚不支持 **有效安全规则**。

- 在门户中，如果添加入站安全规则并选择“服务标记”作为源，“服务标记”列表中会显示多个不适用于 Azure Stack 的选项。 在 Azure Stack 中有效的选项仅限以下几个：

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    在 Azure Stack 中，不支持将其他选项用作源标记。 同样，如果添加出站安全规则并选择“服务标记”作为目标，则显示与“源标记”相同的选项列表。 仅有的有效选项与“源标记”的有效选项相同，如以上列表中所述。

- **New-AzureRmIpSecPolicy** PowerShell cmdlet 不支持为 `DHGroup` 参数设置 **DHGroup24**。

- 网络安全组 (NSG) 无法像在全球 Azure 中一样在 Azure Stack 中运行。 在 Azure 中，可以在一个 NSG 规则中设置多个端口（使用门户、PowerShell 和资源管理器模板）。 在 Azure Stack 中，无法通过门户在一个 NSG 规则中设置多个端口。 若要解决此问题，请使用资源管理器模板设置这些附加的规则。

### <a name="infrastructure-backup"></a>基础结构备份

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- 启用自动备份后，计划程序服务会意外进入已禁用状态。 备份控制器服务将检测到自动备份已禁用，并在管理员门户中引发警告。 禁用自动备份时预期会发出此警告。 
    - 原因：此问题是由于服务中的 bug 导致计划程序配置丢失而导致的。 此 bug 不会更改存储位置、用户名、密码或加密密钥。   
    - 修正：若要缓解此问题，请打开基础结构备份资源提供程序中的 "备份控制器设置" 边栏选项卡，然后选择 " **启用自动备份**"。 请务必设置所需的频率和保留期。
    - 发生次数：低 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>应用服务

<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，必须先注册存储资源提供程序。


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Syslog

- syslog 配置不会在整个更新周期中保留，导致 syslog 客户端丢失其配置，并停止转发 syslog 消息。 此问题适用于自 syslog 客户端正式版 (1809) 发布以来的所有 Azure Stack 版本。 若要解决此问题，请在应用 Azure Stack 更新之后重新配置 syslog 客户端。

## <a name="download-the-update"></a>下载更新

可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1811 更新包。 

只有在连接的情况下，Azure Stack 部署才会定期检查某个安全的终结点，并在已发布云更新的情况下自动通知你。 有关详细信息，请参阅[管理 Azure Stack 的更新](../azure-stack-updates.md#how-to-know-an-update-is-available)。

## <a name="next-steps"></a>后续步骤

- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](../azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](../azure-stack-monitor-update.md)。  
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。  