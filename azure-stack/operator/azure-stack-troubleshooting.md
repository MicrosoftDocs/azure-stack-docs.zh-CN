---
title: 排查 Azure Stack Hub 问题
titleSuffix: Azure Stack
description: 了解如何排查 Azure Stack Hub 的问题，包括 VM、存储和应用服务的问题。
author: justinha
ms.topic: article
ms.date: 05/13/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 15/13/2020
ms.openlocfilehash: 4910a7aaa2462cb53c4ce89246c92a60f61d5017
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2020
ms.locfileid: "83375005"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>排查 Azure Stack Hub 中的问题

本文档提供 Azure Stack Hub 集成环境的故障排除信息。 有关 Azure Stack 开发工具包的帮助，请参阅 [ASDK 故障排除](../asdk/asdk-troubleshooting.md)或获取 [Azure Stack Hub MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)上的专家帮助。

## <a name="frequently-asked-questions"></a>常见问题

这些部分包含指向介绍发送到 Microsoft 客户支持服务（CSS）的常见问题的文档的链接。

### <a name="purchase-considerations"></a>购买注意事项

* [购买方式](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Azure Stack Hub 概述](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>更新和诊断

* [如何在 Azure Stack 集线器中使用诊断工具](azure-stack-diagnostics.md)
* [如何验证 Azure Stack 集线器系统状态](azure-stack-diagnostic-test.md)
* [更新包发布节奏](azure-stack-servicing-policy.md#update-package-release-cadence)
* [验证节点状态并对其进行故障排除](azure-stack-node-actions.md)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>来宾 VM 支持的操作系统和大小

* [Azure Stack 集线器上支持的来宾操作系统](azure-stack-supported-os.md)
* [Azure Stack 中心支持的 VM 大小](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure 市场

* [适用于 Azure Stack 集线器的 Azure Marketplace 项](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>管理容量

#### <a name="memory"></a>内存

若要增加 Azure Stack 集线器的总可用内存容量，可以添加更多内存。 在 Azure Stack 中心，物理服务器也称为缩放单位节点。 属于单个缩放单元的所有缩放单元节点必须具有[相同的内存量](azure-stack-manage-storage-physical-memory-capacity.md)。

#### <a name="retention-period"></a>保留期

使用 "保持期" 设置，云操作员可以指定可能恢复任何已删除帐户的时间段（以天为单位，范围为0到9999天）。 默认保留期设置为**0**天。 如果将值设置为**0** ，则意味着任何已删除的帐户会立即退出并标记为定期垃圾回收。

* [设置保留期](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>安全性、合规性和标识  

#### <a name="manage-rbac"></a>管理 RBAC

Azure Stack 中心内的用户可以是订阅、资源组或服务的每个实例的读者、所有者或参与者。

* [Azure Stack 中心管理 RBAC](azure-stack-manage-permissions.md)

如果 Azure 资源的内置角色不能满足组织的特定需求，则你可以创建自己的自定义角色。 对于本教程，你将使用 Azure PowerShell 创建名为 Reader Support Tickets 的自定义角色。

* [教程：使用 Azure PowerShell 为 Azure 资源创建自定义角色](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>以 CSP 身份管理用量和计费

* [以 CSP 身份管理用量和计费](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [创建 CSP 或应用订阅](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

选择用于 Azure Stack 中心的共享服务帐户的类型。 可用于注册多租户 Azure Stack 中心的订阅类型包括：

* 云解决方案提供商
* 合作伙伴共享服务订阅

### <a name="get-scale-unit-metrics"></a>获取缩放单元指标

可以使用 PowerShell 获取戳记使用情况信息，不需 CSS 的帮助。 若要获取戳记使用率，请执行以下操作：

1. 创建 PEP 会话。
2. 运行 `test-azurestack`。
3. 退出 PEP 会话。
4. `get-azurestacklog -filterbyrole seedring`使用调用命令调用运行。
5. 提取 seedring。 你可以从运行的 ERCS 文件夹中获取验证报告 `test-azurestack` 。

有关详细信息，请参阅[Azure Stack 集线器诊断](azure-stack-get-azurestacklog.md)。

## <a name="troubleshoot-virtual-machines-vms"></a>虚拟机（Vm）故障排除

### <a name="default-image-and-gallery-item"></a>默认映像和库项

在 Azure Stack Hub 中部署 Vm 之前，必须添加 Windows Server 映像和库项。

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>我已删除某些 VM，但仍在磁盘上看到 VHD 文件

此行为是设计使然：

* 删除 VM 时，不会删除 VHD。 磁盘是资源组中的独立资源。
* 删除存储帐户后，可通过 Azure 资源管理器立即看到删除操作。 但它可能包含的磁盘仍保留在存储中，直到垃圾回收运行。

如果看到“孤立的”VHD，必须知道它们是否包含在已删除的存储帐户的文件夹中。 如果存储帐户未被删除，则通常情况下它们仍然存在。

可以在[管理存储帐户](azure-stack-manage-storage-accounts.md)中详细了解如何配置保留阈值和按需回收。

## <a name="troubleshoot-storage"></a>排查存储问题

### <a name="storage-reclamation"></a>存储回收

回收的容量最长可能需要在 14 小时后才显示在门户中。 空间回收取决于不同的因素，包括块 blob 存储中内部容器文件的使用百分比。 因此，我们无法保证运行垃圾回收器时可回收的空间量，这取决于删除的数据量。

### <a name="azure-storage-explorer-not-working-with-azure-stack-hub"></a>Azure 存储资源管理器无法使用 Azure Stack 中心

如果在断开连接的情况下使用集成系统，则建议使用企业证书颁发机构（CA）。 以 Base-64 格式导出根证书，然后将其导入 Azure 存储资源管理器。 请确保从资源管理器终结点删除尾部反斜杠（ `/` ）。 有关详细信息，请参阅[准备连接到 Azure Stack 集线器](/azure-stack/user/azure-stack-storage-connect-se)。

## <a name="troubleshoot-app-service"></a>应用服务故障排除

### <a name="create-aadidentityappps1-script-fails"></a>Create-AADIdentityApp.ps1 脚本失败

如果应用服务所需的 Create-aadidentityapp.ps1 脚本失败，请确保在运行脚本时包含所需的 `-AzureStackAdminCredential` 参数。 有关详细信息，请参阅[在 Azure Stack 集线器上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app)。

## <a name="troubleshoot-azure-stack-hub-updates"></a>Azure Stack 集线器更新疑难解答

Azure Stack Hub 修补程序和更新过程旨在让操作员以一致且简单的方式应用更新包。 虽然不常见，但在修补和更新过程中可能会出现问题。 如果在修补和更新过程中遇到问题，建议执行以下步骤：

0. **先决条件**：确保已遵循[更新活动清单](release-notes-checklist.md)并[启用主动日志收集](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)。

1. 按照在更新失败时创建的失败警报中的补救步骤进行操作。

2. 如果无法解决问题，请创建[Azure Stack 集线器支持票证](azure-stack-help-and-support-overview-tzl.md)。 在出现问题时，请确保为时间跨度[收集日志](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)。

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>常见 Azure Stack Hub 修补程序和更新问题

*适用于： Azure Stack 集线器集成系统*

### <a name="preparationfailed"></a>PreparationFailed

**适用**：此问题适用于所有受支持的版本。

**原因**：尝试安装 Azure Stack 中心更新时，更新的状态可能会失败，并将状态更改为 `PreparationFailed` 。 对于连接到 Internet 的系统，这通常表明由于 Internet 连接不稳定，无法正确下载更新包。 

**修正**：你可以通过单击 "**立即安装**" 来解决此问题。 如果此问题仍然存在，建议按照[安装更新](azure-stack-apply-updates.md?#install-updates-and-monitor-progress)部分的说明手动上传更新包。

**发生次数**：常见
