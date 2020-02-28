---
title: ASDK 故障排除
description: 了解如何对 Azure Stack 开发工具包进行故障排除（ASDK）。
author: justinha
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 73e769358d0b6007a7849edd1cebaac0ade78d04
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77691341"
---
# <a name="troubleshoot-the-asdk"></a>ASDK 故障排除
本文提供 Azure Stack 开发工具包（ASDK）的常见故障排除信息。 有关 Azure Stack 集成系统的帮助，请参阅[Microsoft Azure Stack 故障排除](../operator/azure-stack-troubleshooting.md)。 

由于 ASDK 是一个评估环境，因此 Microsoft 客户支持服务（CSS）不提供支持。 如果遇到未记录的问题，你可以通过[AZURE STACK MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)上的专家获得帮助。 


## <a name="deployment"></a>部署
### <a name="deployment-failure"></a>部署失败
如果在安装过程中遇到失败，可以使用部署脚本的-重新运行选项从失败的步骤重新启动部署。 例如：

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>部署结束时，PowerShell 会话仍处于打开状态，并且不会显示任何输出
此行为可能只是选择了 PowerShell 命令窗口的默认行为。 ASDK 部署已成功，但在选择窗口时，脚本已暂停。 可以通过在命令窗口的标题栏中查找 "选择" 一词来验证安装是否已完成。 按 ESC 键可取消选中它，完成消息之后应显示。

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>不允许使用模板验证错误参数 osProfile

如果在模板验证过程中收到错误消息，指出不允许参数 "osProfile"，请确保对这些组件使用正确的 Api 版本：

- [计算](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftcompute)
- [Network](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftnetwork)

若要从 Azure 将 VHD 复制到 Azure Stack，请使用[AzCopy 7.3.0](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#download-and-install-azcopy)。 与供应商合作解决图像本身的问题。 有关 Azure Stack 的 WALinuxAgent 要求的详细信息，请参阅[Azure LinuX 代理](../operator/azure-stack-linux.md#azure-linux-agent)。

### <a name="deployment-fails-due-to-lack-of-external-access"></a>由于缺少外部访问权限导致部署失败
当部署在需要外部访问的阶段失败时，将返回类似于以下示例的异常：

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
如果发生此错误，请通过查看[部署网络流量文档](../operator/deployment-networking.md)确保满足所有最低网络要求。 作为合作伙伴工具包的一部分，还可以使用网络检查器工具作为合作伙伴。

其他部署失败通常是由于连接到 Internet 上的资源时出现问题。

若要验证是否能够连接到 Internet 上的资源，可以执行以下步骤：

1. 打开 PowerShell。
2. 对于 WAS01 或任何 ERCs Vm，请按 Enter。
3. 运行以下 cmdlet： 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

如果此命令失败，请验证 TOR 开关和任何其他网络设备是否已配置为[允许网络流量](../operator/azure-stack-network.md)。


## <a name="virtual-machines"></a>虚拟机
### <a name="default-image-and-gallery-item"></a>默认图像和库项
在 Azure Stack 中部署 Vm 之前，必须添加 Windows Server 映像和库项。

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>重新启动我的 Azure Stack 主机后，某些 Vm 不会自动启动
重新启动主机后，你可能会注意到 Azure Stack 服务不会立即可用。 这是因为 Azure Stack[基础结构 vm](asdk-architecture.md#virtual-machine-roles)和 RPs 需要一些时间来检查一致性，但最终会自动启动。

你可能还注意到，在 ASDK 主机重新启动后，租户 Vm 不会自动启动。 可以通过几个手动步骤使它们联机：

1.  在 ASDK 主机上，从 "开始" 菜单启动**故障转移群集管理器**。
2.  选择群集的**test-azurestack**。
3.  选择“角色”。
4.  租户 Vm 显示为*已保存*状态。 运行所有基础结构 Vm 后，右键单击租户 Vm，然后选择 "**启动**" 以恢复 VM。

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>我删除了某些 Vm，但仍在磁盘上看到 VHD 文件 
此行为是设计的：

* 删除 VM 时，不会删除 Vhd。 磁盘是资源组中的独立资源。
* 删除存储帐户后，可通过 Azure 资源管理器立即看到删除，但它可能包含的磁盘仍保留在存储中，直到垃圾回收运行。

如果你看到 "孤立" Vhd，必须知道它们是否是已删除的存储帐户的文件夹的一部分，这一点很重要。 如果存储帐户未被删除，则 Vhd 仍保持不变。

可以在[管理存储帐户](../operator/azure-stack-manage-storage-accounts.md)中了解有关配置保留阈值和按需回收的详细信息。

## <a name="storage"></a>存储
### <a name="storage-reclamation"></a>存储回收
回收的容量最多可能需要14小时才能在门户中显示。 空间回收取决于各种因素，包括块 blob 存储中内部容器文件的使用百分比。 因此，根据删除的数据量，无法保证垃圾回收器运行时可回收的空间量。

## <a name="next-steps"></a>后续步骤
[访问 Azure Stack 支持论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
