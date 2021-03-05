---
title: Azure Stack 1901 更新 | Microsoft Docs
description: 了解 Azure Stack 集成系统的 1901 更新，包括新增功能、已知问题和更新下载位置。
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
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 03/27/2019
ms.openlocfilehash: a062fa6f2d4242f1831233f672e0a8dc3dff20f7
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186919"
---
# <a name="azure-stack-1901-update"></a>Azure Stack 1901 更新

*适用于：Azure Stack 集成系统*

本文介绍 1901 更新包的内容。 该更新包含此版 Azure Stack 的改进、修复和新功能。 本文还描述了此版本中的已知问题，并包含一个用于下载该更新的链接。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1901 更新内部版本号为 **1.1901.0.95**（在 2019 年 2 月 26 日以后则为 **1.1901.0.99**）。 请查看以下说明：

> [!IMPORTANT]  
> Microsoft 发现了一个问题，该问题会影响从 1811 (1.1811.0.101) 更新到1901的客户，并已发布了一个更新的1901包以解决此问题：生成1.1901.0.99，已从1.1901.0.95 中更新。 已更新到 1.1901.0.95 的客户不需执行其他操作。
>
> 系统会在管理员门户中为使用 1811 的已连接客户自动显示新的已发布的 1901 (1.1901.0.99) 包，该包在做好准备的情况下即可安装。 断开连接的客户可以根据[此处所述](../azure-stack-apply-updates.md)的相同过程下载和导入新的 1901 包。
>
> 不管使用哪一个版本的 1901，客户在安装下一个完整的包或修补程序包时都不会受影响。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1901 之前，请务必先安装 1811 的[最新 Azure Stack 修补程序](#azure-stack-hotfixes)。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修补程序

如果已经有 1901 但尚未安装任何修补程序，则可直接安装 1902，不需先安装 1901 修补程序。

- **1811**：没有当前可用的修补程序。
- **1901**： [KB 4500636-Azure Stack 修补程序 1.1901.5.109](https://support.microsoft.com/help/4500636)

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 在更新到 1901 之前，请先安装 1811 的[最新 Azure Stack 修补程序](#azure-stack-hotfixes)（如果有）。 如果已经有 1901 但尚未安装任何修补程序，则可直接安装 1902，不需先安装 1901 修补程序。

- 在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](../azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看活动警报，并解决所有需要采取措施的警报。

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- 如果 Azure Stack 由 System Center Operations Manager (SCOM) 进行管理，请确保在应用1901之前将 Microsoft Azure Stack 的管理包更新为版本1.0.3.11。

## <a name="new-features"></a>新增功能

此更新包含以下适用于 Azure Stack 的新功能和改进：

- 使用 Azure Stack 上的托管映像可以在通用化 VM（非托管和托管的 VM）上创建托管映像对象，以后只能创建托管磁盘 VM。 有关详细信息，请参阅 [Azure Stack 托管磁盘](../../user/azure-stack-managed-disk-considerations.md#managed-images)。

- **AzureRm 2.4.0**
   * **AzureRm**  
         Bug 修复 - `Import-AzureRmContext` 可正确反序列化已保存的令牌。  
   * **AzureRm**  
         Bug 修复 - `Get-AzureRmResource` 可按资源类型进行不区分大小写的查询。  
   * **Azure.Storage**  
         AzureRm 汇总模块现在包含已发布的 4.5.0 版，支持 **api-version 2017-07-29**。  
   * **AzureRm**  
         AzureRm 汇总模块现在包含已发布的 5.0.4 版，支持 **api-version 2017-10-01**。  
   * **AzureRm**  
         在 `New-AzureRmVM` 和 `New-AzureRmVmss` 中添加了简单参数集，`-Image` 参数支持指定用户映像。  
   * **AzureRm**  
         AzureRm 汇总模块现在包含已发布的 5.1.5 版，支持适用于指标、指标定义资源类型的 **api-version 2018-01-01**。

- **AzureStack 1.7.1** 这是一个有中断性变更的版本。 有关中断性变更的详细信息，请参阅 https://aka.ms/azspshmigration171
   * **Azs.Backup.Admin 模块**  
         重大更改：备份对基于证书的加密模式的更改。 已弃用对对称密钥的支持。  
   * **Azs.Fabric.Admin 模块**  
         `Get-AzsInfrastructureVolume` 已弃用。 使用新的 cmdlet `Get-AzsVolume`。  
         `Get-AzsStorageSystem` 已弃用。  使用新的 cmdlet `Get-AzsStorageSubSystem`。  
         `Get-AzsStoragePool` 已弃用。 `StorageSubSystem` 对象包含容量属性。  
   * **Azs.Compute.Admin 模块**  
         Bug 修复- `Add-AzsPlatformImage` ， `Get-AzsPlatformImage` ： `ConvertTo-PlatformImageObject` 仅在成功路径中调用。  
         Bug 修复- `Add-AzsVmExtension` ， `Get-AzsVmExtension` ：仅在成功路径中调用 ConvertTo-VmExtensionObject。  
   * **Azs.Storage.Admin 模块**  
         Bug 修复 - 在不提供值的情况下，新的存储配额使用默认值。

若要查看已更新模块的参考，请参阅 [Azure Stack 模块参考](/powershell/azure/azure-stack/overview?preserve-view=true&view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0)。

## <a name="fixed-issues"></a>已修复的问题

- 修复了以下问题：门户显示了创建基于策略的 VPN 网关的选项，但该选项在 Azure Stack 中不受支持。 已从门户中删除此选项。

<!-- 16523695 - IS, ASDK -->
- 修复了以下问题：在将虚拟网络的 DNS 设置从“使用 Azure Stack DNS”更新为“自定义 DNS”之后，不使用新设置更新实例。

- <!-- 3235634 - IS, ASDK -->
  修复了以下问题：部署其大小包含 **v2** 后缀的 VM（例如 **Standard_A2_v2**）时，需要将后缀指定为 **Standard_A2_v2**（小写 v）。 使用全球 Azure 时，现在可以使用 **Standard_A2_V2**（大写 V）。

<!--  2795678 - IS, ASDK --> 
- 修复了以下问题：使用门户创建高级 VM 大小（DS、Ds_v2、FS、FSv2）的虚拟机 (VM) 时生成警告。 VM 是在标准存储帐户中创建的。 尽管这不会影响功能、IOPs 或计费，但我们依然修复了警告。

<!-- 1264761 - IS ASDK -->  
- 修复了以下问题：**运行状况控制器** 组件生成以下警报。 可以放心忽略这些警报：

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


<!-- 3507629 - IS, ASDK --> 
- 修复了以下问题：将[计算配额类型](../azure-stack-quota-types.md#compute-quota-types)下面的托管磁盘配额设置为 0 时，相当于使用默认值 2048 GiB。 现在遵循零配额值。

<!-- 2724873 - IS --> 
- 修复了以下问题：使用 PowerShell cmdlet **Start-AzsScaleUnitNode** 或 **Stop-AzsScaleUnitNode** 管理缩放单元时，首次尝试启动或停止缩放单元可能会失败。

<!-- 2724961- IS ASDK --> 
- 修复了以下问题：尽管在订阅设置中注册了 **Microsoft.Insight** 资源提供程序并创建了支持来宾 OS 诊断的 Windows VM，但 VM 概述页中的“CPU 百分比”图表仍不显示指标数据。 现在会正常显示数据。

- 修复了以下问题：在相同的特权终结点 (PEP) 会话中运行 **Test-AzureStack** 之后，运行 **Get-AzureStackLog** cmdlet 失败。 现在可以使用用于执行 **Test-AzureStack** 的同一 PEP 会话。

<!-- bug 3615401, IS -->
- 修复了自动备份中计划程序服务意外进入禁用状态的问题。 

<!--2850083, IS ASDK -->
- 已从 Azure Stack 门户中删除“重置网关”按钮，以前在单击该按钮时会引发错误。 此按钮在 Azure Stack 中不起任何作用，因为 Azure Stack 包含多租户网关，而不为每个租户 VPN 网关提供专用的 VM 实例，因此已将其删除以避免混淆。 

<!-- 3209594, IS ASDK -->
- 已从“网络属性”边栏选项卡中删除“有效安全规则”链接，因为此功能在 Azure Stack 中不受支持。 显示该链接会让人觉得此功能受支持，但其实它并不起作用。 为了避免混淆，我们已删除该链接。

<!-- 3139614 | IS -->
- 修复了以下问题：将更新从 OEM 应用到 Azure Stack 之后，“有可用的更新”通知不显示在 Azure Stack 管理员门户中。

## <a name="changes"></a>更改

<!-- 3083238 IS -->
- 此项更新中的安全增强功能会导致目录服务角色的备份大小增加。 有关外部存储位置的已更新大小调整指南，请参阅 [基础结构备份文档。/azure-stack-backup-reference.md # 存储空间大小) 。 因为数据传输大小变大，此项更改会导致需要更长时间才能完成备份。 此项更改会影响集成式系统。 

- 从2019年1月开始，你可以在 Active Directory 联合身份验证服务 (上部署 Kubernetes 群集 AD FS) 已注册、已连接 Azure Stack 戳记 (需要 internet 访问) 。 按照 [此处](../azure-stack-solution-template-kubernetes-cluster-add.md) 的说明下载新的 Kubernetes Marketplace 项。 按照 [此处](../../user/azure-stack-solution-template-kubernetes-adfs.md) 的说明部署 Kubernetes 群集。 请注意用于指示目标系统是添加还是注册 AD FS 的新参数。 如果 AD FS，则可以使用新字段输入存储部署证书的 Key Vault 参数。

   请注意，即使在 AD FS 支持的情况下，Kubernetes 群集的部署也需要 internet 访问权限。

- 在安装 Azure Stack 的更新或修补程序之后，可以引入新的功能，这需要将新权限授予一个或多个标识应用程序。 授予这些权限需要主目录的管理访问权限，因此无法自动完成授权。 例如：

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- 准确规划 Azure Stack 容量时需要考虑一个新的因素。 使用 1901 更新时，现在可创建的虚拟机总数有限制。  此限制是暂时性的，目的是避免解决方案不稳定。 我们已解决大量 VM 的稳定性问题根源，但尚未确定补救措施的具体时间表。 使用 1901 更新时，现在每台服务器存在 60 个 VM 的限制，解决方案总体限制为 700 个。  例如，8 个服务器的 Azure Stack VM 数目限制是 480 (8 * 60)。  对于包含 12 到 16 个服务器的 Azure Stack 解决方案，限制为 700 个 VM。 确定此限制时，我们考虑到了所有计算容量，例如复原能力，以及运营商想要在阵列上保持的虚拟与物理 CPU 之比。 有关详细信息，请参阅新版容量规划器。  
如果已达到 VM 缩放限制，则会返回以下错误代码： VMsPerScaleUnitLimitExceeded、VMsPerScaleUnitNodeLimitExceeded。 
 

- 计算 API 版本已递增到 2017-12-01。

- 基础结构备份现在要求使用仅包含公钥的证书 (.CER) 来加密备份数据。 从更新 1901 开始，将会终止对称加密密钥的支持。 如果基础结构备份在更新到 1901 之前已进行配置，则加密密钥仍旧可用。 必须至少有额外两个支持向后兼容的更新才能更新备份设置。 有关详细信息，请参阅 [Azure Stack 基础结构备份最佳做法](../azure-stack-backup-best-practices.md)。

## <a name="common-vulnerabilities-and-exposures"></a>常见漏洞和风险

此更新安装以下安全更新：  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


有关这些漏洞的详细信息，请单击上述链接，或者查看 Microsoft 知识库文章 [4480977](https://support.microsoft.com/en-us/help/4480977)。

## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 尝试安装 Azure Stack 更新时，更新状态可能会显示失败并更改为 **PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。 从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。

- 运行 [Test-AzureStack](../azure-stack-diagnostic-test.md) 时，如果 **AzsInfraRoleSummary** 或 **AzsPortalApiSummary** 测试失败，系统会提示你结合 `-Repair` 标志运行 **Test-AzureStack**。  如果运行此命令，它会失败并显示以下错误消息：`Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

- 运行 [Test-AzureStack](../azure-stack-diagnostic-test.md) 时，会显示基板管理控制器 (BMC) 中的一条警告消息。 可以放心地忽略此警告。

- <!-- 2468613 - IS --> 在安装此更新的过程中，可能会看到标题为 `Error - Template for FaultType UserAccounts.New is missing.` 的警报。可以放心忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

## <a name="post-update-steps"></a>更新后步骤

- 安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅[修补程序](#hotfixes)以及我们的[服务策略](../azure-stack-servicing-policy.md)。  

- 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](../azure-stack-security-bitlocker.md)操作。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

<!-- 2930820 - IS ASDK --> 
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。 

<!-- 2931230 - IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 订阅类型为 **计数订阅** 和 **消耗订阅**。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- 3557860 - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- 1663805 - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是[使用 PowerShell 验证权限](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)。

<!-- ### Health and monitoring -->

### <a name="compute"></a>计算

- 创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。 若要解决此问题，请使用以前所用的同一名称重新创建存储帐户。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。  

<!-- TBD - IS ASDK --> 
- 应用 1901 更新后，在部署包含托管磁盘的 VM 时可能会遇到以下问题：

   - 如果订阅是在 1808 更新之前创建的，则部署具有托管磁盘的 VM 可能会失败并出现内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。 依次选择“资源提供程序”、“Microsoft.Compute”、“重新注册”。
      2. 在同一订阅下，转到“访问控制(IAM)”，检查“AzureStack-DiskRP-Client”是否已列出。
   - 如果已配置多租户环境，在与来宾目录相关联的订阅中部署 VM 可能会失败并出现内部错误消息。 若要解决错误，请执行[此文章](../azure-stack-enable-multitenancy.md#register-a-guest-directory)中的步骤来重新配置每个来宾目录。

- 如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。 若要解决此问题，请在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。

- 不能从 " **虚拟机规模集** " 边栏选项卡中删除规模集。 解决方法是，选择要删除的规模集，然后在“概述”窗格中单击“删除”按钮。

### <a name="networking"></a>网络  

<!-- 3239127 - IS, ASDK -->
- 在 Azure Stack 门户中，对于已附加到 VM 实例的网络适配器，在更改与其绑定的 IP 配置的静态 IP 地址时，会看到一条警告消息，其中指出 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    可以放心忽略此消息；即使 VM 实例未重启，IP 地址也会更改。

<!-- 3632798 - IS, ASDK -->
- 在门户中，如果添加入站安全规则并选择“服务标记”作为源，“服务标记”列表中会显示多个不适用于 Azure Stack 的选项。 在 Azure Stack 中有效的选项仅限以下几个：

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    在 Azure Stack 中，不支持将其他选项用作源标记。 同样，如果添加出站安全规则并选择“服务标记”作为目标，则显示与“源标记”相同的选项列表。 仅有的有效选项与“源标记”的有效选项相同，如以上列表中所述。

- 网络安全组 (NSG) 无法像在全球 Azure 中一样在 Azure Stack 中运行。 在 Azure 中，可以在一个 NSG 规则中设置多个端口（使用门户、PowerShell 和资源管理器模板）。 但是，在 Azure Stack 中，无法通过门户在一个 NSG 规则中设置多个端口。 若要解决此问题，请使用资源管理器模板或 PowerShell 设置这些附加的规则。

<!-- 3203799 - IS, ASDK -->
- 目前，无论实例大小是什么，Azure Stack 都不支持将 4 个以上的网络接口 (NIC) 附加到 VM 实例。

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

可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1901 更新包。 

只有在连接的情况下，Azure Stack 部署才会定期检查某个安全的终结点，并在已发布云更新的情况下自动通知你。 有关详细信息，请参阅[管理 Azure Stack 的更新](../azure-stack-updates.md#how-to-know-an-update-is-available)。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](../azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](../azure-stack-monitor-update.md)。