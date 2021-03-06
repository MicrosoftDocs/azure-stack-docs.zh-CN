---
title: Azure Stack Hub 已知问题
description: 了解 Azure Stack Hub 发行版中的已知问题。
author: sethmanheim
ms.topic: article
ms.date: 03/05/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: f094fbba546821378a804e081ace7460ba651f73
ms.sourcegitcommit: 7ee28fad5b8ba628b1a7dc3d82cabfc36aa62f0d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250341"
---
# <a name="azure-stack-hub-known-issues"></a>Azure Stack Hub 已知问题

本文列出了 Azure Stack Hub 版本中的已知问题。 每当发现新的问题，此列表就会更新。

若要访问不同版本的已知问题，请使用左侧目录上方的版本选择器下拉列表。

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> 在应用更新之前，请先查看本部分。
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> 如果 Azure Stack Hub 实例落后于两个以上的更新，则认为它不符合。 必须[至少更新到最低支持版本才能获得支持](azure-stack-servicing-policy.md#keep-your-system-under-support)。 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2008"
## <a name="update"></a>更新

有关已知的 Azure Stack Hub 更新问题，请参阅[排查 Azure Stack Hub 中的更新问题](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates)。

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>更新无法将包 Microsoft.AzureStack.Compute.Installer 安装到 CA VM

- 适用于：此问题适用于所有支持的版本。
- 原因：在更新过程中，进程会锁定需要复制到 CA VM 的新内容。 更新失败时，会释放锁。
- 补救措施：继续更新。
- 发生次数：罕见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。 
- 补救措施：如果有资源在这两个订阅上运行，请在用户订阅中重新创建这些资源。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="network-security-groups"></a>网络安全组

#### <a name="denyalloutbound-rule-cannot-be-created"></a>无法创建 DenyAllOutbound 规则

- 适用于：此问题适用于所有支持的版本。
- 原因：VM 创建期间无法在 NSG 中创建到 Internet 的显式 DenyAllOutbound 规则，因为这会使 VM 部署所需的通信无法完成。 它还会拒绝两个部署 VM 所需的基本 IP：DHCP IP：169.254.169.254 和 DNS IP：168.63.129.16。

- 补救措施：VM 创建期间允许出站流量流向 Internet，并在 VM 创建完成后修改 NSG 以阻止所需的流量。
- 发生次数：通用

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关概述页中的文档链接链接到特定于 Azure 的文档，而不是 Azure Stack Hub 文档。 使用以下链接获取 Azure Stack Hub 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack Hub 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>负载均衡器

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>在特定情况下将流量定向到一个后端 VM 的负载均衡器

- 适用于：此问题适用于所有支持的版本。 
- 原因：在负载均衡器上启用“会话相关性”时，2 元组哈希使用 PA IP（物理地址 IP）而不是分配给 VM 的专用 IP。 如果定向到负载均衡器的流量通过 VPN 到达，或者，如果所有客户端 VM（源 IP）位于同一节点上并且启用了会话相关性，则所有流量都将定向到一个后端 VM。
- 发生次数：通用

#### <a name="ipv6-button-visible-in-frontend-ip-configuration"></a>在前端 IP 配置中显示的 IPv6 按钮

- 适用：此问题适用于2008版本。
- 原因：在创建公共负载均衡器的前端 IP 配置时，IPv6 按钮是可见的，并已启用。 这是门户上的一个表面问题。 Azure Stack 集线器上不支持 IPv6。
- 发生次数：通用

#### <a name="backend-port-and-frontend-port-need-to-be-the-same-when-floating-ip-is-enabled"></a>启用浮动 IP 后，后端端口和前端端口必须相同

- 适用于：此问题适用于所有版本。 
- 原因：启用浮动 IP 时，前端端口和后端端口都需要与负载均衡规则中的相同。 这是设计的结果。
- 发生次数：通用

### <a name="stop-deallocate-vm-results-in-mtu-configuration"></a>Stop-Deallocate VM 导致 MTU 配置

- 适用于：此问题适用于所有支持的版本。
- 原因：对 VM 执行 **停止解除分配** 会导致删除 vm 上的 MTU 配置。 此行为在 Azure 中不一致。
- 发生次数：通用

<!-- ## Compute -->

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2005"
## <a name="update"></a>更新

有关已知的 Azure Stack Hub 更新问题，请参阅[排查 Azure Stack Hub 中的更新问题](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates)。

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>更新无法将包 Microsoft.AzureStack.Compute.Installer 安装到 CA VM

- 适用于：此问题适用于所有支持的版本。
- 原因：在更新过程中，进程会锁定需要复制到 CA VM 的新内容。 更新失败时，会释放锁。
- 补救措施：继续更新。
- 发生次数：罕见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。 
- 补救措施：如果有资源在这两个订阅上运行，请在用户订阅中重新创建这些资源。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="network-security-groups"></a>网络安全组

#### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>如果 NIC 未附加到运行中的 VM，则无法删除 NSG

- 适用于：此问题适用于所有支持的版本。
- 原因：如果取消关联 NSG 和未附加到运行中 VM 的 NIC，则该对象的更新 (PUT) 操作会在网络控制器层失败。 NSG 将在网络资源提供程序层（而不是网络控制器上）更新，因此 NSG 转变为失败状态。
- 补救措施：将与 NSG 相关联的 NIC 附加到运行中的 VM，然后取消与 NSG 的关联，或者删除与 NSG 关联的所有 NIC。
- 发生次数：通用

#### <a name="denyalloutbound-rule-cannot-be-created"></a>无法创建 DenyAllOutbound 规则

- 适用于：此问题适用于所有支持的版本。
- 原因：VM 创建期间无法在 NSG 中创建到 Internet 的显式 DenyAllOutbound 规则，因为这会使 VM 部署所需的通信无法完成。
- 补救措施：VM 创建期间允许出站流量流向 Internet，并在 VM 创建完成后修改 NSG 以阻止所需的流量。
- 发生次数：通用

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关概述页中的文档链接链接到特定于 Azure 的文档，而不是 Azure Stack Hub 文档。 使用以下链接获取 Azure Stack Hub 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack Hub 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)
  
