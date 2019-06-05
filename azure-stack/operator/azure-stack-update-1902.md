---
title: Azure Stack 1902 更新 | Microsoft Docs
description: 了解 Azure Stack 集成系统的 1902 更新，包括新增功能、已知问题和更新下载位置。
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
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 4a69ece2b97fca13a87819dce6e02e8971121944
ms.sourcegitcommit: a427e72e4f3b6cd6000b1459af9bbf221e049e08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66506294"
---
# <a name="azure-stack-1902-update"></a>Azure Stack 1902 更新

*适用于：Azure Stack 集成系统*

本文介绍 1902 更新包的内容。 该更新包含此版 Azure Stack 的改进、修复和新功能。 本文还描述了此版本中的已知问题，并包含一个用于下载该更新的链接。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="archived-release-notes"></a>已存档的发行说明

您可以看到[较旧版本的 Azure Stack 发行 TechNet 库中的说明](http://aka.ms/azsarchivedrelnotes)。 这些存档的发行说明提供仅供参考，并不表示对这些版本的支持。 进一步的帮助，请联系 Microsoft 客户支持服务。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1902 更新内部版本号为 **1.1902.0.69**。

### <a name="update-type"></a>更新类型

Azure Stack 1902 更新生成类型是**完整**。 有关更新的详细信息生成类型，请参阅[管理 Azure Stack 中的更新](azure-stack-updates.md)一文。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1902 之前，请务必先安装 1901 的[最新 Azure Stack 修补程序](#azure-stack-hotfixes)。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修补程序

- **1901**：[KB 4500636-Azure Stack 修补程序 1.1901.5.109](https://support.microsoft.com/help/4500636)
- **1902**：[KB 4500637-Azure Stack 修补程序 1.1902.3.75](https://support.microsoft.com/help/4500637)

## <a name="prerequisites"></a>必备组件

> [!IMPORTANT]
> 可以直接从 [1.1901.0.95 或 1.1901.0.99](azure-stack-update-1901.md#build-reference) 版本安装 1902，而无需首先安装任何 1901 修补程序。 但是，如果已安装旧版 **1901.2.103** 修补程序，则必须先安装新版 [1901.3.105 修补程序](https://support.microsoft.com/help/4495662)才能继续安装 1902。

- 在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看活动警报，并解决所有需要采取措施的警报。

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  如果在执行 **Test-AzureStack** 时包含 `AzsControlPlane` 参数，**Test-AzureStack** 输出中会显示以下失败：“失败: Azure Stack 控制平面网站摘要”。  可以放心忽略此特定错误。

- 当 Azure Stack 管理由 System Center Operations Manager (SCOM) 时，请确保更新[适用于 Microsoft Azure Stack 管理包](https://www.microsoft.com/download/details.aspx?id=55184)到版本 1.0.3.11 应用 1902年之前。

- 从版本 1902 开始，Azure Stack 更新包的格式已从 **.bin/.exe/.xml** 更改为 **.zip/.xml**。 使用联网 Azure Stack 缩放单元的客户将在门户中看到“有可用更新”消息。  未建立连接的客户现在只需下载并导入包含相应 .xml 的 .zip 文件即可。

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>改进

- 1902 版本在 Azure Stack 管理员门户上引入了新的用户界面用于创建计划、套餐、配额和附加计划。 有关详细信息（包括屏幕截图），请参阅[创建计划、套餐和配额](azure-stack-create-plan.md)。

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- 切换缩放单元的状态从"扩展存储"为"正在运行"时添加节点操作期间的容量扩展可靠性改进。

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- 为了改进包的完整性和安全性并简化脱机引入的管理，Microsoft 已将更新包的格式从 .exe 和 .bin 文件更改为 .zip 文件。 新格式使得有时导致更新准备工作停滞的解包过程更加可靠。 相同的包格式也适用于来自 OEM 的更新包。
- 为了改进运行 Test-AzureStack 时的 Azure Stack 操作员体验，操作员现在只需使用“Test-AzureStack -Group UpdateReadiness”，而无需在 Include 语句后面传递 10 个附加的参数。

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- 为了改进核心基础结构服务在更新过程中的总体可靠性和可用性，更新操作计划中的本机更新资源提供程序可根据需要检测并调用自动全局补救措施。 全局修正"修复"工作流包括：

  - 检查处于欠佳状态的基础结构虚拟机，并根据需要尝试进行修复。
  - 检查控制计划中的 SQL 服务问题，并根据需要尝试进行修复。
  - 检查网络控制器 (NC) 中软件负载均衡器 (SLB) 服务的状态，并根据需要尝试进行修复。
  - 检查网络控制器 (NC) 服务的状态，并根据需要尝试进行修复
  - 检查紧急恢复控制台服务 (ERCS) Service Fabric 节点的状态，并根据需要进行修复。
  - 检查基础结构角色的状态，并根据需要进行修复。
  - 检查 Azure 一致性存储 (ACS) Service Fabric 节点的状态，并根据需要进行修复。

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- 改进了 Azure Stack 诊断工具，以提高日志收集可靠性和性能。 增加了网络和标识服务的日志记录功能。 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- 改进了 Test-AzureStack 在进行机密轮换就绪性测试时的可靠性。

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- 以与客户的 Active Directory 环境进行通信时提高 AD Graph 的可靠性的改进

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- 改进了 Get-AzureStackStampInformation 中的硬件库存收集。

- 为了改进 ERCS 基础结构上运行的操作的可靠性，每个 ERCS 实例的内存已从 8 GB 增加到 12 GB。 在 Azure Stack 集成系统安装中，这会导致内存总量增大 12 GB。

<!-- 110303935 IcM Reported by HKEX -->
- 1902 解决了特定节点上的所有 Vm 都脱机在网络控制器 VSwitch 服务中的问题。  该问题导致它一直处于主丢失状态，无法连接到主数据库，但该角色不经过故障转移到另一个，正常实例，仅可以通过联系 Microsoft 支持服务解决。

> [!IMPORTANT]
> 为了确保修补升级过程尽可能地减少租户停机时间，请在“容量”边栏选项卡中确认 Azure Stack 阵列是否可提供 12 GB 以上的可用空间。  成功安装更新后，“容量”边栏选项卡中会反映内存增大。 

## <a name="common-vulnerabilities-and-exposures"></a>常见漏洞和风险

此更新将安装以下安全更新：  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

有关这些漏洞的详细信息，请单击上述链接，或者查看 Microsoft 知识库文章 [4487006](https://support.microsoft.com/en-us/help/4487006)。

## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 在尝试安装 Azure Stack 更新时，更新的状态可能失败，并将状态更改为**PreparationFailed**。 这被引起更新资源提供程序 (URP) 无法正确地将文件传输从存储容器到处理的内部基础结构共享。 从版本 1901 (1.1901.0.95) 开始，你可以解决此问题通过单击**立即更新**再次 (不**恢复**)。 URP 然后清理文件从上一次尝试，并重新开始下载。

- 运行 [Test-AzureStack](azure-stack-diagnostic-test.md) 时，会显示基板管理控制器 (BMC) 中的一条警告消息。 可以放心地忽略此警告。

- <!-- 2468613 - IS --> 在安装此更新的过程中，可能会看到标题为 `Error - Template for FaultType UserAccounts.New is missing.` 的警报。可以放心忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

## <a name="post-update-steps"></a>更新后步骤

- 安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅[修补程序](#hotfixes)以及我们的[服务策略](azure-stack-servicing-policy.md)。  

- 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](azure-stack-security-bitlocker.md)操作。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

<!-- 2930820 - IS ASDK --> 
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 不是 Azure Stack 中提供。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。 

<!-- 2931230 - IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 这两种订阅类型为“计量订阅”和“消耗订阅”。   从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。 

<!-- 3557860 - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- 1663805 - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是[使用 PowerShell 验证权限](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)。

<!-- Daniel 3/28 -->
- 在用户门户上导航到存储帐户中的某个 Blob 并尝试从导航树中打开“访问策略”时，后续的窗口无法加载。  若要解决此问题，可以分别使用以下 PowerShell cmdlet 来创建、检索、设置和删除访问策略：

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

<!-- ### Health and monitoring -->

### <a name="compute"></a>计算

- 创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。 若要解决此问题，请使用以前所用的同一名称重新创建存储帐户。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 虚拟机规模集创建体验提供了基于 CentOS 7.2，作为一个选项来部署。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。  

<!-- TBD - IS ASDK --> 
- 应用 1902 更新后，在部署包含托管磁盘的 VM 时可能会遇到以下问题：

   - 如果 1808年更新，使用托管磁盘部署 VM 之前创建的订阅可能会失败并显示内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。  依次选择“资源提供程序”、“Microsoft.Compute”、“重新注册”。   
      2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“Azure Stack - 托管磁盘”是否已列出。  
   - 如果已配置多租户环境中，在与来宾目录关联的订阅中部署虚拟机内部的错误消息可能会失败。 若要解决此错误，请按照中的步骤[这篇文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)重新配置每个来宾目录。

- 如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。 若要解决此问题，请在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。

- 无法删除规模集从**虚拟机规模集**边栏选项卡。 解决方法是，选择的小数位数设置你想要删除，然后单击**删除**按钮从**概述**窗格。

- 在 3 个容错域的可用性集中创建 Vm 并创建虚拟机规模集实例失败，并**FabricVmPlacementErrorUnsupportedFaultDomainSize**过程 4 节点 Azure Stack 上的更新过程中出错环境。 可以在可用性集的 2 个容错域已成功创建单个 Vm。 但是，在 4 节点 Azure Stack 上的更新过程中创建规模集实例是仍不可用。

### <a name="networking"></a>网络  

<!-- 3239127 - IS, ASDK -->
- 在 Azure Stack 门户中，对于已附加到 VM 实例的网络适配器，在更改与其绑定的 IP 配置的静态 IP 地址时，会看到一条警告消息，其中指出 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`。

    可以放心忽略此消息；即使 VM 实例未重启，IP 地址也会更改。

<!-- 3632798 - IS, ASDK -->
- 在门户中，如果添加入站安全规则并选择“服务标记”作为源，“服务标记”列表中会显示多个不适用于 Azure Stack 的选项。   在 Azure Stack 中有效的选项仅限以下几个：

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  在 Azure Stack 中，不支持将其他选项用作源标记。 同样，如果添加出站安全规则并选择“服务标记”作为目标，则显示与“源标记”相同的选项列表。   仅有的有效选项与“源标记”的有效选项相同，如以上列表中所述。 

- 网络安全组 (NSG) 无法像在全球 Azure 中一样在 Azure Stack 中运行。 在 Azure 中，可以在一个 NSG 规则中设置多个端口（使用门户、PowerShell 和资源管理器模板）。 但是，在 Azure Stack 中，无法通过门户在一个 NSG 规则中设置多个端口。 若要解决此问题，请使用资源管理器模板或 PowerShell 设置这些附加的规则。

<!-- 3203799 - IS, ASDK -->
- 目前，无论实例大小是什么，Azure Stack 都不支持将 4 个以上的网络接口 (NIC) 附加到 VM 实例。

- 在用户门户中，如果尝试添加**后端池**到**负载均衡器**，则操作将失败并显示错误消息**未能更新负载均衡器...** 若要解决此问题，请使用 PowerShell、 CLI 或 Azure 资源管理器模板与负载均衡器资源关联的后端池。

- 在用户门户中，如果您尝试创建**入站 NAT 规则**有关**负载均衡器**，则操作将失败并显示错误消息**未能更新负载均衡器...** 若要解决此问题，请使用 PowerShell、 CLI 或 Azure 资源管理器模板与负载均衡器资源关联的后端池。

- 在用户门户中，**创建负载均衡器**窗口将显示一个选项以创建**标准**负载均衡器 SKU。 在 Azure Stack 中不支持此选项。

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

可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1902 更新包。 

只有在联网场景中，Azure Stack 部署才会定期检查安全的终结点，并在已发布云更新的情况下自动通知你。 有关详细信息，请参阅[管理 Azure Stack 的更新](azure-stack-updates.md#using-the-update-tile-to-manage-updates)。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
