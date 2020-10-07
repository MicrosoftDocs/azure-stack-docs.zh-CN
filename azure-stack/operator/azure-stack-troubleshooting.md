---
title: 排查 Azure Stack Hub 问题
titleSuffix: Azure Stack
description: 了解如何排查 Azure Stack Hub 的问题，包括 VM、存储和应用服务的问题。
author: myoungerman
ms.topic: article
ms.date: 07/21/2020
ms.author: v-myoung
ms.reviewer: prchint
ms.lastreviewed: 07/21/2020
ms.openlocfilehash: 403335a81a5ac02f2e32432f54df584baf7c2a02
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778149"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>排查 Azure Stack Hub 中的问题

本文档提供 Azure Stack Hub 集成环境的故障排除信息。 有关 Azure Stack 开发工具包的帮助，请参阅 [ASDK 故障排除](../asdk/asdk-troubleshooting.md)或获取 [Azure Stack Hub MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)上的专家帮助。

## <a name="frequently-asked-questions"></a>常见问题

这些部分包括介绍发送到 Microsoft 支持部门常见问题的文档的链接。

### <a name="purchase-considerations"></a>购买注意事项

* [购买方式](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Azure Stack Hub 概述](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>更新和诊断

* [如何在 Azure Stack Hub 中使用诊断工具](./azure-stack-diagnostic-log-collection-overview.md)
* [如何验证 Azure Stack Hub 系统状态](azure-stack-diagnostic-test.md)
* [更新包发布频率](azure-stack-servicing-policy.md#update-package-release-cadence)
* [对节点状态进行验证和故障排除](azure-stack-node-actions.md)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>来宾 VM 支持的操作系统和大小

* [Azure Stack Hub 支持的来宾操作系统](azure-stack-supported-os.md)
* [Azure Stack Hub 中支持的 VM 大小](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure 市场

* [可用于 Azure Stack Hub 的 Azure 市场项](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>管理容量

#### <a name="memory"></a>内存

若要增加 Azure Stack Hub 的总可用内存容量，可以添加更多内存。 在 Azure Stack Hub 中，物理服务器也称为“缩放单元节点”。 属于单个缩放单元的所有缩放单元节点必须具有[相同的内存量](azure-stack-manage-storage-physical-memory-capacity.md)。

#### <a name="retention-period"></a>保留期

云操作员可以使用保留期设置来指定时间间隔天数（0 到 9999 天），在此期间，任何已删除的帐户都有可能能够恢复。 默认保留期设置为 **0** 天。 将值设置为 **0** 表示任何已删除的帐户会立即超出保留期，并标记为定期进行垃圾回收。

* [设置保留期](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>安全性、合规性和标识  

#### <a name="manage-rbac"></a>管理 RBAC

Azure Stack Hub 中的用户可以是订阅、资源组或服务的每个实例的读者、所有者或参与者。

* [Azure Stack Hub 管理 RBAC](azure-stack-manage-permissions.md)

如果 Azure 资源的内置角色不能满足组织的特定需求，则你可以创建自己的自定义角色。 对于本教程，你将使用 Azure PowerShell 创建名为 Reader Support Tickets 的自定义角色。

* [教程：使用 Azure PowerShell 为 Azure 资源创建自定义角色](/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>以 CSP 身份管理使用情况和计费

* [以 CSP 身份管理用量和计费](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [创建 CSP 或 APSS 订阅](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

选择用于 Azure Stack Hub 的共享服务帐户的类型。 可以用来注册多租户 Azure Stack Hub 的订阅类型为：

* 云解决方案提供商
* 合作伙伴共享服务订阅

### <a name="get-scale-unit-metrics"></a>获取缩放单元指标

你可以使用 PowerShell 来获取戳记使用情况信息，而无需 Microsoft 支持部门的帮助。 若要获取戳记使用率，请执行以下操作：

1. 创建 PEP 会话。
2. 运行 `test-azurestack`。
3. 退出 PEP 会话。
4. 使用 invoke-command 调用运行 `get-azurestacklog -filterbyrole seedring`。
5. 提取 seedring .zip。 可以从运行 `test-azurestack` 的 ERCS 文件夹获取验证报告。

有关详细信息，请参阅 [Azure Stack Hub 诊断](azure-stack-get-azurestacklog.md)。

## <a name="troubleshoot-virtual-machines-vms"></a>排查虚拟机 (VM) 的问题

### <a name="reset-linux-vm-password"></a>重置 Linux VM 密码

如果你忘记了 Linux VM 的密码，并且因为 VMAccess 扩展出现问题，“重置密码”选项不起作用，你可执行以下步骤进行重置：

1. 选择要用作恢复 VM 的 Linux VM。

1. 登录到用户门户：
   1. 记下 VM 大小、NIC、公共 IP、NSG 和数据磁盘。
   1. 停止受影响的 VM。
   1. 删除受影响的 VM。
   1. 将受影响的 VM 中的磁盘作为数据磁盘附加到恢复 VM 上（可能需要花费几分钟时间才能使用该磁盘）。

1. 登录到恢复 VM，并运行以下命令：

   ```
   sudo su –
   mkdir /tempmount
   fdisk -l
   mount /dev/sdc2 /tempmount /*adjust /dev/sdc2 as necessary*/
   chroot /tempmount/
   passwd root /*substitute root with the user whose password you want to reset*/
   rm -f /.autorelabel /*Remove the .autorelabel file to prevent a time consuming SELinux relabel of the disk*/
   exit /*to exit the chroot environment*/
   umount /tempmount
   ```

1. 登录到用户门户：

   1. 从恢复 VM 拆离该磁盘。
   1. 从磁盘重新创建 VM。
   1. 请务必从前一个 VM 传输公共 IP、附加数据磁盘等。


还可以拍摄原始磁盘的快照并从中创建新磁盘，而不是直接在原始磁盘上执行更改。 有关详细信息，请参阅以下主题：

- [重置密码](/azure/virtual-machines/troubleshooting/reset-password)
- [从快照创建磁盘](/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-portal-linux#create-a-disk-from-the-snapshot)
- [更改和重置根密码](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-terminal_menu_editing_during_boot#sec-Changing_and_Resetting_the_Root_Password)


### <a name="license-activation-fails-for-windows-server-2012-r2-during-provisioning"></a>预配期间，Windows Server 2012 R2 的许可证激活失败

在这种情况下，Windows 将无法激活，此时屏幕右下角将显示一个水印。 位于 C:\Windows\Panther 下的 WaSetup.xml 日志包含以下事件：

```xml
<Event time="2019-05-16T21:32:58.660Z" category="ERROR" source="Unattend">
    <UnhandledError>
        <Message>InstrumentProcedure: Failed to execute 'Call ConfigureLicensing()'. Will raise error to caller</Message>
        <Number>-2147221500</Number>
        <Description>Could not find the VOLUME_KMSCLIENT product</Description>
        <Source>Licensing.wsf</Source>
    </UnhandledError>
</Event>
```


若要激活许可证，请复制要激活的 SKU 的自动虚拟机激活 (AVMA) 密钥。

|版本|AVMA 密钥|
|-|-|
|数据中心|Y4TGP-NPTV9-HTC2H-7MGQ3-DV4TW|
|Standard|DBGBW-NPF86-BJVTX-K3WKJ-MTB6V|
|Essentials|K2XGM-NMBT3-2R6Q8-WF2FK-P36R2|

在 VM 上运行以下命令：

```powershell
slmgr /ipk <AVMA_key>
```

若要获取完整的详细信息，请参阅 [VM 激活](/windows-server/get-started-19/vm-activation-19)。

### <a name="default-image-and-gallery-item"></a>默认映像和库项

在 Azure Stack Hub 中部署 VM 之前，必须先添加 Windows Server 映像和库项。

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>我已删除某些 VM，但仍在磁盘上看到 VHD 文件

此行为是设计使然：

* 删除 VM 时，不会删除 VHD。 磁盘是资源组中的独立资源。
* 删除某个存储帐户后，Azure 资源管理器会立即反映删除结果。 但是，该存储帐户包含的磁盘仍保留在存储中，直到运行垃圾收集为止。

如果看到“孤立的”VHD，必须知道它们是否包含在已删除的存储帐户的文件夹中。 如果未删除存储帐户，则正常情况下会保留这些 VHD。

可以在[管理存储帐户](azure-stack-manage-storage-accounts.md)中详细了解如何配置保留阈值和按需回收。

## <a name="troubleshoot-storage"></a>排查存储问题

### <a name="storage-reclamation"></a>存储回收

回收的容量最长可能需要在 14 小时后才显示在门户中。 空间回收取决于不同的因素，包括块 Blob 存储中内部容器文件的用量百分比。 因此，我们无法保证运行垃圾回收器时可回收的空间量，这取决于删除的数据量。

### <a name="azure-storage-explorer-not-working-with-azure-stack-hub"></a>Azure 存储资源管理器不兼容 Azure Stack Hub

如果在离线场景中使用集成系统，建议使用企业证书颁发机构 (CA)。 以 Base-64 格式导出根证书，然后将其导入 Azure 存储资源管理器。 确保从资源管理器终结点中删除尾部斜杠 (`/`)。 有关详细信息，请参阅[准备连接到 Azure Stack Hub](../user/azure-stack-storage-connect-se.md)。

## <a name="troubleshoot-app-service"></a>对应用服务进行故障排除

### <a name="create-aadidentityappps1-script-fails"></a>Create-AADIdentityApp.ps1 脚本失败

如果应用服务所需的 Create-AADIdentityApp.ps1 脚本失败，请确保在运行该脚本时包含必需的 `-AzureStackAdminCredential` 参数。 有关详细信息，请参阅[在 Azure Stack Hub 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app)。

## <a name="troubleshoot-azure-stack-hub-updates"></a>对 Azure Stack Hub 更新进行故障排除

Azure Stack Hub 修补程序和更新过程旨在让操作员以一致且简单的方式应用更新包。 虽然不常见，但在修补和更新过程中可能会出现问题。 如果在修补和更新过程中遇到问题，建议执行以下步骤：

0. **先决条件**：请确保已遵循[更新活动清单](release-notes-checklist.md)，并[启用主动日志收集](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively)。

1. 按照在更新失败时创建的失败警报中的补救步骤进行操作。

2. 如果无法解决问题，请创建 [Azure Stack Hub 支持票证](./azure-stack-help-and-support-overview.md?view=azs-2002)。 请确保已针对发生问题的时间跨度[收集日志](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now)。

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>常见 Azure Stack Hub 修补程序和更新问题

*适用于：Azure Stack Hub 集成系统*

### <a name="preparationfailed"></a>PreparationFailed

**适用于**：此问题适用于所有支持的版本。

**原因：** 尝试安装 Azure Stack Hub 更新时，更新的状态可能会失败并将状态更改为 `PreparationFailed`。 对于连接到 Internet 的系统，这通常表明由于 Internet 连接不稳定，无法正确下载更新包。 

**补救措施**：可以通过再次单击“立即安装”来解决此问题。 如果此问题仍然存在，建议按照[安装更新](azure-stack-apply-updates.md?#install-updates-and-monitor-progress)部分的说明手动上传更新包。

**发生率**：通用

::: moniker range="azs-2002"
### <a name="2002-update-failed"></a>2002更新失败

**适用**：此问题仅适用于2002版本。

**原因**：尝试2002更新时，更新可能失败并提供以下消息： `The private network parameter is missing from cloud parameters. Please use set-azsprivatenetwork cmdlet to set private networkTrace` 。

**修正**： [设置专用内部网络](https://docs.microsoft.com/azure-stack/operator/azure-stack-network?view=azs-2002#private-network)。
::: moniker-end