### <a name="load-balancer"></a>负载均衡器

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>在特定情况下将流量定向到一个后端 VM 的负载均衡器

- 适用于：此问题适用于所有支持的版本。 
- 原因：在负载均衡器上启用“会话相关性”时，2 元组哈希使用 PA IP（物理地址 IP）而不是分配给 VM 的专用 IP。 如果定向到负载均衡器的流量通过 VPN 到达，或者，如果所有客户端 VM（源 IP）位于同一节点上并且启用了会话相关性，则所有流量都将定向到一个后端 VM。
- 发生次数：通用

#### <a name="public-ip"></a>公共 IP

- 适用于：此问题适用于所有支持的版本。
- 原因：无法更改与负载均衡器关联的公共 IP 的 IdleTimeoutInMinutes 值。 该操作会将公共 IP 置于失败状态。
- 补救措施：若要使公共 IP 恢复成功状态，请将引用公共 IP 的负载均衡器规则上的 IdleTimeoutInMinutes 值更改回原始值（默认值为 4 分钟）。
- 发生次数：通用

### <a name="stop-deallocate-vm-results-in-mtu-configuration"></a>Stop-Deallocate VM 导致 MTU 配置

- 适用于：此问题适用于所有支持的版本。
- 原因：对 VM 执行 **停止解除分配** 会导致删除 vm 上的 MTU 配置。 此行为在 Azure 中不一致。
- 发生次数：通用

## <a name="compute"></a>计算

### <a name="issues-deploying-virtual-machine-scale-set-with-standard_ds2_v2-size-using-the-portal"></a>使用门户部署 Standard_DS2_v2 大小的虚拟机规模集时遇到的问题

- 适用于：此问题适用于 2005 版本。
- 原因：门户 bug 导致创建 Standard_DS2_v2 大小的规模集失败。
- 补救措施：使用 PowerShell 或 CLI 部署此虚拟机规模集 VM 大小。

### <a name="issues-using-vm-extensions-in-ubuntu-server-2004"></a>在 Ubuntu Server 20.04 中使用 VM 扩展时遇到的问题

