---
title: Azure Stack VM 功能 |Microsoft Docs
description: 使用 Azure Stack 中的 Vm 时，了解有关不同的功能和注意事项。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/19/2018
ms.openlocfilehash: 2a9a9a2c402538eee428bad08c9772b288fa1740
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197342"
---
# <a name="azure-stack-vm-features"></a>Azure Stack VM 功能

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 虚拟机 (Vm) 提供按需、 可缩放的计算资源。 在部署 Vm 之前，应了解 Azure Stack 中可用的 VM 功能和 Microsoft Azure 之间的差异。 本文介绍了这些差异，并标识计划的 VM 部署的关键注意事项。 有关 Azure Stack 与 Azure 之间的大致差异的详细信息，请参阅[重要注意事项](azure-stack-considerations.md)一文。

## <a name="vm-differences"></a>VM 的差异

| Feature | Azure（公有云） | Azure Stack |
| --- | --- | --- |
| 虚拟机映像 | Azure Marketplace 包含可用于创建 VM 的映像。 若要查看 Azure 市场中的可用映像列表，请参阅 [Azure 市场](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1)页。 | 默认情况下，不存在的任何映像可在 Azure Stack marketplace 中。 Azure Stack 云管理员必须发布，或到 Azure Stack marketplace 下载映像，然后用户才能使用它们。 |
| 虚拟机大小 | Azure Vm 支持多种规模。 若要了解有关可用大小和选项，请参阅[Windows Vm 大小](/azure/virtual-machines/virtual-machines-windows-sizes)并[Linux VM 大小](/azure/virtual-machines/linux/sizes)主题。 | Azure Stack 支持一部分可在 Azure 中的 VM 大小。 若要查看支持的大小的列表，请参阅[VM 大小](#vm-sizes)本文的部分。 |
| 虚拟机配额 | [配额限制](/azure/azure-subscription-service-limits#service-specific-limits)由 Microsoft 设置。 | Azure Stack 云管理员必须先他们向其用户提供 VM 将分配配额。 |
| 虚拟机扩展 |Azure 支持各种不同的 VM 扩展。 若要了解有关可用扩展，请参阅[VM 扩展和功能](/azure/virtual-machines/windows/extensions-features)一文。| Azure Stack 支持一部分可在 Azure 中的扩展，每个扩展有特定的版本。 Azure Stack 云管理员还可以选择哪些扩展到为其用户可用。 若要查看支持的扩展插件的列表，请参阅[VM 扩展](#vm-extensions)本文的部分。 |
| 虚拟机网络 | 分配给租户 VM 的公共 IP 地址是可访问 Internet 上。<br><br><br>Azure 虚拟机具有固定的 DNS 名称。 | 分配给租户 VM 的公共 IP 地址进行访问仅在 Azure Stack 开发工具包环境中。 用户必须有权访问通过 Azure Stack 开发工具包[RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)或[VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)以连接到 Azure Stack 中创建的 VM。<br><br>在特定的 Azure Stack 实例中创建的虚拟机具有基于云管理员配置的值的 DNS 名称 |
| 虚拟机存储 | 支持[托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)。 | 版本为 1808 及更高版本的 Azure Stack 支持托管磁盘。 |
| 虚拟机磁盘性能 | 取决于磁盘类型和大小。 | 取决于 vm 的磁盘已附加到的 VM 大小。 有关详细信息，请参阅[Azure Stack 中支持的 VM 大小](azure-stack-vm-sizes.md)一文。
| API 版本 | Azure 始终提供所有虚拟机功能的最新 API 版本。 | Azure Stack 支持特定的 Azure 服务以及这些服务的特定 API 版本。 若要查看支持的 API 版本列表，请参阅本文的 [API 版本](#api-versions)部分。 |
| Azure 实例元数据服务 | Azure 实例元数据服务提供了有关正在运行的可用于管理和配置 VM 的 VM 实例的信息。  | Azure 实例元数据服务不支持在 Azure Stack 上。 |
| 虚拟机可用性集|多个容错域 （2 或 3 个区域）。<br>多个更新域。|多个容错域 （2 或 3 个区域）。<br>多个更新域 （最多 20 个）。|
| 虚拟机规模集|支持自动缩放。|不支持自动缩放。<br><br>使用门户、资源管理器模板或 PowerShell 将更多实例添加到规模集。 |
| 虚拟机诊断 | 支持的 Linux VM 诊断。 | 在 Azure Stack 中不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。

## <a name="vm-sizes"></a>VM 大小

Azure Stack 施加了一些资源限制，以避免资源（服务器本地和服务级别）的过度消耗。这些限制降低了其他租户消耗资源所带来的影响，从而改进了租户体验。

- VM 的网络出口有带宽上限。 Azure Stack 中的上限与 Azure 中的上限相同。
- 对于存储资源，Azure Stack 实施存储 IOPS （输入/输出操作每秒） 限制，以避免租户为了访问存储资源过度。
- VM 磁盘的磁盘的 IOPS 在 Azure Stack 是一个函数而不是磁盘类型的 VM 大小。 这意味着，对于 Standard_Fs 系列 VM，不管你选择 SSD 还是 HDD 作为磁盘类型，单个额外的数据磁盘的 IOPS 限制都是 2300。

下表列出了 Azure Stack 支持的 VM 及其配置：

| Type           | 大小          | 支持的大小范围 |
| ---------------| ------------- | ------------------------ |
|常规用途 |基本 A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|常规用途 |标准 A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|常规用途 |D 系列       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|常规用途 |Dv2 系列     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|常规用途 |DS 系列      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|常规用途 |DSv2-series    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|优化内存|D 系列       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|优化内存|DS 系列      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|优化内存|Dv2 系列     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|优化内存|DSv2 系列 -  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Azure Stack 和 Azure 之间保持一致的 VM 大小和及其关联的资源数量。 这种一致性包括内存、 核心数、 和可以创建的数据磁盘的数量/大小的量。 但是，大小相同的 VM 的性能取决于特定 Azure Stack 环境的基础特征。

## <a name="vm-extensions"></a>VM 扩展

Azure Stack 包含少量的扩展。 可以通过市场联合来获取更新和其他扩展。

使用以下 PowerShell 脚本来获取 Azure Stack 环境中可用的 VM 扩展的列表：

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

如果在 VM 部署上预配某个扩展时耗时过长，请让预配超时，而不要尝试通过停止该进程来解除 VM 的分配或将 VM 删除。

## <a name="api-versions"></a>API 版本

在 Azure Stack 中的 VM 功能支持以下 API 版本：

![VM 资源类型](media/azure-stack-vm-considerations/vm-resoource-types.png)

以下 PowerShell 脚本可用于 Azure Stack 环境中可用的 VM 功能获取的 API 版本：

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

如果云运营商将 Azure Stack 环境更新为较新版本，则支持的资源类型和 API 版本列表可能有所不同。

## <a name="windows-activation"></a>Windows 激活

必须根据产品使用权利和 Microsoft 许可条款使用 Windows 产品。 Azure Stack 使用[自动 VM 激活](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11))(AVMA) 来激活 Windows Server Vm。

- Azure Stack 主机使用 Windows Server 2016 的 AVMA 密钥激活 Windows。 运行 Windows Server 2012 R2 或更高版本的所有 VM 都将自动激活。
- 运行 Windows Server 2012 或之前不会自动激活，必须使用激活的 Vm [MAK 激活](https://technet.microsoft.com/library/ff793438.aspx)。 若要使用 MAK 激活，必须提供自己的产品密钥。

Microsoft Azure 使用 KMS 激活来激活 Windows Vm。 如果你从 Azure Stack 将 VM 移到 Azure 并遭遇激活问题上，请参阅[排查 Azure Windows VM 激活问题](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems)。 其他信息，请参阅[Azure Vm 上的故障排除 Windows 激活故障](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/)Azure 支持团队博客文章。

## <a name="next-steps"></a>后续步骤

[使用 PowerShell 在 Azure Stack 中创建 Windows VM](azure-stack-quick-create-vm-windows-powershell.md)
