---
title: Azure Stack 1907 已知问题 |Microsoft Docs
description: 了解 Azure Stack 1907 中的已知问题。
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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: cf09162fb29630ed01834aa6b2b508785206a088
ms.sourcegitcommit: d96adbb821175167f6a4c8f3aba305981d7e7c3e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68685570"
---
# <a name="azure-stack-1907-known-issues"></a>Azure Stack 1907 已知问题

本文列出了 Azure Stack 1907 版中的已知问题。 每当发现新的问题，此列表就会更新。

> [!IMPORTANT]  
> 在应用更新之前，请先查看本部分。

## <a name="update-process"></a>更新过程

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试安装 1907 Azure Stack 更新时, 更新的状态可能会失败, 并将状态更改为**PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。   然后, URP 将清理上一次尝试的文件, 并重新启动下载。 如果此问题仍然存在, 建议使用[导入和安装更新部分](azure-stack-apply-updates.md#import-and-install-updates)手动上载更新包。
- 发生次数：通用

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用于：此问题适用于所有支持的版本。
- 原因：不应使用版本 1804 中引入的两个管理订阅。 这两种订阅类型为“计量订阅”和“消耗订阅”。  
- 补救措施：如果在这两个订阅上运行了资源, 请在用户订阅中重新创建这些资源。
- 发生次数：通用

### <a name="subscriptions-properties-blade"></a>订阅属性边栏选项卡

- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员门户中, 订阅的 "**属性**" 边栏选项卡未正确加载
- 补救措施：您可以在 "**订阅概述**" 边栏选项卡的 "**概要**" 窗格中查看这些订阅属性。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
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

#### <a name="local-network-gateway-deletion"></a>本地网络网关删除

- 适用于：此问题适用于1906版本。
- 原因：在用户门户中, 删除**本地网络网关**会显示以下错误消息:如果没有活动连接, 则**无法删除具有活动连接的本地网络网关**。
- 缓解措施：此问题的修复将在1907中发布。 此问题的解决方法是使用相同的 IP 地址、地址空间和配置详细信息创建新的本地网络网关。 环境更新到1907后, 可以删除旧的 LNG。
- 发生次数：通用

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
- 原因：虚拟网络网关的 "概述" 页中的文档链接指向特定于 Azure 的文档, 而不是 Azure Stack。 使用以下链接获取 Azure Stack 文档:

  - [网关 Sku](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [高可用连接](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [在 Azure Stack 上配置 BGP](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute 线路](azure-stack-connect-expressroute.md)
  - [指定自定义 IPsec/IKE 策略](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时, 可能会显示以下错误:**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。 如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="virtual-machine-scale-set"></a>虚拟机扩展集

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在4节点 Azure Stack 环境的修补和更新过程中创建故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例在 Azure Stack 环境中的更新过程中出现**FabricVmPlacementErrorUnsupportedFaultDomainSize**错误。
- 补救措施：可以在可用性集中创建单个 Vm, 其中包含2个容错域。 但是, 在4节点 Azure Stack 上, 在更新过程中创建的规模集实例仍不可用。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用于：此问题适用于所有支持的版本。
- 原因：如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。
- 补救措施：在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。
- 发生次数：通用

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>虚拟机规模集重置密码无效

- 适用于：此问题适用于1906和1907版本。
- 原因："规模集" UI 中将出现一个新的 "重置密码" 边栏选项卡, 但 Azure Stack 不支持在规模集中重置密码。
- 补救措施：无。
- 发生次数：通用

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>规模集诊断上的 Rainy 云

- 适用于：此问题适用于1906和1907版本。
- 原因：虚拟机规模集概述页显示一个空图表。 单击空图表会打开 "rainy cloud" 边栏选项卡。 这是规模集诊断信息的图表, 例如 CPU 百分比, 而不是当前 Azure Stack 生成中支持的功能。
- 补救措施：无。
- 发生次数：通用

### <a name="virtual-machine-diagnostic-settings-blade"></a>虚拟机诊断设置边栏选项卡

- 适用于：此问题适用于1906和1907版本。    
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
- [查看安全更新列表](azure-stack-release-notes-security-updates-1907.md)
