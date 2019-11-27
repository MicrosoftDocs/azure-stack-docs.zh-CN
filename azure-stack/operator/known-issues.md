---
title: Azure Stack 已知问题
description: 了解 Azure Stack 版本中的已知问题。
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
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 0621ca19f96e1c33a85ea5308346fafa2e4545f8
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465365"
---
# <a name="azure-stack-known-issues"></a>Azure Stack 已知问题

本文列出了 Azure Stack 发行版中的已知问题。 每当发现新的问题，此列表就会更新。

若要访问不同版本的已知问题，请使用左侧目录上方的 "版本选择器" 下拉列表。

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> 在应用更新之前，请先查看本部分。
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> 如果 Azure Stack 实例超过两个更新，则会将其视为不符合。 必须[至少更新到支持的最低版本](azure-stack-servicing-policy.md#keep-your-system-under-support)。 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-1910"
## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有受支持的版本。
- 原因：不应使用1804版中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。
- 修正：如果在这两个订阅上运行了资源，请在用户订阅中重新创建这些资源。
- 发生次数：常见

### <a name="subscriptions-lock-blade"></a>“订阅锁定”边栏选项卡

- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员门户中，用户订阅的 "**锁定**" 边栏选项卡具有两个称为 "**订阅**" 的按钮。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅权限

- 适用：此问题适用于所有受支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 修正：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

### <a name="upload-blob-with-oauth-error"></a>上传具有 OAuth 错误的 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

### <a name="upload-blob-option-unsupported"></a>不支持上传 blob 选项

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试上传上传边栏选项卡中的 blob 时，可以选择选择**aad**或**密钥身份验证**，但 Azure Stack 中不支持**AAD** 。
- 发生次数：常见

### <a name="load-balancer-backend-pool"></a>负载均衡器后端池

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，添加**负载均衡器**后端池时，操作导致错误消息 "**无法保存负载均衡器后端池**";但是，此操作实际上已成功。
- 发生次数：常见

### <a name="incorrect-tooltip-when-creating-vm"></a>创建 VM 时工具提示不正确

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，选择托管磁盘时，如果磁盘类型为 "高级 SSD"，则下拉列表将显示**操作系统磁盘**。 该选项旁的工具提示指出**可以使用 Azure 免费帐户免费访问某些 OS 磁盘大小**;但是，这对 Azure Stack 无效。 此外，该列表还包括可用于 Azure Stack 的**免费帐户**。
- 发生次数：常见

### <a name="vpn-troubleshoot-and-metrics"></a>VPN 故障排除和指标

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，vpn 网关资源中的**Vpn 故障排除**功能和**指标**会出现，但是 Azure Stack 不支持此功能。
- 发生次数：常见

### <a name="adding-extension-to-vm-scale-set"></a>正在将扩展添加到 VM 规模集

- 适用：此问题适用于版本1907及更高版本。
- 原因：在用户门户中创建虚拟机规模集后，UI 不允许用户添加扩展。
- 发生次数：常见

### <a name="delete-a-storage-container"></a>删除存储容器

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当用户尝试删除存储容器时，如果用户未切换**替代 Azure 策略和 RBAC 角色设置**，则操作将失败。
- 修正：确保选中 "**替代 Azure 策略" 和 "RBAC 角色设置**" 复选框。
- 发生次数：常见

### <a name="refresh-button-on-virtual-machines-fails"></a>虚拟机上的刷新按钮失败

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，导航到 "**虚拟机**" 并尝试使用顶部的按钮刷新时，这些状态无法准确更新。
- 修正：无论是否已单击 "刷新" 按钮，状态将每隔5分钟自动更新一次。 等待5分钟并检查状态。
- 发生次数：常见

### <a name="virtual-network-gateway"></a>虚拟网络网关

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，创建路由表时，"**虚拟网络网关**" 将显示为 "下一跃点类型" 选项;但 Azure Stack 不支持此项。
- 发生次数：常见

### <a name="storage-account-options"></a>存储帐户选项

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，存储帐户的名称显示为**存储帐户-blob、文件、表、队列**，但 Azure Stack 中不支持**文件**。
- 发生次数：常见

### <a name="storage-account-configuration"></a>存储帐户配置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建存储帐户并查看其**配置**时，无法保存配置更改，因为这会导致 AJAX 错误。
- 发生次数：常见

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>SQL 资源提供程序中的容量监视保留加载

- 适用：此问题适用于 Azure Stack 1910 更新或更高版本，并且安装了 SQL 资源提供程序版本1.1.33.0 或更早版本。
- 原因：当前版本的 SQL 资源提供程序与1910更新中的某些最新门户更改不兼容。
- 修正：在将 Azure Stack 升级到1910更新之后，请按照资源提供程序更新过程应用 SQL 资源提供程序修补程序1.1.47.0 （[SQL RP 版本 1.1.47.0](https://aka.ms/azurestacksqlrp11470)）。 对于 MySQL 资源提供程序，在 Azure Stack 升级到1910更新（[MYSQL RP 版本 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)）后，还建议你应用 mysql 资源提供程序修补程序1.1.47.0。
- 发生次数：常见

## <a name="networking"></a>联网

### <a name="load-balancer"></a>负载均衡器

- 适用：此问题适用于所有受支持的版本。
- 原因：将可用性集 Vm 添加到负载均衡器的后端池时，门户上会显示一条错误消息，指出**无法保存负载均衡器后端池**。 这是门户上的一个表面问题;此功能仍然存在，并且在内部将 Vm 成功添加到后端池。
- 发生次数：常见

### <a name="network-security-groups"></a>网络安全组

- 适用：此问题适用于所有受支持的版本。 
- 原因：无法在 NSG 中创建显式**DenyAllOutbound**规则，因为这将阻止 VM 部署所需的所有基础结构的内部通信完成。
- 发生次数：常见

### <a name="service-endpoints"></a>服务终结点

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

### <a name="network-interface"></a>网络接口

#### <a name="addingremoving-network-interface"></a>添加/删除网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法将新的网络接口添加到处于**运行**状态的虚拟机。
- 修正：在添加或删除网络接口之前停止虚拟机。
- 发生次数：常见

#### <a name="primary-network-interface"></a>主网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法更改 VM 的主 NIC。 删除或分离主 NIC 会导致启动 VM 时出现问题。
- 发生次数：常见

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack。 使用以下链接获取 Azure Stack 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="consumed-compute-quota"></a>使用的计算配额

- 适用：此问题适用于所有受支持的版本。
- 创建新的虚拟机时，可能会收到一个错误，例如，**此订阅在此位置的区域个 vcpu 的容量。此订阅使用全部50个可用区域个 vcpu。** 这表示已达到可用内核总数的配额。
- 修正：向操作员询问附加配额的附加计划。 编辑当前计划的配额将不起作用或反映增加的配额。
- 发生次数：罕见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在 4 节点的 Azure Stack 环境中进行修补和更新时出现故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例将在 Azure Stack 环境的4节点上的更新过程中失败，并出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在 Azure Stack 部署的4节点上，在更新过程中创建规模集实例仍不可用。

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>1908更新过程

- 适用：此问题适用于所有受支持的版本。
- 原因：尝试安装 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。
- 修正：从版本1901（1.1901.0.95）开始，你可以通过单击 "**立即更新**" （不**继续**）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题持续存在，建议按照[“安装更新”部分](azure-stack-apply-updates.md#install-updates-and-monitor-progress)的说明手动上传更新包。
- 发生次数：常见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有受支持的版本。
- 原因：不应使用1804版中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。
- 修正：如果在这两个订阅上运行了资源，请在用户订阅中重新创建这些资源。
- 发生次数：常见

### <a name="subscriptions-properties-blade"></a>“订阅属性”边栏选项卡

- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员门户中，订阅的 "**属性**" 边栏选项卡未正确加载
- 修正：你可以在 "**订阅概述**" 边栏选项卡的 "**概要**" 窗格中查看这些订阅属性。
- 发生次数：常见

### <a name="subscriptions-lock-blade"></a>“订阅锁定”边栏选项卡

- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员门户中，用户订阅的 "**锁定**" 边栏选项卡上有两个标记为 "**订阅**" 的按钮。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅权限

- 适用：此问题适用于所有受支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 修正：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

## <a name="networking"></a>联网

### <a name="load-balancer"></a>负载均衡器

- 适用：此问题适用于所有受支持的版本。 
- 原因：将可用性集数 Set Vm 添加到负载均衡器的后端池时，门户上会显示一条错误消息，指出**无法保存负载均衡器后端池**。 这是门户上的一个表面问题，该功能仍然存在，并且已成功将 Vm 添加到后端池内部。 
- 发生次数：常见

### <a name="network-security-groups"></a>网络安全组

- 适用：此问题适用于所有受支持的版本。 
- 原因：无法在 NSG 中创建显式**DenyAllOutbound**规则，因为这将阻止 VM 部署所需的所有基础结构的内部通信完成。
- 发生次数：常见

### <a name="service-endpoints"></a>服务终结点

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

### <a name="network-interface"></a>网络接口

#### <a name="addingremoving-network-interface"></a>添加/删除网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法将新的网络接口添加到处于**运行**状态的虚拟机。
- 修正：添加/删除网络接口之前停止虚拟机。
- 发生次数：常见

#### <a name="primary-network-interface"></a>主网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法将新的网络接口添加到处于**运行**状态的虚拟机。
- 修正：添加/删除网络接口之前停止虚拟机。
- 发生次数：常见

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack。 使用以下链接获取 Azure Stack 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在 4 节点的 Azure Stack 环境中进行修补和更新时出现故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例将在 Azure Stack 环境的4节点上的更新过程中失败，并出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在 4 节点 Azure Stack 上进行更新时，仍然不能创建规模集实例。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有受支持的版本。
- 原因：启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 修正：在预配后，使用适用于 Linux 扩展的 VM 访问来实现 SSH 密钥，或使用基于密码的身份验证。
- 发生次数：常见

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无法进行

- 适用：此问题适用于所有受支持的版本。
- 原因：新的 "重置密码" 边栏选项卡出现在规模集 UI 中，但 Azure Stack 不支持在规模集中重置密码。
- 修正：无。
- 发生次数：常见

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上出现哭泣的云

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击该空白图表会打开“哭泣的云”边栏选项卡。 这是规模集诊断信息的图表（例如 CPU 百分比），但这不是当前 Azure Stack 内部版本支持的功能。
- 修正：无。
- 发生次数：常见

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用：此问题适用于所有受支持的版本。    
- 原因： "虚拟机诊断设置" 边栏选项卡中有一个 "**接收器**" 选项卡，该选项卡要求提供**Application insights 帐户**。 这是新边栏选项卡中的结果，Azure Stack 中尚不支持此功能。
- 修正：无。
- 发生次数：常见

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>1907更新过程

- 适用：此问题适用于所有受支持的版本。
- 原因：尝试安装 1907 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。
- 修正：从版本1901（1.1901.0.95）开始，你可以通过单击 "**立即更新**" （不**继续**）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题持续存在，建议按照[“导入并安装更新”部分](azure-stack-apply-updates.md)的说明手动上传更新包。
- 发生次数：常见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有受支持的版本。
- 原因：不应使用1804版中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。
- 修正：如果在这两个订阅上运行了资源，请在用户订阅中重新创建这些资源。
- 发生次数：常见

### <a name="subscriptions-properties-blade"></a>“订阅属性”边栏选项卡

- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员门户中，订阅的 "**属性**" 边栏选项卡未正确加载
- 修正：你可以在 "**订阅概述**" 边栏选项卡的 "**概要**" 窗格中查看这些订阅属性。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅权限

- 适用：此问题适用于所有受支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 修正：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

## <a name="networking"></a>联网

### <a name="load-balancer"></a>负载均衡器

- 适用：此问题适用于所有受支持的版本。 
- 原因：将可用性集数 Set Vm 添加到负载均衡器的后端池时，门户上会显示一条错误消息，指出**无法保存负载均衡器后端池**。 这是门户上的一个表面问题，该功能仍然存在，并且已成功将 Vm 添加到后端池内部。 
- 发生次数：常见

### <a name="network-security-groups"></a>网络安全组

- 适用：此问题适用于所有受支持的版本。 
- 原因：无法在 NSG 中创建显式**DenyAllOutbound**规则，因为这将阻止 VM 部署所需的所有基础结构的内部通信完成。
- 发生次数：常见

### <a name="service-endpoints"></a>服务终结点

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

### <a name="network-interface"></a>网络接口

#### <a name="addingremoving-network-interface"></a>添加/删除网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法将新的网络接口添加到处于**运行**状态的虚拟机。
- 修正：添加/删除网络接口之前停止虚拟机。
- 发生次数：常见

#### <a name="primary-network-interface"></a>主网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法将新的网络接口添加到处于**运行**状态的虚拟机。
- 修正：添加/删除网络接口之前停止虚拟机。
- 发生次数：常见

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 目前不支持此功能。
- 发生次数：常见

### <a name="network-connection-type"></a>网络连接类型

- 适用：此问题适用于任何1906或1907环境。 
- 原因：在用户门户中，" **AddConnection** " 边栏选项卡显示使用**vnet 到 vnet**的选项。 Azure Stack 目前不支持此功能。 
- 发生次数：常见 

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack。 使用以下链接获取 Azure Stack 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在 4 节点的 Azure Stack 环境中进行修补和更新时出现故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例将在 Azure Stack 环境的4节点上的更新过程中失败，并出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在 4 节点 Azure Stack 上进行更新时，仍然不能创建规模集实例。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有受支持的版本。
- 原因：启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 修正：在预配后，使用适用于 Linux 扩展的 VM 访问来实现 SSH 密钥，或使用基于密码的身份验证。
- 发生次数：常见

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无法进行

- 适用：此问题适用于1906和1907版本。
- 原因：新的 "重置密码" 边栏选项卡出现在规模集 UI 中，但 Azure Stack 不支持在规模集中重置密码。
- 修正：无。
- 发生次数：常见

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上出现哭泣的云

- 适用：此问题适用于1906和1907版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击该空白图表会打开“哭泣的云”边栏选项卡。 这是规模集诊断信息的图表（例如 CPU 百分比），但这不是当前 Azure Stack 内部版本支持的功能。
- 修正：无。
- 发生次数：常见

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用：此问题适用于1906和1907版本。    
- 原因： "虚拟机诊断设置" 边栏选项卡中有一个 "**接收器**" 选项卡，该选项卡要求提供**Application insights 帐户**。 这是新边栏选项卡中的结果，Azure Stack 中尚不支持此功能。
- 修正：无。
- 发生次数：常见

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>1906更新过程

- 适用：此问题适用于所有受支持的版本。
- 原因：尝试安装 1906 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。 
- 修正：从版本1901（1.1901.0.95）开始，你可以通过单击 "**立即更新**" （不**继续**）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题持续存在，建议按照[“导入并安装更新”部分](azure-stack-apply-updates.md)的说明手动上传更新包。
- 发生次数：常见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有受支持的版本。
- 原因：不应使用1804版中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。
- 修正：如果在这两个订阅上运行了资源，请在用户订阅中重新创建这些资源。
- 发生次数：常见

### <a name="subscription-resources"></a>订阅资源

- 适用：此问题适用于所有受支持的版本。
- 原因：删除用户订阅会导致孤立的资源。
- 修正：首先删除用户资源或整个资源组，然后删除用户订阅。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅权限

- 适用：此问题适用于所有受支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 修正：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="subscriptions-properties-blade"></a>“订阅属性”边栏选项卡
- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员门户中，订阅的 "**属性**" 边栏选项卡未正确加载
- 修正：你可以在 "订阅概述" 边栏选项卡的 "概要" 窗格中查看这些订阅属性。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

### <a name="update"></a>更新

- 适用：此问题适用于1906版本。
- 原因：在操作员门户中，修补程序的更新状态显示更新的状态不正确。 即使安装仍在进行，初始状态也会指示无法安装更新。
- 修正：刷新门户，状态将更新为 "正在进行"。
- 出现次数：间歇

## <a name="networking"></a>联网

### <a name="service-endpoints"></a>服务终结点

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

### <a name="network-interface"></a>网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法将新的网络接口添加到处于**运行**状态的虚拟机。
- 修正：添加/删除网络接口之前停止虚拟机。
- 发生次数：常见

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 目前不支持此功能。
- 发生次数：常见

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack。 请使用以下链接获取 Azure Stack 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>负载均衡器

#### <a name="add-backend-pool"></a>添加后端池

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，如果你尝试将**后端池**添加到**负载均衡器**，则操作将失败，并出现错误消息 "**无法更新负载平衡器 ...** "。
- 修正：使用 PowerShell、CLI 或资源管理器模板将后端池与负载均衡器资源关联。
- 发生次数：常见

#### <a name="create-inbound-nat"></a>创建入站 NAT

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，如果你尝试为**负载均衡器**创建**入站 NAT 规则**，操作将失败，并出现错误消息 "**无法更新负载平衡器 ...** "。
- 修正：使用 PowerShell、CLI 或资源管理器模板将后端池与负载均衡器资源关联。
- 发生次数：常见

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在 4 节点的 Azure Stack 环境中进行修补和更新时出现故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例将在 Azure Stack 环境的4节点上的更新过程中失败，并出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在 4 节点 Azure Stack 上进行更新时，仍然不能创建规模集实例。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有受支持的版本。
- 原因：启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 修正：在预配后，使用适用于 Linux 扩展的 VM 访问来实现 SSH 密钥，或使用基于密码的身份验证。
- 发生次数：常见

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无法进行

- 适用：此问题适用于1906版本。
- 原因：新的 "重置密码" 边栏选项卡出现在规模集 UI 中，但 Azure Stack 不支持在规模集中重置密码。
- 修正：无。
- 发生次数：常见

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上出现哭泣的云

- 适用：此问题适用于1906版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击该空白图表会打开“哭泣的云”边栏选项卡。 这是规模集诊断信息的图表（例如 CPU 百分比），但这不是当前 Azure Stack 内部版本支持的功能。
- 修正：无。
- 发生次数：常见

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用：此问题适用于1906版本。
- 原因： "虚拟机诊断设置" 边栏选项卡中有一个 "**接收器**" 选项卡，该选项卡要求提供**Application insights 帐户**。 这是新边栏选项卡中的结果，Azure Stack 中尚不支持此功能。
- 修正：无。
- 发生次数：常见

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="archive"></a>存档

若要访问较早版本的已存档的已知问题，请使用左侧目录上方的 "版本选择器" 下拉列表，然后选择要查看的版本。

## <a name="next-steps"></a>后续步骤

- [查看更新活动清单](release-notes-checklist.md)
- [查看安全更新列表](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1905"
## <a name="1905-archived-known-issues"></a>1905存档的已知问题
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-known-issues"></a>1904存档的已知问题
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-known-issues"></a>1903存档的已知问题
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-known-issues"></a>1902存档的已知问题
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-known-issues"></a>1901存档的已知问题
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-known-issues"></a>1811存档的已知问题
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-known-issues"></a>1809存档的已知问题
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-known-issues"></a>1808存档的已知问题
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-known-issues"></a>1807存档的已知问题
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-known-issues"></a>1805存档的已知问题
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-known-issues"></a>1804存档的已知问题
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-known-issues"></a>1803存档的已知问题
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-known-issues"></a>1802存档的已知问题
::: moniker-end

::: moniker range="<azs-1906"
你可以[在 TechNet 库上访问 Azure Stack 已知问题的旧版本](https://aka.ms/azsarchivedrelnotes)。 这些存档文档仅供参考目的提供，并不表示支持这些版本。 有关 Azure Stack 支持的信息, 请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。 如需进一步的帮助，请联系 Microsoft 客户支持服务。
::: moniker-end
