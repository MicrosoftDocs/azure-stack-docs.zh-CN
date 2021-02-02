---
title: Azure Stack 集线器1910发行说明
description: 1910 Azure Stack 集线器集成系统的发行说明，包括更新和 bug 修复。
author: sethmanheim
ms.topic: article
ms.date: 01/25/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: ced055a2983fb4aabb1c31f314ccb4587f3801cc
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248022"
---
# <a name="azure-stack-hub-1910-release-notes"></a>Azure Stack 集线器1910发行说明

本文介绍 Azure Stack Hub 更新包的内容。 此更新包括最新版 Azure Stack Hub 的改进和修复。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack Hub 集成系统。 请勿将此更新程序包应用于 Azure Stack 开发工具包 (ASDK)。

> [!IMPORTANT]  
> 如果 Azure Stack Hub 实例落后于两个以上的更新，则认为它不符合。 必须[至少更新到最低支持版本才能获得支持](../azure-stack-servicing-policy.md#keep-your-system-under-support)。

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [应用更新之前和之后的活动清单](../release-notes-checklist.md)
- [已知问题](../known-issues.md)
- [修补程序](#hotfixes)
- [安全更新](../release-notes-security-updates.md)

有关对更新和更新过程进行故障排除的帮助，请参阅[对 Azure Stack Hub 的修补和更新问题进行故障排除](../azure-stack-troubleshooting.md)。

## <a name="download-the-update"></a>下载更新

可使用 [Azure Stack Hub 更新下载程序工具](https://aka.ms/azurestackupdatedownload)下载 Azure Stack Hub 更新包。

## <a name="1910-build-reference"></a>1910 内部版本参考

Azure Stack Hub 1910 更新内部版本号为 **1.1910.0.58**。

### <a name="update-type"></a>更新类型

从版本 1908 开始，运行 Azure Stack Hub 的底层操作系统已更新为 Windows Server 2019。 此更新可以实现核心基础增强，并将更多功能引入 Azure Stack Hub。

Azure Stack Hub 1910 更新内部版本类型为“快速”。

与以前的更新相比，1910 更新包更大，因此下载时间更长。 此更新将长时间保留为“正在准备”状态，操作员可预期此过程所需的时间长于以前的更新。 无论 Azure Stack Hub 环境中有多少个物理节点，完成 1910 更新的预估时间都大约为 10 小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。 运行时间超过预期值并不常见，除非更新失败，否则无需 Azure Stack Hub 操作员采取措施。 此运行时近似值特定于 1910 更新，不应与其他 Azure Stack Hub 更新进行比较。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack Hub 中管理更新](../azure-stack-updates.md)。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 管理员门户现在会在区域属性菜单中显示特权终结点 IP 地址，以方便进行发现。 此外，还会显示当前配置的时间服务器和 DNS 转发器。 有关详细信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../azure-stack-privileged-endpoint.md)。

- 现在，在发生错误时，Azure Stack Hub 运行状况和监视系统可针对各种硬件组件引发警报。 这些警报需要额外的配置。 有关详细信息，请参阅[监视 Azure Stack Hub 硬件组件](../azure-stack-hardware-monitoring.md)。

- [Azure Stack Hub 的 Cloud-init 支持](/azure/virtual-machines/linux/using-cloud-init)：Cloud-init 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 来安装程序包和写入文件，或者配置用户和安全性。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。 市场中的 Ubuntu 映像已更新为支持使用 cloud-init 进行预配。

- 与 Azure 一样，Azure Stack Hub 现在支持所有 Windows Azure Linux 代理版本。

- 提供了新版 Azure Stack Hub 管理员 PowerShell 模块。 <!-- For more information, see -->

- 2020 年 4 月 15 日为 Azure Stack Hub 发布了新的 Azure PowerShell 租户模块。 当前使用的 Azure RM 模块会继续工作，但在内部版本 2002 后将不再更新。

- 已在特权终结点 (PEP) 中添加 **Set-AzSDefenderManualUpdate** cmdlet，用于为 Azure Stack Hub 基础结构中的 Windows Defender 定义配置手动更新。 有关详细信息，请参阅[更新 Azure Stack Hub 上的 Windows Defender Antivirus](../azure-stack-security-av.md)。

- 已在特权终结点 (PEP) 中添加 **Set-AzSDnsForwarder** cmdlet，用于在 Azure Stack Hub 中更改 DNS 服务器的转发器设置。 有关 DNS 配置的详细信息，请参阅 [Azure Stack Hub 数据中心 DNS 集成](../azure-stack-integrate-dns.md)。

- 添加了对使用 [AKS 引擎](../../user/azure-stack-kubernetes-aks-engine-overview.md)管理 **Kubernetes 集群** 的支持。 从此更新开始，客户可以部署生产 Kubernetes 群集。 借助 AKS 引擎，用户能够：
  - 管理其 Kubernetes 群集的生命周期。 创建、更新和扩展群集。
  - 使用 AKS 和 Azure Stack Hub 团队生成的托管映像维护其群集。
  - 利用集成了 Azure 资源管理器的 Kubernetes 云提供程序，该提供程序使用本机 Azure 资源构建群集。
  - 在已连接或断开连接的 Azure Stack Hub 戳中部署和管理其群集。
  - 使用 Azure 混合功能：
    - 与 Azure Arc 集成。
    - 用适用于容器的 Azure Monitor 进行集成。
  - 将 Windows 容器与 AKS 引擎一起使用。
  - 接收针对其部署的 Microsoft 支持部门和工程支持。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- Azure Stack Hub 的功能已得到改进，可自动补救一些修补升级问题，过去，这些问题会导致更新失败，或者使操作员无法启动 Azure Stack Hub 更新。 因此，**Test-AzureStack -UpdateReadiness** 组中包含的测试较少。 有关详细信息，请参阅[验证 Azure Stack Hub 系统状态](../azure-stack-diagnostic-test.md#groups)。 以下三项测试保留在 **UpdateReadiness** 组中：

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- 添加了审核规则来报告外部设备（例如 USB 密钥）装载到 Azure Stack Hub 基础结构节点的时间。 审核日志通过 syslog 发出，并显示为“Microsoft-Windows-Security-Auditing:6416|即插即用事件”。 有关如何配置 syslog 客户端的详细信息，请参阅 [Syslog 转发](../azure-stack-integrate-security.md)。

- Azure Stack Hub 的内部证书即将改用 4096 位 RSA 密钥。 运行内部机密轮换会将旧的 2048 位证书替换为 4096 位长的证书。 有关 Azure Stack Hub 中的机密轮换的详细信息，请参阅[在 Azure Stack Hub 中轮换机密](../azure-stack-rotate-secrets.md)。

- 将多个内部组件升级到符合国家安全系统委员会 - 政策 15（CNSSP-15，该政策提供使用公共标准来安全共享信息的最佳做法）的密码编译算法复杂性和密钥强度。 其中的改进包括，在 Kerberos 身份验证中使用 AES256，以及在 VPN 加密中使用 SHA384。 有关 CNSSP-15 的详细信息，请参阅[国家安全系统委员会的“政策”页](http://www.cnss.gov/CNSS/issuances/Policies.cfm)。

- 由于上述升级，Azure Stack Hub 现在对 IPsec/IKEv2 配置使用新的默认值。 Azure Stack Hub 端使用的新默认值如下：

   **IKE 阶段 1（主模式）参数**

   | 属性              | Value|
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

   [默认的 IPsec/IKE 提案](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)文档中也反映了这些更改。

- 基础结构备份服务改进了计算备份所需的可用空间的逻辑，而不是依赖固定的阈值。 该服务使用备份大小、保留策略、预留和外部存储位置的当前利用率，来确定是否需要对操作员引发警告。

### <a name="changes"></a>更改

- 将市场项从 Azure 下载到 Azure Stack Hub 时，可以使用新的用户界面来指定项的版本（如果存在多个版本时）。 新 UI 可用于联网场景和离线场景。 有关详细信息，请参阅[将市场项从 Azure 下载到 Azure Stack Hub](../azure-stack-download-azure-marketplace-item.md)。  

- 从版本 1910 开始，Azure Stack Hub 系统 **需要** 额外的 /20 专用内部 IP 空间。 有关详细信息，请参阅 [Azure Stack 的网络集成规划](../azure-stack-network.md)。
  
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

有关此 Azure Stack Hub 更新中的安全更新的信息，请参阅 [Azure Stack Hub 安全更新](../release-notes-security-updates.md)。

可从 [Qualys 网站](https://www.qualys.com/azure-stack/)下载此版本的 Qualys 漏洞报告。

## <a name="hotfixes"></a>修补程序

Azure Stack Hub 定期发布修补程序。 将 Azure Stack Hub 更新到 1910 之前，请务必先安装 1908 的最新 Azure Stack Hub 修补程序。

> [!NOTE]
> Azure Stack Hub 修补程序版本是累积性的；你只需安装最新的修补程序即可获取该版本的任何以前修补程序版本中包含的所有修补程序。

Azure Stack Hub 修补程序仅适用于 Azure Stack Hub 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-1910-update"></a>先决条件：应用 1910 更新之前

必须在包含以下修补程序的版本 1908 中应用 Azure Stack 版本 1910：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub 修补程序 1.1908.51.133](https://support.microsoft.com/help/4574734)

### <a name="after-successfully-applying-the-1910-update"></a>成功应用 1910 更新之后

安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅我们的[服务策略](../azure-stack-servicing-policy.md)。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack Hub 修补程序 1.1910.63.186](https://support.microsoft.com/help/4574735)
