---
title: 已知问题的 azure Stack 1904 |Microsoft Docs
description: 了解 Azure Stack 1904 中的已知问题。
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
ms.date: 05/28/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/28/2019
ms.openlocfilehash: 9ebbdb19335db4f0c31d68c726f7b8c211d0f2e2
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268337"
---
# <a name="azure-stack-1904-known-issues"></a>Azure Stack 1904 年的已知问题

本文列出了 1904年版本的 Azure Stack 中的已知的问题。 在发现新的问题后，更新列表。

> [!IMPORTANT]  
> 在应用更新之前查看此部分。

## <a name="update-process"></a>更新过程

- 适用：此问题适用于所有支持的版本。
- 原因：在尝试安装 Azure Stack 更新时，更新的状态可能失败，并将状态更改为**PreparationFailed**。 这被引起更新资源提供程序 (URP) 无法正确地将文件传输从存储容器到处理的内部基础结构共享。
- 补救措施：从版本 1901 (1.1901.0.95) 开始，你可以解决此问题通过单击**立即更新**再次 (不**恢复**)。 URP 然后清理文件从上一次尝试，并重新开始下载。
- 发生次数：通用

## <a name="portal"></a>门户

### <a name="administrative-subscriptions"></a>管理订阅

- 适用：此问题适用于所有支持的版本。
- 原因：不应使用与版本 1804年引入了两个管理订阅。 订阅类型是**计量**订阅，并**消耗**订阅。
- 补救措施：第一页为 1905年和最终删除，这些订阅将被挂起。 如果您有以下两个订阅上运行的资源，重新在 1905年之前用户订阅中创建它们。
- 发生次数：通用

### <a name="subscription-resources"></a>订阅资源

- 适用：此问题适用于所有支持的版本。
- 原因：删除用户订阅生成孤立的资源。
- 补救措施：首先删除用户资源或整个资源组，然后再删除用户订阅。
- 发生次数：通用

### <a name="subscription-permissions"></a>订阅的权限

