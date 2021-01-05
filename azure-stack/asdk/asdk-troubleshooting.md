---
title: 排查 ASDK 问题
description: 了解如何排查 Azure Stack 开发工具包 (ASDK) 问题。
author: PatAltimore
ms.topic: article
ms.date: 11/05/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: b5b0310aa778cf470c436f0770c9b5ca0d46fd8f
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872936"
---
# <a name="troubleshoot-the-asdk"></a>排查 ASDK 问题
本文提供了 Azure Stack 开发工具包 (ASDK) 的常见故障排除信息。 有关 Azure Stack 集成系统的帮助，请参阅 [Microsoft Azure Stack 故障排除](../operator/azure-stack-troubleshooting.md)。 

由于 ASDK 是一个评估环境，因此 Microsoft 支持部门不提供支持。 如果遇到了文档未记录的问题，可以在 [Azure Stack MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)上获得专家的帮助。 


## <a name="deployment"></a>部署
### <a name="deployment-failure"></a>部署失败
如果安装期间发生失败，可以使用部署脚本的 -rerun 选项从失败的步骤重新开始部署。 例如：

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>部署结束时，PowerShell 会话仍保持打开状态，但不显示任何输出
此行为可能是选择 PowerShell 命令窗口后的默认行为。 ASDK 部署成功，但选择窗口时，脚本已暂停。 可以通过在命令窗口的标题栏中查找“select”一词，来验证安装是否已完成。 按 ESC 键取消选择窗口，然后即会显示完成消息。

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>不允许模板验证错误参数 osProfile

如果在模板验证期间遇到错误消息，该消息指出系统不允许参数 'osProfile'，请确保对以下组件使用正确的 API 版本：

- [计算](../user/azure-stack-profiles-azure-resource-manager-versions.md#microsoftcompute)
- [网络](../user/azure-stack-profiles-azure-resource-manager-versions.md#microsoftnetwork)

若要将 VHD 从 Azure 复制到 Azure Stack，请使用 [AzCopy 7.3.0](../user/azure-stack-storage-transfer.md#download-and-install-azcopy)。 请联系供应商以解决映像本身的问题。 若要详细了解 Azure Stack 的 WALinuxAgent 要求，请参阅 [Azure LinuX 代理](../operator/azure-stack-linux.md#azure-linux-agent)。

### <a name="deployment-fails-due-to-lack-of-external-access"></a>部署因缺少外部访问而失败
如果部署在需要外部访问的阶段失败，则会返回一个异常，如以下示例所示：

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
如果发生此错误，请查看[部署网络流量文档](../operator/deployment-networking.md)，确保满足所有最低的网络要求。 合作伙伴也可使用网络检查器工具（在合作伙伴工具包中提供）。

其他部署失败通常是由于在连接到 Internet 上的资源时出现问题。

若要验证能否连接到 Internet 上的资源，可以执行以下步骤：

1. 打开 PowerShell。
2. 通过 Enter-PSSession 连接到 WAS01 或任何 ERCs VM。
3. 运行以下 cmdlet： 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

如果此命令失败，请验证TOR 交换机以及任何其他的网络设备是否已配置为[允许网络流量](../operator/azure-stack-network.md)。


## <a name="virtual-machines"></a>虚拟机
### <a name="default-image-and-gallery-item"></a>默认映像和库项
在 Azure Stack 中部署 VM 之前，必须先添加 Windows Server 映像和库项。

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>重启 Azure Stack 主机之后，某些 VM 不会自动启动
将重新启动主机之后，可能会发现，Azure Stack 服务并非立即可用。 这是因为 Azure Stack [基础结构 VM](asdk-architecture.md#virtual-machine-roles) 与 RP 需要花费一段时间来检查一致性，但最终会自动启动。

另外还可能发现，在重新启动 ASDK 主机之后，租户 VM 不会自动启动。 可以通过几个手动步骤将它们联机：

1.  在 ASDK 主机上，从“开始”菜单启动“故障转移群集管理器”。 
2.  选择群集“S-Cluster.azurestack.local”。 
3.  选择“角色”  。
4.  租户 VM 显示为“已保存”状态。  所有基础结构 VM 运行后，右键单击租户 VM，并选择“启动”以恢复该 VM。 

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>我已删除某些 VM，但仍在磁盘上看到 VHD 文件 
此行为是设计使然：

* 删除 VM 时，不会删除 VHD。 磁盘是资源组中的独立资源。
* 删除存储帐户后，Azure 资源管理器会立即反映删除结果，但其中的磁盘仍保留在存储中，直到运行垃圾收集为止。

如果看到“孤立的”VHD，必须知道它们是否包含在已删除的存储帐户的文件夹中。 如果未删除存储帐户，则正常情况下会保留 VHD。

可以在[管理存储帐户](../operator/azure-stack-manage-storage-accounts.md)中详细了解如何配置保留阈值和按需回收。

## <a name="storage"></a>存储
### <a name="storage-reclamation"></a>存储回收
回收的容量最长可能需要在 14 小时后才显示在门户中。 空间回收取决于多种因素，包括块 Blob 存储中内部容器文件的用量百分比。 因此，我们无法保证运行垃圾回收器时可回收的空间量，这取决于删除的数据量。

## <a name="next-steps"></a>后续步骤
[访问 Azure Stack 支持论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
