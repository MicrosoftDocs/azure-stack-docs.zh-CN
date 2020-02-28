---
title: Azure Stack 中心 VM 功能
description: 了解在 Azure Stack 集线器中使用 Vm 时的不同功能和注意事项。
author: mattbriggs
ms.topic: article
ms.date: 2/3/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: ba6e4483475b97b6803781f5b7c5d29d94cbe896
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77705128"
---
# <a name="azure-stack-hub-vm-features"></a>Azure Stack 中心 VM 功能

Azure Stack 集线器虚拟机（Vm）提供按需的可缩放计算资源。 在部署 Vm 之前，应了解 Azure Stack 集线器和 Microsoft Azure 中可用的 VM 功能之间的差异。 本文介绍了这些差异并标识了规划 VM 部署的关键注意事项。 若要了解 Azure Stack 中心与 Azure 之间的高级差异，请参阅[关键注意事项](azure-stack-considerations.md)一文。

## <a name="vm-differences"></a>VM 差异

| Feature | Azure （全局） | Azure Stack 中心 |
| --- | --- | --- |
| 虚拟机映像 | Azure Marketplace 提供了可用于创建 VM 的映像。 请参阅[Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1)页面，查看 azure marketplace 中提供的映像的列表。 | 默认情况下，Azure Stack 中心 Marketplace 中没有可用的映像。 Azure Stack 中心云管理员必须先将映像发布或下载到 Azure Stack 中心市场，然后用户才能使用。 |
| VHD 生成 | 第两代 Vm 支持生成一个 Vm 时不支持的关键功能。 这些功能包括增加的内存、Intel 软件防护扩展（Intel SGX）和虚拟化永久性内存（vPMEM）。 在本地运行的两个 Vm 在 Azure 中不支持某些功能。 有关详细信息，请参阅[Azure 上的第2代 Vm 支持](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)  | Azure Stack 集线器仅支持代一代 Vm。 你可以将第一个 VM 从 VHDX 转换为 VHD 文件格式，并从动态扩展为固定大小磁盘。 无法更改 VM 的代。 有关详细信息，请参阅[Azure 上的第2代 Vm 支持](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)。 |
| 虚拟机大小 | Azure 支持多种 Vm 大小。 若要了解可用大小和选项，请参阅[Windows vm 大小](/azure/virtual-machines/virtual-machines-windows-sizes)和[Linux VM 大小](/azure/virtual-machines/linux/sizes)主题。 | Azure Stack 集线器支持 Azure 中可用的 VM 大小的子集。 若要查看受支持的大小列表，请参阅本文的[VM 大小](#vm-sizes)部分。 |
| 虚拟机配额 | [配额限制](/azure/azure-subscription-service-limits#service-specific-limits)由 Microsoft 设置。 | Azure Stack 中心云管理员必须先分配配额，然后才能向其用户提供 VM。 |
| 虚拟机扩展 |Azure 支持多种 VM 扩展。 若要了解可用扩展，请参阅[VM 扩展和功能](/azure/virtual-machines/windows/extensions-features)一文。| Azure Stack 集线器支持 Azure 中提供的一小部分扩展，每个扩展都有特定的版本。 Azure Stack 中心云管理员可以选择向其用户提供哪些扩展。 若要查看支持的扩展的列表，请参阅本文的[VM 扩展](#vm-extensions)部分。 |
| 虚拟机网络 | 分配给租户 VM 的公共 IP 地址可通过 Internet 访问。<br><br><br>Azure Vm 具有固定的 DNS 名称。 | 分配给租户 VM 的公共 IP 地址只能在 Azure Stack 开发工具包环境中访问。 用户必须有权通过[RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)或[VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)访问 Azure Stack 开发工具包，才能连接到 Azure Stack 中心创建的虚拟机。<br><br>在特定 Azure Stack 中心实例内创建的 Vm 具有基于云管理员配置的值的 DNS 名称。 |
| 虚拟机存储 | 支持[托管磁盘。](/azure/virtual-machines/windows/managed-disks-overview) | 在版本1808和更高版本的 Azure Stack 集线器中支持托管磁盘。 |
| 虚拟机磁盘性能 | 取决于磁盘类型和大小。 | 取决于将磁盘附加到的 VM 的 VM 大小。 有关详细信息，请参阅[Azure Stack 中心支持的 VM 大小](azure-stack-vm-sizes.md)一文。
| API 版本 | Azure 始终具有适用于所有 VM 功能的最新 API 版本。 | Azure Stack 中心为这些服务支持特定的 Azure 服务和特定的 API 版本。 若要查看受支持的 API 版本列表，请参阅本文的[api 版本](#api-versions)部分。 |
| Azure 实例元数据服务 | Azure 实例元数据服务提供有关可用于管理和设置 VM 的正在运行的 VM 实例的信息。  | Azure Stack 集线器上不支持 Azure 实例元数据服务。 |
| 虚拟机可用性集|多个容错域（每个区域2个或3个）。<br>多个更新域。|多个容错域（每个区域2个或3个）。<br>单个更新域，其中包含用于在更新期间保护工作负荷的实时迁移。 支持20个更新域以实现模板兼容性。<br>VM 和可用性集应在相同的位置和资源组中。|
| 虚拟机规模集|支持自动缩放。|不支持自动缩放。<br><br>使用门户、资源管理器模板或 PowerShell 将更多实例添加到规模集。 |
| 云见证 | 从 Azure Stack Hub 中可用的存储帐户属性中选择终结点。 | [Cloud 见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)是一种故障转移群集仲裁见证，使用 Microsoft Azure 在群集仲裁上提供投票。<br>与 Azure Stack 中心相比，全球 Azure 中的终结点可能如下所示：<br>对于全球 Azure：<br>`https://mywitness.blob.core.windows.net/`<br>对于 Azure Stack 中心：<br>`https://mywitness.blob.<region>.<FQDN>/`|
| 虚拟机诊断 | 支持 Linux VM 诊断。 | Azure Stack 集线器不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。 |

## <a name="vm-sizes"></a>VM 大小

Azure Stack 中心施加了资源限制，以避免过度消耗资源（服务器本地和服务级别）。这些限制通过减少其他租户对资源消耗的影响来改善租户体验。

- 对于从 VM 传出的网络，有带宽上限。 Azure Stack 集线器中的 cap 与 Azure 中的 cap 相同。
- 对于存储资源，Azure Stack 集线器实现存储 IOPS （每秒输入/输出操作数）限制，以避免租户使用存储的基本过度。
- 对于 VM 磁盘，Azure Stack 集线器上的磁盘 IOPS 是 VM 大小的函数，而不是磁盘类型。 这意味着，对于 Standard_Fs 系列 VM，无论是为磁盘类型选择 SSD 还是 HDD，第二个数据磁盘的 IOPS 限制为 2300 IOPS。

下表列出了 Azure Stack 集线器上支持的 Vm 及其配置：

| 类型            | 大小          | 支持的大小范围 |
| ----------------| ------------- | ------------------------ |
|常规用途  |基本 A        |[A0-A4](azure-stack-vm-sizes.md#basic-a)                   |
|常规用途  |标准 A     |[A0-A7](azure-stack-vm-sizes.md#standard-a)              |
|常规用途  |Av2 系列     |[A1_v2-A8m_v2](azure-stack-vm-sizes.md#av2-series)     |
|常规用途  |D 系列       |[D1-D4](azure-stack-vm-sizes.md#d-series)              |
|常规用途  |Dv2 系列     |[D1_v2-D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|常规用途  |DS 系列      |[DS1-DS4](azure-stack-vm-sizes.md#dv2-series)            |
|常规用途  |DSv2-series    |[DS1_v2-DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|内存优化 |D 系列       |[D11-D14](azure-stack-vm-sizes.md#mo-d)            |
|内存优化 |DS 系列      |[DS11-DS14](azure-stack-vm-sizes.md#mo-ds)|
|内存优化 |Dv2 系列     |[D11_v2-DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|内存优化 |DSv2-series    |[DS11_v2-DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |
|计算优化|F 系列       |[F1 - F16](azure-stack-vm-sizes.md#f-series)    |
|计算优化|Fs 系列      |[F1s - F16s](azure-stack-vm-sizes.md#fs-series)    |
|计算优化|Fsv2 系列    |[F2s_v2-F64s_v2](azure-stack-vm-sizes.md#fsv2-series)    |

VM 大小及其关联的资源数量在 Azure Stack 中心与 Azure 之间一致。 此一致性包括内存量、内核数，以及可以创建的数据磁盘的数量/大小。 但是，具有相同大小的 Vm 的性能取决于特定 Azure Stack 中心环境的基本特征。

## <a name="vm-extensions"></a>VM 扩展

Azure Stack 中心包含一小部分扩展。 更新和其他扩展可通过 Marketplace 供稿获得。

使用以下 PowerShell 脚本获取 Azure Stack 中心环境中可用的 VM 扩展列表：

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

如果在 VM 部署上预配扩展需要的时间太长，请等待预配超时，而不是尝试停止该进程来解除分配或删除 VM。

## <a name="api-versions"></a>API 版本

Azure Stack 集线器中的 VM 功能支持以下 API 版本：

"2017-12-01"、"2017-03-30"、"2016-03-30"、"2015-06-15"

你可以使用以下 PowerShell 脚本获取 Azure Stack 中心环境中可用的 VM 功能的 API 版本：

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

如果云操作员将 Azure Stack 集线器环境更新到较新的版本，则受支持的资源类型和 API 版本的列表可能会有所不同。

## <a name="windows-activation"></a>Windows 激活

必须按照产品使用权限和 Microsoft 许可条款使用 Windows 产品。 Azure Stack 中心使用[自动 VM 激活](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11))（AVMA）来激活 Windows Server vm。

- Azure Stack 集线器主机会激活 windows，其中包含 Windows Server 2016 的 AVMA 键。 将自动激活运行 Windows Server 2012 R2 或更高版本的所有 Vm。
- 运行 Windows Server 2012 或更早版本的 Vm 不会自动激活，必须使用[MAK 激活](https://technet.microsoft.com/library/ff793438.aspx)来激活。 若要使用 MAK 激活，你必须提供自己的产品密钥。

Microsoft Azure 使用 KMS 激活来激活 Windows Vm。 如果将 VM 从 Azure Stack 中心移动到 Azure 并遇到激活问题，请参阅[排查 Azure WINDOWS VM 激活问题](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems)。 有关详细信息，请参阅[Azure vm 上的 Windows 激活故障疑难解答](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/)Azure 支持团队博客文章。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 集线器中使用 PowerShell 创建 Windows VM](azure-stack-quick-create-vm-windows-powershell.md)