- 适用：此问题适用于所有支持的版本。
- 原因：无法使用 Azure Stack 门户查看订阅的权限。
- 补救措施：使用[PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。
- 发生次数：通用


### <a name="docker-extension"></a>Docker 扩展
- 适用：此问题适用于所有支持的版本。
- 原因：门户管理员和用户门户，如果搜索"Docker"返回的项是不正确。 不是 Azure Stack 中提供。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。
- 补救措施：没有缓解措施。
- 发生次数：通用

### <a name="marketplace-management"></a>市场管理

- 适用：这是版本 1904年新问题。
- 原因：在登录到管理员门户时，将不可见 marketplace 管理屏幕。
- 补救措施：刷新浏览器。
- 发生次数：间歇性

### <a name="marketplace-management"></a>市场管理

- 适用：此问题适用于 1904年。
- 原因：筛选结果中的时**从 Azure 添加**边栏选项卡在管理员门户中 Marketplace 管理选项卡中，可能会看到不正确的筛选的结果。
- 补救措施：将更正的名称列的排序结果和结果。
- 发生次数：间歇性

### <a name="marketplace-management"></a>市场管理

- 适用：此问题适用于 1904年。
- 原因：在 Marketplace 管理管理员门户中的结果进行筛选，会看到重复的发布者名称下发布服务器下拉列表。 
- 补救措施：选择具有下该发布者提供的所有 Marketplace 产品的正确列表的所有重复项。
- 发生次数：间歇性

### <a name="docker-extension"></a>Docker 扩展

- 适用：此问题适用于所有支持的版本。
- 原因：管理员和用户门户网站，如果搜索中**Docker**，错误地返回的项。 不是 Azure Stack 中提供。 如果您尝试创建它，将显示错误。
- 补救措施：没有缓解措施。
- 发生次数：通用

### <a name="upload-blob"></a>上传 blob

- 适用：此问题适用于所有支持的版本。
- 原因：用户门户中，当您尝试使用 OAuth(preview) 选项，将 blob 上传任务将失败并显示错误消息。
- 补救措施：上传的 blob 使用 SAS 选项。
- 发生次数：通用

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡器

#### <a name="add-backend-pool"></a>添加后端池

- 适用：此问题适用于所有支持的版本。
- 原因：在用户门户中，如果尝试添加**后端池**到**负载均衡器**，则操作将失败并显示错误消息**未能更新负载均衡器...** .
- 补救措施：使用 PowerShell、 CLI 或 Azure 资源管理器模板与负载均衡器资源关联的后端池。
- 发生次数：通用

#### <a name="create-inbound-nat"></a>创建入站的 NAT

- 适用：此问题适用于所有支持的版本。
- 原因：在用户门户中，如果您尝试创建**入站 NAT 规则**有关**负载均衡器**，则操作将失败并显示错误消息**未能更新负载均衡器...** .
- 补救措施：使用 PowerShell、 CLI 或 Azure 资源管理器模板与负载均衡器资源关联的后端池。
- 发生次数：通用

#### <a name="create-load-balancer"></a>创建负载均衡器

- 适用：此问题适用于所有支持的版本。
- 原因：在用户门户中，**创建负载均衡器**窗口将显示一个选项以创建**标准**负载均衡器 SKU。 在 Azure Stack 中不支持此选项。
- 补救措施：改为使用基本负载均衡器选项。
- 发生次数：通用

#### <a name="public-ip-address"></a>公用 IP 地址

- 适用：此问题适用于所有支持的版本。
- 原因：在用户门户中，**创建公共 IP 地址**窗口将显示一个选项以创建**标准**SKU。 **标准**SKU 不支持在 Azure Stack 中。
- 补救措施：使用**基本**SKU 改为公共 IP 地址。
- 发生次数：通用

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**未能启动虚拟机 vm-name。错误：未能更新 VM vm-name 的串行输出设置**。
如果 VM 上启用启动诊断，但删除启动诊断存储帐户后，将发生此错误。
- 补救措施：重新创建以前使用的相同名称的存储帐户。
- 发生次数：通用

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="centos"></a>CentOS

- 适用：此问题适用于所有支持的版本。
- 原因：虚拟机规模集 (VMSS) 创建体验提供了基于 CentOS 7.2，作为一个选项来部署。 CentOS 7.2 不是可在 Azure Stack 上的。
- 补救措施：选择另一个适用于你的部署，或使用指定另一个已从 marketplace 部署前运算符所下载的 CentOS 映像的 Azure 资源管理器模板。
- 发生次数：通用

#### <a name="remove-scale-set"></a>删除规模集

- 适用：此问题适用于所有支持的版本。
- 原因：无法删除规模集从**虚拟机规模集**边栏选项卡。
- 补救措施：选择的小数位数设置你想要删除，然后单击**删除**按钮从**概述**窗格。
- 发生次数：通用

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用：此问题适用于所有支持的版本。
- 原因：创建具有 SSH 授权启用的 Ubuntu 18.04 VM 不允许您可以使用 SSH 密钥登录。
- 补救措施：使用 VM 访问 for Linux 扩展预配后，实现 SSH 密钥或使用基于密码的身份验证。
- 发生次数：通用

### <a name="compute-host-agent-alert"></a>计算主机代理警报

- 适用：这是版本 1904年新问题。
- 原因：一个**计算主机代理**缩放单位中重新启动节点之后会出现警告。 在重新启动更改计算主机代理服务的默认启动设置。 此警报看起来类似于下面的示例：

   ```shell
   NAME  
   Compute Host Agent is not responding to calls.
   SEVERITY  
   Warning
   STATE  
   Active
   CREATED TIME  
   5/16/2019, 10:08:23 AM
   UPDATED TIME  
   5/22/2019, 12:27:27 PM
   COMPONENT  
   M#####-NODE02
   DESCRIPTION  
   Could not communicate with the Compute Host Agent running on node: M#####-NODE02
   REMEDIATION  
   Please disable Compute Host Agent feature flag and collect logs for further diagnosis.
   ```

- 补救措施：
  - 可以忽略此警报。 代理未响应并没有对运算符和用户操作或用户应用程序造成任何影响。 警报在 24 小时后将重新出现如果已手动关闭。
  - 在最新版本中已修复问题[1904年的 Azure Stack 修补程序](https://support.microsoft.com/help/4505688)。
- 发生次数：通用

## <a name="storage"></a>存储

- 适用：此问题适用于所有支持的版本。
- 原因：[Convertto-azurermvmmanageddisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk)不支持在 Azure Stack 中，会创建具有磁盘 **$null** id。 这会防止执行在 VM 中，如启动和停止操作。 磁盘不会显示在 UI 中，也不会出现通过 API。 VM 在该点不能修复，必须删除。
- 补救措施：若要正确转换你的磁盘，请按照[将转换为托管的磁盘指南](../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks)。

## <a name="app-service"></a>应用服务

- 在订阅中创建第一个 Azure 函数之前，租户必须注册存储资源提供程序。
- 某些租户门户的用户体验中 1903; 已断开由于与门户框架不兼容主要，UX 部署槽的测试生产站点和站点扩展中。 若要解决此问题，请使用[Azure 应用服务 PowerShell 模块](/azure/app-service/deploy-staging-slots#automate-with-powershell)或[Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest)。 在即将发布的基于 Azure Stack 1.6 (Update 6) 的 Azure 应用服务中，将还原门户体验。

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>后续步骤

- [查看更新活动核对清单](azure-stack-release-notes-checklist.md)
- [查看安全更新列表](azure-stack-release-notes-security-updates-1904.md)
