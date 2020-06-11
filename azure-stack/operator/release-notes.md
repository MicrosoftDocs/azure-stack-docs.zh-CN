---
title: Azure Stack Hub 发行说明
description: Azure Stack Hub 集成系统的发行说明，包括更新和 bug 修复。
author: sethmanheim
ms.topic: article
ms.date: 06/09/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 50ff7fc42cc6993c4ceeb009515cdad62ce980de
ms.sourcegitcommit: 396f79ce073d99d14fcc71b85c4a4932334832a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636880"
---
# <a name="azure-stack-hub-release-notes"></a>Azure Stack Hub 发行说明

本文介绍 Azure Stack Hub 更新包的内容。 此更新包括最新版 Azure Stack Hub 的改进和修复。

若要访问不同版本的发行说明，请使用左侧目录上方的版本选择器下拉列表。

::: moniker range=">=azs-1907"
> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack Hub 集成系统。 请勿将此更新程序包应用于 Azure Stack 开发工具包 (ASDK)。
::: moniker-end
::: moniker range="<azs-1907"
> [!IMPORTANT]  
> 如果 Azure Stack Hub 实例落后于两个以上的更新，则认为它不符合。 必须[至少更新到最低支持版本才能获得支持](azure-stack-servicing-policy.md#keep-your-system-under-support)。
::: moniker-end

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

有关对更新和更新过程进行故障排除的帮助，请参阅[对 Azure Stack Hub 的修补和更新问题进行故障排除](azure-stack-troubleshooting.md)。

## <a name="download-the-update"></a>下载更新

可从 [Azure Stack Hub 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack Hub 更新程序包。

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2002"
## <a name="2002-build-reference"></a>2002 内部版本参考

Azure Stack Hub 2002 更新内部版本号为 **1.2002.0.35**。

> [!IMPORTANT]  
> 在 Azure Stack 中心2002更新中，Microsoft 暂时扩展了我们的[Azure Stack 中心支持策略声明](azure-stack-servicing-policy.md)。  我们正在与世界各地应对 COVID-19 的客户合作，这些客户可能正在就其 Azure Stack Hub 系统以及如何更新和管理这些系统做出决定，以确保其数据中心业务操作继续正常运行。 为支持我们的客户，Microsoft 提供了临时支持策略更改扩展，以包括三个以前的更新版本。  因此，将支持新发布的 2002 更新以及以前的三个更新版本（例如 1910、1908 和 1907）之一。

### <a name="update-type"></a>更新类型

Azure Stack Hub 2002 更新内部版本类型为“完整”。

与以前的更新相比，2002 更新程序包更大。 增加的大小会导致下载时间更长。 此更新将长时间保留为“正在准备”状态，操作员可预期此过程所需的时间长于以前的更新。 在我们的内部测试中，2002 更新的预期运行时间如下 - 4 个节点：15-42 小时，8 个节点：20-50 小时，12 个节点：20-60 小时，16 个节点：25-70 小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。 运行时间短于或长于预期值并不常见。因此，除非更新失败，否则不需要 Azure Stack Hub 操作员执行操作。 此运行时近似值特定于 2002 更新，不应与其他 Azure Stack Hub 更新进行比较。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack Hub 中管理更新](azure-stack-updates.md)。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 提供了基于 AzureRM 的 Azure Stack Hub 管理员 PowerShell 模块的一个新版本 (1.8.1)。
- 将在 2020 年 4 月 15 日为 Azure Stack Hub 发布新的 Azure PowerShell 租户模块。 当前使用的 Azure RM 模块会继续工作，但在内部版本 2002 后将不再更新。
- 在 Azure Stack Hub 管理员门户上添加了新的警告警报，用于报告所配置的 syslog 服务器的连接问题。 警报标题是 **Syslog 客户端发送 Syslog 消息时遇到网络问题**。
- 在 Azure Stack Hub 管理员门户上添加了新的警告警报，用于报告网络时间协议 (NTP) 服务器的连接问题。 警报标题是 **[node name] 上的时间来源无效**。
- 由于 2002 中与 TLS 限制相关的一个中断性变更，[Java SDK](https://azure.microsoft.com/develop/java/) 发布了新的程序包。 你必须安装新的 Java SDK 依赖项。 可以在 [Java 和 API 版本配置文件](../user/azure-stack-version-profiles-java.md?view=azs-2002#java-and-api-version-profiles)中找到相关说明。
- 新版 (1.0.5.10) System Center Operations Manager - Azure Stack Hub MP 已发布，该版本是运行 2002 的所有系统所必需的，因为存在中断性 API 变更。 API 变更会影响备份和存储性能仪表板，建议你先将所有系统更新为 2002，然后再更新 MP。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 此更新包含对更新过程的更改，这些更改会显著提高将来的完整更新的性能。 这些更改将随 2002 发行版之后的下一次完整更新生效，具体目标是提高完整更新期间对主机操作系统进行更新的阶段的性能。 提高主机操作系统更新的性能会大大减小租户工作负荷在完整更新过程中受影响的时间窗口。
- Azure Stack Hub 就绪性检查器工具现在使用分配给 AD Graph 的所有 TCP IP 端口来验证 AD Graph 集成。
- 脱机联合工具已更新，改进了可靠性。 该工具在 GitHub 上不再可用，已[移到 PowerShell 库](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/)中。 有关详细信息，请参阅[将市场项下载到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)。
- 将引入一项新的监视功能。 针对物理主机和基础结构 VM 的磁盘空间不足警报将由平台自动修正。仅当此操作失败时，该警报才会显示在 Azure Stack Hub 管理员门户中，供操作员执行操作。
- 对[诊断日志收集](azure-stack-diagnostic-log-collection-overview-tzl.md)的改进。 新的体验优化和简化了诊断日志收集，它不需要预先配置 blob 存储帐户。 存储环境已预先配置，因此你可以在创建支持案例之前发送日志，并减少支持人员通话时间。
- [主动日志收集和按需日志收集](azure-stack-diagnostic-log-collection-overview-tzl.md)所用的时间已   减少80%。 日志收集时间比预期值长，但不需要 Azure Stack 中心操作员执行操作，除非日志收集失败。
- 启动更新后，“更新”边栏选项卡中会显示 Azure Stack Hub 更新程序包的下载进度。 这仅适用于那些选择[通过自动下载功能准备更新程序包](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages)且已连接的 Azure Stack Hub 系统。
- 改进了网络控制器主机代理的可靠性。
- 引入了一个名为 DNS Orchestrator 的新微服务，它改进了在修补和更新期间内部 DNS 服务的复原逻辑。
- 添加了一个新的请求验证，用于在创建 VM 时使启动诊断存储帐户参数的无效 blob URI 失败。
- 为 Rdagent 和主机代理（主机上用于方便执行 VM CRUD 操作的两个服务）添加了自动修正和日志记录改进。
- 向市场管理添加了一项新功能，使 Microsoft 能够添加属性，目的是防止管理员下载由于存在各种属性（例如 Azure Stack 版本或计费模型）而与其 Azure Stack 不兼容的市场产品。 只有 Microsoft 才能添加这些属性。 有关详细信息，请参阅[使用门户下载市场项](azure-stack-download-azure-marketplace-item.md#use-the-portal-to-download-marketplace-items)。

### <a name="changes"></a>更改

- 管理员门户现在会指示操作是否正在进行，并在 Azure Stack 区域旁显示一个图标。 当你将鼠标悬停在该图标上时，它会显示操作的名称。 这使你可以识别正在运行的系统后台操作；例如，可能会运行几个小时的备份作业或存储扩展。

- 以下管理员 API 已弃用：

  | 资源提供程序       | 资源              | 版本            |
  |-------------------------|-----------------------|--------------------|
  | Microsoft.Storage.Admin | 农场                 | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/acquisitions    | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/shares          | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/storageaccounts | 2015-12-01-preview |

- 以下管理员 API 已替换为较新版本 (2018-09-01)：

  | 资源提供程序      | 资源              | 版本    |
  |------------------------|-----------------------|------------|
  | Microsoft.Backup.Admin | backupLocation         | 2016-05-01 |
  | Microsoft.Backup.Admin | backups                | 2016-05-01 |
  | Microsoft.Backup.Admin | 操作             | 2016-05-01 |

- 使用 PowerShell 创建 Windows VM 时，如果希望 VM 部署扩展，请确保添加 `provisionvmagent` 标志。 如果没有此标志，则会创建不带来宾代理的 VM，这将移除部署 VM 扩展的功能：

   ```powershell
   $VirtualMachine = Set-AzureRmVMOperatingSystem `
     -VM $VirtualMachine `
     -Windows `
     -ComputerName "MainComputer" `
     -Credential $Credential -ProvisionVMAgent
  ```

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了在虚拟机的同一 NIC 上添加多个公共 IP 会导致 Internet 连接问题的问题。 现在，具有两个公共 IP 的 NIC 能够按预期方式工作。
- 修复了一个导致系统引发警报的问题，该警报指出需要配置 Azure AD 主目录。
- 修复了一个导致警报无法自动关闭的问题。 该警报指出必须配置 Azure AD 主目录，但是，即使问题得到缓解，该警报也不关闭。
- 修复了一个因更新资源提供程序存在内部故障而导致更新在更新准备阶段失败的问题。
- 修复了一个在执行 Azure Stack Hub 机密轮换后导致附加产品资源提供程序操作失败的问题。
- 修复了一个问题，该问题是由于 ERCS 角色的内存压力导致 Azure Stack Hub 更新失败的常见原因。
- 修复了“更新”边栏选项卡中的一个 bug：在 Azure Stack Hub 更新的准备阶段，更新状态显示为“正在安装”而不是“正在准备”。
- 修复了物理交换机上的 RSC 功能导致不一致并丢弃流经负载均衡器的流量的问题。 现在，RSC 功能默认处于禁用状态。
- 解决了 NIC 上的多个 IP 配置导致流量被误操作并阻止出站连接的问题。 
- 修复了在 NIC 的 MAC 地址被缓存的情况下将该地址分配给另一资源导致 VM 部署失败的问题。
- 修复了来自零售渠道的 Windows VM 映像无法通过 AVMA 激活其许可证的问题。
- 修复了当 VM 所请求的虚拟核心数等于节点的物理核心数时无法创建 VM 的问题。 我们现在允许 VM 的虚拟核心数等于或少于节点的物理核心数。
- 修复了不允许将许可证类型设置为“null”以将即用即付映像切换到 BYOL 的问题。
- 修复了一个问题，现在允许向 VM 规模集添加扩展。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack Hub 更新中的安全更新的信息，请参阅 [Azure Stack Hub 安全更新](release-notes-security-updates.md)。

## <a name="hotfixes"></a>修补程序

Azure Stack Hub 定期发布修补程序。 将 Azure Stack Hub 更新到 2002 之前，请务必先安装 1910 的最新 Azure Stack Hub 修补程序。

> [!NOTE]
> Azure Stack Hub 修补程序版本是累积性的；你只需安装最新的修补程序即可获取该版本的任何以前修补程序版本中包含的所有修补程序。

Azure Stack Hub 修补程序仅适用于 Azure Stack Hub 集成系统；请勿尝试在 ASDK 上安装修补程序。

有关修补程序的详细信息，请参阅[Azure Stack 集线器维护策略](azure-stack-servicing-policy.md#hotfixes)。

### <a name="prerequisites-before-applying-the-2002-update"></a>先决条件：应用 2002 更新之前

必须在包含以下修补程序的版本 1910 中应用 Azure Stack Hub 版本 2002：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1910.42.142](https://support.microsoft.com/help/4564874)

### <a name="after-successfully-applying-the-2002-update"></a>成功应用 2002 更新之后

安装此更新之后，请安装所有适用的修补程序。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.2002.28.93](https://support.microsoft.com/help/4562966)
::: moniker-end

::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>1910 内部版本参考

Azure Stack Hub 1910 更新内部版本号为 **1.1910.0.58**。

### <a name="update-type"></a>更新类型

从版本 1908 开始，运行 Azure Stack Hub 的底层操作系统已更新为 Windows Server 2019。 此更新可以实现核心基础增强，并将更多功能引入 Azure Stack Hub。

Azure Stack Hub 1910 更新内部版本类型为“快速”。

与以前的更新相比，1910 更新包更大，因此下载时间更长。 此更新将长时间保留为“正在准备”状态，操作员可预期此过程所需的时间长于以前的更新。 无论 Azure Stack Hub 环境中有多少个物理节点，完成 1910 更新的预估时间都大约为 10 小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。 运行时间超过预期值并不常见，除非更新失败，否则无需 Azure Stack Hub 操作员采取措施。 此运行时近似值特定于 1910 更新，不应与其他 Azure Stack Hub 更新进行比较。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack Hub 中管理更新](azure-stack-updates.md)。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 管理员门户现在会在区域属性菜单中显示特权终结点 IP 地址，以方便进行发现。 此外，还会显示当前配置的时间服务器和 DNS 转发器。 有关详细信息，请参阅[使用 Azure Stack Hub 中的特权终结点](azure-stack-privileged-endpoint.md)。

- 现在，在发生错误时，Azure Stack Hub 运行状况和监视系统可针对各种硬件组件引发警报。 这些警报需要额外的配置。 有关详细信息，请参阅[监视 Azure Stack Hub 硬件组件](azure-stack-hardware-monitoring.md)。

- [Azure Stack Hub 的 Cloud-init 支持](/azure/virtual-machines/linux/using-cloud-init)：Cloud-init 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 来安装程序包和写入文件，或者配置用户和安全性。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。 市场中的 Ubuntu 映像已更新为支持使用 cloud-init 进行预配。

- 与 Azure 一样，Azure Stack Hub 现在支持所有 Windows Azure Linux 代理版本。

- 提供了新版 Azure Stack Hub 管理员 PowerShell 模块。 <!-- For more information, see -->

- 已在特权终结点 (PEP) 中添加 **Set-AzSDefenderManualUpdate** cmdlet，用于为 Azure Stack Hub 基础结构中的 Windows Defender 定义配置手动更新。 有关详细信息，请参阅[更新 Azure Stack Hub 上的 Windows Defender Antivirus](azure-stack-security-av.md)。

- 已在特权终结点 (PEP) 中添加 **Get-AzSDefenderManualUpdate** cmdlet，用于为 Azure Stack Hub 基础结构中的 Windows Defender 定义检索手动更新配置。 有关详细信息，请参阅[更新 Azure Stack Hub 上的 Windows Defender Antivirus](azure-stack-security-av.md)。

- 已在特权终结点 (PEP) 中添加 **Set-AzSDnsForwarder** cmdlet，用于在 Azure Stack Hub 中更改 DNS 服务器的转发器设置。 有关 DNS 配置的详细信息，请参阅 [Azure Stack Hub 数据中心 DNS 集成](azure-stack-integrate-dns.md)。

- 已在特权终结点 (PEP) 中添加 **Get-AzSDnsForwarder** cmdlet，用于在 Azure Stack Hub 中检索 DNS 服务器的转发器设置。 有关 DNS 配置的详细信息，请参阅 [Azure Stack Hub 数据中心 DNS 集成](azure-stack-integrate-dns.md)。

- 添加了对使用[AKS 引擎](../user/azure-stack-kubernetes-aks-engine-overview.md)管理**Kubernetes 群集**的支持。 从此更新开始，客户可以部署生产 Kubernetes 群集。 AKS 引擎使用户能够：
  - 管理其 Kubernetes 群集的生命周期。 他们可以创建、更新和缩放群集。
  - 使用 AKS 和 Azure Stack 中心团队生成的托管映像维护其群集。
  - 利用 Azure 资源管理器集成的 Kubernetes 云提供程序，该提供程序使用本机 Azure 资源构建群集。
  - 在连接或断开 Azure Stack 集线器戳记中部署和管理其群集。
  - 使用 Azure 混合功能：
    - 与 Azure Arc 集成。
    - 与容器 Azure Monitor 集成。
  - 使用带有 AKS 引擎的 Windows 容器。
  - 接收针对其部署的 CSS 和工程支持。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- Azure Stack Hub 的功能已得到改进，可自动补救一些修补升级问题，过去，这些问题会导致更新失败，或者使操作员无法启动 Azure Stack Hub 更新。 因此，**Test-AzureStack -UpdateReadiness** 组中包含的测试较少。 有关详细信息，请参阅[验证 Azure Stack Hub 系统状态](azure-stack-diagnostic-test.md#groups)。 以下三项测试保留在 **UpdateReadiness** 组中：

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- 添加了审核规则来报告外部设备（例如 USB 密钥）装载到 Azure Stack Hub 基础结构节点的时间。 审核日志通过 syslog 发出，并显示为“Microsoft-Windows-Security-Auditing:6416|即插即用事件”。 有关如何配置 syslog 客户端的详细信息，请参阅 [Syslog 转发](azure-stack-integrate-security.md)。

- Azure Stack Hub 的内部证书即将改用 4096 位 RSA 密钥。 运行内部机密轮换会将旧的 2048 位证书替换为 4096 位长的证书。 有关 Azure Stack Hub 中的机密轮换的详细信息，请参阅[在 Azure Stack Hub 中轮换机密](azure-stack-rotate-secrets.md)。

- 将多个内部组件升级到符合国家安全系统委员会 - 政策 15（CNSSP-15，该政策提供使用公共标准来安全共享信息的最佳做法）的密码编译算法复杂性和密钥强度。 其中的改进包括，在 Kerberos 身份验证中使用 AES256，以及在 VPN 加密中使用 SHA384。 有关 CNSSP-15 的详细信息，请参阅[国家安全系统委员会的“政策”页](http://www.cnss.gov/CNSS/issuances/Policies.cfm)。

- 由于上述升级，Azure Stack Hub 现在对 IPsec/IKEv2 配置使用新的默认值。 Azure Stack Hub 端使用的新默认值如下：

   **IKE 阶段 1（主模式）参数**

   | Property              | Value|
   |-|-|
   | SDK 版本           | IKEv2 |
   |Diffie-Hellman 组   | ECP384 |
   | 身份验证方法 | 预共享密钥 |
   |加密和哈希算法 | AES256、SHA384 |
   |SA 生存期（时间）     | 28,800 秒|

   **IKE 阶段 2（快速模式）参数**

   | 属性| Value|
   |-|-|
   |SDK 版本 |IKEv2 |
   |加密和哈希算法（加密）     | GCMAES256|
   |加密和哈希算法（身份验证） | GCMAES256|
   |SA 生存期（时间）  | 27,000 秒  |
   |SA 生存期（千字节） | 33,553,408     |
   |完全向前保密 (PFS) | ECP384 |
   |死对等体检测 | 支持|

   [默认的 IPsec/IKE 提案](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)文档中也反映了这些更改。

- 基础结构备份服务改进了计算备份所需的可用空间的逻辑，而不是依赖固定的阈值。 该服务使用备份大小、保留策略、预留和外部存储位置的当前利用率，来确定是否需要对操作员引发警告。

### <a name="changes"></a>更改

- 将市场项从 Azure 下载到 Azure Stack Hub 时，可以使用新的用户界面来指定项的版本（如果存在多个版本时）。 新 UI 可用于联网场景和离线场景。 有关详细信息，请参阅[将市场项从 Azure 下载到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)。  

- 从版本 1910 开始，Azure Stack Hub 系统**需要**额外的 /20 专用内部 IP 空间。 有关详细信息，请参阅 [Azure Stack 的网络集成规划](azure-stack-network.md)。
  
- 如果在上传过程中外部存储位置耗尽了容量，基础结构备份服务将会删除部分上传的备份数据。  

- 基础结构备份服务将标识服务添加到 AAD 部署的备份有效负载。  

- Azure Stack Hub PowerShell 模块已更新为适用于版本 1910 的 1.8.0 版。<br>更改包括：
   - **新的 DRP 管理模块**：使用部署资源提供程序 (DRP) 能够以协调的方式将资源提供程序部署到 Azure Stack Hub。 这些命令与 Azure 资源管理器层交互，从而与 DRP 交互。
   - **BRP**： <br />
           - 支持 Azure stack 基础结构备份的单个角色还原。 <br />
           - 将参数 `RoleName` 添加到 cmdlet `Restore-AzsBackup`。
   - **FRP**：“驱动器”和“卷”资源的中断性变更，提供 API 版本 `2019-05-01`。  Azure Stack Hub 1910 和更高版本支持的功能： <br />
            - `ID`、`Name`、`HealthStatus` 和 `OperationalStatus` 的值已更改。 <br />
            - 支持“驱动器”资源的新属性 `FirmwareVersion`、`IsIndicationEnabled`、`Manufacturer`和 `StoragePool`。 <br />
            - 已弃用“驱动器”资源的属性 `CanPool` 和 `CannotPoolReason`；改用 `OperationalStatus`。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- 修复了以下问题：在 Azure Stack Hub 1904 版本之前部署的环境中无法强制实施 TLS 1.2 策略。
- 修复了以下问题：创建时已启用 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 从虚拟机规模集 UI 中删除了“重置密码”。
- 修复了以下问题：从门户删除负载均衡器不会删除基础结构层中的对象。
- 修复了以下问题：管理员门户上的网关池用量警报显示不正确的百分比。
<!-- Fixed an issue where adding more than one public IP on the same NIC on a Virtual Machine resulted in internet connectivity issues. Now, a NIC with two public IPs should work as expected.[This fix actually didn't go in 1910 due to build issues, commenting out until next build (2002) ] -->

## <a name="security-updates"></a>安全更新

有关此 Azure Stack Hub 更新中的安全更新的信息，请参阅 [Azure Stack Hub 安全更新](release-notes-security-updates.md)。

可从 [Qualys 网站](https://www.qualys.com/azure-stack/)下载此版本的 Qualys 漏洞报告。

## <a name="hotfixes"></a>修补程序

Azure Stack Hub 定期发布修补程序。 将 Azure Stack Hub 更新到 1910 之前，请务必先安装 1908 的最新 Azure Stack Hub 修补程序。

> [!NOTE]
> Azure Stack Hub 修补程序版本是累积性的；你只需安装最新的修补程序即可获取该版本的任何以前修补程序版本中包含的所有修补程序。

Azure Stack Hub 修补程序仅适用于 Azure Stack Hub 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1910-update"></a>先决条件：应用 1910 更新之前

必须在包含以下修补程序的版本 1908 中应用 Azure Stack 版本 1910：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1908.29.87](https://support.microsoft.com/help/4558083)

### <a name="after-successfully-applying-the-1910-update"></a>成功应用 1910 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1910.42.142](https://support.microsoft.com/help/4564874)
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908 内部版本参考

Azure Stack Hub 1908 更新内部版本号为 **1.1908.4.33**。

### <a name="update-type"></a>更新类型

对于 1908，运行 Azure Stack Hub 的底层操作系统已更新为 Windows Server 2019。 此更新可以实现核心基础增强，并将更多功能引入 Azure Stack Hub。

Azure Stack Hub 1908 更新内部版本类型为“完整”。 因此，1908 更新的运行时间比快速更新（例如 1906 和 1907）更久。 完整更新的确切运行时间通常取决于 Azure Stack Hub 实例包含的节点数目、租户工作负荷在系统上使用的容量、系统的网络连接（如果已连接到 Internet），以及系统的硬件配置。 在我们的内部测试中，1908 更新的预期运行时间如下：4 个节点 - 42 小时，8 个节点 - 50 小时，12 个节点 - 60 小时，16 个节点 - 70 小时。 更新运行时间超过这些预期值并不常见，因此，除了更新失败之外，无需要求 Azure Stack Hub 操作员执行操作。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack Hub 中管理更新](azure-stack-updates.md)。

- 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件配置。
- 运行时间超过预期值并不常见，除非更新失败，否则无需 Azure Stack Hub 操作员采取措施。
- 此运行时近似值特定于 1908 更新，不应与其他 Azure Stack Hub 更新进行比较。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 对于 1908，请注意，运行 Azure Stack Hub 的底层操作系统已更新为 Windows Server 2019。 此更新可以实现核心基础增强，并将更多功能引入 Azure Stack Hub。
- Azure Stack Hub 基础结构的所有组件现在都以 FIPS 140-2 模式运行。
- Azure Stack Hub 操作员现在可以删除门户用户数据。 有关详细信息，请参阅[从 Azure Stack Hub 中清除门户用户数据](azure-stack-portal-clear.md)。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->
- Azure Stack Hub 的静态数据加密已得到改进，可将机密持久保存到物理节点的硬件受信任平台模块 (TPM)。

### <a name="changes"></a>更改

- 硬件提供商将在 Azure Stack Hub 版本 1908 的同一发布时间发布 OEM 扩展包 2.1 或更高版本。 必须安装 OEM 扩展包 2.1 或更高版本才能使用 Azure Stack Hub 版本 1908。 有关如何下载 OEM 扩展包 2.1 或更高版本的详细信息，请与系统的硬件提供商联系，并参阅 [OEM 更新](azure-stack-update-oem.md#oem-contact-information)一文。  

### <a name="fixes"></a>修复项

- 修复了与将来的 Azure Stack Hub OEM 更新兼容的问题，以及使用客户用户映像进行 VM 部署的问题。 此问题是在 1907 中发现的，已在修补程序 [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44) 中予以修复  
- 修复了 OEM 固件更新的问题，并更正了 Fabric Ring Health 的 Test-AzureStack 中的诊断错误。 此问题是在 1907 中发现的，已在修补程序 [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35) 中予以修复
- 修复了 OEM 固件更新过程的问题。 此问题是在 1907 中发现的，已在修补程序 [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37) 中予以修复

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

## <a name="security-updates"></a>安全更新

有关此 Azure Stack Hub 更新中的安全更新的信息，请参阅 [Azure Stack Hub 安全更新](release-notes-security-updates.md)。

可从 [Qualys 网站](https://www.qualys.com/azure-stack/)下载此版本的 Qualys 漏洞报告。

## <a name="download-the-update"></a>下载更新

可从 [Azure Stack Hub 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack Hub 1908 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack Hub 定期发布修补程序。 将 Azure Stack Hub 更新到 1908 之前，请务必先安装 1907 的最新 Azure Stack Hub 修补程序。

Azure Stack Hub 修补程序仅适用于 Azure Stack Hub 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1908-update"></a>先决条件：应用 1908 更新之前

必须在包含以下修补程序的版本 1907 中应用 Azure Stack Hub 版本 1908：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub 修补程序 1.1907.29.80](https://support.microsoft.com/help/4555650)

Azure Stack Hub 1908 更新需要系统硬件提供商提供的 **Azure Stack Hub OEM 2.1 或更高版本**。 OEM 更新包括 Azure Stack Hub 系统硬件的驱动程序和固件更新。 有关应用 OEM 更新的详细信息，请参阅[应用 Azure Stack Hub 原始设备制造商更新](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>成功应用 1908 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1908.29.87](https://support.microsoft.com/help/4558083)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907 内部版本参考

Azure Stack Hub 1907 更新内部版本号为 **1.1907.0.20**。

### <a name="update-type"></a>更新类型

Azure Stack Hub 1907 更新内部版本类型为“快速”。 有关更新内部版本类型的详细信息，请参阅[管理 Azure Stack Hub 中的更新](azure-stack-updates.md)一文。 根据内部测试，完成 1907 更新所需的预期时间约为 13 个小时。

- 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件配置。
- 运行时间超过预期值并不常见，除非更新失败，否则无需 Azure Stack Hub 操作员采取措施。
- 此运行时近似值特定于 1907 更新，不应与其他 Azure Stack Hub 更新进行比较。

## <a name="whats-in-this-update"></a>此更新的内容

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 正式推出 Azure Stack Hub 诊断日志收集服务，以加速和改善诊断日志的收集。 Azure Stack 集线器诊断日志收集服务提供了一种简单的方法来收集和共享 Microsoft 客户支持服务（CSS）的诊断日志。 此诊断日志收集服务可在 Azure Stack Hub 管理员门户中提供新的用户体验，让操作员设置在引发特定的关键警报时自动将诊断日志上传到存储 Blob。 该服务还可用于按需执行相同的操作。 有关详细信息，请参阅[诊断日志收集](azure-stack-diagnostic-log-collection-overview.md)一文。

- 正式推出 Azure Stack Hub 网络基础结构验证作为 Azure Stack Hub 验证工具 **Test-AzureStack** 的一部分。 Azure Stack Hub 网络基础结构将成为 **Test-AzureStack** 的一部分，可识别 Azure Stack Hub 的网络基础结构是否发生故障。 此测试绕过 Azure Stack Hub 软件定义的网络来检查网络基础结构的连接。 它会演示如何从公共 VIP 连接到配置的 DNS 转发器、NTP 服务器和标识终结点。 此外，在使用 Azure AD 作为标识提供者时，它会检查与 Azure 的连接；使用 ADFS 时，它会检查与联合服务器的连接。 有关详细信息，请参阅 [Azure Stack Hub 验证工具](azure-stack-diagnostic-test.md)一文。

- 添加了内部机密轮换过程，以便在系统更新期间轮换内部 SQL TLS 证书。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- “Azure Stack Hub 更新”边栏选项卡现在会显示活动更新的“最后一个步骤已完成”时间。 转到“更新”边栏选项卡，然后单击某个正在运行的更新，即可查看此添加内容。 “最后一个步骤已完成”随后将显示在“更新运行详细信息”部分。 

- 改进了 **Start-AzureStack** 和 **Stop-AzureStack** 操作员操作。 Azure Stack Hub 的启动时间平均已减少 50%。 Azure Stack Hub 的关闭时间平均已减少 30%。 当缩放单元中的节点数目增加时，平均启动和关闭时间保持不变。

- 改进了已断开连接的市场工具的错误处理方式。 如果在使用 **Export-AzSOfflineMarketplaceItem** 时下载失败或部分成功，系统将显示详细的错误消息，其中包含有关错误的更详细信息和任何可行的缓解步骤。

- 改进了从大型页 Blob/快照创建托管磁盘的性能。 以前在创建大型磁盘时会触发超时。  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- 改进了关闭节点之前的虚拟磁盘运行状况检查，以避免发生意外的虚拟磁盘分离。

- 改进了管理员操作内部日志的存储方式。 此新增功能可以尽量减少内部日志进程使用的内存和存储，从而改进管理员操作期间的性能和可靠性。 你还可能会注意到，管理员门户中的更新边栏选项卡页加载时间有所改善。 作为此项改进的一部分，系统将不再提供超过 6 个月的更新日志。 如果需要这些更新的日志，请务必先针对超过 6 个月的所有已运行更新[下载摘要](azure-stack-apply-updates.md)，然后执行 1907 更新。

### <a name="changes"></a>更改

- Azure Stack Hub 版本 1907 包含警告警报，指示操作员在更新到版本 1908 之前，先将其系统的 OEM 包更新为版本 2.1 或更高版本。 有关如何应用 Azure Stack Hub OEM 更新的详细信息，请参阅[应用 Azure Stack Hub 原始设备制造商更新](azure-stack-update-oem.md)。

- 已添加新的出站规则 (HTTPS) 来启用 Azure Stack Hub 诊断日志收集服务的通信。 有关详细信息，请参阅 [Azure Stack Hub 数据中心集成 - 发布终结点](azure-stack-integrate-endpoints.md#ports-and-urls-outbound)。

- 现在，如果外部存储位置耗尽了容量，基础结构备份服务将会删除部分上传的备份。

- 基础结构备份不再包含域服务数据的备份。 此项更改仅适用于使用 Azure Active Directory 作为标识提供者的系统。

- 我们现在会验证引入到“计算”->“VM 映像”边栏选项卡中的映像是否为页 Blob 类型。

- 特权终结点命令 **Set-BmcCredential** 现在会更新基板管理控制器中的凭据。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->
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

有关此 Azure Stack Hub 更新中的安全更新的信息，请参阅 [Azure Stack Hub 安全更新](release-notes-security-updates.md)。

可从 [Qualys 网站](https://www.qualys.com/azure-stack/)下载此版本的 Qualys 漏洞报告。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可从 [Azure Stack Hub 下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack Hub 1907 更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack Hub 定期发布修补程序。 将 Azure Stack Hub 更新到 1907 之前，请务必先安装 1906 的最新 Azure Stack Hub 修补程序。

Azure Stack Hub 修补程序仅适用于 Azure Stack Hub 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1907-update"></a>应用 1907 更新之前

必须在包含以下修补程序的版本 1906 中应用 Azure Stack Hub 版本 1907：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub 修补程序 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>成功应用 1907 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub 修补程序 1.1907.29.80](https://support.microsoft.com/help/4555650)
::: moniker-end

::: moniker range=">=azs-1907"
## <a name="automatic-update-notifications"></a>自动更新通知

其系统可从基础结构网络访问 Internet 的客户在操作员门户中会看到“有可用的更新”消息。 无法访问 Internet 的系统可以下载并导入包含相应 .xml 的 .zip 文件。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack Hub 修补程序的最新信息：
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archive

若要访问旧版的已存档发行说明，请使用左侧目录上方的版本选择器下拉列表选择要查看的版本。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack Hub 中更新管理的概述，请参阅[在 Azure Stack Hub 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack Hub 中应用更新的详细信息，请参阅[在 Azure Stack Hub 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack Hub 的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack Hub 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack Hub 中的更新](azure-stack-monitor-update.md)。
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1906"
## <a name="1906-archived-release-notes"></a>1906 已存档的发行说明
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>1905 已存档的发行说明
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904 已存档的发行说明
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903 已存档的发行说明
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902 已存档的发行说明
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901 已存档的发行说明
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811 已存档的发行说明
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809 已存档的发行说明
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808 已存档的发行说明
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807 已存档的发行说明
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805 已存档的发行说明
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804 已存档的发行说明
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803 已存档的发行说明
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802 已存档的发行说明
::: moniker-end

::: moniker range="<azs-1907"
可以访问 [TechNet 库中旧版本 Azure Stack Hub 的发行说明](https://aka.ms/azsarchivedrelnotes)。 提供这些已存档文档仅供参考，并不意味着支持这些版本。 有关 Azure Stack Hub 支持的信息，请参阅 [Azure Stack Hub 服务策略](azure-stack-servicing-policy.md)。 如需进一步的帮助，请联系 Microsoft 客户支持服务。
::: moniker-end


