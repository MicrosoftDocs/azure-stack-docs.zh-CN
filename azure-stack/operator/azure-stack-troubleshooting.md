---
title: Microsoft Azure Stack 故障排除 |Microsoft Docs
description: Azure Stack 故障排除。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: prchint
ms.lastreviewed: 01/23/2019
ms.openlocfilehash: c7486cb56dee87b8a894d165fce4c3a0dfaae6d9
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618128"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure Stack 故障排除

本文档提供 Azure Stack 的常见故障排除信息。 

> [!NOTE]
> 由于 Azure Stack 技术开发工具包 (ASDK) 以评估环境的形式提供，因此 Microsoft 客户支持服务不会提供官方支持。 如果遇到问题，请务必查看 [Azure Stack MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)以获取更多帮助和信息。  

本部分针对故障排除问题提供的建议派生自多个来源，不保证能够解决具体的问题。 代码示例按原样提供，不保证生成预期的结果。 随着产品的不断改进，本部分的内容可能会频繁更新。

## <a name="deployment"></a>部署
### <a name="general-deployment-failure"></a>常见的部署失败
如果安装期间发生失败，可以使用部署脚本的 -rerun 选项从失败的步骤重新开始部署。  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>在 ASDK 部署结束时，PowerShell 会话仍处于打开状态，并不显示任何输出。
此行为可能是选择 PowerShell 命令窗口后的默认行为。 开发工具包部署成功，但选择窗口时，脚本已暂停。 可以通过在命令窗口的标题栏中查找“select”一词，来验证安装是否已完成。  按 ESC 键取消选择窗口，然后即会显示完成消息。

### <a name="deployment-fails-due-to-lack-of-external-access"></a>部署因缺少外部访问而失败
如果部署在需要外部访问的阶段失败，则会返回一个异常，如以下示例所示：

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
如果发生此错误，请查看[部署网络流量文档](deployment-networking.md)，通过检查确保满足所有最低的网络要求。 合作伙伴也可使用网络检查器工具（在合作伙伴工具包中提供）。

如果部署失败且出现上述异常，则通常是由于在连接到 Internet 上的资源时出现问题

若要验证这是否是你的问题，可以执行以下步骤：

1. 打开 Powershell
2. 通过 Enter-PSSession 连接到 WAS01 或任何 ERCs VM
3. 运行 commandlet：Test-NetConnection login.windows.net -port 443

如果此命令失败，请验证TOR 交换机以及任何其他的网络设备是否已配置为[允许网络流量](azure-stack-network.md)。

## <a name="virtual-machines"></a>虚拟机
### <a name="default-image-and-gallery-item"></a>默认映像和库项
在 Azure Stack 中部署 VM 之前，必须先添加 Windows Server 映像和库项。

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>重启 Azure Stack 主机之后，某些 VM 可能不会自动启动。
将重新启动主机之后，可能会发现，Azure Stack 服务并非立即可用。  这是因为 Azure Stack [基础结构 VM](../asdk/asdk-architecture.md#virtual-machine-roles ) 与资源提供程序需要花费一些时间来检查一致性，但最终会自动启动。

另外还可能发现，在重新启动 Azure Stack 开发工具包主机之后，租户 VM 不会自动启动。 这是一个已知问题，只需执行几个手动步骤就能让它们联机：

1.  在 Azure Stack 开发工具包主机上，从“开始”菜单启动“故障转移群集管理器”。
2.  选择群集“S-Cluster.azurestack.local”。
3.  选择“角色”。
4.  租户 VM 显示为“已保存”状态。 所有基础结构 VM 运行后，右键单击租户 VM，并选择“启动”以恢复该 VM。

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>我已删除某些虚拟机，但仍在磁盘上看到 VHD 文件。 这是预期行为吗？
是的，这是预期的行为。 设计此行为的原因如下：

* 删除 VM 时，不会删除 VHD。 磁盘是资源组中的独立资源。
* 删除存储帐户后，Azure 资源管理器会立即反映删除结果，但其中的磁盘仍保留在存储中，直到运行垃圾收集为止。

如果看到“孤立的”VHD，必须知道它们是否包含在已删除的存储帐户的文件夹中。 如果未删除存储帐户，则正常情况下，这些 VHD 仍在存储帐户中。

可以在[管理存储帐户](azure-stack-manage-storage-accounts.md)中详细了解如何配置保留阈值和按需回收。

## <a name="storage"></a>存储
### <a name="storage-reclamation"></a>存储回收
回收的容量最长可能需要在 14 小时后才显示在门户中。 空间回收取决于多种因素，包括块 Blob 存储中内部容器文件的用量百分比。 因此，我们无法保证运行垃圾收集器时可回收的空间量，这取决于删除的数据量。

