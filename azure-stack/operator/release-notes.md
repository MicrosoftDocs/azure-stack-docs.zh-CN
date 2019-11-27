---
title: Azure Stack 发行说明 |Microsoft Docs
description: 了解 Azure Stack 集成系统的更新，包括新功能以及下载更新的位置。
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
ms.date: 11/25/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/22/2019
ms.openlocfilehash: a0e925d0c7a8401ea6d3f14f82cdb01bba4b354f
ms.sourcegitcommit: 55ec59f831a98c42a4e9ff0dd954bf10adb98ff1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74540341"
---
# <a name="azure-stack-updates-release-notes"></a>Azure Stack 更新：发行说明

*适用于：Azure Stack 集成系统*

本文介绍 Azure Stack 更新包的内容。 此更新包括新的改进，以及此 Azure Stack 版本的修复。

若要访问不同版本的发行说明，请使用左侧目录上方的 "版本选择器" 下拉列表。

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> 如果 Azure Stack 实例超过两个更新，则会将其视为不符合。 必须[至少更新到支持的最低版本](azure-stack-servicing-policy.md#keep-your-system-under-support)。
::: moniker-end

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

有关更新和更新过程故障排除的帮助，请参阅[解决 Azure Stack 的修补程序和更新问题](azure-stack-updates-troubleshoot.md)。

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>1910生成引用

Azure Stack 1910 更新内部版本号为**1.1910.0.58**。

### <a name="update-type"></a>更新类型

从1908开始，Azure Stack 运行的基础操作系统已更新到 Windows Server 2019。 这可以实现核心基础增强，并在不久的将来将更多功能引入 Azure Stack。

Azure Stack 1910 更新生成类型为**Express**。

与以前的更新相比，1910更新包的大小较大。 增加的大小会导致下载时间较长。 此更新将在**准备**状态中保留较长时间，操作员预期此过程所花费的时间比以前的更新要长。 完成1910更新所需的预期时间大约为10小时，而不考虑 Azure Stack 环境中的物理节点数。 确切的更新运行时通常取决于您的系统上使用的容量、租户工作负荷、系统网络连接（如果已连接到 internet）以及系统硬件规范。 运行时间超过预期值并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。 此运行时近似值特定于1910更新，不应与其他 Azure Stack 更新进行比较。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack 中管理更新](azure-stack-updates.md)。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 管理员门户现在在 "区域属性" 菜单中显示特权终结点 IP 地址，以方便发现。 此外，它还显示当前配置的时间服务器和 DNS 转发器。

- 如果发生错误，Azure Stack 运行状况和监视系统现在可以为各种硬件组件发出警报。 这需要其他配置。 有关详细信息，请参阅[监视 Azure Stack 硬件组件](azure-stack-hardware-monitoring.md)。

- [Azure Stack 的 cloud init 支持](/azure/virtual-machines/linux/using-cloud-init)：云初始化是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。 已更新 marketplace 中的 Ubuntu 映像，以支持云初始化，以便进行设置。

- Azure Stack 现在支持 Azure 中的所有 Microsoft Azure Linux 代理版本。

- Azure Stack 管理 PowerShell 模块的新版本可用。 <!-- For more information, see -->

- 在特权终结点（PEP）中添加了**AzSDefenderManualUpdate** cmdlet，以在 Azure Stack 基础结构中配置 Windows Defender 定义的手动更新。 有关详细信息，请参阅[更新 Azure Stack 上的 Windows Defender 防病毒](azure-stack-security-av.md)。

- 在特权终结点（PEP）中添加了**AzSDefenderManualUpdate** cmdlet，以检索 Azure Stack 基础结构中 Windows Defender 定义的手动更新的配置。 有关详细信息，请参阅[更新 Azure Stack 上的 Windows Defender 防病毒](azure-stack-security-av.md)。

