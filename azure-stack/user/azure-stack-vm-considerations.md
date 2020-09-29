---
title: Azure Stack Hub VM 功能
description: 了解使用 Azure Stack Hub 中的 VM 时的不同功能和注意事项。
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: 993fb7a054cd350f0231eeb2108cc5a43ddb3f9e
ms.sourcegitcommit: 7d518629bd55f24e7459404bb19b7db8a54f4b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88145431"
---
# <a name="azure-stack-hub-vm-features"></a>Azure Stack Hub VM 功能

Azure Stack Hub 虚拟机 (VM) 提供可按需缩放的计算资源。 在部署 Vm 之前，应了解 Azure Stack 集线器和 Microsoft Azure 中可用的 VM 功能之间的差异。 本文将说明这些差异，并指明规划 VM 部署方面的重要注意事项。 有关 Azure Stack Hub 与 Azure 之间的大致差异的详细信息，请参阅[重要注意事项](azure-stack-considerations.md)一文。

## <a name="vm-differences"></a>VM 差异

| 功能 | Azure（公有云） | Azure Stack Hub |
| --- | --- | --- |
| 虚拟机映像 | Azure 市场包含可用于创建 VM 的映像。 若要查看 Azure 市场中的可用映像列表，请参阅 [Azure 市场](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1)页。 | 默认情况下，Azure Stack 中心 Marketplace 中没有可用的映像。 Azure Stack 中心云管理员必须先将映像发布或下载到 Azure Stack 中心市场，然后用户才能使用。 |
| VHD 代系 | 第二代 VM 支持第一代 VM 所不支持的某些关键特性。 这些特性包括更大的内存、Intel Software Guard Extensions (Intel SGX) 和虚拟化持久性内存 (vPMEM)。 在本地运行的第二代 VM 具有 Azure 中尚不支持的一些功能。 有关详细信息，请参阅[对 Azure 上的第 2 代 VM 的支持](/azure/virtual-machines/windows/generation-2)  | Azure Stack Hub 仅支持第一代 VM。 可以将第一代 VM 从 VHDX 转换为 VHD 文件格式，从动态扩展磁盘转换为固定大小磁盘。 无法更改 VM 的代次。 有关详细信息，请参阅[对 Azure 上的第 2 代 VM 的支持](/azure/virtual-machines/windows/generation-2)。 |
| 虚拟机大小 | Azure 支持各种不同的 VM 大小。 若要了解可用的大小和选项，请参阅 [Azure VM 大小](/azure/virtual-machines/sizes)。 | Azure Stack Hub 支持一部分可在 Azure 中使用的 VM 大小。 若要查看支持的大小列表，请参阅本文的 [VM 大小](#vm-sizes)部分。 |
| 虚拟机配额 | [配额限制](/azure/azure-resource-manager/management/azure-subscription-service-limits#managing-limits) 由 Microsoft 设置。 | Azure Stack Hub 云管理员在提供 VM 给其用户之前，必须先分配配额。 |
| 虚拟机扩展 |Azure 支持多种不同的 VM 扩展。 若要了解可用的扩展，请参阅 [VM 扩展和功能](/azure/virtual-machines/windows/extensions-features)一文。| Azure Stack Hub 支持一部分可在 Azure 中使用的扩展，每个扩展有特定的版本。 Azure Stack Hub 云管理员可以选择要将哪些扩展提供给其用户使用。 若要查看支持的扩展列表，请参阅本文的 [VM 扩展](#vm-extensions)部分。 |
| 虚拟机网络 | 分配给租户 VM 的公共 IP 地址可通过 Internet 访问。<br><br><br>Azure VM 具有固定的 DNS 名称。 | 只能在 Azure Stack 开发工具包环境中访问分配给租户 VM 的公共 IP 地址。 用户必须能够通过 [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) 或 [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) 访问 Azure Stack 开发工具包，才能连接到在 Azure Stack Hub 中创建的 VM。<br><br>在特定 Azure Stack Hub 实例中创建的 VM 的 DNS 名称基于云管理员配置的值。 |
| 虚拟机存储 | 支持[托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)。 | 版本为 1808 及更高版本的 Azure Stack Hub 支持托管磁盘。 |
| 虚拟机磁盘性能 | 取决于磁盘类型和大小。 | 取决于磁盘所附加到的 VM 的大小。 有关详细信息，请参阅 [Azure Stack Hub 中支持的 VM 大小](azure-stack-vm-sizes.md)一文。
| API 版本 | Azure 始终提供所有 VM 功能的最新 API 版本。 | Azure Stack Hub 支持特定的 Azure 服务以及这些服务的特定 API 版本。 若要查看支持的 API 版本列表，请参阅本文的 [API 版本](#api-versions)部分。 |
| Azure 实例元数据服务 | Azure 实例元数据服务提供有关可用于管理和设置 VM 的正在运行的 VM 实例的信息。  | Azure Stack Hub 不支持 Azure 实例元数据服务。 |
| 虚拟机可用性集|多个容错域（每个区域 2 个或 3 个）。<br>多个更新域。|多个容错域（每个区域 2 个或 3 个）。<br>单个更新域，具有实时迁移功能，可在更新期间保护工作负荷。 支持 20 个更新域以实现模板兼容性。<br>VM 和可用性集应位于相同的位置和资源组中。|
| 虚拟机规模集|支持自动缩放。|不支持自动缩放。<br><br>使用门户、资源管理器模板或 PowerShell 将更多实例添加到规模集。 |
| 云见证 | 从 Azure Stack Hub 中提供的存储帐户属性中选择终结点。 | [Cloud 见证](/windows-server/failover-clustering/deploy-cloud-witness) 是一种故障转移群集仲裁见证，使用 Microsoft Azure 在群集仲裁上提供投票。<br>与 Azure Stack 中心相比，全球 Azure 中的终结点可能如下所示：<br>对于全球 Azure：<br>`https://mywitness.blob.core.windows.net/`<br>对于 Azure Stack Hub：<br>`https://mywitness.blob.<region>.<FQDN>/`|
| 虚拟机诊断 | 支持 Linux VM 诊断。 | Azure Stack Hub 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。 |
| 嵌套虚拟化 VM 大小 | 支持 | 不支持 |

## <a name="vm-sizes"></a>VM 大小

Azure Stack Hub 施加了一些资源限制，以避免资源（服务器本地和服务级别）的过度消耗。这些限制降低了其他租户消耗资源所带来的影响，从而改进了租户体验。

- VM 的网络出口有带宽上限。 Azure Stack Hub 中的上限与 Azure 中的上限相同。
- 对于存储资源，Azure Stack Hub 实施存储 IOPS（每秒输入/输出操作次数）限制，以避免租户因使用存储而造成资源过度消耗。
- 对于 VM 磁盘，Azure Stack Hub 上的磁盘 IOPS 取决于 VM 大小而不是磁盘类型。 这意味着，对于 Standard_Fs 系列 VM，不管你选择 SSD 还是 HDD 作为磁盘类型，第二个数据磁盘的 IOPS 限制都是 2300 IOPS。
- 附加到 VM 的临时磁盘并不是永久性的，并且可能会在控制面操作（如调整大小或停止解除分配）时丢失。

下表列出了 Azure Stack Hub 支持的 VM 及其配置：

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

VM 大小及其关联的资源数量在 Azure Stack Hub 与 Azure 之间是一致的。 这种一致性涉及到内存量、核心数，以及可创建的数据磁盘的数量/大小。 但是，大小相同的 VM 的性能取决于特定 Azure Stack Hub 环境的基础特征。

## <a name="vm-extensions"></a>VM 扩展

Azure Stack Hub 包含少量的扩展。 可以通过市场联合来获取更新和其他扩展。

使用以下 PowerShell 脚本可获取 Azure Stack Hub 环境中可用的 VM 扩展的列表：

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

如果在 VM 部署上预配某个扩展时耗时过长，请让预配超时，而不要尝试通过停止该进程来解除 VM 的分配或将 VM 删除。

## <a name="api-versions"></a>API 版本

Azure Stack Hub 中的 VM 功能支持以下 API 版本：

"2017-12-01", "2017-03-30", "2016-03-30", "2015-06-15"

可以使用以下 PowerShell 脚本来获取 Azure Stack Hub 环境中可用的 VM 功能的 API 版本：

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

如果云运营商将 Azure Stack Hub 环境更新为较新版本，则支持的资源类型和 API 版本列表可能有所不同。

## <a name="windows-activation"></a>Windows 激活

必须根据产品使用权利和 Microsoft 许可条款使用 Windows 产品。 Azure Stack Hub 使用[自动 VM 激活](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn303421(v%3dws.11)) (AVMA) 来激活 Windows Server VM。

- Azure Stack Hub 主机使用 Windows Server 2016 的 AVMA 密钥激活 Windows。 运行 Windows Server 2012 R2 或更高版本的所有 VM 都将自动激活。
- 运行 Windows Server 2012 或更早版本的 VM 不会自动激活，必须使用 [MAK 激活](/previous-versions/tn-archive/ff793438(v=technet.10))进行激活。 若要使用 MAK 激活，必须提供自己的产品密钥。

Microsoft Azure 使用 KMS 激活来激活 Windows Vm。 如果将 VM 从 Azure Stack Hub 移到 Azure 并且遇到了激活问题，请参阅[排查 Azure Windows VM 激活问题](/azure/virtual-machines/windows/troubleshoot-activation-problems)。 可以在 Azure 支持团队博客文章 [Troubleshooting Windows activation failures on Azure VMs](/archive/blogs/mast/troubleshooting-windows-activation-failures-on-azure-vms)（排查 Azure VM 上的 Windows 激活故障）中找到其他信息。

## <a name="high-availability"></a>高可用性

由于 Azure Stack Hub 操作员计划的计划内维护，你的VM 可能需要重新启动。 为了在 Azure 中实现多 VM 生产系统的高可用性，可以将 VM 置于横跨多个容错域和更新域的[可用性集](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)中。 在较小规模的 Azure Stack Hub 中，可用性集中的容错域定义为缩放单元中的单个节点。  

在发生硬件故障时，虽然 Azure Stack Hub 的基础结构已具备故障还原能力，但基础技术（故障转移群集功能）的局限仍会导致受影响物理服务器上的 VM 出现停机。 为了与 Azure 保持一致，Azure Stack Hub 支持的可用性集最多有三个容错域。

|                   |             |
|-------------------|-------------|
| **容错域** | 置于可用性集中的 VM 在物理上是彼此隔离的，换句话说，会尽可能均衡地让其分散到多个容错域（Azure Stack Hub 节点）中。 如果发生硬件故障，出现故障的容错域中的 VM 将在其他容错域中重启。 它们保留在与其他 VM 不同的容错域中，但如果可能，则保留在相同的可用性集中。 当硬件重新联机时，会对 VM 重新进行均衡操作，以维持高可用性。 |
| **更新域**| 更新域是 Azure 在可用性集中提供高可用性的另一种方法。 更新域是可以同时维护的基础硬件逻辑组。 同一个更新域中的 VM 会在计划内维护期间一起重启。 当租户在可用性集内创建 VM 时，Azure 平台会自动将 VM 分布到这些更新域。 <br>在 Azure Stack Hub 中，VM 会先跨群集中的其他联机主机进行实时迁移，然后其基础主机才会进行更新。 由于在主机更新期间不会造成租户停机，因此 Azure Stack Hub 上存在更新域功能只是为了确保与 Azure 实现模板兼容。 可用性集中的 VM 将显示 0 作为其在门户上的更新域编号。 |

## <a name="next-steps"></a>后续步骤

[在 Azure Stack Hub 中使用 PowerShell 创建 Windows VM](azure-stack-quick-create-vm-windows-powershell.md)
