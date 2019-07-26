---
title: Azure Stack 1906 已知问题 |Microsoft Docs
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
ms.date: 07/12/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: bcdcb43cb58535a4ab4a8a543c1fbf31824dacbc
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494135"
---
# <a name="azure-stack-1906-known-issues"></a>Azure Stack 1906 已知问题

本文列出了 Azure Stack 1906 版中的已知问题。 每当发现新的问题，此列表就会更新。

> [!IMPORTANT]  
> 在应用更新之前，请先查看本部分。

## <a name="update-process"></a>更新过程

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试安装 1906 Azure Stack 更新时, 更新的状态可能会失败, 并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。 
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。 然后, URP 将清理上一次尝试的文件, 并重新启动下载。 如果此问题仍然存在, 建议使用[导入和安装更新部分](azure-stack-apply-updates.md#import-and-install-updates)手动上载更新包。
- 发生次数：通用

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。
- 补救措施：如果在这两个订阅上运行了资源, 请在用户订阅中重新创建这些资源。
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

### <a name="subscriptions-properties-blade"></a>订阅属性边栏选项卡
- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员门户中, 订阅的 "**属性**" 边栏选项卡未正确加载
- 补救措施：你可以在 "订阅概述" 边栏选项卡的 "概要" 窗格中查看这些订阅属性。
- 发生次数：通用

### <a name="storage-account-settings"></a>存储帐户设置

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中, "存储帐户**配置**" 边栏选项卡会显示一个用于更改**安全传输类型**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：通用

### <a name="upload-blob"></a>上传 blob

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：通用

### <a name="update"></a>Update

- 适用于：此问题适用于1906版本。
- 原因：在操作员门户中, 修补程序的更新状态显示更新的状态不正确。 初始状态表明即使仍在进行, 也无法安装更新。
- 补救措施：刷新门户, 状态将更新为 "正在进行"。
- 发生次数：间歇性

## <a name="networking"></a>网络

### <a name="service-endpoints"></a>服务终结点

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中, "**虚拟网络**" 边栏选项卡会显示使用**服务终结点**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：通用

### <a name="network-interface"></a>网络接口

- 适用于：此问题适用于所有支持的版本。
- 原因：无法向处于**运行**状态的 VM 添加新的网络接口。
- 补救措施：请在添加/删除网络接口之前停止虚拟机。
- 发生次数：通用

### <a name="virtual-network-gateway"></a>虚拟网络网关

#### <a name="alerts"></a>警报

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中, "**虚拟网络网关**" 边栏选项卡会显示使用**警报**的选项。 Azure Stack 当前不支持此功能。
- 发生次数：通用

#### <a name="active-active"></a>主动-主动

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中创建时, 在**虚拟网络网关**的 "资源" 菜单中, 你将看到一个启用**主动-主动**配置的选项。 Azure Stack 当前不支持此功能。
- 发生次数：通用

#### <a name="vpn-troubleshooter"></a>VPN 疑难解答

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中, "**连接**" 边栏选项卡显示了一个名为 " **VPN 疑难解答**" 的功能。 Azure Stack 当前不支持此功能。
- 发生次数：通用

#### <a name="documentation"></a>文档

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟网络网关的 "概述" 页中的文档链接指向特定于 Azure 的文档, 而不是 Azure Stack。 请使用以下链接获取 Azure Stack 文档:

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>负载均衡器

#### <a name="add-backend-pool"></a>添加后端池

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中, 如果尝试将**后端池**添加到**负载均衡器**, 则操作将失败, 并出现错误消息 "**无法更新负载平衡器 ...** "。
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
- 原因：创建新的 Windows 虚拟机 (VM) 时, 可能会显示以下错误:**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="virtual-machine-scale-set"></a>虚拟机扩展集

#### <a name="centos"></a>CentOS

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟机规模集创建体验提供基于 CentOS 的7.2 作为部署选项。 Azure Stack 不提供 CentOS 7.2。
- 补救措施：为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。
- 发生次数：通用

#### <a name="remove-scale-set"></a>删除规模集

- 适用于：此问题适用于所有支持的版本。
- 原因：不能从 "**虚拟机规模集**" 边栏选项卡中删除规模集。
- 补救措施：选择要删除的规模集，然后在“概述”窗格中单击“删除”按钮。
- 发生次数：通用

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在4节点 Azure Stack 环境的修补和更新过程中创建故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例在 Azure Stack 环境中的更新过程中出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 补救措施：可以在可用性集中创建单个 Vm, 其中包含2个容错域。 但是, 在4节点 Azure Stack 上, 在更新过程中创建的规模集实例仍不可用。

#### <a name="scale-set-instance-view-blade-does-not-load"></a>"规模集实例视图" 边栏选项卡未加载

- 适用于：此问题适用于1904、1905和1906版本。
- 原因：位于 Azure Stack 门户上的虚拟机规模集的 "实例视图" 边栏选项卡中 **> 虚拟机规模集 > AnyScaleSet > 实例 > AnyScaleSetInstance**无法加载, 并显示 "rainy cloud" 映像。
- 补救措施：目前没有任何补救措施, 我们正在努力解决问题。 在此之前, 请使用 CLI 命令`az vmss get-instance-view`获取规模集的实例视图。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用于：此问题适用于所有支持的版本。
- 原因：如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。
- 补救措施：在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。
- 发生次数：通用

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无效

- 适用于：此问题适用于1906版本。
- 原因："规模集" UI 中将出现一个新的 "重置密码" 边栏选项卡, 但 Azure Stack 不支持在规模集中重置密码。
- 补救措施：无。
- 发生次数：通用

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上的 Rainy 云

- 适用于：此问题适用于1906版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击空图表会打开 "rainy cloud" 边栏选项卡。 这是规模集诊断信息的图表, 例如 CPU 百分比, 而不是当前 Azure Stack 生成中支持的功能。
- 补救措施：无。
- 发生次数：通用

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用于：此问题适用于1906版本。
- 原因："虚拟机诊断设置" 边栏选项卡中有一个 "**接收器**" 选项卡, 该选项卡要求提供**应用程序见解帐户**。 这是新的边栏选项卡的结果, 在 Azure Stack 尚不受支持。
- 补救措施：无。
- 发生次数：通用

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>后续步骤

- [查看更新活动清单](azure-stack-release-notes-checklist.md)
- [查看安全更新列表](azure-stack-release-notes-security-updates-1906.md)
