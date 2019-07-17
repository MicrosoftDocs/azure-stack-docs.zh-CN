---
title: Azure Stack 1904 的已知问题 | Microsoft Docs
description: 了解 Azure Stack 1904 的已知问题。
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 3a92a142181b9e2268948d430b0a62d6ffa189d8
ms.sourcegitcommit: 90ed5aa051d0756b2432c8aca0e2232c8ec493a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68239444"
---
# <a name="azure-stack-1904-known-issues"></a>Azure Stack 1904 的已知问题

本文列出了 Azure Stack 版本 1904 中的已知问题。 每当发现新的问题，此列表就会更新。

> [!IMPORTANT]  
> 在应用更新之前，请先查看本部分。

## <a name="update-process"></a>更新过程

- 适用于：此问题适用于所有支持的版本。
- 原因：尝试安装 Azure Stack 更新时，更新状态可能会显示失败并更改为 **PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。
- 补救措施：从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。   然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。
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

### <a name="docker-extension"></a>Docker 扩展

- 适用于：此问题适用于所有支持的版本。
- 原因：管理员和用户门户网站，如果搜索中**Docker**，错误地返回的项。 不是 Azure Stack 中提供。 如果您尝试创建它，将显示错误。
- 补救措施：无缓解措施。
- 发生次数：常见

### <a name="marketplace-management"></a>市场管理

- 适用于：这是版本 1904 中的一个新问题。
- 原因：在登录到管理员门户时，将不可见 marketplace 管理屏幕。
- 补救措施：刷新浏览器。
- 发生次数：间歇性

### <a name="marketplace-management"></a>市场管理

- 适用于：此问题适用于版本 1904。
- 原因：筛选结果中的时**从 Azure 添加**边栏选项卡在管理员门户中 Marketplace 管理选项卡中，可能会看到不正确的筛选的结果。
- 补救措施：将更正的名称列的排序结果和结果。
- 发生次数：间歇性

### <a name="marketplace-management"></a>市场管理

- 适用于：此问题适用于版本 1904。
- 原因：在 Marketplace 管理管理员门户中的结果进行筛选，会看到重复的发布者名称下发布服务器下拉列表。 
- 补救措施：选择具有下该发布者提供的所有 Marketplace 产品的正确列表的所有重复项。
- 发生次数：间歇性

### <a name="docker-extension"></a>Docker 扩展

- 适用于：此问题适用于所有支持的版本。
- 原因：管理员和用户门户网站，如果搜索中**Docker**，错误地返回的项。 不是 Azure Stack 中提供。 如果您尝试创建它，将显示错误。
- 补救措施：无缓解措施。
- 发生次数：常见

### <a name="upload-blob"></a>上传 blob

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中尝试使用“OAuth(preview)”选项上传 Blob 时，任务将会失败并出现错误消息。
- 补救措施：使用 SAS 选项上传 Blob。
- 发生次数：常见

### <a name="template"></a>模板

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，模板部署 UI 不会填充模板名称以"_"（下划线字符） 开头的参数。
- 补救措施：从模板名称中删除"_"（下划线字符）。
- 发生次数：常见

## <a name="networking"></a>网络

### <a name="load-balancer"></a>负载均衡

#### <a name="add-backend-pool"></a>添加后端池

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户尝试将**后端池**添加到**负载均衡器**时，该操作失败并出现错误消息“无法更新负载均衡器...”。 
- 补救措施：使用 PowerShell、 CLI 或 Azure 资源管理器模板与负载均衡器资源关联的后端池。
- 发生次数：常见

#### <a name="create-inbound-nat"></a>创建入站的 NAT

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户尝试为**负载均衡器**创建**入站 NAT 规则**时，该操作失败并出现错误消息“无法更新负载均衡器...”。 
- 补救措施：使用 PowerShell、 CLI 或 Azure 资源管理器模板与负载均衡器资源关联的后端池。
- 发生次数：常见

#### <a name="create-load-balancer"></a>创建负载均衡器

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，**创建负载均衡器**窗口将显示一个选项以创建**标准**负载均衡器 SKU。 Azure Stack 不支持此选项。
- 补救措施：改用“基本”负载均衡器选项。
- 发生次数：常见

#### <a name="public-ip-address"></a>公共 IP 地址

- 适用于：此问题适用于所有支持的版本。
- 原因：在用户门户中，“创建公共 IP 地址”窗口显示一个用于创建“标准”SKU 的选项。   Azure Stack 不支持“标准”SKU。 
- 补救措施：使用**基本**SKU 改为公共 IP 地址。
- 发生次数：常见

## <a name="compute"></a>计算

### <a name="vm-boot-diagnostics"></a>VM 启动诊断

