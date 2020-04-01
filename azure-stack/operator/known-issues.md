---
title: Azure Stack 集线器已知问题
description: 了解 Azure Stack 集线器版本中的已知问题。
author: sethmanheim
ms.topic: article
ms.date: 03/20/2020
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: ca29dd169523872b2dcc21b323bc489de5caf9b3
ms.sourcegitcommit: b824c7b9af9ba415ca4fe8d15673b521362f0abb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479239"
---
# <a name="azure-stack-hub-known-issues"></a>Azure Stack 集线器已知问题

本文列出了 Azure Stack 集线器的发行版中的已知问题。 当发现新问题时，将更新此列表。

若要访问不同版本的已知问题，请使用左侧目录上方的 "版本选择器" 下拉列表。

::: moniker range=">=azs-1907"
> [!IMPORTANT]  
> 在应用更新之前，请查看此部分。
::: moniker-end
::: moniker range="<azs-1907"
> [!IMPORTANT]  
> 如果 Azure Stack 集线器实例落后于两个以上的更新，则将其视为不符合。 必须[至少更新到支持的最低版本](azure-stack-servicing-policy.md#keep-your-system-under-support)。 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2002"
## <a name="update"></a>更新

有关已知的 Azure Stack 中心更新问题，请参阅[Azure Stack 中心的更新疑难解答](azure-stack-updates-troubleshoot.md)。

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有受支持的版本。
- 原因：不应使用1804版中引入的两个管理订阅。 订阅类型为**计数**订阅和**消耗**订阅。
- 修正：如果在这两个订阅上运行了资源，请在用户订阅中重新创建这些资源。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅权限

- 适用：此问题适用于所有受支持的版本。
- 原因：无法使用 Azure Stack 中心门户查看订阅的权限。
- 修正：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="storage-account-options"></a>存储帐户选项

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，存储帐户的名称显示为**存储帐户-blob、文件、表、队列**;但 Azure Stack 集线器不支持**文件**。
- 发生次数：常见

### <a name="create-managed-disk-snapshot"></a>创建托管磁盘快照

- 适用：此问题适用于发布2002。
- 原因：在用户门户中创建托管磁盘快照时，"**帐户类型**" 框为空。 选择 "**创建**" 按钮时，如果帐户类型为空，则快照创建将失败。
- 修正：从 "**帐户类型**" 下拉列表中选择帐户类型，然后创建快照。
- 发生次数：常见

### <a name="alert-for-network-interface-disconnected"></a>网络接口断开连接警报

- 适用：此问题适用于1908及更高版本。
- 原因：当电缆与网络适配器断开连接时，管理员门户中不会显示警报。 此问题是由 Windows Server 2019 中默认禁用此错误引起的。
- 发生次数：常见

## <a name="networking"></a>网络

### <a name="network-security-groups"></a>网络安全组

- 适用：此问题适用于所有受支持的版本。 
- 原因：无法在 NSG 中创建显式**DenyAllOutbound**规则，因为这将阻止 VM 部署所需的所有基础结构的内部通信完成。
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

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack 集线器的链接。 使用以下链接了解 Azure Stack 中心文档：

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 集线器上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>VM 概述边栏选项卡未显示正确的计算机名称

- 适用：此问题适用于2002及更高版本。
- 原因：在 "概览" 边栏选项卡中查看 VM 的详细信息时，计算机名称显示为 " **（不可用）** "。
- 修正：查看 "**设置**" 下的 "**属性**" 边栏选项卡。

### <a name="nvv4-vm-size-on-portal"></a>门户上的 NVv4 VM 大小

- 适用：此问题适用于2002及更高版本。
- 原因：遍历 VM 创建体验时，你会看到 VM 大小为： NV4as_v4。 对于具有基于 AMD Mi25 的 Azure Stack 集线器 GPU 预览所需硬件的客户，可以成功部署 VM。 所有其他客户具有此 VM 大小的 VM 部署失败。
- 修正：按照设计准备 Azure Stack 集线器 GPU 预览。

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用启动诊断，但要删除启动诊断存储帐户，则会发生此错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="consumed-compute-quota"></a>使用的计算配额

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的虚拟机时，可能会收到一个错误，例如，**此订阅在此位置的区域个 vcpu 的容量。此订阅使用全部50个可用区域个 vcpu。** 这表示已达到可用内核总数的配额。
- 修正：向操作员询问附加配额的附加计划。 编辑当前计划的配额将不起作用或反映增加的配额。
- 发生次数：罕见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>在4节点 Azure Stack 中心环境中的修补和更新过程中创建故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在包含3个容错域的可用性集中创建 Vm 并创建虚拟机规模集实例在 Azure Stack 集线器环境的4节点上的更新过程中出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在 Azure Stack 中心部署的4节点上，在更新过程中创建规模集实例仍不可用。

### <a name="sql-vm-provision-will-be-failed-in-asdk"></a>SQL VM 预配将在 ASDK 中失败
- 适用：此问题仅适用于 ASDK 2002。 
- 原因：在 ASDK 2002 中创建新的 SQL VM 时，可能会收到一条错误消息 **"扩展名为发布服务器 ' SqlIaaSAgent '"，类型为 ""，在扩展存储库中找不到类型处理程序版本 "2.0"。** Azure Stack 集线器中没有 "SqlIaaSAgent" 2.0。 


## <a name="resource-providers"></a>资源提供程序

### <a name="sqlmysql"></a>SQL/MySQL

- 适用：此问题适用于发布2002。 
- 原因：如果 stamp 包含 SQL 资源提供程序（RP）版本1.1.33.0 或更早版本，则在更新 stamp 时，将不会加载 SQL server 边栏选项卡。
- 修正：将 RP 更新到版本1.1.47。0

### <a name="app-service"></a>应用服务

- 适用：此问题适用于发布2002。
- 原因：如果 stamp 包含应用服务资源提供程序（RP）版本1.7 及更低版本，则在更新 stamp 后，应用服务的边栏选项卡将不会加载。
- 修正：将 RP 更新到版本1.8。

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1910"
## <a name="update"></a>更新

有关已知的 Azure Stack 中心更新问题，请参阅[Azure Stack 中心的更新疑难解答](azure-stack-updates-troubleshoot.md)。

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有受支持的版本。
- 原因：不应使用1804版中引入的两个管理订阅。 订阅类型为**计数**订阅和**消耗**订阅。
- 修正：如果在这两个订阅上运行了资源，请在用户订阅中重新创建这些资源。
- 发生次数：常见

### <a name="subscriptions-lock-blade"></a>订阅锁定边栏选项卡

- 适用：此问题适用于所有受支持的版本。
- 原因：在管理员门户中，用户订阅的 "**锁定**" 边栏选项卡具有两个称为 "**订阅**" 的按钮。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅权限

- 适用：此问题适用于所有受支持的版本。
- 原因：无法使用 Azure Stack 中心门户查看订阅的权限。
- 修正：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

### <a name="upload-blob-with-oauth-error"></a>上传具有 OAuth 错误的 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

### <a name="upload-blob-option-unsupported"></a>不支持上传 blob 选项

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试上传上传边栏选项卡中的 blob 时，有一个选择**aad**或**密钥身份验证**的选项，但 Azure Stack 集线器不支持**AAD** 。
- 发生次数：常见


### <a name="alert-for-network-interface-disconnected"></a>网络接口断开连接警报

- 适用：此问题适用于1908及更高版本。
- 原因：当电缆与网络适配器断开连接时，管理员门户中不会显示警报。 此问题是由 Windows Server 2019 中默认禁用此错误引起的。
- 发生次数：常见

### <a name="incorrect-tooltip-when-creating-vm"></a>创建 VM 时工具提示不正确

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，选择托管磁盘时，如果磁盘类型为 "高级 SSD"，则下拉列表将显示**操作系统磁盘**。 该选项旁的工具提示指出**可以使用 Azure 免费帐户免费访问某些 OS 磁盘大小**;但是，这对 Azure Stack 中心无效。 此外，该列表还包括**可用的免费帐户**，该帐户对 Azure Stack 中心也无效。
- 发生次数：常见

### <a name="vpn-troubleshoot-and-metrics"></a>VPN 故障排除和指标

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，vpn 网关资源中的**Vpn 故障排除**功能和**度量值**将出现，但是 Azure Stack 集线器不支持此功能。
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
- 原因：在用户门户中，创建路由表时，"**虚拟网络网关**" 将显示为 "下一跃点类型" 选项;但 Azure Stack 集线器不支持此项。
- 发生次数：常见

### <a name="storage-account-options"></a>存储帐户选项

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，存储帐户的名称显示为**存储帐户-blob、文件、表、队列**;但 Azure Stack 集线器不支持**文件**。
- 发生次数：常见

### <a name="storage-account-configuration"></a>存储帐户配置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建存储帐户并查看其**配置**时，无法保存配置更改，因为这会导致 AJAX 错误。
- 发生次数：常见

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>SQL 资源提供程序中的容量监视保留加载

- 适用：此问题适用于 Azure Stack 集线器1910更新或更高版本，并且安装了 SQL 资源提供程序版本1.1.33.0 或更早版本。
- 原因：当前版本的 SQL 资源提供程序与1910更新中的某些最新门户更改不兼容。
- 修正：在 Azure Stack 集线器升级到1910更新后，请按照资源提供程序更新过程应用 SQL 资源提供程序修补程序1.1.47.0 （[SQL RP 版本 1.1.47.0](https://aka.ms/azurestacksqlrp11470)）。 对于 MySQL 资源提供程序，还建议在 Azure Stack 集线器升级到1910更新（[MYSQL RP 版本 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)）后应用 mysql 资源提供程序修补程序1.1.47.0。
- 发生次数：常见

### <a name="access-control-iam"></a>访问控制（IAM）

- 适用：此问题适用于所有受支持的版本。
- 原因： IAM 扩展已过期。 Azure Stack 集线器随附的 Ibiza 门户引入了新的行为，如果用户正在为未在全局订阅选择器中选择的订阅（在用户门户中为**目录 + 订阅**）打开 "**访问控制（IAM）** " 边栏选项卡，则该行为会导致 RBAC 扩展失败。 边栏选项卡以循环显示**加载**，用户无法向订阅添加新的角色。 "**添加**" 边栏选项卡还会在循环中显示**加载**。
- 修正：确保在 "**目录 + 订阅**" 菜单中选中订阅。 可以从门户顶部、在 "**通知**" 按钮附近或通过显示的 "**所有资源**" 边栏选项卡上的快捷方式访问该菜单 **。打开目录 + 订阅设置**。 必须在此菜单中选择订阅。

### <a name="sql-resource-provider"></a>SQL 资源提供程序

- 适用：此问题适用于运行1908或更早版本的戳记。
- 原因：部署 SQL 资源提供程序（RP）版本1.1.47.0 时，门户不会显示与 SQL RP 关联的资产。
- 修正：删除 RP，升级 stamp，并重新部署 SQL RP。

### <a name="activity-log-blade"></a>活动日志边栏选项卡

- 适用：此问题适用于运行1907或更高版本的戳记。 <!-- Note: Applies to 2002 as well -->
- 原因：访问活动日志时，门户只显示条目的第一页。 **加载更多结果**将不会加载附加条目。
- 修正：调整筛选器中的时间范围以查看位于第一页之后的项。

## <a name="networking"></a>网络

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
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 中心目前不支持此功能。
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
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 疑难解答

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack 集线器的链接。 使用以下链接了解 Azure Stack 中心文档：

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 集线器上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用启动诊断，但要删除启动诊断存储帐户，则会发生此错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="consumed-compute-quota"></a>使用的计算配额

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的虚拟机时，可能会收到一个错误，例如，**此订阅在此位置的区域个 vcpu 的容量。此订阅使用全部50个可用区域个 vcpu。** 这表示已达到可用内核总数的配额。
- 修正：向操作员询问附加配额的附加计划。 编辑当前计划的配额将不起作用或反映增加的配额。
- 发生次数：罕见

### <a name="privileged-endpoint"></a>特权终结点

- 适用：此问题适用于1910及更早版本。
- 原因：无法从运行非英语版本的 Windows 的计算机连接到特权终结点（ERC Vm）。
- 修正：这是在版本晚于1910的版本中修复的已知问题。 一种解决方法是，可以使用**en-us**区域性运行**新的 pssession**并**输入-pssession** Powershell cmdlet;有关示例，请使用以下脚本设置区域性： https://resources.oreilly.com/examples/9780596528492/blob/master/Use-Culture.ps1。
- 发生次数：罕见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>在4节点 Azure Stack 中心环境中的修补和更新过程中创建故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在包含3个容错域的可用性集中创建 Vm 并创建虚拟机规模集实例在 Azure Stack 集线器环境的4节点上的更新过程中出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在 Azure Stack 中心部署的4节点上，在更新过程中创建规模集实例仍不可用。

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
- 原因：尝试安装 Azure Stack 中心更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序（URP）无法正确地将文件从存储容器传输到内部基础结构共享进行处理。
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
- 原因：在管理员门户中，用户订阅的 "**锁定**" 边栏选项卡上有两个标记为 "**订阅**" 的按钮。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅权限

- 适用：此问题适用于所有受支持的版本。
- 原因：无法使用 Azure Stack 中心门户查看订阅的权限。
- 修正：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

### <a name="alert-for-network-interface-disconnected"></a>网络接口断开连接警报

- 适用：此问题适用于1908版本。
- 原因：当电缆与网络适配器断开连接时，管理员门户中不会显示警报。 此问题是由 Windows Server 2019 中默认禁用此错误引起的。
- 发生次数：常见

## <a name="networking"></a>网络

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
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 中心目前不支持此功能。
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
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 疑难解答

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack 集线器的链接。 使用以下链接了解 Azure Stack 中心文档：

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 集线器上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用启动诊断，但要删除启动诊断存储帐户，则会发生此错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>在4节点 Azure Stack 中心环境中的修补和更新过程中创建故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在包含3个容错域的可用性集中创建 Vm 并创建虚拟机规模集实例在 Azure Stack 集线器环境的4节点上的更新过程中出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在4节点 Azure Stack 集线器上，在更新过程中创建的规模集实例仍不可用。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有受支持的版本。
- 原因：启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 修正：在预配后，使用适用于 Linux 扩展的 VM 访问来实现 SSH 密钥，或使用基于密码的身份验证。
- 发生次数：常见

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无效

- 适用：此问题适用于所有受支持的版本。
- 原因：新的 "重置密码" 边栏选项卡出现在规模集 UI 中，但 Azure Stack 集线器不支持在规模集中重置密码。
- 修正：无。
- 发生次数：常见

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上的 Rainy 云

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击空图表会打开 "rainy cloud" 边栏选项卡。 这是规模集诊断信息的图表，例如 CPU 百分比，而不是当前 Azure Stack 中心内部版本支持的功能。
- 修正：无。
- 发生次数：常见

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用：此问题适用于所有受支持的版本。    
- 原因： "虚拟机诊断设置" 边栏选项卡中有一个 "**接收器**" 选项卡，该选项卡要求提供**Application insights 帐户**。 这是新的边栏选项卡的结果，在 Azure Stack 集线器中尚不支持。
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
- 原因：尝试安装 1907 Azure Stack 中心更新时，更新的状态可能会失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序（URP）无法正确地将文件从存储容器传输到内部基础结构共享进行处理。
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
- 原因：无法使用 Azure Stack 中心门户查看订阅的权限。
- 修正：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"存储帐户**配置**" 边栏选项卡显示了更改**安全传输类型**的选项。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，当你尝试使用**OAuth （预览版）** 选项上载 blob 时，该任务将失败并出现错误消息。
- 修正：使用 SAS 选项上传 blob。
- 发生次数：常见

## <a name="networking"></a>网络

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
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了一个使用**服务终结点**的选项。 Azure Stack 中心目前不支持此功能。
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
- 原因：在用户门户中，"**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中创建时，在**虚拟网络网关**的 "资源" 菜单中，你将看到一个启用**主动-主动**配置的选项。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 疑难解答

- 适用：此问题适用于所有受支持的版本。
- 原因：在用户门户中，"**连接**" 边栏选项卡显示名为 " **VPN 疑难解答**" 的功能。 Azure Stack 中心目前不支持此功能。
- 发生次数：常见

### <a name="network-connection-type"></a>网络连接类型

- 适用：此问题适用于任何1906或1907环境。 
- 原因：在用户门户中，" **AddConnection** " 边栏选项卡显示使用**vnet 到 vnet**的选项。 Azure Stack 中心目前不支持此功能。 
- 发生次数：常见 

#### <a name="documentation"></a>文档

- 适用：此问题适用于所有受支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接，指向特定于 Azure 的文档，而不是 Azure Stack 集线器的链接。 使用以下链接了解 Azure Stack 中心文档：

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 集线器上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有受支持的版本。
- 原因：创建新的 Windows 虚拟机（VM）时，可能会显示以下错误：**无法启动虚拟机 "VM-名称"。错误：无法更新 VM "vm-name" 的串行输出设置**。 如果在 VM 上启用启动诊断，但要删除启动诊断存储帐户，则会发生此错误。
- 修正：使用之前使用的同一名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>在4节点 Azure Stack 中心环境中的修补和更新过程中创建故障

- 适用：此问题适用于所有受支持的版本。
- 原因：在包含3个容错域的可用性集中创建 Vm 并创建虚拟机规模集实例在 Azure Stack 集线器环境的4节点上的更新过程中出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 修正：你可以在可用性集中创建单个 Vm，其中包含2个容错域。 但是，在4节点 Azure Stack 集线器上，在更新过程中创建的规模集实例仍不可用。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有受支持的版本。
- 原因：启用了 SSH 授权的 Ubuntu 18.04 VM 不允许使用 SSH 密钥登录。
- 修正：在预配后，使用适用于 Linux 扩展的 VM 访问来实现 SSH 密钥，或使用基于密码的身份验证。
- 发生次数：常见

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无效

- 适用：此问题适用于1906和1907版本。
- 原因：新的 "重置密码" 边栏选项卡出现在规模集 UI 中，但 Azure Stack 集线器不支持在规模集中重置密码。
- 修正：无。
- 发生次数：常见

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上的 Rainy 云

- 适用：此问题适用于1906和1907版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击空图表会打开 "rainy cloud" 边栏选项卡。 这是规模集诊断信息的图表，例如 CPU 百分比，而不是当前 Azure Stack 中心内部版本支持的功能。
- 修正：无。
- 发生次数：常见

### <a name="issues-creating-resources"></a>创建资源时出现问题

- 适用：此问题适用于1906版本。
- 原因：在1906中存在一个已知问题，其中包含用于创建资源的自定义角色和权限分配。 即使您拥有正确的权限，也可能会面临创建资源的问题。
- 修正：若要缓解此问题，请更新到版本1907。
- 发生次数：常见

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用：此问题适用于1906和1907版本。    
- 原因： "虚拟机诊断设置" 边栏选项卡中有一个 "**接收器**" 选项卡，该选项卡要求提供**Application insights 帐户**。 这是新的边栏选项卡的结果，在 Azure Stack 集线器中尚不支持。
- 修正：无。
- 发生次数：常见

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1907"
## <a name="archive"></a>存档

若要访问较早版本的已存档的已知问题，请使用左侧目录上方的 "版本选择器" 下拉列表，然后选择要查看的版本。

## <a name="next-steps"></a>后续步骤

- [查看更新活动清单](release-notes-checklist.md)
- [查看安全更新列表](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1906"
## <a name="1906-archived-known-issues"></a>1906存档的已知问题
::: moniker-end
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

::: moniker range="<azs-1907"
可以在[TechNet 库中访问 Azure Stack 中心已知问题的旧版本](https://aka.ms/azsarchivedrelnotes)。 这些存档文档仅供参考目的提供，并不表示支持这些版本。 有关 Azure Stack 中心支持的详细信息，请参阅[Azure Stack 中心维护策略](azure-stack-servicing-policy.md)。 若要获得更多帮助，请与 Microsoft 客户支持服务联系。
::: moniker-end
