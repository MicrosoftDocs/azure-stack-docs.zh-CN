---
title: Azure Stack 集线器1910已知问题
description: 了解 Azure Stack 集线器1910中的已知问题。
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: 0a23951d53d57b103c442ecf2b5b18f894e0f58b
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248026"
---
# <a name="azure-stack-hub-1910-known-issues"></a>Azure Stack 集线器1910已知问题

本文列出了 Azure Stack Hub 版本中的已知问题。 每当发现新的问题，此列表就会更新。

> [!IMPORTANT]  
> 如果 Azure Stack Hub 实例落后于两个以上的更新，则认为它不符合。 必须[至少更新到最低支持版本才能获得支持](../azure-stack-servicing-policy.md#keep-your-system-under-support)。

## <a name="update"></a>更新

有关已知的 Azure Stack Hub 更新问题，请参阅[排查 Azure Stack Hub 中的更新问题](../azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates)。

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
- 原因：在用户门户中，存储帐户的“配置”边栏选项卡显示用于更改 **安全传输类型** 的选项。 Azure Stack Hub 目前不支持此功能。
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
- 原因：访问活动日志时，门户只显示条目的第一页。 **加载更多结果** 将不会加载附加条目。
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

  - [网关 SKU](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用性连接](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack Hub 上配置 BGP](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](../azure-stack-connect-expressroute.md)
  - [指定自定义的 IPsec/IKE 策略](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

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
