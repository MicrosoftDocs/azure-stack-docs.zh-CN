---
title: 已知问题的 azure Stack 1905 |Microsoft Docs
description: 了解 Azure Stack 1905 中的已知问题。
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
ms.date: 06/05/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: 0b5d2a913d93326f5cb815aeafe1c64c80d71746
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693807"
---
# <a name="azure-stack-1905-known-issues"></a>Azure Stack 1905 已知问题

本文列出了 Azure Stack 1905年版本中的已知的问题。 在发现新的问题后，更新列表。

> [!IMPORTANT]  
> 在应用更新之前查看此部分。

## <a name="update-process"></a>更新过程

- 适用：此问题适用于所有支持的版本。
- 原因：在尝试安装 1905 Azure Stack 更新时，更新的状态可能失败，并将状态更改为**PreparationFailed**。 这被引起更新资源提供程序 (URP) 无法正确地将文件传输从存储容器到处理的内部基础结构共享。 1905 更新包大于上一个更新包，这可能会使更有可能发生此问题。
- 补救措施：从版本 1901 (1.1901.0.95) 开始，你可以解决此问题通过单击**立即更新**再次 (不**恢复**)。 URP 然后清理文件从上一次尝试，并重新启动下载。 如果问题仍然存在，我们建议按照以下更新程序包手动上传[导入和安装更新部分](azure-stack-apply-updates.md#import-and-install-updates)。
- 发生次数：常见

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有支持的版本。
- 原因：不应使用与版本 1804年引入了两个管理订阅。 订阅类型是**计量**订阅，并**消耗**订阅。
- 补救措施：第一页为 1906年和最终删除，这些订阅将被挂起。 如果您有以下两个订阅上运行的资源，重新在 1906年之前用户订阅中创建它们。
- 发生次数：常见

### <a name="subscription-resources"></a>订阅资源

- 适用：此问题适用于所有支持的版本。
- 原因：删除用户订阅生成孤立的资源。
- 补救措施：首先删除用户资源或整个资源组，然后再删除用户订阅。
- 发生次数：常见

### <a name="subscription-permissions"></a>订阅的权限

- 适用：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 补救措施：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：常见

### <a name="docker-extension"></a>Docker 扩展

- 适用：此问题适用于所有支持的版本。
- 原因：管理员和用户门户网站，如果搜索中**Docker**，错误地返回的项。 不是 Azure Stack 中提供。 如果您尝试创建它，将显示错误。
- 补救措施：没有缓解措施。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。 
- 补救措施：上传的 blob 使用 SAS 选项。
- 发生次数：常见

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡

#### <a name="add-backend-pool"></a>添加后端池

- 适用：此问题适用于所有支持的版本。
- 原因：在用户门户中，如果尝试添加**后端池**到**负载均衡器**，则操作将失败并显示错误消息**未能更新负载均衡器...** .
- 补救措施：使用 PowerShell、 CLI 或 Resource Manager 模板以将后端池与一个负载均衡器资源相关联。
- 发生次数：常见

#### <a name="create-inbound-nat"></a>创建入站的 NAT

- 适用：此问题适用于所有支持的版本。
- 原因：在用户门户中，如果您尝试创建**入站 NAT 规则**有关**负载均衡器**，则操作将失败并显示错误消息**未能更新负载均衡器...** .
- 补救措施：使用 PowerShell、 CLI 或 Resource Manager 模板以将后端池与一个负载均衡器资源相关联。
- 发生次数：常见

#### <a name="create-load-balancer"></a>创建负载均衡器

- 适用：此问题适用于所有支持的版本。
- 原因：在用户门户中，**创建负载均衡器**窗口将显示一个选项以创建**标准**负载均衡器 SKU。 在 Azure Stack 中不支持此选项。
- 补救措施：使用**基本**改为负载均衡器选项。
- 发生次数：常见

### <a name="public-ip-address"></a>公共 IP 地址

- 适用：此问题适用于所有支持的版本。
- 原因：在用户门户中，**创建公共 IP 地址**窗口将显示一个选项以创建**标准**SKU。 **标准**SKU 不支持在 Azure Stack 中。
- 补救措施：使用**基本**SKU 公共 IP 地址。
- 发生次数：常见

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有支持的版本。
- 原因：在创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**未能启动虚拟机 vm-name。错误：未能更新 VM vm-name 的串行输出设置**。
如果 VM 上启用启动诊断，但删除启动诊断存储帐户后，将发生此错误。
- 补救措施：重新创建以前使用的相同名称的存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="centos"></a>CentOS

- 适用：此问题适用于所有支持的版本。
- 原因：虚拟机规模集创建体验提供了基于 CentOS 7.2，作为一个选项来部署。 CentOS 7.2 不是可在 Azure Stack 上的。
- 补救措施：选择另一个适用于你的部署，或使用指定另一个已从 marketplace 部署前运算符所下载的 CentOS 映像的 Azure 资源管理器模板。
- 发生次数：常见

#### <a name="remove-scale-set"></a>删除规模集

- 适用：此问题适用于所有支持的版本。
- 原因：无法删除规模集从**虚拟机规模集**边栏选项卡。
- 补救措施：选择的小数位数设置你想要删除，然后单击**删除**按钮从**概述**窗格。
- 发生次数：常见

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在修补和更新在 4 节点 Azure Stack 环境的过程中创建失败

- 适用：此问题适用于所有支持的版本。
- 原因：在 3 个容错域的可用性集中创建 Vm 并创建虚拟机规模集实例失败，并**FabricVmPlacementErrorUnsupportedFaultDomainSize**过程 4 节点 Azure Stack 上的更新过程中出错环境。
- 补救措施：可以在可用性集的 2 个容错域已成功创建单个 Vm。 但是，在 4 节点 Azure Stack 上的更新过程中创建规模集实例是仍不可用。

#### <a name="vmss-instance-view-blade-doesnt-load"></a>VMSS 实例视图边栏选项卡不会加载
 
- 适用：此问题适用于 1904年和 1905年版。
- 原因：规模集位于 Azure Stack 门户的实例视图边栏选项卡-> 仪表板-> 虚拟机规模集-> AnyScaleSet-实例-> 加载的云 AnyScaleSetInstance 失败。
- 补救措施：目前尚无修正，我们正在努力修复程序。 在此之前，请使用 CLI 命令 az vmss 获取实例视图以获取 VMSS 实例视图

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有支持的版本。
- 原因：创建具有 SSH 授权启用的 Ubuntu 18.04 VM 不允许您可以使用 SSH 密钥登录。
- 补救措施：使用 VM 访问 for Linux 扩展预配后，实现 SSH 密钥或使用基于密码的身份验证。
- 发生次数：常见

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>后续步骤

- [查看更新活动核对清单](azure-stack-release-notes-checklist.md)
- [查看安全更新列表](azure-stack-release-notes-security-updates-1905.md)
