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
ms.openlocfilehash: ba5b8358dced378f499c30f9b8d409497319fc86
ms.sourcegitcommit: 2c37ac8e88de19430080128bac1b70e33557d354
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71164240"
---
# <a name="azure-stack-known-issues"></a>Azure Stack 已知问题

本文列出了 Azure Stack 发行版中的已知问题。 每当发现新的问题，此列表就会更新。

若要访问不同版本的已知问题，请使用左侧目录上方的 "版本选择器" 下拉列表。

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> 在应用更新之前，请先查看本部分。
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

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试安装 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题仍然存在，建议使用 "[安装更新" 部分](azure-stack-apply-updates.md#install-updates-and-monitor-progress)手动上载更新包。
- 发生次数：通用

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。
- 补救措施：如果有资源在这两个订阅上运行，请在用户订阅中重新创建这些资源。
- 发生次数：通用

### <a name="subscriptions-properties-blade"></a>“订阅属性”边栏选项卡

- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员门户中，订阅的“属性”边栏选项卡未正确加载
- 补救措施：可以在“订阅概述”边栏选项卡的“概要”窗格中查看这些订阅属性。
- 发生次数：通用

### <a name="subscriptions-lock-blade"></a>订阅锁定边栏选项卡

- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员门户中，**用户订阅的** **锁定**边栏选项卡有两个 butons。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：通用

### <a name="storage-account-settings"></a>存储帐户设置

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，存储帐户的“配置”边栏选项卡显示用于更改**安全传输类型**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

### <a name="upload-blob"></a>上传 Blob

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="service-endpoints"></a>服务终结点

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络”边栏选项卡显示使用“服务终结点”的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

### <a name="network-interface"></a>网络接口

- 适用于：此问题适用于所有支持的版本。
- 原因：新网络接口无法添加到处于“正在运行”状态的 VM。
- 补救措施：添加/删除网络接口之前，先停止虚拟机。
- 发生次数：通用

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="local-network-gateway-deletion"></a>本地网络网关删除

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，删除**本地网络网关**会显示以下错误消息：如果没有活动连接，则**无法删除具有活动连接的本地网络网关**。
- 缓解措施：此问题的修复将在1907中发布。 此问题的解决方法是使用相同的 IP 地址、地址空间和配置详细信息创建新的本地网络网关。 环境更新到1907后，可以删除旧的 LNG。
- 发生次数：通用

#### <a name="alerts"></a>警报

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络网关”边栏选项卡显示使用“警报”的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

#### <a name="active-active"></a>主动-主动

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户（创建期间）和“虚拟网络网关”的资源菜单中，看到用于启用“主动-主动”配置的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“连接”边栏选项卡显示一项名为“VPN 故障排除程序”的功能。 Azure Stack 目前不支持此功能。
- 发生次数：通用

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关概述页中的文档链接链接到特定于 Azure 的文档，而不是 Azure Stack 文档。 使用以下链接获取 Azure Stack 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="virtual-machine-scale-set"></a>虚拟机扩展集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在 4 节点的 Azure Stack 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack 上进行更新时，仍然不能创建规模集实例。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用于：此问题适用于所有支持的版本。
- 原因：如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。
- 补救措施：在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。
- 发生次数：通用

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无法进行

- 适用于：此问题适用于所有支持的版本。
- 原因：规模集 UI 中出现新的密码重置边栏选项卡，但 Azure Stack 尚不支持在规模集上重置密码。
- 补救措施：无。
- 发生次数：通用

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上出现哭泣的云

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟机规模集概述页显示空白图表。 单击该空白图表会打开“哭泣的云”边栏选项卡。 这是规模集诊断信息的图表（例如 CPU 百分比），但这不是当前 Azure Stack 内部版本支持的功能。
- 补救措施：无。
- 发生次数：通用

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用于：此问题适用于所有支持的版本。    
- 原因：虚拟机诊断设置边栏选项卡包含“接收器”选项卡，用于请求 **Application Insights 帐户**。 这是新边栏选项卡中的结果，Azure Stack 中尚不支持此功能。
- 补救措施：无。
- 发生次数：通用

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>1907更新过程

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试安装 1907 Azure Stack 更新时，更新状态可能会失败并更改为 **PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题持续存在，建议按照[“导入并安装更新”部分](azure-stack-apply-updates.md)的说明手动上传更新包。
- 发生次数：通用

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。
- 补救措施：如果有资源在这两个订阅上运行，请在用户订阅中重新创建这些资源。
- 发生次数：通用

### <a name="subscriptions-properties-blade"></a>“订阅属性”边栏选项卡

- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员门户中，订阅的“属性”边栏选项卡未正确加载
- 补救措施：可以在“订阅概述”边栏选项卡的“概要”窗格中查看这些订阅属性。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：通用

### <a name="storage-account-settings"></a>存储帐户设置

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，存储帐户的“配置”边栏选项卡显示用于更改**安全传输类型**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

### <a name="upload-blob"></a>上传 Blob

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="service-endpoints"></a>服务终结点

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络”边栏选项卡显示使用“服务终结点”的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

### <a name="network-interface"></a>网络接口

- 适用于：此问题适用于所有支持的版本。
- 原因：新网络接口无法添加到处于“正在运行”状态的 VM。
- 补救措施：添加/删除网络接口之前，先停止虚拟机。
- 发生次数：通用

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络网关”边栏选项卡显示使用“警报”的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

#### <a name="active-active"></a>主动-主动

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户（创建期间）和“虚拟网络网关”的资源菜单中，看到用于启用“主动-主动”配置的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“连接”边栏选项卡显示一项名为“VPN 故障排除程序”的功能。 Azure Stack 目前不支持此功能。
- 发生次数：通用

### <a name="network-connection-type"></a>网络连接类型

- 适用于：此问题适用于任何1906或1907环境。 
- 原因：在用户门户中，" **AddConnection** " 边栏选项卡显示使用**vnet 到 vnet**的选项。 Azure Stack 目前不支持此功能。 
- 发生次数：通用 

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关概述页中的文档链接链接到特定于 Azure 的文档，而不是 Azure Stack 文档。 使用以下链接获取 Azure Stack 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="virtual-machine-scale-set"></a>虚拟机扩展集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在 4 节点的 Azure Stack 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack 上进行更新时，仍然不能创建规模集实例。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用于：此问题适用于所有支持的版本。
- 原因：如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。
- 补救措施：在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。
- 发生次数：通用

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无法进行

- 适用于：此问题适用于 1906 和 1907 版本。
- 原因：规模集 UI 中出现新的密码重置边栏选项卡，但 Azure Stack 尚不支持在规模集上重置密码。
- 补救措施：无。
- 发生次数：通用

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上出现哭泣的云

- 适用于：此问题适用于 1906 和 1907 版本。
- 原因：虚拟机规模集概述页显示空白图表。 单击该空白图表会打开“哭泣的云”边栏选项卡。 这是规模集诊断信息的图表（例如 CPU 百分比），但这不是当前 Azure Stack 内部版本支持的功能。
- 补救措施：无。
- 发生次数：通用

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用于：此问题适用于 1906 和 1907 版本。    
- 原因：虚拟机诊断设置边栏选项卡包含“接收器”选项卡，用于请求 **Application Insights 帐户**。 这是新边栏选项卡中的结果，Azure Stack 中尚不支持此功能。
- 补救措施：无。
- 发生次数：通用

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>1906更新过程

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试安装 1906 Azure Stack 更新时，更新状态可能会显示失败并更改为 **PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。 
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题持续存在，建议按照[“导入并安装更新”部分](azure-stack-apply-updates.md)的说明手动上传更新包。
- 发生次数：通用

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。
- 补救措施：如果有资源在这两个订阅上运行，请在用户订阅中重新创建这些资源。
- 发生次数：通用

### <a name="subscription-resources"></a>订阅资源

- 适用于：此问题适用于所有支持的版本。
- 原因：删除用户订阅生成孤立的资源。
- 补救措施：先删除用户资源或整个资源组，然后再删除用户订阅。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：通用

### <a name="subscriptions-properties-blade"></a>“订阅属性”边栏选项卡
- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员门户中，订阅的“属性”边栏选项卡未正确加载
- 补救措施：可以在“订阅概述”边栏选项卡的“概要”窗格中查看这些订阅属性
- 发生次数：通用

### <a name="storage-account-settings"></a>存储帐户设置

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，存储帐户的“配置”边栏选项卡显示用于更改**安全传输类型**的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

### <a name="upload-blob"></a>上传 Blob

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：通用

### <a name="update"></a>Update

- 适用于：此问题适用于 1906 版本。
- 原因：在操作员门户中，修补程序的更新状态显示更新状态不正确。 即使安装仍在进行，初始状态也会指示无法安装更新。
- 补救措施：刷新门户，然后状态将更新为“正在进行”。
- 发生次数：间歇性

## <a name="networking"></a>网络

### <a name="service-endpoints"></a>服务终结点

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络”边栏选项卡显示使用“服务终结点”的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

### <a name="network-interface"></a>网络接口

- 适用于：此问题适用于所有支持的版本。
- 原因：新网络接口无法添加到处于“正在运行”状态的 VM。
- 补救措施：添加/删除网络接口之前，先停止虚拟机。
- 发生次数：通用

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络网关”边栏选项卡显示使用“警报”的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

#### <a name="active-active"></a>主动-主动

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户（创建期间）和“虚拟网络网关”的资源菜单中，看到用于启用“主动-主动”配置的选项。 Azure Stack 目前不支持此功能。
- 发生次数：通用

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“连接”边栏选项卡显示一项名为“VPN 故障排除程序”的功能。 Azure Stack 目前不支持此功能。
- 发生次数：通用

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关概述页中的文档链接链接到特定于 Azure 的文档，而不是 Azure Stack 文档。 请使用以下链接获取 Azure Stack 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>负载均衡器

#### <a name="add-backend-pool"></a>添加后端池

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户尝试将**后端池**添加到**负载均衡器**时，该操作失败并出现错误消息“无法更新负载均衡器...”。
- 补救措施：使用 PowerShell、CLI 或资源管理器模板将后端池关联到负载均衡器资源。
- 发生次数：通用

#### <a name="create-inbound-nat"></a>创建入站 NAT

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户尝试为**负载均衡器**创建**入站 NAT 规则**时，该操作失败并出现错误消息“无法更新负载均衡器...”。
- 补救措施：使用 PowerShell、CLI 或资源管理器模板将后端池关联到负载均衡器资源。
- 发生次数：通用

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="virtual-machine-scale-set"></a>虚拟机扩展集


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在 4 节点的 Azure Stack 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack 上进行更新时，仍然不能创建规模集实例。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用于：此问题适用于所有支持的版本。
- 原因：如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。
- 补救措施：在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。
- 发生次数：通用

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无法进行

- 适用于：此问题适用于 1906 版本。
- 原因：规模集 UI 中出现新的密码重置边栏选项卡，但 Azure Stack 尚不支持在规模集上重置密码。
- 补救措施：无。
- 发生次数：通用

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上出现哭泣的云

- 适用于：此问题适用于 1906 版本。
- 原因：虚拟机规模集概述页显示空白图表。 单击该空白图表会打开“哭泣的云”边栏选项卡。 这是规模集诊断信息的图表（例如 CPU 百分比），但这不是当前 Azure Stack 内部版本支持的功能。
- 补救措施：无。
- 发生次数：通用

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用于：此问题适用于 1906 版本。
- 原因：虚拟机诊断设置边栏选项卡包含“接收器”选项卡，用于请求 **Application Insights 帐户**。 这是新边栏选项卡中的结果，Azure Stack 中尚不支持此功能。
- 补救措施：无。
- 发生次数：通用

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

- 适用于：此问题适用于 1905 更新。
- 原因：尝试安装 1905 Azure Stack 更新时，更新状态可能会因**主机节点更新先决条件**的缘故而显示失败。 这通常是因为主机节点没有足够的可用磁盘空间而导致的。
- 补救措施：请联系 Azure Stack 支持部门，要求其帮助清理主机节点上的磁盘空间。
- 发生次数：非通用

### <a name="preparation-failed"></a>准备失败

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试安装 1905 Azure Stack 更新时，更新状态可能会显示失败并更改为 **PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。 1905 更新包大于以前的更新包，导致此问题更可能发生。
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题持续存在，建议按照[“导入并安装更新”部分](azure-stack-apply-updates.md)的说明手动上传更新包。
- 发生次数：通用

## <a name="portal"></a>门户

### <a name="subscription-resources"></a>订阅资源

- 适用于：此问题适用于所有支持的版本。
- 原因：删除用户订阅生成孤立的资源。
- 补救措施：先删除用户资源或整个资源组，然后再删除用户订阅。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：通用

### <a name="marketplace-management"></a>市场管理

- 适用于：此问题适用于 1904 和 1905
- 原因：登录到管理员门户时未显示市场管理屏幕。
- 补救措施：刷新浏览器，或者转到“设置”并选择“重置为默认设置”选项。
- 发生次数：间歇性

### <a name="docker-extension"></a>Docker 扩展

- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 不是 Azure Stack 中提供。 如果尝试创建它，则会显示一个错误。
- 补救措施：无缓解措施。
- 发生次数：通用

### <a name="upload-blob"></a>上传 Blob

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：通用

### <a name="template"></a>模板

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，目标部署 UI 不为以“_”（下划线字符）开头的模板名称填充参数。
- 补救措施：从模板名称中删除“_”（下划线字符）。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡器

#### <a name="add-backend-pool"></a>添加后端池

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户尝试将**后端池**添加到**负载均衡器**时，该操作失败并出现错误消息“无法更新负载均衡器...”。
- 补救措施：使用 PowerShell、CLI 或资源管理器模板将后端池关联到负载均衡器资源。
- 发生次数：通用

#### <a name="create-inbound-nat"></a>创建入站 NAT

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户尝试为**负载均衡器**创建**入站 NAT 规则**时，该操作失败并出现错误消息“无法更新负载均衡器...”。
- 补救措施：使用 PowerShell、CLI 或资源管理器模板将后端池关联到负载均衡器资源。
- 发生次数：通用

#### <a name="create-load-balancer"></a>创建负载均衡器

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“创建负载均衡器”窗口显示一个用于创建“标准”负载均衡器 SKU 的选项。 Azure Stack 不支持此选项。
- 补救措施：改用“基本”负载均衡器选项。
- 发生次数：通用

### <a name="public-ip-address"></a>公用 IP 地址

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“创建公共 IP 地址”窗口显示一个用于创建“标准”SKU 的选项。 Azure Stack 不支持“标准”SKU。
- 补救措施：对公共 IP 地址使用“基本”SKU。
- 发生次数：通用

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。
如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="vm-resize"></a>VM 重设大小

- 适用于：此问题适用于 1905 版本。
- 原因：无法成功重设托管磁盘 VM 的大小。 尝试重设 VM 大小时生成一个错误，其 "code" 为"InternalOperationError"，其 "message" 为"操作中发生内部错误。"
- 补救措施：我们会努力在下一版本中修正此问题。 目前，你必须使用新的 VM 大小重新创建该 VM。
- 发生次数：通用

### <a name="virtual-machine-scale-set"></a>虚拟机扩展集

#### <a name="centos"></a>CentOS

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟机规模集创建体验提供基于 CentOS 的 7.2 作为部署选项。 CentOS 7.2 在 Azure Stack 市场上不提供，这会导致部署失败，错误消息会指出找不到映像。
- 补救措施：为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。
- 发生次数：通用

#### <a name="remove-scale-set"></a>删除规模集

- 适用于：此问题适用于所有支持的版本。
- 原因：无法从“虚拟机规模集”边栏选项卡中删除规模集。
- 补救措施：选择要删除的规模集，然后在“概述”窗格中单击“删除”按钮。
- 发生次数：通用

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在 4 节点的 Azure Stack 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack 上进行更新时，仍然不能创建规模集实例。

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>“规模集实例视图”边栏选项卡不加载

- 适用于：此问题适用于 1904 和 1905 版本。
- 原因：位于 Azure Stack 门户 ->“仪表板”->“虚拟机规模集”->“AnyScaleSet”-“实例”->“AnyScaleSetInstance”的虚拟机规模集的实例视图边栏选项卡无法加载，显示一个哭泣的云的图像。
- 补救措施：目前没有补救措施，我们正在努力进行修复。 在补救措施出来之前，请使用 CLI 命令 `az vmss get-instance-view` 获取规模集的实例视图。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用于：此问题适用于所有支持的版本。
- 原因：如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。
- 补救措施：在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。
- 发生次数：通用

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
你可以[在 TechNet 库上访问 Azure Stack 已知问题的旧版本](https://aka.ms/azsarchivedrelnotes)。 这些存档文档仅供参考目的提供，并不表示支持这些版本。 有关 Azure Stack 支持的信息, 请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。 若要获得更多帮助，请与 Microsoft 客户支持服务联系。
::: moniker-end