- 适用于：此问题适用于所有支持的版本。
- 原因：创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：**无法启动虚拟机 'vm-name'。错误：无法更新 VM 'vm-name' 的串行输出设置**。
如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。
- 补救措施：使用以前所用的相同名称重新创建存储帐户。
- 发生次数：常见

### <a name="virtual-machine-scale-set"></a>虚拟机规模集

#### <a name="centos"></a>CentOS

- 适用于：此问题适用于所有支持的版本。
- 原因：虚拟机规模集创建体验提供了基于 CentOS 7.2，作为一个选项来部署。 CentOS 7.2 在 Azure Stack Marketplace，这将会导致部署失败调用找不到图像上不可用。
- 补救措施：为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。
- 发生次数：常见

#### <a name="remove-scale-set"></a>删除规模集

- 适用于：此问题适用于所有支持的版本。
- 原因：无法从“虚拟机规模集”边栏选项卡中删除规模集。 
- 补救措施：选择要删除的规模集，然后在“概述”窗格中单击“删除”按钮。  
- 发生次数：常见

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>在修补和更新在 4 节点 Azure Stack 环境的过程中创建失败

- 适用于：此问题适用于所有支持的版本。
- 原因：在 3 个容错域的可用性集中创建 Vm 并创建虚拟机规模集实例失败，并**FabricVmPlacementErrorUnsupportedFaultDomainSize**过程 4 节点 Azure Stack 上的更新过程中出错环境。
- 补救措施：可以在可用性集的 2 个容错域已成功创建单个 Vm。 但是，在 4 节点 Azure Stack 上的更新过程中创建规模集实例是仍不可用。

### <a name="ubuntu-ssh-access"></a>Ubuntu SSH 访问

- 适用于：此问题适用于所有支持的版本。
- 原因：如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。
- 补救措施：在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。
- 发生次数：常见

### <a name="compute-host-agent-alert"></a>计算主机代理警报

- 适用于：这是版本 1904 中的一个新问题。
- 原因：一个**计算主机代理**缩放单位中重新启动节点之后会出现警告。 重启操作会更改计算主机代理服务的默认启动设置。 此警报看起来类似于下面的示例：

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
  - 可以忽略此警报。 代理无响应并不会影响操作员和用户的操作或用户应用程序。 如果手动关闭此警报，它会在 24 小时后再次出现。
  - 在最新版本中已修复问题[1904年的 Azure Stack 修补程序](https://support.microsoft.com/help/4505688)。
- 发生次数：常见

### <a name="virtual-machine-scale-set-instance-view"></a>虚拟机规模集实例视图

- 适用于：此问题适用于 1904年和 1905年版本。
- 原因：规模集实例视图边栏选项卡位于 Azure Stack 门户中**仪表板** > **虚拟机规模集** >  **AnyScaleSet-实例** > **AnyScaleSetInstance**加载失败。
- 补救措施：目前尚无修正，我们正在努力修复程序。 在此之前，请使用 CLI cmdlet`az vmss get-instance-view`获取虚拟机规模集实例视图。

### <a name="user-image-service"></a>用户映像服务
- 适用于：此问题适用于所有支持的版本。
- 原因：失败的用户映像创建将放入错误的状态的用户映像服务。 用户映像创建和删除操作将启动失败。 用户映像删除可能会失败，错误："错误：内部磁盘管理时出错。"
- 补救措施：无缓解措施。 与 Microsoft 开具支持票证。

## <a name="storage"></a>存储

- 适用于：此问题适用于所有支持的版本。
- 原因：[Convertto-azurermvmmanageddisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk)不支持在 Azure Stack 中，会创建具有磁盘 **$null** id。 这会防止执行在 VM 中，如启动和停止操作。 磁盘不会显示在 UI 中，也不会出现通过 API。 VM 在该点不能修复，必须删除。
- 补救措施：若要正确转换你的磁盘，请按照[将转换为托管的磁盘指南](../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks)。

## <a name="app-service"></a>应用服务

- 在订阅中创建第一个 Azure 函数之前，租户必须注册存储资源提供程序。
- 某些租户门户的用户体验中 1903; 已断开由于与门户框架不兼容主要，UX 部署槽的测试生产站点和站点扩展中。 若要解决此问题，请使用[Azure 应用服务 PowerShell 模块](/azure/app-service/deploy-staging-slots#automate-with-powershell)或[Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest)。 通过升级的部署，将还原的门户体验[到 1.6 版 (更新 6) 的 Azure Stack 上的 Azure 应用服务](azure-stack-app-service-release-notes-update-six.md)。

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>后续步骤

- [查看更新活动清单](azure-stack-release-notes-checklist.md)
- [查看安全更新列表](azure-stack-release-notes-security-updates-1904.md)
