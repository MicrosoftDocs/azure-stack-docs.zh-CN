---
title: Azure Site Recovery 故障回复工具用户指南
description: 了解如何使用 Azure Site Recovery 故障回复工具来保护虚拟机 (VM)。
author: sethmanheim
ms.author: sethm
ms.date: 11/19/2020
ms.topic: how-to
ms.reviewer: rtiberiu
ms.lastreviewed: 11/19/2020
ms.openlocfilehash: 0cb3bccab11d337a8a8804578233edb95ac02dc6
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517219"
---
# <a name="azure-site-recovery-failback-tool"></a>Azure Site Recovery 故障回复工具

在连接的环境中，你可以使用 Azure Site Recovery 来保护在 Azure Stack Hub 上运行的虚拟机 (VM)。 [本文](/azure/site-recovery/azure-stack-site-recovery)介绍了如何设置环境，以及如何通过 Site Recovery 为这些工作负荷改进总体业务连续性和灾难恢复策略。

发生中断时，Azure Stack Hub 操作员会经历故障转移过程；在 Azure Stack Hub 启动并再次运行后，它们会经历故障回复过程。 故障转移过程在[此 Site Recovery 文章](/azure/site-recovery/azure-stack-site-recovery)中进行了介绍，但故障回复过程涉及几个手动步骤：

1. 停止在 Azure 中运行的 VM。
2. 下载 VHD。
3. 将 VHD 上传到 Azure Stack Hub。
4. 重新创建 VM。
5. 最后，启动在 Azure Stack Hub 上运行的 VM。 

由于此过程容易出错且耗时，因此我们构建了脚本来帮助加速和自动化此过程。

> [!Note]  
> Azure Site Recovery 工具需要 Azure Stack 集线器 Az 模块。 如果正在运行 Azure Stack Hub AzureRM 模块，则需要升级工作站，或使用 Az 模块在隔离的环境中使用 Azure Site Recovery 故障回复工具。 有关详细信息，请参阅 [安装 PowerShell Az module for Azure Stack Hub](powershell-install-az-module.md)。

## <a name="failback-procedure"></a>故障回复过程

自动故障回复过程包含三个主要部分：

- **Copy-AzSiteRecoveryVmVHD**：
  - 关闭 Azure VM。
  - 准备磁盘导出。
  - 通过 AzCopy 或 StorageBlobCopy 复制磁盘。
  - 将磁盘上传到 Azure Stack Hub 存储帐户。

- 复制磁盘后，在使用 **Prepare-AzSiteRecoveryVMFailBack** 时涉及两种场景：
  - 原始 Azure Stack Hub 已恢复。 原始 VM 仍然存在，你只需更改其 VHD。
  - 发生灾难时，如果原始 VM 丢失，必须重建整个 VM。

  此过程通过创建模板和所需的参数文件来涵盖这两种场景。

- 使用参数文件来实际部署 Azure 资源管理器模板，然后在 Azure Stack Hub 上部署/创建 VM。

### <a name="prerequisites"></a>先决条件

执行故障回复过程需要满足以下先决条件：

