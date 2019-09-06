---
title: Microsoft Azure Stack 故障排除 |Microsoft Docs
description: Azure Stack 故障排除。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 09/04/2019
ms.openlocfilehash: a9d62640b2baabfd3283099656719a880dd0a41b
ms.sourcegitcommit: a8379358f11db1e1097709817d21ded0231503eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70377246"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure Stack 疑难解答

本文档提供 Azure Stack 的疑难解答信息。 


## <a name="frequently-asked-questions"></a>常见问题

这些部分包含指向介绍发送到 Microsoft 客户支持服务（CSS）的常见问题的文档的链接。

### <a name="purchase-considerations"></a>购买注意事项

* [如何购买](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Azure Stack 概述](azure-stack-overview.md)

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack 开发工具包 (ASDK)

有关[Azure Stack 开发工具包](../asdk/asdk-what-is.md)的帮助，请联系[Azure Stack MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)上的专家。 ASDK 作为评估环境提供，无需通过 CSS。 为 ASDK 打开的支持案例称为 MSDN 论坛。

### <a name="updates-and-diagnostics"></a>更新和诊断

* [如何在 Azure Stack 中使用诊断工具](azure-stack-diagnostics.md)
* [如何验证 Azure Stack 系统状态](azure-stack-diagnostic-test.md)
* [更新包发布节奏](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>来宾 Vm 支持的操作系统和大小

* [Azure Stack 上支持的来宾操作系统](azure-stack-supported-os.md)
* [Azure Stack 中支持的 VM 大小](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [可供 Azure Stack 使用的 Azure 市场项](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>管理容量

#### <a name="memory"></a>内存

若要增加 Azure Stack 的总可用内存容量，可以添加内存。 在 Azure Stack 中，物理服务器也称为缩放单元节点。 属于单个缩放单元的所有缩放单元节点必须具有[相同的内存量](azure-stack-manage-storage-physical-memory-capacity.md)。

#### <a name="retention-period"></a>保留期

云操作员可以使用“保留期”设置来指定一个时间段（以天为单位，值为 0 到 9999 天），在此时间段内删除的帐户有可能能够恢复。 默认保留期设置为 0 天。 将值设置为“0”表示任何已删除的帐户会立即失去保留期，并标记为定期接受垃圾回收。

* [设置保留期](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>安全性、符合性和标识  

#### <a name="manage-rbac"></a>管理 RBAC

Azure Stack 中的用户可以是订阅、资源组或服务的每个实例的读取者、所有者或参与者。

* [Azure Stack 管理 RBAC](azure-stack-manage-permissions.md)

如果 Azure 资源的内置角色不能满足组织的特定需求，则你可以创建自己的自定义角色。 对于本教程，你将使用 Azure PowerShell 创建名为 Reader Support Tickets 的自定义角色。

* [教程：使用 Azure PowerShell 为 Azure 资源创建自定义角色](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>以 CSP 身份管理使用情况和计费

* [以 CSP 身份管理用量和计费](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [创建 CSP 或应用订阅](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

选择用于 Azure Stack 的共享服务帐户类型。 可用于注册多租户 Azure Stack 的订阅类型为：

* 云服务提供商
* 合作伙伴共享服务订阅


## <a name="troubleshoot-deployment"></a>排查部署问题 
### <a name="general-deployment-failure"></a>常见的部署失败
如果安装期间发生失败，可以使用部署脚本的 -rerun 选项从失败的步骤重新开始部署。  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>ASDK 部署结束时，PowerShell 会话仍保持打开状态，但不显示任何输出。
此行为可能是选择 PowerShell 命令窗口后的默认行为。 开发工具包部署成功，但选择窗口时，脚本已暂停。 可以通过在命令窗口的标题栏中查找“select”一词，来验证安装是否已完成。 按 ESC 键取消选择窗口，然后即会显示完成消息。

### <a name="deployment-fails-due-to-lack-of-external-access"></a>部署因缺少外部访问而失败
如果部署在需要外部访问的阶段失败，则会返回一个异常，如以下示例所示：

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
如果发生此错误，请查看[部署网络流量文档](deployment-networking.md)，通过检查确保满足所有最低的网络要求。 合作伙伴也可使用网络检查器工具（在合作伙伴工具包中提供）。

如果部署失败且出现上述异常，则通常是由于在连接到 Internet 上的资源时出现问题。

若要验证这是否是你的问题，可以执行以下步骤：

1. 打开 Powershell
2. 通过 Enter-PSSession 连接到 WAS01 或任何 ERCs VM
3. 运行 commandlet：Test-NetConnection login.windows.net -port 443

如果此命令失败，请验证TOR 交换机以及任何其他的网络设备是否已配置为[允许网络流量](azure-stack-network.md)。

## <a name="troubleshoot-virtual-machines"></a>排查虚拟机问题
### <a name="default-image-and-gallery-item"></a>默认映像和库项
在 Azure Stack 中部署 VM 之前，必须先添加 Windows Server 映像和库项。

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>重启 Azure Stack 主机之后，某些 VM 可能不会自动启动。
将重新启动主机之后，可能会发现，Azure Stack 服务并非立即可用。  这是因为 Azure Stack [基础结构 VM](../asdk/asdk-architecture.md#virtual-machine-roles ) 与资源提供程序需要花费一些时间来检查一致性，但最终会自动启动。

另外还可能发现，在重新启动 Azure Stack 开发工具包主机之后，租户 VM 不会自动启动。 这是一个已知问题，只需执行几个手动步骤就能让它们联机：

1.  在 Azure Stack 开发工具包主机上，从“开始”菜单启动“故障转移群集管理器”。
2.  选择群集“S-Cluster.azurestack.local”。
3.  选择“角色”。
4.  租户 VM 显示为“已保存”状态。 运行所有基础结构 Vm 后，右键单击租户 Vm，然后选择 "**启动**" 以恢复它们。

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>我已删除某些虚拟机，但仍在磁盘上看到 VHD 文件。 这是预期行为吗？
是的，这是预期的行为。 设计此行为的原因如下：

* 删除 VM 时，不会删除 VHD。 磁盘是资源组中的独立资源。
* 删除存储帐户后，Azure 资源管理器会立即反映删除结果，但其中的磁盘仍保留在存储中，直到运行垃圾收集为止。

如果看到“孤立的”VHD，必须知道它们是否包含在已删除的存储帐户的文件夹中。 如果未删除存储帐户，则正常情况下，这些 VHD 仍在存储帐户中。

可以在[管理存储帐户](azure-stack-manage-storage-accounts.md)中详细了解如何配置保留阈值和按需回收。

## <a name="troubleshoot-storage"></a>排查存储问题
### <a name="storage-reclamation"></a>存储回收
回收的容量最长可能需要在 14 小时后才显示在门户中。 空间回收取决于多种因素，包括块 Blob 存储中内部容器文件的用量百分比。 因此，我们无法保证运行垃圾收集器时可回收的空间量，这取决于删除的数据量。