- 在特权终结点（PEP）中添加了**AzSDnsForwarder** cmdlet，以在 Azure Stack 中更改 DNS 服务器的转发器设置。 有关 DNS 配置的详细信息，请参阅[Azure Stack DATACENTER DNS 集成](azure-stack-integrate-dns.md)。

- 在特权终结点（PEP）中添加了**AzSDnsForwarder** cmdlet，以检索 AZURE STACK 中 DNS 服务器的转发器设置。 有关 DNS 配置的详细信息，请参阅[Azure Stack DATACENTER DNS 集成](azure-stack-integrate-dns.md)。


### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 添加了审核规则，以便在将外部设备（例如 USB 密钥）装入到 Azure Stack 基础结构的节点时进行报告。 审核日志通过 syslog 发出，并显示为**Microsoft Windows 安全审核： 6416 |即插即用事件**。 有关如何配置 syslog 客户端的详细信息，请参阅[syslog 转发](azure-stack-integrate-security.md)。

- 对于内部证书，Azure Stack 将转移到4096位 RSA 密钥。 运行内部机密轮换会将旧的2048位证书替换为4096位长证书。 有关 Azure Stack 中的机密旋转的详细信息，请参阅[在 Azure Stack 中轮换机密](azure-stack-rotate-secrets.md)。