- 适用于：此问题适用于 Ubuntu Server 20.04 LTS。
- 原因：某些 Linux 发行版已转换为 Python 3.8，并完全删除了 Python 的旧版 `/usr/bin/python` 入口点。 已转换到 Python 3.x 的 Linux 发行版用户在尝试将这些扩展部署到自己的 VM 之前，必须确保存在旧版 `/usr/bin/python` 入口点。 否则，扩展部署可能会失败。
- 补救措施：请按照[在启用 Python 3 的 Linux Azure 虚拟机系统中使用 VM 扩展时遇到的问题](/azure/virtual-machines/extensions/issues-using-vm-extensions-python-3)中的解决步骤操作，但请跳过步骤 2，因为 Azure Stack Hub 没有“运行命令”功能。

### <a name="nvv4-vm-size-on-portal"></a>门户上的 NVv4 VM 大小

- 适用于：此问题适用于 2002 及更高版本。
- 原因：完成 VM 创建体验后，你将看到 VM 大小：NV4as_v4。 如果客户拥有基于 AMD MI25 的 Azure Stack Hub GPU 预览版所需的硬件，那么这些客户可以成功部署 VM。 所有其他客户将无法使用此 VM 大小部署 VM。
- 补救措施：按照设计为 Azure Stack Hub GPU 预览版做准备。

### <a name="consumed-compute-quota"></a>已消耗的计算配额

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新虚拟机时，可能会收到一则错误消息，例如“此订阅在此位置的区域 vCPU 总数已达到上限。此订阅使用了所有可用的 50 个区域 vCPU”。 这表示可用的核心配额总计已达到上限。
- 补救措施：请求操作员提供配额更高的附加计划。 编辑当前计划的配额不起作用，也不会反映提高的配额。
- 发生次数：罕见

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>VM 概述边栏选项卡未显示正确的计算机名称

- 适用于：此问题适用于所有版本。
- 原因：在概述边栏选项卡中查看 VM 的详细信息时，计算机名称显示为“(不可用)”。 这是针对从专用磁盘/磁盘快照创建的 VM 设计的，也会为市场映像而出现。
- 补救措施：在“设置”下查看“属性”边栏选项卡。

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>在 4 节点的 Azure Stack Hub 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack Hub 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack Hub 部署中进行更新时，仍然不能创建规模集实例。

## <a name="storage"></a>存储

### <a name="retention-period-reverts-to-0"></a>保留期恢复为 0

- 适用于：此问题适用于版本 2002 和 2005。
- 原因：如果在保留期设置中指定非 0 的时间段，则它将在 2002 或 2005 更新期间恢复为 0（此设置的默认值）。 这个 0 天的设置将在更新完成后立即生效，从而导致所有现有已删除的存储帐户和任何即将新删除的存储帐户立即不予保留，并标记为需要进行定期垃圾回收（每小时运行一次）。
- 补救措施：将保留期手动指定为正确的时间段。 在指定新的保留期之前已进行垃圾回收的任何存储帐户都不可恢复。  

## <a name="resource-providers"></a>资源提供程序

### <a name="sqlmysql"></a>SQL/MySQL

- 适用于：此问题适用于版本 2002。
- 原因：如果标记包含 SQL 资源提供程序 (RP) 版本 1.1.33.0 或更早版本，则在更新标记时，不会加载 SQL/MySQL 的边栏选项卡。
- 补救措施：将 RP 更新到版本 1.1.47.0。

### <a name="app-service"></a>应用服务

- 适用于：此问题适用于版本 2002。
- 原因：如果标记包含应用服务资源提供程序 (RP) 版本 1.7 和更早版本，则在更新标记时，不会加载应用服务的边栏选项卡。
- 补救措施：将 RP 更新到版本 2002 Q2。

## <a name="powershell"></a>PowerShell

[!Include[Known issue for install - one](../includes/known-issue-az-install-1.md)]

[!Include[Known issue for install - two](../includes/known-issue-az-install-2.md)]

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2002"
## <a name="update"></a>更新

应用 2002 更新后，管理员门户中可能会错误地显示“时间源无效”警报。 这个警报是可忽略的误报，会在即将发布的版本中得到解决。 

有关已知的 Azure Stack Hub 更新问题，请参阅[排查 Azure Stack Hub 中的更新问题](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates)。

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>更新无法将包 Microsoft.AzureStack.Compute.Installer 安装到 CA VM

