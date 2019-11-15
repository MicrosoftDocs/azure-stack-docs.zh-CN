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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 09/13/2019
ms.openlocfilehash: 367a2cb5a07c0db2b272b8ffc951f51e5ed9cc0e
ms.sourcegitcommit: bb2bbfad8061f7677954f6ce5a435b4e6f9299b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74100029"
---
# <a name="azure-stack-known-issues"></a>Azure Stack 已知问题

本文列出了 Azure Stack 发行版中的已知问题。 当发现新问题时，将更新此列表。

若要访问不同版本的已知问题，请使用左侧目录上方的 "版本选择器" 下拉列表。

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> 在应用更新之前，请查看此部分。
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> 如果 Azure Stack 实例超过两个更新，则会将其视为不符合。 必须[至少更新到支持的最低版本](azure-stack-servicing-policy.md#keep-your-system-under-support)。 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>1908更新过程

- 适用：此问题适用于所有受支持的版本。
- 原因：尝试安装 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序（URP）无法正确地将文件从存储容器传输到内部基础结构共享进行处理。
- 修正：从版本1901（1.1901.0.95）开始，你可以通过单击 "**立即更新**" （不**继续**）来解决此问题。 然后，URP 将清理上一次尝试的文件，并重新启动下载。 如果此问题仍然存在，建议使用 "[安装更新" 部分](azure-stack-apply-updates.md#install-updates-and-monitor-progress)手动上载更新包。
- 发生次数：常见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有受支持的版本。
- 原因：不应使用1804版中引入的两个管理订阅。 订阅类型为**计数**订阅和**消耗**订阅。
- 修正：如果在这两个订阅上运行了资源，请在用户订阅中重新创建这些资源。
- 发生次数：常见

### <a name="subscriptions-properties-blade"></a>订阅属性边栏选项卡

- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员门户中，订阅的 "**属性**" 边栏选项卡未正确加载
- 修正：你可以在 "**订阅概述**" 边栏选项卡的 "**概要**" 窗格中查看这些订阅属性。
- 发生次数：常见

### <a name="subscriptions-lock-blade"></a>订阅锁定边栏选项卡

- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员门户中，**用户订阅的**"**锁定**" 边栏选项卡上有两个 butons。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅权限

- 适用：此问题适用于所有受支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 修正：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

## <a name="networking"></a>网络

### <a name="network-security-groups"></a>网络安全组
- 适用：此问题适用于所有受支持的版本。 
- 原因：无法在 NSG 中创建显式**DenyAllOutbound**规则，因为这将阻止 VM 部署所需的所有基础结构的内部通信完成。
- 发生次数：常见

### <a name="service-endpoints"></a>服务终结点

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

### <a name="network-interface"></a>网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法将新的网络接口添加到处于**运行**状态的虚拟机。
- 修正：添加/删除网络接口之前停止虚拟机。
- 发生次数：常见

- 适用：此问题适用于所有受支持的版本。
- 原因：无法更改 VM 的主 NIC。 删除/分离主 NIC 将导致启动 VM 时出现问题。
- 发生次数：常见

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="local-network-gateway-deletion"></a>本地网络网关删除

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，删除**本地网络网关**会显示以下错误消息：**不能删除具有活动连接的本地网络网关**，即使没有活动的连接也是如此。
- 缓解：此问题的修复将在1907中发布。 此问题的解决方法是使用相同的 IP 地址、地址空间和配置详细信息创建新的本地网络网关。 环境更新到1907后，可以删除旧的 LNG。
- 发生次数：常见

#### <a name="alerts"></a>警报

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 疑难解答

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 当前不支持此功能。
- 发生次数：常见

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack。 使用以下链接获取 Azure Stack 文档：

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用启动诊断，但要删除启动诊断存储帐户，则会发生此错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在4节点 Azure Stack 环境的修补和更新过程中创建故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例将在 Azure Stack 环境的4节点上的更新过程中失败，并出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在4节点 Azure Stack 上，在更新过程中创建的规模集实例仍不可用。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有受支持的版本。
- 原因：启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 修正：在预配后，使用适用于 Linux 扩展的 VM 访问来实现 SSH 密钥，或使用基于密码的身份验证。
- 发生次数：常见

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无效

- 适用：此问题适用于所有受支持的版本。
- 原因：新的 "重置密码" 边栏选项卡出现在规模集 UI 中，但 Azure Stack 不支持在规模集中重置密码。
- 修正：无。
- 发生次数：常见

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上的 Rainy 云

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击空图表会打开 "rainy cloud" 边栏选项卡。 这是规模集诊断信息的图表，例如 CPU 百分比，而不是当前 Azure Stack 生成中支持的功能。
- 修正：无。
- 发生次数：常见

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用：此问题适用于所有受支持的版本。    
- 原因： "虚拟机诊断设置" 边栏选项卡中有一个 "**接收器**" 选项卡，该选项卡要求提供**Application insights 帐户**。 这是新的边栏选项卡的结果，在 Azure Stack 尚不受支持。
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
- 原因：尝试安装 1907 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序（URP）无法正确地将文件从存储容器传输到内部基础结构共享进行处理。
- 修正：从版本1901（1.1901.0.95）开始，你可以通过单击 "**立即更新**" （不**继续**）来解决此问题。 然后，URP 将清理上一次尝试的文件，并重新启动下载。 如果此问题仍然存在，建议使用[导入和安装更新部分](azure-stack-apply-updates.md)手动上载更新包。
- 发生次数：常见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有受支持的版本。
- 原因：不应使用1804版中引入的两个管理订阅。 订阅类型为**计数**订阅和**消耗**订阅。
- 修正：如果在这两个订阅上运行了资源，请在用户订阅中重新创建这些资源。
- 发生次数：常见

### <a name="subscriptions-properties-blade"></a>订阅属性边栏选项卡

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
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

## <a name="networking"></a>网络

### <a name="service-endpoints"></a>服务终结点

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

### <a name="network-interface"></a>网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法将新的网络接口添加到处于**运行**状态的虚拟机。
- 修正：添加/删除网络接口之前停止虚拟机。
- 发生次数：常见

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 疑难解答

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 当前不支持此功能。
- 发生次数：常见

### <a name="network-connection-type"></a>网络连接类型

- 适用：此问题适用于任何1906或1907环境。 
- 原因：在用户门户中，" **AddConnection** " 边栏选项卡显示使用**vnet 到 vnet**的选项。 Azure Stack 当前不支持此功能。 
- 发生次数：常见 

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack。 使用以下链接获取 Azure Stack 文档：

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用启动诊断，但要删除启动诊断存储帐户，则会发生此错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在4节点 Azure Stack 环境的修补和更新过程中创建故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例将在 Azure Stack 环境的4节点上的更新过程中失败，并出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在4节点 Azure Stack 上，在更新过程中创建的规模集实例仍不可用。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有受支持的版本。
- 原因：启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 修正：在预配后，使用适用于 Linux 扩展的 VM 访问来实现 SSH 密钥，或使用基于密码的身份验证。
- 发生次数：常见

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无效

- 适用：此问题适用于1906和1907版本。
- 原因：新的 "重置密码" 边栏选项卡出现在规模集 UI 中，但 Azure Stack 不支持在规模集中重置密码。
- 修正：无。
- 发生次数：常见

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上的 Rainy 云

- 适用：此问题适用于1906和1907版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击空图表会打开 "rainy cloud" 边栏选项卡。 这是规模集诊断信息的图表，例如 CPU 百分比，而不是当前 Azure Stack 生成中支持的功能。
- 修正：无。
- 发生次数：常见

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用：此问题适用于1906和1907版本。    
- 原因： "虚拟机诊断设置" 边栏选项卡中有一个 "**接收器**" 选项卡，该选项卡要求提供**Application insights 帐户**。 这是新的边栏选项卡的结果，在 Azure Stack 尚不受支持。
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
- 原因：尝试安装 1906 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序（URP）无法正确地将文件从存储容器传输到内部基础结构共享进行处理。 
- 修正：从版本1901（1.1901.0.95）开始，你可以通过单击 "**立即更新**" （不**继续**）来解决此问题。 然后，URP 将清理上一次尝试的文件，并重新启动下载。 如果此问题仍然存在，建议使用[导入和安装更新部分](azure-stack-apply-updates.md)手动上载更新包。
- 发生次数：常见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有受支持的版本。
- 原因：不应使用1804版中引入的两个管理订阅。 订阅类型为**计数**订阅和**消耗**订阅。
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

### <a name="subscriptions-properties-blade"></a>订阅属性边栏选项卡
- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员门户中，订阅的 "**属性**" 边栏选项卡未正确加载
- 修正：你可以在 "订阅概述" 边栏选项卡的 "概要" 窗格中查看这些订阅属性。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

### <a name="update"></a>更新

- 适用：此问题适用于1906版本。
- 原因：在操作员门户中，修补程序的更新状态显示更新的状态不正确。 初始状态表明即使仍在进行，也无法安装更新。
- 修正：刷新门户，状态将更新为 "正在进行"。
- 出现次数：间歇

## <a name="networking"></a>网络

### <a name="service-endpoints"></a>服务终结点

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

### <a name="network-interface"></a>网络接口

- 适用：此问题适用于所有受支持的版本。
- 原因：无法将新的网络接口添加到处于**运行**状态的虚拟机。
- 修正：添加/删除网络接口之前停止虚拟机。
- 发生次数：常见

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 当前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 疑难解答

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 当前不支持此功能。
- 发生次数：常见

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack。 请使用以下链接获取 Azure Stack 文档：

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>负载均衡

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
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用启动诊断，但要删除启动诊断存储帐户，则会发生此错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在4节点 Azure Stack 环境的修补和更新过程中创建故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例将在 Azure Stack 环境的4节点上的更新过程中失败，并出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在4节点 Azure Stack 上，在更新过程中创建的规模集实例仍不可用。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有受支持的版本。
- 原因：启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 修正：在预配后，使用适用于 Linux 扩展的 VM 访问来实现 SSH 密钥，或使用基于密码的身份验证。
- 发生次数：常见

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无效

- 适用：此问题适用于1906版本。
- 原因：新的 "重置密码" 边栏选项卡出现在规模集 UI 中，但 Azure Stack 不支持在规模集中重置密码。
- 修正：无。
- 发生次数：常见

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上的 Rainy 云

- 适用：此问题适用于1906版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击空图表会打开 "rainy cloud" 边栏选项卡。 这是规模集诊断信息的图表，例如 CPU 百分比，而不是当前 Azure Stack 生成中支持的功能。
- 修正：无。
- 发生次数：常见

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用：此问题适用于1906版本。
- 原因： "虚拟机诊断设置" 边栏选项卡中有一个 "**接收器**" 选项卡，该选项卡要求提供**Application insights 帐户**。 这是新的边栏选项卡的结果，在 Azure Stack 尚不受支持。
- 修正：无。
- 发生次数：常见

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-update-process"></a>1905更新过程

### <a name="host-node-update-prerequisite-failure"></a>主机节点更新先决条件失败

- 适用：此问题适用于1905更新。
- 原因：尝试安装 1905 Azure Stack 更新时，更新的状态可能因**主机节点更新必备组件**失败。 这通常是由于主机节点的可用磁盘空间不足引起的。
- 修正：请联系 Azure Stack 支持人员，以获得在主机节点上清除磁盘空间的帮助。
- 发生次数：不常见

### <a name="preparation-failed"></a>准备失败

- 适用：此问题适用于所有受支持的版本。
- 原因：尝试安装 1905 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序（URP）无法正确地将文件从存储容器传输到内部基础结构共享进行处理。 1905更新包比上一个更新包大，这可能会使此问题更有可能出现。
- 修正：从版本1901（1.1901.0.95）开始，你可以通过单击 "**立即更新**" （不**继续**）来解决此问题。 然后，URP 将清理上一次尝试的文件，并重新启动下载。 如果此问题仍然存在，建议使用[导入和安装更新部分](azure-stack-apply-updates.md)手动上载更新包。
- 发生次数：常见

## <a name="portal"></a>门户

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

### <a name="marketplace-management"></a>Marketplace 管理

- 适用：此问题适用于1904和1905
- 原因：登录到管理员门户时，marketplace 管理屏幕不可见。
- 修正：刷新浏览器或中转到 "**设置**"，然后选择 "**重置为默认设置**" 选项。
- 出现次数：间歇

### <a name="docker-extension"></a>Docker 扩展

- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员和用户门户中，如果搜索**Docker**，则会错误地返回该项。 它在 Azure Stack 中不可用。 如果尝试创建此错误，则会显示错误。
- 修正：无缓解措施。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

### <a name="template"></a>模板

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，模板部署 UI 不会填充以 "_" （下划线字符）开头的模板名称的参数。
- 修正：从模板名称中删除 "_" （下划线字符）。
- 发生次数：常见

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡

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

#### <a name="create-load-balancer"></a>创建负载均衡器

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**创建负载均衡器**" 窗口会显示一个用于创建**标准**负载均衡器 SKU 的选项。 Azure Stack 中不支持此选项。
- 修正：改为使用**基本**负载均衡器选项。
- 发生次数：常见

### <a name="public-ip-address"></a>公共 IP 地址

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**创建公共 IP 地址**" 窗口会显示一个用于创建**标准**SKU 的选项。 Azure Stack 中不支持**标准**SKU。
- 修正：使用**基本**SKU 作为公共 IP 地址。
- 发生次数：常见

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。
如果在 VM 上启用启动诊断，但要删除启动诊断存储帐户，则会发生此错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="vm-resize"></a>VM 调整大小

- 适用：此问题适用于1905版本。
- 原因：无法成功调整托管磁盘 VM 的大小。 尝试调整 VM 的大小时，会生成错误，其中包含 "代码"： "InternalOperationError"，"消息"： "操作中出现内部错误"。
- 补救措施：我们正在努力在下一个版本中对此进行修正。 目前，必须用新的 VM 大小重新创建 VM。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="centos"></a>CentOS

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟机规模集创建体验提供基于 CentOS 的7.2 作为部署选项。 CentOS 7.2 在 Azure Stack Marketplace 上不可用，这将导致找不到映像，导致部署失败。
- 修正：为你的部署选择另一个操作系统，或使用 Azure 资源管理器模板，该模板指定了另一个 CentOS 映像，该映像已在通过应用商店在 marketplace 部署之前下载。
- 发生次数：常见

#### <a name="remove-scale-set"></a>删除规模集

- 适用：此问题适用于所有受支持的版本。
- 原因：无法从**虚拟机规模集**边栏选项卡中删除规模集。
- 修正：选择要删除的规模集，然后单击 "**概述**" 窗格中的 "**删除**" 按钮。
- 发生次数：常见

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在4节点 Azure Stack 环境的修补和更新过程中创建故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例将在 Azure Stack 环境的4节点上的更新过程中失败，并出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在4节点 Azure Stack 上，在更新过程中创建的规模集实例仍不可用。

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>未加载规模集实例视图边栏选项卡

- 适用：此问题适用于1904和1905版本。
- 原因：位于 Azure Stack 门户的虚拟机规模集的实例视图边栏选项卡-> 仪表板 > 虚拟机规模集-> AnyScaleSet-> AnyScaleSetInstance 无法加载，并显示哭泣的云映像。
- 补救措施：目前没有修正，我们正在努力解决问题。 在此之前，请使用 CLI 命令 `az vmss get-instance-view` 获取规模集的实例视图。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有受支持的版本。
- 原因：启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 修正：在预配后，使用适用于 Linux 扩展的 VM 访问来实现 SSH 密钥，或使用基于密码的身份验证。
- 发生次数：常见

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="archive"></a>存档

若要访问较早版本的已存档的已知问题，请使用左侧目录上方的 "版本选择器" 下拉列表，然后选择要查看的版本。

## <a name="next-steps"></a>后续步骤

- [查看更新活动清单](release-notes-checklist.md)
- [查看安全更新列表](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1905"
你可以[在 TechNet 库上访问 Azure Stack 已知问题的旧版本](https://aka.ms/azsarchivedrelnotes)。 这些存档文档仅供参考目的提供，并不表示支持这些版本。 有关 Azure Stack 支持的信息，请参阅[Azure Stack 服务策略](azure-stack-servicing-policy.md)。 若要获得更多帮助，请与 Microsoft 客户支持服务联系。
::: moniker-end