- 复制 [Azure Site Recovery 故障回复工具](https://aka.ms/azshasr)。

- 在 PowerShell 中导入 FailbackTool.psm1 模块。

- 按照[此文](powershell-install-az-module.md)中的过程安装 Azure Stack Hub 的 Az 模块。

- （可选）[下载 AzCopy 版本 10](/azure/storage/common/storage-use-azcopy-v10)。

  - 使用 **AzCopy** 复制 blob 的速度更快，但需要额外的本地磁盘空间来临时存储 blob 文件。
  - 如果不使用 **AzCopy**，则使用 **AzStorageBlobCopy** 进行 VHD 复制。 这意味着不需要本地存储，但该过程需要更长的时间。

- 在 Azure 门户中访问资源的权限，以及在 Azure Stack Hub 上创建这些资源的访问权限。

## <a name="step-1-copy-blob-from-azure-to-azure-stack-hub"></a>步骤 1：将 blob 从 Azure 复制到 Azure Stack Hub

调用 **Copy-AzSiteRecoveryVmVHD** PowerShell cmdlet 以停止 Azure VM，从 Azure 下载 VHD，然后将其上传到 Azure Stack Hub。 例如：

```powershell
$uris = Copy-AzSiteRecoveryVmVHD `
        -SourceVM $vmOnAzure `
        -TargetStorageAccountName "targetaccountName" `
        -TargetStorageEndpoint "redmond.ext-v.masd.stbtest.microsoft.com" `
        -TargetStorageAccountKey $accountKey `
        -AzCopyPath "C:\azcopy_v10\azcopy.exe" `
        -VhdLocalFolder "C:\tempfolder"
```

请注意以下事项：

- 此示例使用 `$uris` 来保存步骤 2 中使用的 `SourceDiskVhdUris` 值。

- `-SourceVM` 参数是由 `Get-AzVM` 检索的 VM 对象。
  - 这是在 Azure 上进行了故障转移的 Azure Stack Hub 中受保护的 VM。
  - 如果 VM 正在运行，则不重要，因为脚本会关闭 VM。 但是，建议你显式关闭它并相应地停止 VM 内的服务。

- 你可以在 Azure Stack Hub 端提供帐户密钥（使用 `TargetStorageAccountKey`）或存储帐户的 SAS 令牌（使用 `TargetStorageAccountSasToken`）。 SAS 令牌必须在存储帐户级别创建，至少需要以下权限：

   :::image type="content" source="media/site-recovery-failback/sasperms.png" alt-text="SAS 令牌权限":::

- 你可以提供存储终结点（包括区域和 FQDN），例如 `regionname.azurestack.microsoft.com`，也可以提供 Azure Stack Hub 的环境名称，例如 `AzureStackTenant`。 如果使用环境名称，则应使用 **Get-AzEnvironment** 来列出它。

- 可以选择使用 **AzCopy** 或 **AzStorageBlobCopy** 将 VHD 从 Azure 复制到 Azure Stack Hub。 **AzCopy** 速度更快，但必须先将 VHD 文件下载到本地文件夹：
  - 若要使用 **AzCopy**，请提供参数 `-AzCopyPath` 和 `-VhdLocalFolder`（VHD 将复制到的路径）。
  - 如果本地没有足够的空间，则可以选择通过省略参数 `-AzCopyPath` 和 `-VhdLocalFolder` 来直接复制 VHD，而不使用 **AzCopy**。 默认情况下，此命令使用 **AzStorageBlobCopy** 将其直接复制到 Azure Stack Hub 存储帐户。

## <a name="step-2-generate-resource-manager-templates"></a>步骤 2：生成资源管理器模板

复制磁盘后，使用 **Prepare-AzSiteRecoveryVMFailBack** cmdlet 来创建在 Azure Stack Hub 上部署 VM 所需的 `$templateFile` 和 `$parameterFile`：

```powershell
$templateFile, $parameterFile = Prepare-AzSiteRecoveryVMFailBack `
                                -SourceContextName "PublicAzure" `
                                -SourceVM $vmOnAzure `
                                -SourceDiskVhdUris $uris `
                                -TargetResourceLocation "redmond" `
                                -ArmTemplateDestinationPath "C:\ARMtemplates" `
                                -TargetVM $vmOnHub `
                                -TargetContextName "AzureStack"

```

请注意以下事项：

- 此示例使用 `-SourceDiskVhdUris` 作为步骤 1（使用 `$uris`）中的返回值。

- 此 cmdlet 支持两种场景：
  - 通过指定 `-TargetVM`，你假设 VM 在 Azure Stack Hub 端处于活动状态，并且你想要将其磁盘替换为从 Azure 复制的最新磁盘。
  - 此脚本生成一个资源管理器模板来部署此 VM，并从 Azure Stack Hub 中删除现有的 VM。
  
  > [!NOTE]
  > 删除 Azure Stack Hub VM 本身不会删除其他对象（例如 VNET、资源组、NSG）。 它仅删除 VM 资源本身，然后使用 `-incremental` 参数部署模板。

  - 通过不提供 `-TargetVM` 参数，此脚本假设该 VM 不再存在于 Azure Stack Hub 端，因此此脚本会创建一个资源管理器模板来部署全新的 VM。

- 生成的资源管理器模板文件置于 `-ArmTemplateDestinationPath` 下，将会返回模板文件或参数文件的完整路径。

- 如果提供了 `-TargetVM` 参数，则该 cmdlet 会删除 VM，因此你可以继续执行以下步骤。

## <a name="step-3-deploy-the-resource-manager-template"></a>步骤 3：部署资源管理器模板

此时已将 VHD 上传到 Azure Stack Hub，并创建了资源管理器模板和相应的参数文件。 剩下的事情就是在 Azure Stack Hub 上部署 VM。

在某些场景中，你可能需要编辑此模板并添加、删除或更改某些名称或资源。 这是允许的，因为你可以根据需要编辑和调整模板。

准备就绪后，确认资源管理器模板中的资源符合预期，然后就可以调用 **New-AzResourceGroupDeployment** cmdlet 来部署资源。 例如：

```powershell
New-AzResourceGroupDeployment `
  -Name "Failback" `
  -ResourceGroupName "failbackrg" `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile `
  -Mode Incremental
```

请注意以下事项：

- `-ResourceGroupName` 参数应为某个现有资源组。
- `-TemplateFile` 和 `-TemplateParameterFile` 参数来自步骤 2 中的返回值。

## <a name="next-steps"></a>后续步骤

- [Azure Stack Hub VM 功能](../user/azure-stack-vm-considerations.md)
- [在 Azure Stack Hub 中添加和删除自定义 VM 映像](azure-stack-add-vm-image.md)
- [在 Azure Stack Hub 中使用 PowerShell 创建 Windows VM](../user/azure-stack-quick-create-vm-windows-powershell.md)