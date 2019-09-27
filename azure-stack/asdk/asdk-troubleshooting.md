---
title: 排查 ASDK 问题 |Microsoft Docs
description: 了解如何对 Azure Stack 开发工具包进行故障排除（ASDK）。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: ab43d94c2e65032e5e525ec000e38cacb01b2980
ms.sourcegitcommit: 1bae55e754d7be75e03af7a4db3ec43fd7ff3e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319089"
---
# <a name="troubleshoot-the-asdk"></a>ASDK 故障排除
本文提供 Azure Stack 开发工具包（ASDK）的常见故障排除信息。 有关 Azure Stack 集成系统的帮助，请参阅[Microsoft Azure Stack 故障排除](../operator/azure-stack-troubleshooting.md)。 

由于 ASDK 是一个评估环境，因此 Microsoft 客户支持服务（CSS）不提供支持。 如果遇到未记录的问题，你可以通过[AZURE STACK MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)上的专家获得帮助。 


## <a name="deployment"></a>部署
### <a name="deployment-failure"></a>部署失败
如果安装期间发生失败，可以使用部署脚本的 -rerun 选项从失败的步骤重新开始部署。 例如：

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>部署结束时，PowerShell 会话仍保持打开状态，但不显示任何输出
此行为可能只是选择了 PowerShell 命令窗口的默认行为。 ASDK 部署已成功，但在选择窗口时，脚本已暂停。 可以通过在命令窗口的标题栏中查找“select”一词，来验证安装是否已完成。 按 ESC 键取消选择窗口，然后即会显示完成消息。

## <a name="virtual-machines"></a>虚拟机
### <a name="default-image-and-gallery-item"></a>默认映像和库项
在 Azure Stack 中部署 VM 之前，必须先添加 Windows Server 映像和库项。

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>重新启动我的 Azure Stack 主机后，某些 Vm 不会自动启动
重新启动主机后，你可能会注意到 Azure Stack 服务不会立即可用。 这是因为 Azure Stack [基础结构 VM](asdk-architecture.md#virtual-machine-roles) 与 RP 需要花费一段时间来检查一致性，但最终会自动启动。

你可能还注意到，在 ASDK 主机重新启动后，租户 Vm 不会自动启动。 可以通过几个手动步骤使它们联机：

1.  在 ASDK 主机上，从 "开始" 菜单启动**故障转移群集管理器**。
2.  选择群集“S-Cluster.azurestack.local”。
3.  选择“角色”。
4.  租户 VM 显示为“已保存”状态。 所有基础结构 VM 运行后，右键单击租户 VM，并选择“启动”以恢复该 VM。

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>我删除了某些 Vm，但仍在磁盘上看到 VHD 文件 
此行为是设计的：

* 删除 VM 时，不会删除 Vhd。 磁盘是资源组中的独立资源。
* 删除存储帐户后，Azure 资源管理器会立即反映删除结果，但其中的磁盘仍保留在存储中，直到运行垃圾收集为止。

如果你看到 "孤立" Vhd，必须知道它们是否是已删除的存储帐户的文件夹的一部分，这一点很重要。 如果存储帐户未被删除，则 Vhd 仍保持不变。

可以在[管理存储帐户](../operator/azure-stack-manage-storage-accounts.md)中详细了解如何配置保留阈值和按需回收。

## <a name="storage"></a>存储
### <a name="storage-reclamation"></a>存储回收
回收的容量最多可能需要14小时才能在门户中显示。 空间回收取决于多种因素，包括块 Blob 存储中内部容器文件的用量百分比。 因此，根据删除的数据量，无法保证垃圾回收器运行时可回收的空间量。

## <a name="next-steps"></a>后续步骤
[访问 Azure Stack 支持论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