- 适用于：此问题适用于所有支持的版本。
- 原因：在更新过程中，进程会锁定需要复制到 CA VM 的新内容。 更新失败时，会释放锁。
- 补救措施：继续更新。
- 发生次数：罕见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。 
- 补救措施：如果有资源在这两个订阅上运行，请在用户订阅中重新创建这些资源。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack Hub 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：通用

### <a name="storage-account-options"></a>存储帐户选项

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，存储帐户名称显示为“存储帐户 - Blob、文件、表、队列”；但是，Azure Stack Hub 不支持“文件”。 
- 发生次数：通用

### <a name="create-managed-disk-snapshot"></a>创建托管磁盘快照

- 适用于：此问题适用于版本 2002。
- 原因：在用户门户中创建托管磁盘快照时，“帐户类型”框为空。 选择“创建”按钮时，如果帐户类型为空，则快照创建将失败。
- 补救措施：从“帐户类型”下拉列表中选择一种帐户类型，然后创建快照。
- 发生次数：通用

### <a name="alert-for-network-interface-disconnected"></a>网络接口已断开连接的警报

- 适用于：此问题适用于 1908 及更高版本。
- 原因：当线缆与网络适配器断开连接时，管理员门户中不显示警报。 此问题的原因是 Windows Server 2019 中默认会禁用此故障。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="denyalloutbound-rule-cannot-be-created"></a>无法创建 DenyAllOutbound 规则

- 适用于：此问题适用于所有支持的版本。
- 原因：VM 创建期间无法在 NSG 中创建到 Internet 的显式 DenyAllOutbound 规则，因为这会使 VM 部署所需的通信无法完成。
- 补救措施：VM 创建期间允许出站流量流向 Internet，并在 VM 创建完成后修改 NSG 以阻止所需的流量。
- 发生次数：通用

### <a name="icmp-protocol-not-supported-for-nsg-rules"></a>NSG 规则不支持 ICMP 协议

- 适用于：此问题适用于所有支持的版本。 
- 原因：创建入站或出站网络安全规则时，“协议”选项显示“ICMP”选项。 Azure Stack Hub 目前不支持此选项。 此问题已修复，不会出现在下一个 Azure Stack Hub 版本中。
- 发生次数：通用

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>如果 NIC 未附加到运行中的 VM，则无法删除 NSG

- 适用于：此问题适用于所有支持的版本。
- 原因：如果取消关联 NSG 和未附加到运行中 VM 的 NIC，则该对象的更新 (PUT) 操作会在网络控制器层失败。 NSG 将在网络资源提供程序层（而不是网络控制器上）更新，因此 NSG 转变为失败状态。
- 补救措施：将与 NSG 相关联的 NIC 附加到运行中的 VM，然后取消与 NSG 的关联，或者删除与 NSG 关联的所有 NIC。
- 发生次数：通用

### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>在特定情况下将流量定向到一个后端 VM 的负载均衡器 

- 适用于：此问题适用于所有支持的版本。 
- 原因：在负载均衡器上启用“会话相关性”时，2 元组哈希使用 PA IP（物理地址 IP）而不是分配给 VM 的专用 IP。 如果定向到负载均衡器的流量通过 VPN 到达，或者，如果所有客户端 VM（源 IP）位于同一节点上并且启用了会话相关性，则所有流量都将定向到一个后端 VM。
- 发生次数：通用

### <a name="network-interface"></a>Linux

#### <a name="addingremoving-network-interface"></a>添加/删除网络接口

- 适用于：此问题适用于所有支持的版本。
- 原因：新网络接口无法添加到处于“正在运行”状态的 VM。
- 补救措施：添加或删除网络接口之前，先停止虚拟机。
- 发生次数：通用

#### <a name="primary-network-interface"></a>主网络接口

- 适用于：此问题适用于所有支持的版本。
- 原因：无法更改 VM 的主要 NIC。 删除或分离主要 NIC 导致启动 VM 时出现问题。
- 发生次数：通用

### <a name="public-ip"></a>公共 IP

