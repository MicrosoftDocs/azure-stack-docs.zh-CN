---
title: Azure Stack Hub 已知问题
description: 了解 Azure Stack Hub 发行版中的已知问题。
author: sethmanheim
ms.topic: article
ms.date: 09/18/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 08/13/2020
ms.openlocfilehash: d86149b041abd3737ed03696e2c041bbd24f0392
ms.sourcegitcommit: d197e8d3c3b69c20d09de4c43d8089ec0a993baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/21/2020
ms.locfileid: "90836482"
---
# <a name="azure-stack-hub-known-issues"></a>Azure Stack Hub 已知问题

本文列出了 Azure Stack Hub 发行版中的已知问题。 每当发现新的问题，此列表就会更新。

若要访问不同版本的已知问题，请使用左侧目录上方的版本选择器下拉列表。

::: moniker range=">=azs-1908"
> [!IMPORTANT]  
> 在应用更新之前，请先查看本部分。
::: moniker-end
::: moniker range="<azs-1908"
> [!IMPORTANT]  
> 如果 Azure Stack Hub 实例落后于两个以上的更新，则认为它不符合。 必须[至少更新到最低支持版本才能获得支持](azure-stack-servicing-policy.md#keep-your-system-under-support)。 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2005"
## <a name="update"></a>更新

有关已知的 Azure Stack Hub 更新问题，请参阅[排查 Azure Stack Hub 中的更新问题](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates)。

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

## <a name="compute"></a>计算

### <a name="issues-deploying-virtual-machine-scale-set-with-standard_ds2_v2-size-using-the-portal"></a>使用门户部署 Standard_DS2_v2 大小的虚拟机规模集时出现问题

- 适用：此问题适用于2005版本。
- 原因：门户 bug 导致 Standard_DS2_v2 大小的规模集创建失败。
- 修正：使用 PowerShell 或 CLI 部署此虚拟机规模集 VM 大小。

### <a name="issues-using-vm-extensions-in-ubuntu-server-2004"></a>在 Ubuntu Server 20.04 中使用 VM 扩展的问题

- 适用：此问题适用于 **Ubuntu Server 20.04 LTS**。
- 原因：某些 Linux 分发版已转换为 Python 3.8，并完全删除了 `/usr/bin/python` 用于 python 的旧入口点。 转换到 Python 3.x 的 Linux 分发用户必须确保旧 `/usr/bin/python` 入口点存在，然后再尝试将这些扩展部署到其 vm。 否则，扩展部署可能会失败。
- 补救方法：遵循在 [已启用 Python 3 的 Linux Azure 虚拟机系统中使用 VM 扩展的问题](/azure/virtual-machines/extensions/issues-using-vm-extensions-python-3) 解决步骤，但跳过步骤2，因为 Azure Stack 集线器没有 **Run 命令** 功能。

### <a name="nvv4-vm-size-on-portal"></a>门户上的 NVv4 VM 大小

- 适用于：此问题适用于 2002 及更高版本。
- 原因：完成 VM 创建体验后，你将看到 VM 大小：NV4as_v4。 对于具有基于 AMD MI25 的 Azure Stack 集线器 GPU 预览所需硬件的客户，可以成功部署 VM。 所有其他客户将无法使用此 VM 大小部署 VM。
- 补救措施：按照设计为 Azure Stack Hub GPU 预览版做准备。

### <a name="consumed-compute-quota"></a>已消耗的计算配额

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新虚拟机时，可能会收到一则错误消息，例如“此订阅在此位置的区域 vCPU 总数已达到上限。此订阅使用了所有可用的 50 个区域 vCPU”。 这表示可用的核心配额总计已达到上限。
- 补救措施：请求操作员提供配额更高的附加计划。 编辑当前计划的配额不起作用，也不会反映提高的配额。
- 发生次数：罕见

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>VM 概述边栏选项卡未显示正确的计算机名称

- 适用于：此问题适用于所有版本。
- 原因：在概述边栏选项卡中查看 VM 的详细信息时，计算机名称显示为“(不可用)”。 这种设计适用于从专用磁盘/磁盘快照创建的 Vm，也为 Marketplace 映像显示。
- 补救措施：在“设置”下查看“属性”边栏选项卡。

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>在 4 节点的 Azure Stack Hub 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack Hub 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack Hub 部署中进行更新时，仍然不能创建规模集实例。

## <a name="storage"></a>存储

### <a name="retention-period-reverts-to-0"></a>保持期将恢复为0

- 适用：此问题适用于版本2002和2005。
- 原因：如果在 "保持期" 设置中指定0以外的时间段，则它会在2002或2005更新期间)  (此设置的默认值。 "0 天" 设置将在更新完成后立即生效，这会使所有现有已删除的存储帐户和任何即将发生的新删除存储帐户立即退出，并标记为定期垃圾回收 (每小时运行) 。
- 修正：手动将保留期指定为正确的时间段。 指定新的保持期之前已经过垃圾回收的任何存储帐户都是不可恢复的。  

## <a name="resource-providers"></a>资源提供程序

### <a name="sqlmysql"></a>SQL/MySQL

- 适用于：此问题适用于版本 2002。
- 原因：如果标记包含 SQL 资源提供程序 (RP) 版本 1.1.33.0 或更早版本，则在更新标记时，不会加载 SQL/MySQL 的边栏选项卡。
- 补救措施：将 RP 更新到版本 1.1.47.0。

### <a name="app-service"></a>应用服务

- 适用于：此问题适用于版本 2002。
- 原因：如果标记包含应用服务资源提供程序 (RP) 版本 1.7 和更早版本，则在更新标记时，不会加载应用服务的边栏选项卡。
- 补救措施：将 RP 更新到版本 2002 Q2。

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

## <a name="compute"></a>计算

### <a name="cannot-create-a-virtual-machine-scale-set-with-standard_ds2_v2-vm-size-on-portal"></a>无法在门户上创建 Standard_DS2_v2 VM 大小的虚拟机规模集

- 适用于：此问题适用于 2002 版本。
- 原因：存在一个门户 bug，该 bug 阻止创建具有 Standard_DS2_v2 VM 大小的虚拟机规模集。 创建即会产生错误：“{"code":"DeploymentFailed","message":":"至少一项资源部署操作失败。 请列出部署操作以获取详细信息。 请参阅 https://aka.ms/arm-debug 了解使用情况详细信息。","details":[{"code":"BadRequest","message":"{\r\n \" error\": {\r\n \" code\":\" NetworkProfileValidationError\" ,\r\n \" message\":\" 虚拟机大小 Standard_DS2_v2 不在 VM 大小的允许列表中，不能在 VM 规模集 /subscriptions/x/resourceGroups/RGVMSS/providers/Microsoft.Compute/virtualMachineScaleSets/vmss 的索引 0 处的 VM 上启用加速网络。 允许的大小：.\"\r\n }\r\n}"}]}"
- 修正：使用 PowerShell 或 resource manager 模板创建虚拟机规模集。

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>VM 概述边栏选项卡未显示正确的计算机名称

- 适用于：此问题适用于所有版本。
- 原因：在概述边栏选项卡中查看 VM 的详细信息时，计算机名称显示为“(不可用)”。 这种设计适用于从专用磁盘/磁盘快照创建的 Vm，也为 Marketplace 映像显示。
- 补救措施：在“设置”下查看“属性”边栏选项卡。

### <a name="nvv4-vm-size-on-portal"></a>门户上的 NVv4 VM 大小

- 适用于：此问题适用于版本 2002 及更高版本。
- 原因：完成 VM 创建体验后，你将看到 VM 大小：NV4as_v4。 对于具有基于 AMD MI25 的 Azure Stack 集线器 GPU 预览所需硬件的客户，可以成功部署 VM。 所有其他客户将无法使用此 VM 大小部署 VM。
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

### <a name="retention-period-revert-to-0"></a>保持期恢复为0

- 适用：此问题适用于版本2002和2005。
- 原因：如果你之前在 "保留期" 设置中指定了0以外的时间段，则它将恢复为 0 (在2002和2005更新期间此设置) 的默认值。 在更新完成后，0天设置将会生效 immdiately，这会导致所有已删除的存储帐户和任何即将出现的新删除存储帐户立即退出，并标记为定期垃圾回收 (每小时运行一次) 。 
- 修正：手动将保留期指定为适当的时间段。 但是，在指定新的保持期之前，已对所有存储帐户进行了垃圾回收，这是不可恢复的。  

## <a name="resource-providers"></a>资源提供程序

### <a name="sqlmysql"></a>SQL/MySQL

- 适用于：此问题适用于版本 2002。 
- 原因：如果标记包含 SQL 资源提供程序 (RP) 版本 1.1.33.0 或更早版本，则在更新标记时，不会加载 SQL/MySQL 的边栏选项卡。
- 补救措施：将 RP 更新到版本 1.1.47.0

### <a name="app-service"></a>应用服务

- 适用于：此问题适用于版本 2002。
- 原因：如果标记包含应用服务资源提供程序 (RP) 版本 1.7 和更早版本，则在更新标记时，不会加载应用服务的边栏选项卡。
- 补救措施：将 RP 更新到版本 [2020 Q2](azure-stack-app-service-update.md)。

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1910"
## <a name="update"></a>更新

有关已知的 Azure Stack Hub 更新问题，请参阅[排查 Azure Stack Hub 中的更新问题](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates)。

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。 
- 补救措施：如果有资源在这两个订阅上运行，请在用户订阅中重新创建这些资源。
- 发生次数：通用

### <a name="duplicate-subscription-button-in-lock-blade"></a>“锁定”边栏选项卡中的重复“订阅”按钮

- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员门户中，用户订阅的“锁定”边栏选项卡有两个按钮，显示“订阅”。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack Hub 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：通用

### <a name="storage-account-settings"></a>存储帐户设置

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，存储帐户的“配置”边栏选项卡显示用于更改**安全传输类型**的选项。 Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

### <a name="upload-blob-with-oauth-error"></a>使用 OAuth 上传 Blob 时出错

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：通用

### <a name="upload-blob-option-unsupported"></a>不支持上传 Blob 选项

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试从“上传”边栏选项卡上传 Blob 时，有一个选项可用于选择“AAD”或“密钥身份验证”，但是，Azure Stack Hub 不支持“AAD”。  
- 发生次数：通用

### <a name="alert-for-network-interface-disconnected"></a>网络接口已断开连接的警报

- 适用于：此问题适用于 1908 及更高版本。
- 原因：当线缆与网络适配器断开连接时，管理员门户中不显示警报。 此问题的原因是 Windows Server 2019 中默认会禁用此故障。
- 发生次数：通用

### <a name="incorrect-tooltip-when-creating-vm"></a>创建 VM 时出现不正确的工具提示

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中选择类型为“高级 SSD”的托管磁盘时，下拉列表显示“OS 磁盘”。 该选项旁的工具提示指出 **可以使用 Azure 免费帐户免费访问某些 OS 磁盘大小**;但是，这对 Azure Stack 中心无效。 此外，该列表包含“符合免费帐户的条件”，而这对 Azure Stack Hub 而言也是不正确的。
- 发生次数：通用

### <a name="delete-a-storage-container"></a>删除存储容器

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，当用户尝试删除存储容器时，如果用户未切换到“替代 Azure Policy 和 RBAC 角色设置”，则该操作将会失败。
- 补救措施：确保已选中“替代 Azure Policy 和 RBAC 角色设置”框。
- 发生次数：通用

### <a name="refresh-button-on-virtual-machines-fails"></a>虚拟机上的刷新按钮失效

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中导航到“虚拟机”并尝试使用顶部的按钮进行刷新时，状态无法准确更新。
- 补救措施：无论是否已按下刷新按钮，状态每隔 5 分钟都会自动更新。 请等待 5 分钟，然后检查状态。
- 发生次数：通用

### <a name="storage-account-options"></a>存储帐户选项

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，存储帐户名称显示为“存储帐户 - Blob、文件、表、队列”；但是，Azure Stack Hub 不支持“文件”。 
- 发生次数：通用

### <a name="storage-account-configuration"></a>存储帐户配置

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中创建存储帐户并查看其“配置”时，无法保存配置更改，因为这会导致 AJAX 错误。
- 发生次数：通用

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>SQL 资源提供程序中的容量监视保持正在加载状态

- 适用于：此问题适用于已安装 SQL 资源提供程序 1.1.33.0 或更低版本的 Azure Stack Hub 1910 更新或更高版本。
- 原因：当前的 SQL 资源提供程序版本与 1910 更新中最新的一些门户更改不兼容。
- 补救措施：将 Azure Stack Hub 升级到 1910 更新后，请按照资源提供程序更新过程应用 SQL 资源提供程序修补程序 1.1.47.0（[SQL RP 版本 1.1.47.0](https://aka.ms/azurestacksqlrp11470)）。 对于 MySQL 资源提供程序，我们还建议在将 Azure Stack Hub 升级到 1910 更新后，应用 MySQL 资源提供程序修补程序 1.1.47.0（[MySQL RP 版本 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)）。
- 发生次数：通用

### <a name="access-control-iam"></a>访问控制 (IAM)

- 适用于：此问题适用于所有支持的版本。
- 原因：IAM 扩展已过时。 Azure Stack Hub 随附的 Ibiza 门户引入了新的行为，如果用户针对未在全局订阅选择器（用户门户中的“目录 + 订阅”）中选择的订阅打开“访问控制(IAM)”边栏选项卡，则会导致 RBAC 扩展失败。  该边栏选项卡反复显示“正在加载”，且用户无法将新角色添加到订阅。 “添加”边栏选项卡也反复显示“正在加载”。 
- 补救措施：确保已在“目录 + 订阅”菜单中选中该订阅。 可以从门户顶部、“通知”按钮附近，或通过显示以下内容的“所有资源”边栏选项卡上的快捷方式来访问此菜单：“看不到订阅?  请打开‘目录 + 订阅’设置”。 必须在此菜单中选择该订阅。

### <a name="sql-resource-provider"></a>SQL 资源提供程序

- 适用于：此问题适用于运行 1908 或更低版本的阵列。
- 原因：部署 SQL 资源提供程序 (RP) 版本 1.1.47.0 时，门户不显示与 SQL RP 关联的任何资产。
- 补救措施：删除 RP、升级阵列，然后重新部署 SQL RP。

### <a name="activity-log-blade"></a>“活动日志”边栏选项卡

- 适用于：此问题适用于运行 1907 或更高版本的阵列。 <!-- Note: Applies to 2002 as well -->
- 原因：访问活动日志时，门户只显示条目的第一页。 **加载更多结果**将不会加载附加条目。
- 补救措施：调整筛选器中的时间范围，以查看第一页之后的条目。

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡器

- 适用于：此问题适用于所有支持的版本。
- 原因：将可用性集 VM 添加到负载均衡器的后端池时，门户上显示错误消息，指出“无法保存负载均衡器后端池”。 这是门户的表面问题；此功能仍在起作用，VM 在内部已成功添加到后端池。
- 发生次数：通用

### <a name="network-security-groups"></a>网络安全组

- 适用于：此问题适用于所有支持的版本。 
- 原因：无法在 NSG 中创建显式 **DenyAllOutbound** 规则，因为这会使 VM 部署所需的基础结构无法完成所有内部通信。
- 发生次数：通用

### <a name="service-endpoints"></a>服务终结点

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络”边栏选项卡显示使用“服务终结点”的选项。  Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>如果 NIC 未附加到运行中的 VM，则无法删除 NSG

- 适用于：此问题适用于所有支持的版本。
- 原因：如果取消关联 NSG 和未附加到运行中 VM 的 NIC，则该对象的更新 (PUT) 操作会在网络控制器层失败。 NSG 将在网络资源提供程序层（而不是网络控制器上）更新，因此 NSG 转变为失败状态。
- 补救措施：将与 NSG 相关联的 NIC 附加到运行中的 VM，然后取消与 NSG 的关联，或者删除与 NSG 关联的所有 NIC。
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

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="next-hop-type"></a>下一跃点类型

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中创建路由表时，“虚拟网络网关”显示为下一跃点类型选项；但是，Azure Stack Hub 不支持此类型。
- 发生次数：通用

#### <a name="alerts"></a>警报

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络网关”边栏选项卡显示使用“警报”的选项。  Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

#### <a name="active-active"></a>主动-主动

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户（创建期间）和“虚拟网络网关”的资源菜单中，看到用于启用“主动-主动”配置的选项。  Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“连接”边栏选项卡显示一项名为“VPN 故障排除程序”的功能。  Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用于：此问题适用于所有支持的版本。
- 原因：用户门户显示 VPN 网关资源中的“VPN 故障排除”功能和“指标”，但这在 Azure Stack Hub 中不受支持。 
- 发生次数：通用

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关概述页中的文档链接链接到特定于 Azure 的文档，而不是 Azure Stack Hub 文档。 使用以下链接获取 Azure Stack Hub 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack Hub 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="consumed-compute-quota"></a>已消耗的计算配额

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新虚拟机时，可能会收到一则错误消息，例如“此订阅在此位置的区域 vCPU 总数已达到上限。此订阅使用了所有可用的 50 个区域 vCPU”。 这表示可用的核心配额总计已达到上限。
- 补救措施：请求操作员提供配额更高的附加计划。 编辑当前计划的配额不起作用，也不会反映提高的配额。
- 发生次数：罕见

### <a name="privileged-endpoint"></a>特权终结点

- 适用于：此问题适用于 1910 和更低版本。
- 原因：无法从运行非英语版 Windows 的计算机连接到特权终结点 (ERC VM)。
- 补救措施：这是一个已知问题，已在 1910 以上的版本中修复。 解决方法之一是，使用 en-US 区域性运行 New-PSSession 和 Enter-PSSession Powershell cmdlet；有关示例，请使用以下脚本来设置区域性： https://resources.oreilly.com/examples/9780596528492/blob/master/Use-Culture.ps1  。
- 发生次数：罕见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>在 4 节点的 Azure Stack Hub 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack Hub 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack Hub 部署中进行更新时，仍然不能创建规模集实例。

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>1908 更新过程

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试安装 Azure Stack Hub 更新时，更新状态可能会失败并将状态更改为 **PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。  然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题持续存在，建议按照[“安装更新”部分](azure-stack-apply-updates.md#install-updates-and-monitor-progress)的说明手动上传更新包。
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
- 补救措施：可以在“订阅概述”边栏选项卡的“概要”窗格中查看这些订阅属性 。
- 发生次数：通用

### <a name="duplicate-subscription-button-in-lock-blade"></a>“锁定”边栏选项卡中的重复“订阅”按钮

- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员门户中，用户订阅的“锁定”边栏选项卡有两个按钮，其带有“订阅”标签。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack Hub 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：通用

### <a name="storage-account-settings"></a>存储帐户设置

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，存储帐户的“配置”边栏选项卡显示用于更改**安全传输类型**的选项。 Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

### <a name="upload-blob"></a>上传 blob

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：通用

### <a name="alert-for-network-interface-disconnected"></a>网络接口已断开连接的警报

- 适用于：此问题适用于 1908 版本。
- 原因：当线缆与网络适配器断开连接时，管理员门户中不显示警报。 此问题的原因是 Windows Server 2019 中默认会禁用此故障。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡器

- 适用于：此问题适用于所有支持的版本。 
- 原因：将可用性集 VM 添加到负载均衡器的后端池时，门户上显示错误消息，指出“未能保存负载均衡器后端池”。 这是门户的表面问题；此功能仍在起作用，VM 在内部已成功添加到后端池。 
- 发生次数：通用

### <a name="network-security-groups"></a>网络安全组

- 适用于：此问题适用于所有支持的版本。 
- 原因：无法在 NSG 中创建显式 **DenyAllOutbound** 规则，因为这会使 VM 部署所需的基础结构无法完成所有内部通信。
- 发生次数：通用

### <a name="service-endpoints"></a>服务终结点

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络”边栏选项卡显示使用“服务终结点”的选项。  Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>如果 NIC 未附加到运行中的 VM，则无法删除 NSG

- 适用于：此问题适用于所有支持的版本。
- 原因：如果取消关联 NSG 和未附加到运行中 VM 的 NIC，则该对象的更新 (PUT) 操作会在网络控制器层失败。 NSG 将在网络资源提供程序层（而不是网络控制器上）更新，因此 NSG 转变为失败状态。
- 补救措施：将与 NSG 相关联的 NIC 附加到运行中的 VM，然后取消与 NSG 的关联，或者删除与 NSG 关联的所有 NIC。
- 发生次数：通用

### <a name="network-interface"></a>Linux

#### <a name="addingremoving-network-interface"></a>添加/删除网络接口

- 适用于：此问题适用于所有支持的版本。
- 原因：新网络接口无法添加到处于“正在运行”状态的 VM。
- 补救措施：添加/删除网络接口之前，先停止虚拟机。
- 发生次数：通用

#### <a name="primary-network-interface"></a>主要网络接口

- 适用于：此问题适用于所有支持的版本。
- 原因：新网络接口无法添加到处于“正在运行”状态的 VM。
- 补救措施：添加/删除网络接口之前，先停止虚拟机。
- 发生次数：通用

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="next-hop-type"></a>下一跃点类型

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中创建路由表时，“虚拟网络网关”显示为下一跃点类型选项；但是，Azure Stack Hub 不支持此类型。
- 发生次数：通用

#### <a name="alerts"></a>警报

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“虚拟网络网关”边栏选项卡显示使用“警报”的选项。  Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

#### <a name="active-active"></a>主动-主动

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户（创建期间）和“虚拟网络网关”的资源菜单中，看到用于启用“主动-主动”配置的选项。  Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

#### <a name="vpn-troubleshooter"></a>VPN 故障排除程序

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“连接”边栏选项卡显示一项名为“VPN 故障排除程序”的功能。  Azure Stack Hub 目前不支持此功能。
- 发生次数：通用

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关概述页中的文档链接链接到特定于 Azure 的文档，而不是 Azure Stack Hub 文档。 使用以下链接获取 Azure Stack Hub 文档：

  - [网关 SKU](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack Hub 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>在 4 节点的 Azure Stack Hub 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack Hub 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack Hub 上进行更新时，仍然不能创建规模集实例。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用于：此问题适用于所有支持的版本。
- 原因：如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。
- 补救措施：在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。
- 发生次数：通用

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无法进行

- 适用于：此问题适用于所有支持的版本。
- 原因：规模集 UI 中出现新的密码重置边栏选项卡，但 Azure Stack Hub 尚不支持在规模集上重置密码。
- 补救措施：无。
- 发生次数：通用

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上出现哭泣的云

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟机规模集概述页显示空白图表。 单击该空白图表会打开“哭泣的云”边栏选项卡。 这是规模集诊断信息的图表（例如 CPU 百分比），但这不是当前 Azure Stack Hub 内部版本支持的功能。
- 补救措施：无。
- 发生次数：通用

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用于：此问题适用于所有支持的版本。    
- 原因：虚拟机诊断设置边栏选项卡包含“接收器”选项卡，用于请求 **Application Insights 帐户** 。 这是新边栏选项卡中的结果，Azure Stack Hub 中尚不支持此功能。
- 补救措施：无。
- 发生次数：通用

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1908"
## <a name="archive"></a>Archive

若要访问旧版本的已存档已知问题，请使用左侧目录上方的版本选择器下拉列表，然后选择要查看的版本。

## <a name="next-steps"></a>后续步骤

- [查看更新活动清单](release-notes-checklist.md)
- [查看安全更新列表](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1908"
可以访问 [TechNet 库中旧版本 Azure Stack Hub 的已知问题](https://aka.ms/azsarchivedrelnotes)。 提供这些已存档文档仅供参考，并不意味着支持这些版本。 有关 Azure Stack Hub 支持的信息，请参阅 [Azure Stack Hub 服务策略](azure-stack-servicing-policy.md)。 如需进一步的帮助，请联系 Microsoft 客户支持服务。
::: moniker-end
