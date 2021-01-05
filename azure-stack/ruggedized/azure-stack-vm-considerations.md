---
title: Azure Stack VM 功能 | Microsoft Docs
description: 了解在 Azure Stack Hub 耐用中使用 Vm 时的不同功能和注意事项。
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
ms.date: 10/14/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: cec3095120a596429936bc3f85735d336d6cc134
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874585"
---
# <a name="azure-stack-vm-features"></a>Azure Stack VM 功能

Azure Stack 虚拟机 (VM) 提供可按需缩放的计算资源。 在部署 Vm 之前，应了解 Azure Stack 和 Microsoft Azure 中可用的 VM 功能之间的差异。 本文将说明这些差异，并指明规划 VM 部署方面的重要注意事项。 有关 Azure Stack 与 Azure 之间的大致差异的详细信息，请参阅[重要注意事项](../user/azure-stack-considerations.md)一文。

## <a name="vm-differences"></a>VM 差异

| 功能 | Azure（公有云） | Azure Stack |
| --- | --- | --- |
| 虚拟机映像 | Azure 市场包含可用于创建 VM 的映像。 若要查看 Azure 市场中的可用映像列表，请参阅 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1)页。 | Azure Stack 市场中默认不会提供任何映像。 Azure Stack 云管理员必须先将映像发布或下载到 Azure Stack 市场，然后用户才能使用这些映像。 |
| 虚拟机大小 | Azure 支持各种不同的 VM 大小。 若要了解可用的大小和选项，请参阅 [Windows VM 大小](/azure/virtual-machines/sizes)和 [Linux VM 大小](/azure/virtual-machines/linux/sizes)主题。 | Azure Stack 支持一部分可在 Azure 中使用的 VM 大小。 若要查看支持的大小列表，请参阅本文的 [VM 大小](#vm-sizes)部分。 |
| 虚拟机配额 | [配额限制](/azure/azure-subscription-service-limits#service-specific-limits) 由 Microsoft 设置。 | Azure Stack 云管理员在提供 VM 给其用户之前，必须先分配配额。 |
| 虚拟机扩展 |Azure 支持多种不同的 VM 扩展。 若要了解可用的扩展，请参阅 [VM 扩展和功能](/azure/virtual-machines/windows/extensions-features)一文。| Azure Stack 支持一部分可在 Azure 中使用的扩展，每个扩展有特定的版本。 Azure Stack 云管理员可以选择要将哪些扩展提供给其用户使用。 若要查看支持的扩展列表，请参阅本文的 [VM 扩展](#vm-extensions)部分。 |
| 虚拟机网络 | 分配给租户 VM 的公共 IP 地址可通过 Internet 访问。<br><br><br>Azure VM 具有固定的 DNS 名称。 | <!-- TZLASDKFIX Public IP addresses assigned to a tenant VM are accessible within the Azure Stack Development Kit environment only. A user must have access to the Azure Stack Development Kit via [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) or [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) to connect to a VM that is created in Azure Stack.--><br><br>在特定 Azure Stack 实例中创建的 VM 的 DNS 名称基于云管理员配置的值。 |
| 虚拟机存储 | 支持[托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)。 | 版本为 1808 及更高版本的 Azure Stack 支持托管磁盘。 |
| 虚拟机磁盘性能 | 取决于磁盘类型和大小。 | 取决于磁盘所附加到的 VM 的大小。 有关详细信息，请参阅 [Azure Stack 中支持的 VM 大小](azure-stack-vm-sizes.md)一文。
| API 版本 | Azure 始终提供所有 VM 功能的最新 API 版本。 | Azure Stack 支持特定的 Azure 服务以及这些服务的特定 API 版本。 若要查看支持的 API 版本列表，请参阅本文的 [API 版本](#api-versions)部分。 |
| Azure 实例元数据服务 | Azure 实例元数据服务提供有关可用于管理和设置 VM 的正在运行的 VM 实例的信息。  | Azure Stack 不支持 Azure 实例元数据服务。 |
| 虚拟机可用性集|多个容错域（每个区域 2 个或 3 个）。<br>多个更新域。|多个容错域（每个区域 2 个或 3 个）。<br>单个更新域，具有实时迁移功能，可在更新期间保护工作负荷。 支持 20 个更新域以实现模板兼容性。<br>VM 和可用性集应位于相同的位置和资源组中。|
| 虚拟机规模集|支持自动缩放。|不支持自动缩放。<br><br>使用门户、资源管理器模板或 PowerShell 将更多实例添加到规模集。 |
| 云见证 | 从 Azure Stack 中提供的存储帐户属性中选择终结点。 | [Cloud 见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) 是一种故障转移群集仲裁见证，使用 Microsoft Azure 在群集仲裁上提供投票。<br>与 Azure Stack 相比，全局 Azure 中的终结点如下所示：<br>对于全球 Azure：<br>`https://mywitness.blob.core.windows.net/`<br>对于 Azure Stack：<br>`https://mywitness.blob.<region>.<FQDN>/`|
| 虚拟机诊断 | 支持 Linux VM 诊断。 | Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。 |

## <a name="vm-sizes"></a>VM 大小

Azure Stack 施加了一些资源限制，以避免资源（服务器本地和服务级别）的过度消耗。这些限制降低了其他租户消耗资源所带来的影响，从而改进了租户体验。

- VM 的网络出口有带宽上限。 Azure Stack 中的上限与 Azure 中的上限相同。
- 对于存储资源，Azure Stack 实施存储 IOPS（每秒输入/输出操作次数）限制，以避免租户因使用存储而造成资源过度消耗。
- 对于 VM 磁盘，Azure Stack 上的磁盘 IOPS 取决于 VM 大小而不是磁盘类型。 这意味着，对于 Standard_Fs 系列 VM，不管你选择 SSD 还是 HDD 作为磁盘类型，第二个数据磁盘的 IOPS 限制都是 2300 IOPS。

下表列出了 Azure Stack 支持的 VM 及其配置：

| 类型            | 大小          | 支持的大小范围 |
| ----------------| ------------- | ------------------------ |
|常规用途  |基本 A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|常规用途  |标准 A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|常规用途  |Av2 系列     |[A1_v2 - A8m_v2](azure-stack-vm-sizes.md#av2-series)     |
|常规用途  |D 系列       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|常规用途  |Dv2 系列     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|常规用途  |DS 系列      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|常规用途  |DSv2 系列    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|内存优化 |D 系列       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|内存优化 |DS 系列      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|内存优化 |Dv2 系列     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|内存优化 |DSv2 系列    |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |
|计算优化|F 系列       |[F1 - F16](azure-stack-vm-sizes.md#f-series)    |
|计算优化|Fs 系列      |[F1s - F16s](azure-stack-vm-sizes.md#fs-series)    |
|计算优化|Fsv2 系列    |[F2s_v2 - F64s_v2](azure-stack-vm-sizes.md#fsv2-series)    |

VM 大小及其关联的资源数量在 Azure Stack 与 Azure 之间是一致的。 这种一致性涉及到内存量、核心数，以及可创建的数据磁盘的数量/大小。 但是，大小相同的 VM 的性能取决于特定 Azure Stack 环境的基础特征。

## <a name="vm-extensions"></a>VM 扩展

Azure Stack 包含少量的扩展。 可以通过市场联合来获取更新和其他扩展。

使用以下 PowerShell 脚本可获取 Azure Stack 环境中可用的 VM 扩展的列表：

如果在 VM 部署上预配扩展需要的时间太长，请让预配超时，而不是尝试停止该进程来解除分配或删除 VM。

### <a name="az-modules"></a>[Az 模块](#tab/az1)

```powershell
Get-AzVmImagePublisher -Location local | `
  Get-AzVMExtensionImageType | `
  Get-AzVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm1)

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

---

## <a name="api-versions"></a>API 版本

Azure Stack 中的 VM 功能支持以下 API 版本：

"2017-12-01", "2017-03-30", "2016-03-30", "2015-06-15"

可以使用以下 PowerShell 脚本来获取 Azure Stack 环境中可用的 VM 功能的 API 版本：

### <a name="az-modules"></a>[Az 模块](#tab/az2)

```powershell
Get-AzResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm2)

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

---


如果云运营商将 Azure Stack 环境更新为较新版本，则支持的资源类型和 API 版本列表可能有所不同。

## <a name="windows-activation"></a>Windows 激活

必须根据产品使用权利和 Microsoft 许可条款使用 Windows 产品。 Azure Stack 使用[自动 VM 激活](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) 来激活 Windows Server VM。

- Azure Stack 主机使用 Windows Server 2016 的 AVMA 密钥激活 Windows。 运行 Windows Server 2012 R2 或更高版本的所有 VM 都将自动激活。
- 运行 Windows Server 2012 或更早版本的 VM 不会自动激活，必须使用 [MAK 激活](https://technet.microsoft.com/library/ff793438.aspx)进行激活。 若要使用 MAK 激活，必须提供自己的产品密钥。

Microsoft Azure 使用 KMS 激活来激活 Windows Vm。 如果将 VM 从 Azure Stack 移动到 Azure 并且遇到了激活问题，请参阅[排查 Azure Windows VM 激活问题](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems)。 可以在 Azure 支持团队博客文章 [Troubleshooting Windows activation failures on Azure VMs](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/)（排查 Azure VM 上的 Windows 激活故障）中找到其他信息。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 中使用 PowerShell 创建 Windows VM](../user/azure-stack-quick-create-vm-windows-powershell.md)