- 适用于：此问题适用于所有支持的版本。
- 原因：无法更改与负载均衡器关联的公共 IP 的 IdleTimeoutInMinutes 值。 该操作会将公共 IP 置于失败状态。
- 补救措施：若要使公共 IP 恢复成功状态，请将引用公共 IP 的负载均衡器规则上的 IdleTimeoutInMinutes 值更改回原始值（默认值为 4 分钟）。
- 发生次数：通用

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关概述页中的文档链接链接到特定于 Azure 的文档，而不是 Azure Stack Hub 文档。 使用以下链接获取 Azure Stack Hub 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack Hub 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)
  
### <a name="stop-deallocate-vm-results-in-mtu-configuration"></a>Stop-Deallocate VM 导致 MTU 配置

- 适用于：此问题适用于所有支持的版本。
- 原因：对 VM 执行 **停止解除分配** 会导致删除 vm 上的 MTU 配置。 此行为在 Azure 中不一致。
- 发生次数：通用

## <a name="compute"></a>计算

### <a name="cannot-create-a-virtual-machine-scale-set-with-standard_ds2_v2-vm-size-on-portal"></a>无法在门户中使用 Standard_DS2_v2 VM 大小创建规模集

- 适用于：此问题适用于 2002 版本。
- 原因：存在一个门户 bug，它阻止使用Standard_DS2_v2 VM 大小创建规模集。 创建即会产生错误：“{"code":"DeploymentFailed","message":":"至少一项资源部署操作失败。 请列出部署操作以获取详细信息。 请参阅 https://aka.ms/arm-debug 了解使用情况详细信息。","details":[{"code":"BadRequest","message":"{\r\n \" error\": {\r\n \" code\":\" NetworkProfileValidationError\" ,\r\n \" message\":\" 虚拟机大小 Standard_DS2_v2 不在 VM 大小的允许列表中，不能在虚拟机规模集 /subscriptions/x/resourceGroups/RGVMSS/providers/Microsoft.Compute/virtualMachineScaleSets/vmss 的索引 0 处的 VM 上启用加速网络。 允许的大小：.\"\r\n }\r\n}"}]}"
- 补救措施：使用 PowerShell 或资源管理器模板创建虚拟机规模集。

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>VM 概述边栏选项卡未显示正确的计算机名称

- 适用于：此问题适用于所有版本。
- 原因：在概述边栏选项卡中查看 VM 的详细信息时，计算机名称显示为“(不可用)”。 这是针对从专用磁盘/磁盘快照创建的 VM 设计的，也会为市场映像而出现。
- 补救措施：在“设置”下查看“属性”边栏选项卡。

### <a name="nvv4-vm-size-on-portal"></a>门户上的 NVv4 VM 大小

- 适用于：此问题适用于版本 2002 及更高版本。
- 原因：完成 VM 创建体验后，你将看到 VM 大小：NV4as_v4。 如果客户拥有基于 AMD MI25 的 Azure Stack Hub GPU 预览版所需的硬件，那么这些客户可以成功部署 VM。 所有其他客户将无法使用此 VM 大小部署 VM。
- 补救措施：按照设计为 Azure Stack Hub GPU 预览版做准备。

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试启动“已停止-解除分配”的虚拟机时，可能会显示以下错误：“找不到 VM 诊断存储帐户 "diagnosticstorageaccount"。请确保未删除存储帐户”。 如果尝试在启用了启动诊断的情况下启动 VM，但引用的启动诊断存储帐户被删除，则会发生此错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="consumed-compute-quota"></a>已消耗的计算配额

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新虚拟机时，可能会收到一则错误消息，例如“此订阅在此位置的区域 vCPU 总数已达到上限。此订阅使用了所有可用的 50 个区域 vCPU”。 这表示可用的核心配额总计已达到上限。
- 补救措施：请求操作员提供配额更高的附加计划。 编辑当前计划的配额不起作用，也不会反映提高的配额。
- 发生次数：罕见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>在 4 节点的 Azure Stack Hub 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack Hub 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack Hub 部署中进行更新时，仍然不能创建规模集实例。

### <a name="sql-vm"></a>SQL VM

#### <a name="storage-account-creating-failure-when-configuring-auto-backup"></a>配置自动备份时存储帐户创建失败

- 适用于：此问题适用于版本 2002。
- 原因：当使用新的存储帐户配置 SQL VM 的自动备份时，它会失败并显示错误“部署模板验证失败。找不到 "SqlAutobackupStorageAccountKind" 的模板参数。”
- 补救措施：应用最新的 2002 修补程序。

#### <a name="auto-backup-cannot-be-configured-with-tls-12-enabled"></a>无法在启用了 TLS 1.2 的情况下配置自动备份

- 适用于：此问题适用于新安装的 2002 及更高版本，或任何启用了 TLS 1.2 的早期版本。
- 原因：当使用现有存储帐户配置 SQL VM 的自动备份时，它会失败并显示错误“SQL Server IaaS 代理:基础连接已关闭:发送时出现意外错误。”
- 发生次数：通用

## <a name="storage"></a>存储

### <a name="retention-period-revert-to-0"></a>保留期恢复为 0

- 适用于：此问题适用于版本 2002 和 2005。
- 原因：如果以前在保留期设置中指定了非 0 的时间段，则它将在 2002 或 2005 更新期间恢复回 0（此设置的默认值）。 这个 0 天的设置会在更新完成后立即生效，导致所有现有的已删除存储帐户和任何新删除的存储帐户被系统立即清除，并会被标记为需要进行定期垃圾回收（每小时运行一次）。 
- 补救措施：将保留期手动指定为正确的时间段。 但是，在指定新的保留期之前已进行垃圾回收的任何存储帐户都不可恢复。  

## <a name="resource-providers"></a>资源提供程序

### <a name="sqlmysql"></a>SQL/MySQL

- 适用于：此问题适用于版本 2002。 
- 原因：如果标记包含 SQL 资源提供程序 (RP) 版本 1.1.33.0 或更早版本，则在更新标记时，不会加载 SQL/MySQL 的边栏选项卡。
- 补救措施：将 RP 更新到版本 1.1.47.0

### <a name="app-service"></a>应用服务

- 适用于：此问题适用于版本 2002。
- 原因：如果标记包含应用服务资源提供程序 (RP) 版本 1.7 和更早版本，则在更新标记时，不会加载应用服务的边栏选项卡。
- 补救措施：将 RP 更新到版本 [2020 Q2](azure-stack-app-service-update.md)。

## <a name="powershell"></a>PowerShell

[!Include[Known issue for install - one](../includes/known-issue-az-install-1.md)]

[!Include[Known issue for install - two](../includes/known-issue-az-install-2.md)]

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-2002"
## <a name="archive"></a>Archive

若要访问旧版本的已存档已知问题，请使用左侧目录上方的版本选择器下拉列表，然后选择要查看的版本。

## <a name="next-steps"></a>后续步骤

- [查看更新活动清单](release-notes-checklist.md)
- [查看安全更新列表](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-known-issues"></a>1910存档的已知问题
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-known-issues"></a>1908存档的已知问题
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-known-issues"></a>1907 已存档的已知问题
::: moniker-end
::: moniker range="azs-1906"
## <a name="1906-archived-known-issues"></a>1906 已存档的已知问题
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-known-issues"></a>1905 已存档的已知问题
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-known-issues"></a>1904 已存档的已知问题
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-known-issues"></a>1903 已存档的已知问题
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-known-issues"></a>1902 已存档的已知问题
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-known-issues"></a>1901 已存档的已知问题
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-known-issues"></a>1811 已存档的已知问题
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-known-issues"></a>1809 已存档的已知问题
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-known-issues"></a>1808 已存档的已知问题
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-known-issues"></a>1807 已存档的已知问题
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-known-issues"></a>1805 已存档的已知问题
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-known-issues"></a>1804 已存档的已知问题
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-known-issues"></a>1803 已存档的已知问题
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-known-issues"></a>1802 已存档的已知问题
::: moniker-end

::: moniker range="<azs-2002"
你可以在 " [**资源 > 发行说明存档**](./relnotearchive/known-issues-1910.md)" 下，访问左侧目录中 Azure Stack 集线器已知问题的旧版本。 这些存档文章仅供参考目的提供，并不意味着支持这些版本。 有关 Azure Stack Hub 支持的信息，请参阅 [Azure Stack Hub 服务策略](azure-stack-servicing-policy.md)。 如需进一步的帮助，请联系 Microsoft 客户支持服务。
::: moniker-end
