---
title: Azure Stack 1905 的已知问题 | Microsoft Docs
description: 了解 Azure Stack 1905 的已知问题。
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
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/14/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1cd35416188cbd10827dd74fa6ba702d67c675ad
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248041"
---
# <a name="azure-stack-1905-known-issues"></a>Azure Stack 1905 的已知问题

本文列出了 Azure Stack 版本 1905 中的已知问题。 每当发现新的问题，此列表就会更新。

> [!IMPORTANT]  
> 在应用更新之前，请先查看本部分。

## <a name="update-process"></a>更新过程

### <a name="host-node-update-prerequisite-failure"></a>主机节点更新先决条件失败

- 适用：此问题适用于1905更新。
- 原因：尝试安装 1905 Azure Stack 更新时，更新的状态可能因 **主机节点更新必备组件** 失败。 这通常是因为主机节点没有足够的可用磁盘空间而导致的。
- 修正：请联系 Azure Stack 支持人员，以获得在主机节点上清除磁盘空间的帮助。
- 发生次数：不常见

### <a name="preparation-failed"></a>准备失败

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试安装 1905 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为 **PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。 1905 更新包大于以前的更新包，导致此问题更可能发生。
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。  然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题持续存在，建议按照[“导入并安装更新”部分](../azure-stack-apply-updates.md)的说明手动上传更新包。
- 发生次数：通用

## <a name="portal"></a>门户

### <a name="subscription-resources"></a>订阅资源

- 适用于：此问题适用于所有支持的版本。
- 原因：删除用户订阅会导致孤立的资源。
- 修正：首先删除用户资源或整个资源组，然后删除用户订阅。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅权限

- 适用于：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 补救措施：使用 [PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：通用

### <a name="marketplace-management"></a>市场管理

- 适用：此问题适用于1904和1905
- 原因：登录到管理员门户时，marketplace 管理屏幕不可见。
- 修正：刷新浏览器或中转到 " **设置** "，然后选择 " **重置为默认设置**" 选项。
- 出现次数：间歇

### <a name="docker-extension"></a>Docker 扩展

- 适用于：此问题适用于所有支持的版本。
- 原因：在管理员和用户门户中，如果搜索 **Docker**，则会错误地返回该项。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个错误。
- 修正：无缓解措施。
- 发生次数：通用

### <a name="upload-blob"></a>上传 blob

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：通用

### <a name="template"></a>模板

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，模板部署 UI 不会填充以 "_" 开头 (下划线字符) 的模板名称的参数。
- 修正：从模板名称中删除)  (的下划线字符 "_"。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡器

#### <a name="add-backend-pool"></a>添加后端池

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，如果你尝试将 **后端池** 添加到 **负载均衡器**，则操作将失败，并出现错误消息 " **无法更新负载平衡器 ...**"。
- 修正：使用 PowerShell、CLI 或资源管理器模板将后端池与负载均衡器资源关联。
- 发生次数：通用

#### <a name="create-inbound-nat"></a>创建入站 NAT

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，如果你尝试为 **负载均衡器** 创建 **入站 NAT 规则**，操作将失败，并出现错误消息 "**无法更新负载平衡器 ...**"。
- 修正：使用 PowerShell、CLI 或资源管理器模板将后端池与负载均衡器资源关联。
- 发生次数：通用

#### <a name="create-load-balancer"></a>创建负载均衡器

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，" **创建负载均衡器** " 窗口会显示一个用于创建 **标准** 负载均衡器 SKU 的选项。 Azure Stack 不支持此选项。
- 修正：改为使用 **基本** 负载均衡器选项。
- 发生次数：通用

### <a name="public-ip-address"></a>公共 IP 地址

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，" **创建公共 IP 地址** " 窗口会显示一个用于创建 **标准** SKU 的选项。 Azure Stack 不支持“标准”SKU。
- 修正：使用 **基本** SKU 作为公共 IP 地址。
- 发生次数：通用

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。
如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：通用

### <a name="vm-resize"></a>VM 重设大小

- 适用：此问题适用于1905版本。
- 原因：无法成功调整托管磁盘 VM 的大小。 尝试调整 VM 的大小时，会生成错误，其中包含 "代码"： "InternalOperationError"，"消息"： "操作中出现内部错误"。
- 补救措施：我们正在努力在下一个版本中对此进行修正。 目前，你必须使用新的 VM 大小重新创建该 VM。
- 发生次数：通用

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="centos"></a>CentOS

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟机规模集创建体验提供基于 CentOS 的7.2 作为部署选项。 CentOS 7.2 在 Azure Stack 市场上不提供，这会导致部署失败，错误消息会指出找不到映像。
- 修正：为你的部署选择另一个操作系统，或使用 Azure 资源管理器模板，该模板指定了另一个 CentOS 映像，该映像已在通过应用商店在 marketplace 部署之前下载。
- 发生次数：通用

#### <a name="remove-scale-set"></a>删除规模集

- 适用于：此问题适用于所有支持的版本。
- 原因：无法从 **虚拟机规模集** 边栏选项卡中删除规模集。
- 修正：选择要删除的规模集，然后单击 "**概述**" 窗格中的 "**删除**" 按钮。
- 发生次数：通用

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在 4 节点的 Azure Stack 环境中进行修补和更新时出现故障

- 适用于：此问题适用于所有支持的版本。
- 原因：在具有3个容错域的可用性集中创建虚拟机并创建虚拟机规模集实例将在 Azure Stack 环境的4节点上的更新过程中失败，并出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。
- 补救措施：可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack 上进行更新时，仍然不能创建规模集实例。

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>“规模集实例视图”边栏选项卡不加载

- 适用：此问题适用于1904和1905版本。
- 原因：位于 Azure Stack 门户的虚拟机规模集的实例视图边栏选项卡-> 仪表板 > 虚拟机规模集-> AnyScaleSet-> AnyScaleSetInstance 无法加载，并显示哭泣的云映像。
- 补救措施：目前没有修正，我们正在努力解决问题。 在补救措施出来之前，请使用 CLI 命令 `az vmss get-instance-view` 获取规模集的实例视图。

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

## <a name="next-steps"></a>后续步骤

- [查看更新活动清单](../release-notes-checklist.md)
- [查看安全更新列表](../release-notes-security-updates.md)
