---
title: Azure Stack 集线器发行说明
description: Azure Stack 集线器集成系统的发行说明，包括更新和 bug 修复。
author: sethmanheim
ms.topic: article
ms.date: 03/20/2020
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: a819dedd695175d6b4ff28e72965c53969a2f9b2
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2020
ms.locfileid: "80069403"
---
# <a name="azure-stack-hub-release-notes"></a>Azure Stack 集线器发行说明

本文介绍 Azure Stack 中心更新包的内容。 此更新包括 Azure Stack 中心最新版本的改进和修补程序。

若要访问不同版本的发行说明，请使用左侧目录上方的 "版本选择器" 下拉列表。

::: moniker range=">=azs-1907"
> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集线器集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包（ASDK）。
::: moniker-end
::: moniker range="<azs-1907"
> [!IMPORTANT]  
> 如果 Azure Stack 集线器实例落后于两个以上的更新，则将其视为不符合。 必须[至少更新到支持的最低版本](azure-stack-servicing-policy.md#keep-your-system-under-support)。
::: moniker-end

## <a name="update-planning"></a>更新规划

在应用更新之前，请确保查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新前后的活动清单](release-notes-checklist.md)

有关更新和更新过程故障排除的帮助，请参阅[排查 Azure Stack 集线器的修补程序和更新问题](azure-stack-updates-troubleshoot.md)。

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2002"
## <a name="2002-build-reference"></a>2002生成引用

Azure Stack 中心2002更新内部版本号为**1.2002.12.59**。

> [!IMPORTANT]  
> 在 Azure Stack 中心2002更新中，Microsoft 暂时扩展了我们的[Azure Stack 中心支持策略声明](azure-stack-servicing-policy.md)。  我们正在与世界各地的客户合作，他们对 COVID-19 作出回应，他们可能会作出有关其 Azure Stack 中心系统的重要决策，以及如何更新和管理这些系统，因此确保其数据中心业务运营继续正常运行。 为支持我们的客户，Microsoft 提供了临时支持策略更改扩展，以包括三个以前的更新版本。  因此，将支持新发布的2002更新以及三个以前的更新版本（例如1910、1908和1907）。

### <a name="update-type"></a>更新类型

Azure Stack 中心2002更新生成类型已**满**。

与以前的更新相比，2002更新包的大小较大。 增加的大小会导致下载时间较长。 此更新将在**准备**状态中保留较长时间，操作员预期此过程所花费的时间比以前的更新要长。 2002更新在我们的内部测试-4 节点中具有以下所需的运行时：15-42 小时、8个节点：20-50 小时、12个节点：20-60 小时、16个节点：25-70 个小时。 确切的更新运行时通常取决于您的系统上使用的容量、租户工作负荷、系统网络连接（如果已连接到 internet）以及系统硬件规范。 更短或更长时间的运行时不太常见，因此，如果更新失败，则不需要 Azure Stack 中心操作员执行操作。 此运行时近似值特定于2002更新，不应与其他 Azure Stack 中心更新进行比较。

