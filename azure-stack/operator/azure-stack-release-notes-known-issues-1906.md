---
title: 已知问题的 azure Stack 1906 |Microsoft Docs
description: 了解 Azure Stack 1906 中的已知问题。
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
ms.date: 06/28/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 96a29ab11ffc15b35c7035576fa68544caef594b
ms.sourcegitcommit: 1c4eda123857d714109e38bb853eb1ce49af5f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67648045"
---
# <a name="azure-stack-1906-known-issues"></a>Azure Stack 1906 已知问题

本文列出了 Azure Stack 1906年版本中的已知的问题。 每当发现新的问题，此列表就会更新。

> [!IMPORTANT]  
> 在应用更新之前，请先查看本部分。

## <a name="update-process"></a>更新过程

- 适用于：此问题适用于所有支持的版本。
- 原因：在尝试安装 1906 Azure Stack 更新时，更新的状态可能失败，并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。 
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。   URP 然后清理文件从上一次尝试，并重新启动下载。 如果问题仍然存在，我们建议按照以下更新程序包手动上传[导入和安装更新部分](azure-stack-apply-updates.md#import-and-install-updates)。
- 发生次数：常见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。  
- 补救措施：如果您有以下两个订阅上运行的资源，重新在用户订阅中创建它们。
- 发生次数：常见

### <a name="subscription-resources"></a>订阅资源

- 适用于：此问题适用于所有支持的版本。
- 原因：删除用户订阅生成孤立的资源。
- 补救措施：先删除用户资源或整个资源组，然后再删除用户订阅。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="storage-account-settings"></a>存储帐户设置

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，存储帐户**配置**边栏选项卡显示一个选项以更改**安全传输类型**。 在 Azure Stack 中当前不支持该功能。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。 
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：常见

### <a name="template"></a>模板

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，模板部署用户界面不会填充参数的模板名称以 _ （下划线字符）。
- 补救措施：从模板名称中删除 _ （下划线字符）。
- 发生次数：常见

## <a name="networking"></a>网络

### <a name="service-endpoints"></a>服务终结点

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，**虚拟网络**边栏选项卡显示了使用选项**服务终结点**。 在 Azure Stack 中当前不支持此功能。
- 发生次数：常见

### <a name="network-interface"></a>网络接口

- 适用于：此问题适用于所有支持的版本。
- 原因：无法将新的网络接口添加到中的 VM**运行**状态。
- 补救措施：添加/删除网络接口之前停止虚拟机。
- 发生次数：常见

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，**虚拟网络网关**边栏选项卡显示了使用选项**警报**。 在 Azure Stack 中当前不支持此功能。
- 发生次数：常见

#### <a name="active-active"></a>主动-主动

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，在创建时，并在资源菜单中的**虚拟网络网关**，你将看到一个选项以启用**主动-主动**配置。 在 Azure Stack 中当前不支持此功能。
- 发生次数：常见

#### <a name="vpn-troubleshooter"></a>VPN 故障排除工具

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，**连接**边栏选项卡显示名为的功能**VPN 故障排除程序**。 在 Azure Stack 中当前不支持此功能。
- 发生次数：常见

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：而不是 Azure Stack 的特定于 Azure 的文档的链接中的虚拟网络网关的概述页的文档链接。 请有关 Azure Stack 文档，使用以下链接：

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高度可用的连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>负载均衡

#### <a name="add-backend-pool"></a>添加后端池

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，如果尝试添加**后端池**到**负载均衡器**，则操作将失败并显示错误消息**未能更新负载均衡器...** .
- 补救措施：使用 PowerShell、CLI 或资源管理器模板将后端池关联到负载均衡器资源。
- 发生次数：常见

#### <a name="create-inbound-nat"></a>创建入站的 NAT

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户尝试为**负载均衡器**创建**入站 NAT 规则**时，该操作失败并出现错误消息“无法更新负载均衡器...”。 
- 补救措施：使用 PowerShell、CLI 或资源管理器模板将后端池关联到负载均衡器资源。
- 发生次数：常见

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：在创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="centos"></a>CentOS

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟机规模集创建体验提供了基于 CentOS 7.2，作为一个选项来部署。 Azure Stack 不提供 CentOS 7.2。
- 补救措施：为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。
- 发生次数：常见

#### <a name="remove-scale-set"></a>删除规模集

- 适用于：此问题适用于所有支持的版本。
- 原因：无法删除规模集从**虚拟机规模集**边栏选项卡。
- 补救措施：选择要删除的规模集，然后在“概述”窗格中单击“删除”按钮。  
- 发生次数：常见

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在修补和更新在 4 节点 Azure Stack 环境的过程中创建失败

- 适用于：此问题适用于所有支持的版本。
- 原因：在 3 个容错域的可用性集中创建 Vm 并创建虚拟机规模集实例失败，并**FabricVmPlacementErrorUnsupportedFaultDomainSize**过程 4 节点 Azure Stack 上的更新过程中出错环境。
- 补救措施：可以在可用性集的 2 个容错域已成功创建单个 Vm。 但是，在 4 节点 Azure Stack 上的更新过程中创建规模集实例是仍不可用。

#### <a name="scale-set-instance-view-blade-does-not-load"></a>规模集实例视图边栏选项卡不会加载

- 适用于：此问题适用于 1904年、 1905，和 1906年版本。
- 原因：虚拟机规模集实例视图边栏选项卡位于 Azure Stack 门户中**仪表板 > 虚拟机规模集 > AnyScaleSet > 实例 > AnyScaleSetInstance**无法加载，并显示"多雨云"的图像。
- 补救措施：目前尚无修正，我们正在努力修复程序。 在此之前，请使用 CLI 命令`az vmss get-instance-view`获取规模集实例视图。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用于：此问题适用于所有支持的版本。
- 原因：如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。
- 补救措施：在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。
- 发生次数：常见

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码不起作用

- 适用于：此问题适用于 1906年版。
- 原因：一个新重置密码边栏选项卡将显示在规模集中 UI，但 Azure Stack 上的规模集尚不支持重置密码。
- 补救措施：无。
- 发生次数：常见

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>多雨云规模上的设置诊断

- 适用于：此问题适用于 1906年版。
- 原因：虚拟机规模集概述页上显示的空图表。 单击空图表上将打开一个"多雨云"边栏选项卡。 这是有关规模集诊断信息，例如 CPU 百分比、 图表，不是在当前 Azure Stack 内部版本中受支持的功能。
- 补救措施：无。
- 发生次数：常见

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用于：此问题适用于 1906年版。
- 原因：虚拟机诊断设置边栏选项卡包含**接收器**选项卡上，系统要求输入**应用程序见解帐户**。 这是一个新的边栏选项卡的结果，在 Azure Stack 中尚不支持。
- 补救措施：无。
- 发生次数：常见

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>后续步骤

- [查看更新活动清单](azure-stack-release-notes-checklist.md)
- [查看安全更新列表](azure-stack-release-notes-security-updates-1906.md)