- 升级到多个内部组件的加密算法和密钥强度的复杂性，以遵守国家安全系统上的委员会-Policy 15 （CNSSP-15），其中提供了使用公共标准实现安全的最佳实践信息共享。 在改进中，Kerberos 身份验证和用于 VPN 加密的 SHA384 AES256。 有关 CNSSP-15 的详细信息，请参阅[国家安全系统上的委员会 "策略" 页](http://www.cnss.gov/CNSS/issuances/Policies.cfm)。

- 由于上述升级，Azure Stack 现在为 IPsec/IKEv2 配置提供了新的默认值。 Azure Stack 端使用的新默认值如下所示：

   **IKE 阶段1（主模式）参数**

   | 属性              | 值|
   |-|-|
   | SDK 版本           | IKEv2 |
   |Diffie-Hellman 组   | ECP384 |
   | 身份验证方法 | 预共享密钥 |
   |加密和哈希算法 | AES256、SHA384 |
   |SA 生存期（时间）     | 28,800 秒|

   **IKE 阶段2（快速模式）参数**

   | 属性| 值|
   |-|-|
   |SDK 版本 |IKEv2 |
   |加密和哈希算法（加密）     | GCMAES256|
   |加密和哈希算法（身份验证） | GCMAES256|
   |SA 生存期（时间）  | 27,000 秒  |
   |SA 生存期（千字节） | 33,553,408     |
   |完全向前保密 (PFS) | ECP384 |
   |死对等体检测 | 支持|

   这些更改也会反映在[默认的 IPsec/IKE 建议](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)文档中。

- 基础结构备份服务改进了计算需要的可用空间以进行备份的逻辑，而不是依赖固定的阈值。 服务将使用外部存储位置的备份、保留策略、保留和当前使用率的大小来确定是否需要向 operater 发出警告。 

### <a name="changes"></a>更改

- 将 marketplace 项从 Azure 下载到 Azure Stack 时，有一个新的用户界面，当存在多个版本时，可以使用它来指定项的版本。 已连接和已断开连接的方案中都提供了新的 UI。 有关详细信息，请参阅[将 marketplace 项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)。  

- 从1910版本开始，Azure Stack 系统需要额外的/20 个专用内部 IP 空间。 此网络专用于 Azure Stack 系统，可在数据中心内的多个 Azure Stack 系统上重复使用。 网络专用于 Azure Stack 时，它不得与数据中心内的网络重叠。 /20 专用 IP 空间划分为多个网络，以便在容器上运行 Azure Stack 基础结构（如上文[1905 发行说明](release-notes.md?view=azs-1905)中所述）。 在容器中运行 Azure Stack 基础结构的目标是优化利用率和提高性能。 此外，还使用了/20 个专用 IP 空间来实现正在进行的工作，从而减少部署之前所需的可路由 IP 空间。

  - 请注意，在1910之后，/20 输入作为下一个 Azure Stack 更新的先决条件。 如果在1910之后发布了下一个 Azure Stack 更新，并且尝试安装该更新，则在更正步骤中，如果尚未完成/20 输入，则更新会失败。 在完成上述更正步骤之前，将在管理门户中显示警报。 请参阅[数据中心网络集成](azure-stack-network.md#private-network)一文，了解如何使用这个新的专用空间。 

  - 更正步骤：若要进行修正，请按照说明[打开 PEP 会话](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)。 准备大小为/20 的[专用内部 IP 范围](azure-stack-network.md#logical-networks)，并使用以下格式运行以下 cmdlet （仅从1910开始提供）： `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`。 如果操作成功执行，你将收到消息**Azs 将内部网络范围添加到配置**。如果成功完成，则警报将在管理门户中关闭。 Azure Stack 系统现在可以更新到下一个版本。
  
- 如果在上传过程中外部存储位置的容量用尽，则基础结构备份服务将删除部分上传的备份数据。  

- 基础结构备份服务将标识服务添加到 AAD 部署的备份负载。  

- Test-azurestack PowerShell 模块已更新为1910版本的1.8.0 版本。<br>更改包括：
   - **新的 DRP 管理模块**：部署资源提供程序（DRP）允许资源提供程序的协调部署 Azure Stack。 这些命令与 Azure 资源管理器层交互，以便与 DRP 交互。
   - **BRP**： <br />
           -支持符号 stack 基础结构备份的单一角色还原。 <br />
           -将参数 `RoleName` 添加到 cmdlet `Restore-AzsBackup`。
   - **FRP**： API 版本 `2019-05-01`的**驱动器**和**卷**资源的重大更改。 Azure Stack 1910 及更高版本支持以下功能： <br />
            -`ID`、`Name`、`HealthStatus` 和 `OperationalStatus` 的值已更改。 <br />
            -支持**驱动器**资源的 `FirmwareVersion`、`IsIndicationEnabled`、`Manufacturer`和 `StoragePool` 的新属性。 <br />
            -已不推荐使用**驱动器**资源的属性 `CanPool` 和 `CannotPoolReason`;改用 `OperationalStatus`。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了在 Azure Stack 1904 版本之前部署的环境上阻止强制执行 TLS 1.2 策略的问题。
- 解决了已启用 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录的问题。 
- 已从虚拟机规模集 UI 中删除**重置密码**。
- 解决了从门户中删除负载均衡器的问题不会导致在基础结构层中删除对象。
- 修复了在管理门户上显示不准确的网关池使用率警报百分比的问题。
- 修复了在虚拟机上的同一 NIC 上添加多个公共 IP 导致 internet 连接问题的问题。 现在，具有两个公共 Ip 的 NIC 应该按预期方式工作。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](release-notes-security-updates.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从[Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1910 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 在将 Azure Stack 更新为1910之前，请务必安装最新 Azure Stack 修补程序1908。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1910-update"></a>先决条件：在应用1910更新之前

1910版本的 Azure Stack 必须应用于带有以下修补程序的1908版本：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1908.9.43](https://support.microsoft.com/help/4531007)

### <a name="after-successfully-applying-the-1910-update"></a>成功应用1910更新后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- 没有可用于1910的修补程序。
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908生成引用

Azure Stack 1908 更新内部版本号为**1.1908.4.33**。

### <a name="update-type"></a>更新类型

对于 1908，运行 Azure Stack 的底层操作系统已更新为 Windows Server 2019。 这可以实现核心基础增强，并在不久的将来将更多功能引入 Azure Stack。

Azure Stack 1908 更新内部版本类型为“完整”。 因此，1908 更新的运行时间比快速更新（例如 1906 和 1907）更久。 完整更新的确切运行时间通常取决于 Azure Stack 实例包含的节点数目、租户工作负荷在系统上使用的容量、系统的网络连接（如果已连接到 Internet），以及系统的硬件配置。 1908更新具有以下所需的内部测试运行时：4个节点-42 小时、8个节点-50 小时、12个节点-60 小时、16个节点-70 小时。 更新运行时间超过预期值并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack 中管理更新](azure-stack-updates.md)。

- 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件配置。
- 运行时间超过预期并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。
- 此运行时近似值特定于 1908 更新，不应与其他 Azure Stack 更新进行比较。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 对于 1908，请注意，运行 Azure Stack 的底层操作系统已更新为 Windows Server 2019。 这可以实现核心基础增强，并在不久的将来将更多功能引入 Azure Stack。
- Azure Stack 基础结构的所有组件现在都以 FIPS 140-2 模式运行。
- Azure Stack 操作员现在可以删除门户用户数据。 有关详细信息，请参阅[从 Azure Stack 中清除门户用户数据](azure-stack-portal-clear.md)。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->
- Azure Stack 的静态数据加密已得到改进，可将机密持久保存到物理节点的硬件受信任平台模块 (TPM)。

### <a name="changes"></a>更改

- 硬件提供商将在 Azure Stack 版本 1908 的同一发布时间发布 OEM 扩展包 2.1 或更高版本。 必须安装 OEM 扩展包 2.1 或更高版本才能使用 Azure Stack 版本 1908。 有关如何下载 OEM 扩展包 2.1 或更高版本的详细信息，请与系统的硬件提供商联系，并参阅 [OEM 更新](azure-stack-update-oem.md#oem-contact-information)一文。  

### <a name="fixes"></a>修复项

- 修复了与未来 Azure Stack OEM 更新兼容的问题，以及使用客户用户映像的 VM 部署问题。 此问题是在 1907 中发现的，已在修补程序 [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44) 中予以修复  
- 修复了 OEM 固件更新的问题，并更正了 Fabric Ring Health 的 Test-AzureStack 中的诊断错误。 此问题是在 1907 中发现的，已在修补程序 [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35) 中予以修复
- 修复了 OEM 固件更新过程的问题。 此问题是在 1907 中发现的，已在修补程序 [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37) 中予以修复

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](release-notes-security-updates.md)。

## <a name="download-the-update-1908"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1908 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1908 之前，请务必先安装 1907 的最新 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1908-update"></a>先决条件：在应用1908更新之前

必须在包含以下修补程序的版本 1907 中应用 Azure Stack 版本 1908：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1907.18.56](https://support.microsoft.com/help/4528552)

Azure Stack 1908 更新需要系统硬件提供商提供的 **Azure Stack OEM 2.1 或更高版本**。 OEM 更新包括 Azure Stack 系统硬件的驱动程序和固件更新。 有关应用 OEM 更新的详细信息，请参阅[应用 Azure Stack 原始设备制造商更新](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>成功应用 1908 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1908.9.43](https://support.microsoft.com/help/4531007)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907生成引用

Azure Stack 1907 更新内部版本号为 **1.1907.0.20**。

### <a name="update-type"></a>更新类型

Azure Stack 1907 更新内部版本类型为“快速”。 有关更新生成类型的详细信息，请参阅[管理 Azure Stack 中的更新](azure-stack-updates.md)一文。 根据内部测试，完成 1907 更新所需的预期时间约为 13 个小时。

- 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件配置。
- 运行时间超过预期并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。
- 此运行时近似值特定于 1907 更新，不应与其他 Azure Stack 更新进行比较。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 正式推出 Azure Stack 诊断日志收集服务，以加速和改善诊断日志的收集。 Azure Stack 诊断日志收集服务提供了一种简单的方法来收集和共享与 Microsoft 客户支持服务（CSS）的诊断日志。 此诊断日志收集服务可在 Azure Stack 管理员门户中提供新的用户体验，让操作员设置在引发特定的关键警报时自动将诊断日志上传到存储 Blob，或按需执行相同的操作。 有关详细信息，请参阅[诊断日志收集](azure-stack-diagnostic-log-collection-overview.md)一文。

- 正式推出 Azure Stack 网络基础结构验证作为 Azure Stack 验证工具 **Test-AzureStack** 的一部分。 Azure Stack 网络基础结构将成为 **Test-AzureStack** 的一部分，可识别 Azure Stack 的网络基础结构是否发生故障。 此测试绕过 Azure Stack 软件定义的网络来检查网络基础结构的连接。 它会演示如何从公共 VIP 连接到配置的 DNS 转发器、NTP 服务器和标识终结点。 此外，在使用 Azure AD 作为标识提供者时，它会检查与 Azure 的连接；使用 ADFS 时，它会检查与联合服务器的连接。 有关详细信息，请参阅 [Azure Stack 验证工具](azure-stack-diagnostic-test.md)一文。

- 添加了内部机密轮换过程，以便在系统更新期间轮换内部 SQL TLS 证书。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- “Azure Stack 更新”边栏选项卡现在会显示活动更新的“最后一个步骤已完成”时间。 转到“更新”边栏选项卡，然后单击某个正在运行的更新，即可查看此信息。 “最后一个步骤已完成”随后将显示在“更新运行详细信息”部分。

- 改进了 **Start-AzureStack** 和 **Stop-AzureStack** 操作员操作。 Azure Stack 的启动时间平均已减少 50%。 Azure Stack 的关闭时间平均已减少 30%。 当缩放单元中的节点数目增加时，平均启动和关闭时间保持不变。

- 改进了已断开连接的市场工具的错误处理方式。 如果在使用 **Export-AzSOfflineMarketplaceItem** 时下载失败或部分成功，系统将显示详细的错误消息，其中包含有关错误和缓解步骤（如果有）的更详细信息。

- 改进了从大型页 Blob/快照创建托管磁盘的性能。 以前在创建大型磁盘时会触发超时。  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- 改进了关闭节点之前的虚拟磁盘运行状况检查，以避免发生意外的虚拟磁盘分离。

- 改进了管理员操作内部日志的存储方式。 这可以尽量减少内部日志进程使用的内存和存储，从而改进管理员操作期间的性能和可靠性。 你还可能会注意到，管理员门户中的更新边栏选项卡页加载时间有所改善。 作为此项改进的一部分，系统将不再提供超过 6 个月的更新日志。 如果需要这些更新的日志，请务必先针对超过 6 个月的所有已运行更新[下载摘要](azure-stack-apply-updates.md)，然后执行 1907 更新。

### <a name="changes"></a>更改

- Azure Stack 版本 1907 包含警告警报，指示操作员在更新到版本 1908 之前，先将其系统的 OEM 包更新为版本 2.1 或更高版本。 有关如何应用 Azure Stack OEM 更新的详细信息，请参阅[应用 Azure Stack 原始设备制造商更新](azure-stack-update-oem.md)。

- 已添加新的出站规则 (HTTPS) 来启用 Azure Stack 诊断日志收集服务的通信。 有关详细信息，请参阅 [Azure Stack 数据中心集成 - 发布终结点](azure-stack-integrate-endpoints.md#ports-and-urls-outbound)。

- 现在，如果外部存储位置耗尽了容量，基础结构备份服务将会删除部分上传的备份。

- 基础结构备份不再包含域服务数据的备份。 这仅适用于使用 Azure Active Directory 作为标识提供者的系统。

- 我们现在会验证引入到“计算”->“VM 映像”边栏选项卡中的映像是否为页 Blob 类型。

- 特权终结点命令**BmcCredential**现在将更新基板管理控制器中的凭据。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- 修复了在资源管理器模板中将发布者、套餐和 SKU 视为区分大小写的问题：除非映像参数的大小写与发布者、套餐和 SKU 的大小写相同，否则不会提取该映像进行部署。

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- 修复了因存储服务元数据备份期间超时而导致备份失败并显示 **PartialSucceeded** 错误消息的问题。  

- 修复了删除用户订阅导致孤立资源的问题。

- 修复了在创建套餐时不保存说明字段的问题。

- 修复了具有“只读”权限的用户能够创建、编辑和删除资源的问题。 现在，只有在获得“参与者”权限之后，用户才能创建资源。 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- 修复了由于 WMI 提供程序主机锁定 DLL 文件而导致更新失败的问题。

- 修复了更新服务中使得可用更新无法显示在更新磁贴或资源提供程序中的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4511282](https://support.microsoft.com/help/4511282/) 中予以修复。

- 修复了由于配置不当而导致管理平面变得不正常，从而导致更新失败的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4512794](https://support.microsoft.com/help/4512794/) 中予以修复。

- 修复了导致用户无法从市场完成第三方映像部署的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4511259](https://support.microsoft.com/help/4511259/) 中予以修复。

- 修复了用户映像管理器服务崩溃可能导致无法从托管映像创建 VM 的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4512794](https://support.microsoft.com/help/4512794/) 中予以修复

- 修复了由于应用程序网关缓存未按预期刷新而导致 VM CRUD 操作失败的问题。 此问题是在 1906 中发现的，已在修补程序 [KB4513119](https://support.microsoft.com/en-us/help/4513119/) 中予以修复

- 修复了运行状况资源提供程序中的一个问题，该问题会影响管理员门户中区域和警报边栏选项卡的可用性。 此问题是在 1906 中发现的，已在修补程序 [KB4512794](https://support.microsoft.com/help/4512794) 中予以修复。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](release-notes-security-updates.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1907 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1907 之前，请务必先安装 1906 的最新 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1907-update"></a>应用 1907 更新之前

必须在包含以下修补程序的版本 1906 中应用 Azure Stack 版本 1907：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>成功应用 1907 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1907.18.56](https://support.microsoft.com/help/4528552)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>1906生成引用

Azure Stack 1906 更新内部版本号为 **1.1906.0.30**。

### <a name="update-type"></a>更新类型

Azure Stack 1906 更新内部版本类型为“快速”。 有关更新生成类型的详细信息，请参阅[管理 Azure Stack 中的更新](azure-stack-updates.md)一文。 无论 Azure Stack 环境中有多少个物理节点，完成 1906 更新的预估时间都大约为 10 小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。 运行时间超过预期值并不常见，因此，除了更新失败之外，无需要求 Azure Stack 操作员执行操作。 此运行时近似值特定于 1906 更新，不应与其他 Azure Stack 更新进行比较。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- 在特权终结点 (PEP) 中添加 **Set-TLSPolicy** cmdlet，以在所有终结点上强制实施 TLS 1.2。 有关详细信息，请参阅 [Azure Stack 安全控制](azure-stack-security-configuration.md)。

- 在特权终结点 (PEP) 中添加 **Get-TLSPolicy** cmdlet，以检索应用的 TLS 策略。 有关详细信息，请参阅 [Azure Stack 安全控制](azure-stack-security-configuration.md)。

- 添加了内部机密轮换过程，以便在系统更新期间轮换内部 TLS 证书。

- 添加了一项保护措施，以便在机密过期的重大警报遭到忽略时，通过强制实施内部机密轮换来防止内部机密过期。 在日常运营过程中不应依赖此措施。 应在维护时段规划机密轮换。 有关详细信息，请参阅 [Azure Stack 机密轮换](azure-stack-rotate-secrets.md)。

- 使用 AD FS 的 Azure Stack 部署现在支持 Visual Studio Code。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 特权终结点中的 **Get-GraphApplication** cmdlet 现在显示当前使用的证书的指纹。 使用 AD FS 部署 Azure Stack 时，这可以改善服务主体的证书管理。

- 添加了运行状况监视规则来验证 AD Graph 和 AD FS 的可用性，包括引发警报的功能。

- 改善了基础结构备份服务移到另一个实例时的备份资源提供程序可靠性。

- 优化了外部机密轮换过程的性能，提供了统一的执行时间以简化维护时段的计划。

- **Test-AzureStack** cmdlet 现在会报告即将过期的内部机密（关键警报）。

- 特权终结点中的 **Register-CustomAdfs** cmdlet 有新参数可用，可让你在设置 AD FS 联合信任时跳过证书吊销列表的检查。

- 版本 1906 引入了更高的更新进度可见性，让你确保更新不会暂停。 操作员可在“更新”边栏选项卡中看到更多的更新步骤总数。 你还可能会发现，与以前的更新相比，现在有更多的更新步骤同时进行。

#### <a name="networking-updates"></a>网络更新

- 将 DHCP 响应程序中设置的租约时间更新为与 Azure 一致。

- 改善了在发生资源部署失败的情况时资源提供程序的重试率。

- 从负载均衡器和公共 IP 中删除了**标准** SKU 选项，因为目前不支持该选项。

### <a name="changes"></a>更改

- 创建存储帐户的体验现在与 Azure 一致。

- 更改了内部机密过期的警报触发器：
  - 警告警报现在会在机密过期之前的 90 天引发。
  - 关键警报现在会在机密过期之前的 30 天引发。

- 更新了基础结构备份资源提供程序中的字符串以使用一致的术语。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了托管磁盘的 VM 大小调整因“内部操作错误”而失败的问题。

- 修复了以下问题：失败的用户映像创建使管理映像的服务处于不正常状态；这会导致无法删除失败的映像，并且无法创建新映像。 此问题也已在 1905 修补程序中得到修复。

- 现在，内部机密即将过期的活动警报在内部机密轮换成功执行之后会自动关闭。

- 修复了以下问题：如果更新运行超过 99 个小时，更新历史记录选项卡中的更新持续时间会去掉第一位数。

- “更新”边栏选项卡包含针对失败更新的“继续”选项。

- 在管理员和用户门户中修复了以下市场问题：Docker 扩展未正确从搜索中返回，并且无法采取进一步的措施，因为 Azure Stack 不提供此类措施。

- 修复了模板部署 UI 中的以下问题：如果模板名称以下划线束“_”开头，则不会填充参数。

- 修复了虚拟机规模集创建体验提供基于 CentOS 的 7.2 作为部署选项的问题。 Azure Stack 不提供 CentOS 7.2。 我们现在提供 Centos 7.5 作为部署选项

- 现在可以从“虚拟机规模集”边栏选项卡中删除规模集。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack 更新中的安全更新的信息，请参阅 [Azure Stack 安全更新](release-notes-security-updates.md)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可从 [Azure Stack 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1906 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1906 之前，请务必先安装 1905 的最新 Azure Stack 修补程序。 更新后，安装[适用于 1906 的任何修补程序](#after-successfully-applying-the-1906-update)。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1906-update"></a>应用 1906 更新之前

必须在包含以下修补程序的版本 1905 中应用 Azure Stack 版本 1906：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>成功应用 1906 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 修补程序1.1906.15.60](https://support.microsoft.com/help/4524559)
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。 无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>存档

若要访问较早版本的存档发行说明，请使用左侧目录上方的 "版本选择器" 下拉列表，然后选择要查看的版本。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>1905存档的发行说明
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904存档的发行说明
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903存档的发行说明
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902存档的发行说明
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901存档的发行说明
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811存档的发行说明
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809存档的发行说明
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808存档的发行说明
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807存档的发行说明
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805存档的发行说明
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804存档的发行说明
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803存档的发行说明
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802存档的发行说明
::: moniker-end

::: moniker range="<azs-1906"
你可以[在 TechNet 库上访问 Azure Stack 发行说明的旧版本](https://aka.ms/azsarchivedrelnotes)。 这些存档文档仅供参考目的提供，并不表示支持这些版本。 有关 Azure Stack 支持的信息, 请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。 如需进一步的帮助，请联系 Microsoft 客户支持服务。
::: moniker-end