有关更新生成类型的详细信息，请参阅[管理 Azure Stack 集线器中的更新](azure-stack-updates.md)。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 提供基于 AzureRM Azure Stack 集线器管理 PowerShell 模块的新版本（1.8.1）。
- 在 Azure Stack 中心管理员门户上添加了新的警告警报，以报告有关配置的 syslog 服务器的连接问题。 警报标题是**syslog 客户端在发送 Syslog 消息时遇到网络问题**。
- 添加了有关网络时间协议（NTP）服务器的网络时间协议（NTP） Azure Stack 的新警告警报。 警报标题**在 [node name] 上的时间源无效**。
- [JAVA SDK](https://azure.microsoft.com/develop/java/)发布了新包，因为2002中的重大更改与 TLS 限制相关。 必须安装新的 Java SDK 依赖项。 可以在[Java 和 API 版本配置文件](../user/azure-stack-version-profiles-java.md?view=azs-1910#java-and-api-version-profiles)中找到相关说明。
- 由于中断 API 更改，所有运行2002的系统都提供了 System Center Operations Manager Azure Stack 中心 MP 的新版本（1.0.5.10）。 API 更改会影响备份和存储性能仪表板，并且建议你先将所有系统更新为2002，然后再更新 MP。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 此更新包含对更新过程的更改，可显著提高未来完全更新的性能。 在2002版本后，这些更改将在下一次完全更新后生效，尤其是提高更新主机操作系统的完整更新阶段的性能。 提高主机操作系统更新的性能会大大减少租户工作负荷在完全更新过程中受到影响的时间窗口。
- Azure Stack 集线器就绪检查器工具现在使用分配给 AD Graph 的所有 TCP IP 端口来验证 AD Graph 集成。
- 已更新了脱机联合工具的可靠性。 该工具在 GitHub 上不再可用，并已[移动到 PowerShell 库](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/)。 有关详细信息，请参阅[将 Marketplace 项下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)。
- 正在引入新的监视功能。 物理主机和基础结构 Vm 的低磁盘空间警报将由平台自动修正，并且仅当此操作失败时，该警报才会显示在 Azure Stack 中心管理员门户中，以供操作员执行操作。
- [诊断日志收集](azure-stack-diagnostic-log-collection-overview-tzl.md)改进。 新体验通过不必提前配置 blob 存储帐户来简化和简化诊断日志收集。 存储环境已预先配置，因此你可以在打开支持案例之前发送日志，并花费更少的时间来支持调用。
- [主动日志收集和按需日志收集](azure-stack-diagnostic-log-collection-overview-tzl.md) 所用的时间已降低80%。 日志收集时间比预期值长，但不需要 Azure Stack 中心操作员执行操作，除非日志收集失败。
- 开始更新后，"更新" 边栏选项卡中将显示 Azure Stack 中心更新包的下载进度。 这仅适用于选择[通过自动下载准备更新包](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages)的已连接 Azure Stack 集线器系统。
- 网络控制器主机代理的可靠性改进。
- 引入了一项名为 DNS 协调器的新的微服务，可提高修补和更新过程中内部 DNS 服务的复原逻辑。
- 添加了新的请求验证，以便在创建 Vm 时为启动诊断存储帐户参数失败 blob Uri 无效。
- 添加了 Rdagent 和主机代理的自动修正和日志记录改进，主机上的两个服务有助于 VM CRUD 操作。
- 向 marketplace 管理添加了一项新功能，该功能使管理员能够阻止管理员下载与其 Azure Stack 不兼容的 marketplace 产品，因为 Azure Stack 版本或计费模式等各种属性。

### <a name="changes"></a>更改

- 管理员门户现在指示操作是否正在进行，并在 Azure Stack 区域旁显示一个图标。 当你将鼠标悬停在该图标上时，它会显示操作的名称。 这使您可以确定运行的系统后台操作;例如，可以运行几个小时的备份作业或存储扩展。

- 以下管理员 Api 已弃用：

  | 资源提供程序       | 资源              | 版本            |
  |-------------------------|-----------------------|--------------------|
  | Microsoft. Admin | 农场                 | 2015-12-01-预览 |
  | Microsoft. Admin | 场/收购    | 2015-12-01-预览 |
  | Microsoft. Admin | 场/共享          | 2015-12-01-预览 |
  | Microsoft. Admin | 场/storageaccounts | 2015-12-01-预览 |

- 以下管理员 Api 已替换为较新版本（2018-09-01）：

  | 资源提供程序      | 资源              | 版本    |
  |------------------------|-----------------------|------------|
  | Microsoft. Admin | backupLocation         | 2016-05-01 |
  | Microsoft. Admin | 备份                | 2016-05-01 |
  | Microsoft. Admin | 操作             | 2016-05-01 |
  
### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了在虚拟机上的同一 NIC 上添加多个公共 IP 导致 internet 连接问题的问题。 现在，具有两个公共 Ip 的 NIC 会按预期方式工作。
- 修复了一个问题，该问题导致系统引发警报，指出需要配置 Azure AD home 目录。
- 修复了导致警报无法自动关闭的问题。 该警报指示必须配置 Azure AD 主目录，但在问题得到缓解后甚至不会关闭。
- 修复了因更新资源提供程序内部故障而导致更新准备阶段失败的问题。
- 修复了导致加载项资源提供程序操作在执行 Azure Stack 集线器机密旋转后失败的问题。
- 修复了由于 ERCS 角色的内存压力导致 Azure Stack 集线器更新失败的常见原因。
- 修复了更新边栏选项卡中的一个 bug，在该边栏选项卡中，更新状态显示为 "**正在安装**"，而不是在 Azure Stack 中心更新的准备阶段进行**准备**。
- 解决了物理交换机上的 RSC 功能正在创建任何不一致并删除流过负载均衡器的流量的问题。 默认情况下，RSC 功能处于禁用状态。
- 解决了向 VM 添加辅助 IP 导致 RDP 问题的问题。
- 修复了正在缓存 NIC 的 MAC 地址，并将该地址分配给另一个资源的问题导致 VM 部署失败。
- 修复了来自零售渠道的 Windows VM 映像无法通过 AVMA 激活的许可证的问题。
- 修复了 VM 请求的虚拟核心数等于节点的物理内核时，无法创建 Vm 的问题。 我们现在允许 Vm 的虚拟内核数等于或小于节点的物理内核数。
- 解决了我们不允许将许可证类型设置为 "null" 以将即用即付映像切换到 BYOL 的问题。
- 修复了一个问题，允许将扩展添加到 VM 规模集。

## <a name="security-updates"></a>安全更新

有关 Azure Stack 中心更新的安全更新的信息，请参阅[Azure Stack 中心安全更新](release-notes-security-updates.md)。

## <a name="update-planning"></a>更新规划

在应用更新之前，请确保查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新前后的活动清单](release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从[Azure Stack 中心下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 中心2002更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 中心定期发布修补程序。 将 Azure Stack 中心更新为2002之前，请务必安装最新的 Azure Stack 中心修补程序1910。

> [!NOTE]
> Azure Stack 中心修补程序版本是累积性的;只需安装最新的修补程序，即可获取该版本以前的任何修补程序版本中包含的所有修补程序。

Azure Stack 集线器修补程序仅适用于 Azure Stack 集线器集成系统;不要尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-2002-update"></a>先决条件：在应用2002更新之前

Azure Stack 集线器2002版必须应用于带有以下修补程序的1910版本：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1910.24.108](https://support.microsoft.com/help/4541350)

### <a name="after-successfully-applying-the-2002-update"></a>成功应用2002更新后

安装此更新后，安装任何适用的修补程序。 有关详细信息，请参阅[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- 没有适用于2002的 Azure Stack 集线器修补程序。
::: moniker-end

::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>1910生成引用

Azure Stack 中心1910更新内部版本号为**1.1910.0.58**。

### <a name="update-type"></a>更新类型

从1908开始，Azure Stack 中心运行的基础操作系统已更新到 Windows Server 2019。 此更新可实现核心基础增强功能，并能够将其他功能引入 Azure Stack 中心。

Azure Stack 中心1910更新生成类型为**Express**。

与以前的更新相比，1910更新包的大小较大，从而导致下载时间较长。 更新将保留较长时间的**准备**状态，并且操作员预期此过程需要的时间比以前的更新长。 完成1910更新的预计时间大约为10小时，而与 Azure Stack 中心环境中的物理节点数量无关。 确切的更新运行时通常取决于您的系统上使用的容量、租户工作负荷、系统网络连接（如果已连接到 internet）以及系统硬件规范。 持续时间比预期值长的运行时不太常见，因此，如果更新失败，则不需要 Azure Stack 中心操作员执行操作。 此运行时近似值特定于1910更新，不应与其他 Azure Stack 中心更新进行比较。

有关更新生成类型的详细信息，请参阅[管理 Azure Stack 集线器中的更新](azure-stack-updates.md)。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 管理员门户现在在 "区域属性" 菜单中显示特权终结点 IP 地址，以方便发现。 此外，它还显示当前配置的时间服务器和 DNS 转发器。 有关详细信息，请参阅[使用 Azure Stack 集线器中的特权终结点](azure-stack-privileged-endpoint.md)。

- 如果发生错误，Azure Stack 中心运行状况和监视系统现在可以为各种硬件组件发出警报。 这些警报需要其他配置。 有关详细信息，请参阅[监视 Azure Stack 集线器硬件组件](azure-stack-hardware-monitoring.md)。

- [Azure Stack 集线器的 cloud init 支持](/azure/virtual-machines/linux/using-cloud-init)：云初始化是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。 已更新 marketplace 中的 Ubuntu 映像，以支持云初始化，以便进行设置。

- Azure Stack 集线器现在支持 Azure 中的所有 Microsoft Azure Linux 代理版本。

- 提供了新版本的 Azure Stack 集线器管理 PowerShell 模块。 <!-- For more information, see -->

- 在特权终结点（PEP）中添加了**AzSDefenderManualUpdate** cmdlet，以在 Azure Stack 中心基础结构中配置 Windows Defender 定义的手动更新。 有关详细信息，请参阅[更新 Azure Stack 集线器上的 Windows Defender 防病毒](azure-stack-security-av.md)。

- 在特权终结点（PEP）中添加了**AzSDefenderManualUpdate** cmdlet，以检索 Azure Stack 中心基础结构中的 Windows Defender 定义的手动更新的配置。 有关详细信息，请参阅[更新 Azure Stack 集线器上的 Windows Defender 防病毒](azure-stack-security-av.md)。

- 在特权终结点（PEP）中添加了**AzSDnsForwarder** cmdlet，以更改 Azure Stack 集线器中的 DNS 服务器的转发器设置。 有关 DNS 配置的详细信息，请参阅[Azure Stack 中心数据中心 DNS 集成](azure-stack-integrate-dns.md)。

- 在特权终结点（PEP）中添加了**AzSDnsForwarder** cmdlet，以检索 Azure Stack 集线器中的 DNS 服务器的转发器设置。 有关 DNS 配置的详细信息，请参阅[Azure Stack 中心数据中心 DNS 集成](azure-stack-integrate-dns.md)。

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

- Azure Stack 中心提高了自动修正某些修补程序的能力，并更新了以前导致更新失败或阻止操作员启动 Azure Stack 中心更新的问题。 因此， **test-azurestack-UpdateReadiness**组中包含的测试越少。 有关详细信息，请参阅[验证 Azure Stack 集线器系统状态](azure-stack-diagnostic-test.md#groups)。 **UpdateReadiness**组中保留了以下三个测试：

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- 添加了审核规则，以便在将外部设备（例如 USB 密钥）装入到 Azure Stack 中心基础结构的节点时进行报告。 审核日志通过 syslog 发出，并显示为**Microsoft Windows 安全审核： 6416 |即插即用事件**。 有关如何配置 syslog 客户端的详细信息，请参阅[syslog 转发](azure-stack-integrate-security.md)。

- Azure Stack 集线器正在迁移到内部证书的4096位 RSA 密钥。 运行内部机密轮换会将旧的2048位证书替换为4096位长证书。 有关 Azure Stack 中心中的机密旋转的详细信息，请参阅[在 Azure Stack 中心中旋转机密](azure-stack-rotate-secrets.md)。

- 升级到多个内部组件的加密算法和密钥强度的复杂性，以遵守国家安全系统上的委员会-Policy 15 （CNSSP-15），其中提供了使用公共标准实现安全的最佳实践信息共享。 在改进中，Kerberos 身份验证和用于 VPN 加密的 SHA384 的 AES256。 有关 CNSSP-15 的详细信息，请参阅[国家安全系统上的委员会 "策略" 页](http://www.cnss.gov/CNSS/issuances/Policies.cfm)。

- 由于上述升级，Azure Stack 集线器现在具有新的 IPsec/IKEv2 配置默认值。 Azure Stack 中心端使用的新默认值如下所示：

   **IKE 阶段1（主模式）参数**

   | properties              | 值|
   |-|-|
   | SDK 版本           | IKEv2 |
   |Diffie-Hellman 组   | ECP384 |
   | 身份验证方法 | 预共享密钥 |
   |加密和哈希算法 | AES256、SHA384 |
   |SA 生存期（时间）     | 28,800 秒|

   **IKE 阶段2（快速模式）参数**

   | properties| 值|
   |-|-|
   |SDK 版本 |IKEv2 |
   |加密 & 哈希算法（加密）     | GCMAES256|
   |加密 & 哈希算法（身份验证） | GCMAES256|
   |SA 生存期（时间）  | 27,000 秒  |
   |SA 生存期（Kb） | 33553408     |
   |完全向前保密 (PFS) | ECP384 |
   |死对等体检测 | 支持|

   这些更改也会反映在[默认的 IPsec/IKE 建议](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)文档中。

- 基础结构备份服务改进了计算所需可用空间以进行备份的逻辑，而不是依赖固定阈值。 服务将使用外部存储位置的备份、保留策略、保留和当前使用率的大小来确定是否需要向操作员发出警告。

### <a name="changes"></a>更改

- 将 marketplace 项从 Azure 下载到 Azure Stack 中心时，有一个新的用户界面，可用于在多个版本存在时指定项的版本。 已连接和已断开连接的方案中都提供了新的 UI。 有关详细信息，请参阅[将 marketplace 项从 Azure 下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)。  

- 从1910版本开始，Azure Stack 集线器系统**需要**额外的/20 个专用内部 IP 空间。 此网络专用于 Azure Stack 中心系统，可在数据中心内的多个 Azure Stack 集线器系统上重复使用。 网络专用于 Azure Stack 集线器时，它不得与数据中心内的网络重叠。 /20 专用 IP 空间划分为多个网络，以便在容器上运行 Azure Stack 中心基础结构（如上文[1905 发行说明](release-notes.md?view=azs-1905)中所述）。 在容器中运行 Azure Stack 中心基础结构的目标是优化利用率和提高性能。 此外，还使用了/20 个专用 IP 空间来实现正在进行的工作，以便在部署之前减少所需的可路由 IP 空间。

  - 请注意，在1910之后，/20 输入作为下一个 Azure Stack 中心更新的先决条件。 当下一个 Azure Stack 中心更新1910版后，如果你尝试安装它，则更新将会失败，如果你尚未按照下述更正步骤中所述完成/20 输入。 在完成上述更正步骤之前，管理员门户中将显示一个警报。 请参阅[数据中心网络集成](azure-stack-network.md#private-network)一文，了解如何使用这个新的专用空间。

  - 更正步骤：若要进行修正，请按照说明[打开 PEP 会话](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)。 准备大小为/20 的[专用内部 IP 范围](azure-stack-network.md#logical-networks)，并使用以下示例在 PEP 会话中运行以下 cmdlet （仅从1910开始提供）： `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`。 如果成功执行了该操作，则会收到消息 Azs，其中**添加到配置的内部网络范围**。如果成功完成，则警报将在管理员门户中关闭。 Azure Stack 集线器系统现在可以更新到下一个版本。
  
- 如果在上传过程中外部存储位置的容量用尽，则基础结构备份服务将删除部分上传的备份数据。  

- 基础结构备份服务将标识服务添加到 AAD 部署的备份负载。  

- Azure Stack 中心 PowerShell 模块已更新为1910版本的版本1.8.0。<br>更改包括：
   - **新的 DRP 管理模块**：部署资源提供程序（DRP）支持将资源提供程序部署到 Azure Stack 中心。 这些命令与 Azure 资源管理器层交互，以便与 DRP 交互。
   - **BRP**： <br />
           -支持符号 stack 基础结构备份的单一角色还原。 <br />
           -将参数 `RoleName` 添加到 cmdlet `Restore-AzsBackup`。
   - **FRP**： API 版本 `2019-05-01`的**驱动器**和**卷**资源的重大更改。 Azure Stack 集线器1910和更高版本支持以下功能： <br />
            -`ID`、`Name`、`HealthStatus`和 `OperationalStatus` 的值已更改。 <br />
            -支持**驱动器**资源的 `FirmwareVersion`、`IsIndicationEnabled`、`Manufacturer`和 `StoragePool` 的新属性。 <br />
            -已不推荐使用**驱动器**资源的属性 `CanPool` 和 `CannotPoolReason`;改用 `OperationalStatus`。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- 修复了一个问题，该问题阻止在 Azure Stack 集线器1904版本之前部署的环境上强制执行 TLS 1.2 策略。
- 解决了启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录的问题。
- 已从虚拟机规模集 UI 中删除**重置密码**。
- 修复了从门户中删除负载平衡器的问题，而不会导致在基础结构层中删除对象。
- 修复了在管理员门户上显示不准确的网关池使用率警报百分比的问题。
<!-- Fixed an issue where adding more than one public IP on the same NIC on a Virtual Machine resulted in internet connectivity issues. Now, a NIC with two public IPs should work as expected.[This fix actually didn't go in 1910 due to build issues, commenting out until next build (2002) ] -->

## <a name="security-updates"></a>安全更新

有关 Azure Stack 中心更新的安全更新的信息，请参阅[Azure Stack 中心安全更新](release-notes-security-updates.md)。

## <a name="update-planning"></a>更新规划

在应用更新之前，请确保查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新前后的活动清单](release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从[Azure Stack 中心下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 中心1910更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 中心定期发布修补程序。 将 Azure Stack 中心更新为1910之前，请务必安装最新的 Azure Stack 中心修补程序1908。

> [!NOTE]
> Azure Stack 中心修补程序版本是累积性的;只需安装最新的修补程序，即可获取该版本以前的任何修补程序版本中包含的所有修补程序。

Azure Stack 集线器修补程序仅适用于 Azure Stack 集线器集成系统;不要尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1910-update"></a>先决条件：在应用1910更新之前

Azure Stack 集线器1910版必须应用于带有以下修补程序的1908版本：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1908.19.62](https://support.microsoft.com/help/4541349)

### <a name="after-successfully-applying-the-1910-update"></a>成功应用1910更新后

安装此更新后，安装任何适用的修补程序。 有关详细信息，请参阅[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1910.24.108](https://support.microsoft.com/help/4541350)
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908生成引用

Azure Stack 中心1908更新内部版本号为**1.1908.4.33**。

### <a name="update-type"></a>更新类型

对于1908，Azure Stack 中心运行的基础操作系统已更新为 Windows Server 2019。 此更新可实现核心基础增强功能，并能够将其他功能引入 Azure Stack 中心。

Azure Stack 中心1908更新生成类型已**满**。 因此，1908更新的运行时比快速更新（如1906和1907）长。 完全更新的确切运行时通常取决于 Azure Stack 中心实例包含的节点数、租户工作负荷在系统上使用的容量、系统的网络连接（如果已连接到 internet）以及系统硬件configuration. 1908更新具有以下所需的内部测试运行时：4个节点-42 小时、8个节点-50 小时、12个节点-60 小时、16个节点-70 小时。 更新运行时持续时间比预期值长不太常见，无需 Azure Stack 中心操作员执行操作，除非更新失败。

有关更新生成类型的详细信息，请参阅[管理 Azure Stack 集线器中的更新](azure-stack-updates.md)。

- 确切的更新运行时通常取决于您的系统上使用的容量、租户工作负荷、系统网络连接（如果已连接到 internet）以及系统硬件配置。
- 持续时间比预期长的运行时不太常见，因此，如果更新失败，则不需要 Azure Stack 中心操作员执行操作。
- 此运行时近似值特定于1908更新，不应与其他 Azure Stack 中心更新进行比较。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 对于1908，请注意，Azure Stack 中心运行的基础操作系统已更新为 Windows Server 2019。 此更新可实现核心基础增强功能，并能够将其他功能引入 Azure Stack 中心。
- Azure Stack 集线器基础结构的所有组件现在在 FIPS 140-2 模式下运行。
- Azure Stack 中心操作员现在可以删除门户用户数据。 有关详细信息，请参阅[从 Azure Stack 中心清除门户用户数据](azure-stack-portal-clear.md)。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->
- 对 Azure Stack 集线器静态加密数据的改进，将机密保存到物理节点的硬件受信任的平台模块（TPM）中。

### <a name="changes"></a>更改

- 硬件提供程序将发布 OEM 扩展包2.1 或更高版本（Azure Stack 集线器版本1908）。 OEM 扩展包2.1 或更高版本是 Azure Stack 集线器版本1908的必备组件。 有关如何下载 OEM 扩展包2.1 或更高版本的详细信息，请与系统的硬件提供商联系，并参阅[OEM 更新](azure-stack-update-oem.md#oem-contact-information)一文。  

### <a name="fixes"></a>修复项

- 修复了与未来 Azure Stack 中心 OEM 更新兼容的问题，以及使用客户用户映像的 VM 部署问题。 此问题已在1907修补程序[KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)中发现和修复。  
- 修复了在 Test-azurestack for Fabric 环形运行状况中，OEM 固件更新和更正后的 misdiagnosis 的问题。 此问题已在1907修补程序[KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)中发现和修复。
- 修复了 OEM 固件更新过程的问题。 此问题已在1907修补程序[KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)中发现和修复。

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

## <a name="security-updates"></a>安全更新

有关 Azure Stack 中心更新的安全更新的信息，请参阅[Azure Stack 中心安全更新](release-notes-security-updates.md)。

## <a name="download-the-update"></a><a name="download-the-update-1908"></a>下载更新

可以从[Azure Stack 中心下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 中心1908更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 中心定期发布修补程序。 将 Azure Stack 中心更新为1908之前，请务必安装最新的 Azure Stack 中心修补程序1907。

Azure Stack 集线器修补程序仅适用于 Azure Stack 集线器集成系统;请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1908-update"></a>先决条件：在应用1908更新之前

Azure Stack 集线器1908版必须应用于带有以下修补程序的1907版本：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1907.26.70](https://support.microsoft.com/help/4541348)

Azure Stack 集线器1908更新需要从系统的硬件提供程序**Azure Stack 集线器 OEM 版本2.1 或更高版本**。 OEM 更新包括 Azure Stack 集线器系统硬件的驱动程序和固件更新。 有关应用 OEM 更新的详细信息，请参阅[Apply Azure Stack Hub 原始设备制造商更新](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>成功应用1908更新后

安装此更新后，安装任何适用的修补程序。 有关详细信息，请参阅[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1908.19.62](https://support.microsoft.com/help/4541349)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907生成引用

Azure Stack 中心1907更新内部版本号为**1.1907.0.20**。

### <a name="update-type"></a>更新类型

Azure Stack 中心1907更新生成类型为**Express**。 有关更新生成类型的详细信息，请参阅[管理 Azure Stack 集线器中的更新](azure-stack-updates.md)一文。 基于内部测试，完成1907更新所需的预期时间大约为13小时。

- 确切的更新运行时通常取决于您的系统上使用的容量、租户工作负荷、系统网络连接（如果已连接到 internet）以及系统硬件配置。
- 持续时间比预期长的运行时不太常见，因此，如果更新失败，则不需要 Azure Stack 中心操作员执行操作。
- 此运行时近似值特定于1907更新，不应与其他 Azure Stack 中心更新进行比较。

## <a name="whats-in-this-update"></a>此更新中的内容

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- Azure Stack 集线器诊断日志收集服务的公开发行版，以便于和改善诊断日志收集。 Azure Stack 集线器诊断日志收集服务提供了一种简单的方法来收集和共享 Microsoft 客户支持服务（CSS）的诊断日志。 此诊断日志收集服务在 Azure Stack 中心管理员门户中提供了新的用户体验，使操作员能够在引发某些关键警报时，将诊断日志自动上传到存储 blob。 该服务还可用于按需执行相同的操作。 有关详细信息，请参阅[诊断日志收集](azure-stack-diagnostic-log-collection-overview.md)一文。

- 作为 Azure Stack 中心验证工具**test-azurestack**的一部分的 Azure Stack 集线器网络基础结构验证的正式发行版。 Azure Stack 中心网络基础结构将是**test-azurestack**的一部分，用于确定 Azure Stack 中心的网络基础结构是否发生了故障。 测试通过绕过 Azure Stack 集线器软件定义的网络来检查网络基础结构的连接性。 它演示从公共 VIP 连接到已配置的 DNS 转发器、NTP 服务器和标识终结点。 当使用 Azure AD 作为标识提供程序时，它还会检查与 Azure 的连接性，如果使用 ADFS，还会检查联合服务器。 有关详细信息，请参阅[Azure Stack 中心验证工具](azure-stack-diagnostic-test.md)一文。

- 添加了内部机密旋转过程，以便在系统更新过程中根据需要轮替内部 SQL TLS 证书。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- "Azure Stack 中心更新" 边栏选项卡现在将显示活动更新的**最后一个步骤完成**时间。 可以通过转到 "更新" 边栏选项卡并单击正在运行的更新来查看此添加。 **最后一个步骤完成**后，将在 "**更新运行详细信息**" 部分中找到。

- **Test-azurestack**和**test-azurestack**运算符操作的改进。 开始 Azure Stack 集线器的时间已降低平均50%。 关闭 Azure Stack 集线器的时间已降低平均30%。 平均启动和关闭时间与缩放单位中的节点数相同。

- 改善了断开连接的 Marketplace 工具的错误处理。 如果在使用**AzSOfflineMarketplaceItem**时下载失败或部分成功，则会显示详细的错误消息，其中包含有关错误的详细信息以及任何可能的缓解步骤。

- 提高了从大型页面 blob/快照创建托管磁盘的性能。 以前，它在创建大磁盘时触发了超时。  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- 改善了虚拟磁盘运行状况检查，然后关闭节点，以避免意外的虚拟磁盘分离。

- 改善了用于管理员操作的内部日志的存储。 这种补充会使内部日志进程的内存和存储消耗降到最低，从而提高了管理员操作期间的性能和可靠性。 你可能还会注意到在管理员门户中的 "更新" 边栏选项卡上的改进页面加载时间。 作为此改进的一部分，超过6个月的更新日志将不再出现在系统中。 如果需要这些更新的日志，请确保在执行1907更新之前，下载所有超过6个月的更新运行[的摘要](azure-stack-apply-updates.md)。

### <a name="changes"></a>更改

- Azure Stack 集线器1907版包含警告性警报，该警报指示操作员在更新到版本1908之前，确保操作员将其系统的 OEM 包更新到2.1 或更高版本。 有关如何应用 Azure Stack 中心 OEM 更新的详细信息，请参阅[应用 Azure Stack 中心原始设备制造商更新](azure-stack-update-oem.md)。

- 添加了新的出站规则（HTTPS）以启用 Azure Stack 集线器诊断日志收集服务的通信。 有关详细信息，请参阅[Azure Stack 中心数据中心集成-发布终结点](azure-stack-integrate-endpoints.md#ports-and-urls-outbound)。

- 如果外部存储位置的容量不足，则基础结构备份服务将删除部分上载的备份。

- 基础结构备份不再包含域服务数据的备份。 此更改仅适用于使用 Azure Active Directory 作为标识提供者的系统。

- 现在，我们验证正在引入 > 的映像是否已**VM 映像**边栏选项卡的类型为页 blob。

- 特权终结点命令**BmcCredential**现在将更新基板管理控制器中的凭据。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->
- 修复了发布者、产品/服务和 SKU 在资源管理器模板中被视为区分大小写的问题：没有为部署提取映像，除非映像参数与发布者、产品/服务和 SKU 的大小写相同。

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- 修复了由于备份存储服务元数据期间出现超时，导致备份失败并出现**PartialSucceeded**错误消息的问题。  

- 修复了删除用户订阅导致孤立资源的问题。

- 修复了在创建产品/服务时未保存说明字段的问题。

- 解决了具有**只读**权限的用户能够创建、编辑和删除资源的问题。 现在，用户只能在分配 "**参与者**" 权限时创建资源。 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- 修复了由于 WMI 提供程序主机锁定的 DLL 文件而导致更新失败的问题。

- 修复了更新服务中阻止可用更新在更新磁贴或资源提供程序中显示的问题。 此问题已在1906修补程序[KB4511282](https://support.microsoft.com/help/4511282/)中发现和修复。

- 修复了一个问题，此问题可能会导致更新失败，因为管理平面由于配置错误而变得不正常。 此问题已在1906修补程序[KB4512794](https://support.microsoft.com/help/4512794/)中发现和修复。

- 修复了阻止用户完成从 marketplace 部署第三方映像的问题。 此问题已在1906修补程序[KB4511259](https://support.microsoft.com/help/4511259/)中发现和修复。

- 修复了可能导致从托管映像创建 VM 失败的问题，因为用户映像管理器服务崩溃。 此问题已在1906修补程序[KB4512794](https://support.microsoft.com/help/4512794/)中发现和修复。

- 解决了由于应用网关缓存未按预期刷新而导致 VM CRUD 操作失败的问题。 此问题已在1906修补程序[KB4513119](https://support.microsoft.com/en-us/help/4513119/)中发现和修复。

- 修复了运行状况资源提供程序中的问题，该提供程序在管理员门户中影响了区域和警报 blade 的可用性。 此问题已在1906修补程序[KB4512794](https://support.microsoft.com/help/4512794)中发现和修复。

## <a name="security-updates"></a>安全更新

有关 Azure Stack 中心更新的安全更新的信息，请参阅[Azure Stack 中心安全更新](release-notes-security-updates.md)。

## <a name="update-planning"></a>更新规划

在应用更新之前，请确保查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新前后的活动清单](release-notes-checklist.md)

## <a name="download-the-update"></a>下载更新

可以从[Azure Stack 中心下载页](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 中心1907更新包。

## <a name="hotfixes"></a>修补程序

Azure Stack 中心定期发布修补程序。 将 Azure Stack 中心更新为1907之前，请务必安装最新的 Azure Stack 中心修补程序1906。

Azure Stack 集线器修补程序仅适用于 Azure Stack 集线器集成系统;请勿尝试在 ASDK 上安装修补程序。

### <a name="before-applying-the-1907-update"></a>在应用1907更新之前

Azure Stack 集线器1907版必须应用于带有以下修补程序的1906版本：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>成功应用1907更新后

安装此更新后，安装任何适用的修补程序。 有关详细信息，请参阅[服务策略](azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1907.26.70](https://support.microsoft.com/help/4541348)
::: moniker-end

::: moniker range=">=azs-1907"
## <a name="automatic-update-notifications"></a>自动更新通知

可从基础结构网络访问 internet 的系统将在操作员门户中看到 "**更新可用**" 消息。 无 internet 访问权限的系统可以下载包含相应 .xml 的 .zip 文件并将其导入。

> [!TIP]  
> 订阅以下*RSS*或*Atom*源，与 Azure Stack 中心修补程序保持密切相同：
>
> - [技术](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>存档

若要访问较早版本的存档发行说明，请使用左侧目录上方的 "版本选择器" 下拉列表，然后选择要查看的版本。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中心中的更新管理的概述，请参阅[在 Azure Stack 中心中管理更新概述](azure-stack-updates.md)。  
- 有关如何将更新与 Azure Stack 中心应用的详细信息，请参阅[在 Azure Stack 中心应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 中心的服务策略，以及为了使系统处于受支持状态而必须执行的操作，请参阅[Azure Stack 中心维护策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点（PEP）来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 集线器中的更新](azure-stack-monitor-update.md)。
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1906"
## <a name="1906-archived-release-notes"></a>1906存档的发行说明
::: moniker-end
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

::: moniker range="<azs-1907"
可以[在 TechNet 库中访问 Azure Stack 中心发行说明的早期版本](https://aka.ms/azsarchivedrelnotes)。 这些存档文档仅供参考目的提供，并不表示支持这些版本。 有关 Azure Stack 中心支持的详细信息，请参阅[Azure Stack 中心维护策略](azure-stack-servicing-policy.md)。 若要获得更多帮助，请与 Microsoft 客户支持服务联系。
::: moniker-end


