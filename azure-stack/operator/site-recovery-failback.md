---
title: Azure Site Recovery 故障回复工具用户指南
description: 了解如何使用 Azure Site Recovery 故障回复工具来保护虚拟机)  (Vm。
author: sethmanheim
ms.author: sethm
ms.date: 9/18/2020
ms.topic: how-to
ms.reviewer: rtiberiu
ms.lastreviewed: 9/18/2020
ms.openlocfilehash: 2b57527f3a65e97f5b83ada115faa63ace563ea4
ms.sourcegitcommit: 0f2852c3302c6723e7afad637f55b80359182ae3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91366238"
---
# <a name="azure-site-recovery-failback-tool"></a>Azure Site Recovery 故障回复工具

在连接的环境中，你可以使用 Azure Site Recovery 来保护 Azure Stack 集线器上运行)  (Vm 的虚拟机。 [本文](/azure/site-recovery/azure-stack-site-recovery) 介绍如何设置环境，以及 Site Recovery 如何帮助对这些工作负荷提供总体业务连续性和灾难恢复策略。

发生服务中断时，Azure Stack 中心操作员会经历 *故障转移* 过程;Azure Stack 集线器启动并再次运行后，它们会经历 *故障回复* 过程。 [此 Site Recovery 一文](/azure/site-recovery/azure-stack-site-recovery)中介绍了故障转移过程，但故障回复过程涉及几个手动步骤：

- 停止在 Azure 中运行的 VM。
- 下载 Vhd。
- 将 Vhd 上传到 Azure Stack 中心。
- 重新创建 Vm。
- 最后，启动 Azure Stack 集线器上运行的虚拟机。 

由于此过程可能很容易出错且非常耗时，因此我们构建了脚本来帮助加速和自动执行此过程。

## <a name="failback-procedure"></a>故障回复过程

自动故障回复过程包含三个主要部分：

- **AzSiteRecoveryVmVHD**：
  - 关闭 Azure VM。
  - 准备磁盘导出。
  - 通过 AzCopy 或 StorageBlobCopy 复制磁盘。
  - 将磁盘上传到 Azure Stack 中心存储帐户。

- 磁盘复制完成后， **AzSiteRecoveryVMFailBack**将介绍两个方案：
  - 原始 Azure Stack 中心已恢复。 原始 VM 仍然存在，你只需更改其 Vhd。
  - 发生灾难时，如果原始 Vm 丢失，必须重建整个 VM。

  此过程通过创建模板和所需的参数文件来涵盖这两种方案。

- 使用参数文件实际部署 Azure 资源管理器模板，然后在 Azure Stack 中心部署/创建 VM。

### <a name="prerequisites"></a>先决条件

执行故障回复过程需要满足以下先决条件：

- 复制 [Azure Site Recovery 故障回复工具](https://aka.ms/azshasr)。

- 导入 PowerShell 中的 FailbackTool hbase-runner.psm1 模块。

- 按照本文中的过程 [安装 Azure Stack 集线器的 Az 模块](powershell-install-az-module.md)。

-  (可选) [下载 AzCopy 版本 10](/azure/storage/common/storage-use-azcopy-v10)。

  - 使用 **AzCopy** 复制 blob 的速度更快，但需要额外的本地磁盘空间来临时存储 blob 文件。
  - 如果未使用 **AzCopy** ，则使用 **AzStorageBlobCopy**完成 VHD 复制。 这意味着不需要本地存储，但该过程需要更长的时间。

- 访问 Azure 门户上的资源，以及在 Azure Stack 中心创建这些资源的访问权限。

## <a name="step-1-copy-blob-from-azure-to-azure-stack-hub"></a>步骤1：从 Azure 将 blob 复制到 Azure Stack 中心

调用 **AzSiteRecoveryVmVHD** PowerShell cmdlet 以停止 azure VM，从 Azure 下载 vhd，并将其上传到 Azure Stack 集线器。 例如：

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

- 此示例使用 `$uris` 保存 `SourceDiskVhdUris` 步骤2中使用的值。

- `-SourceVM`参数是检索的 VM 对象 `Get-AzVM` 。
  - 这是在 Azure 上进行故障转移的 Azure Stack 集线器中受保护的 VM。
  - 如果 VM 正在运行，则不重要，因为脚本会关闭 VM。 但是，建议你显式关闭它并相应地停止 VM 内的服务。

- 你可以使用 `TargetStorageAccountKey`) 或 SAS 令牌提供帐户密钥 ( (在 `TargetStorageAccountSasToken` Azure Stack 中心端使用存储帐户) 。 必须在存储帐户级别创建 SAS 令牌，并且至少具有以下权限：

   :::image type="content" source="media/site-recovery-failback/sasperms.png" alt-text="SAS 令牌权限":::

- 可以提供存储终结点，其中包括区域和 FQDN;例如， `regionname.azurestack.microsoft.com` 或 Azure Stack 集线器的环境名称，如 `AzureStackTenant` 。 如果使用环境名称，则应使用 **AzEnvironment**来列出它。

- 可以选择使用 **AzCopy** 或 **AzStorageBlobCopy** 将 VHD 从 Azure 复制到 Azure Stack 中心。 **AzCopy** 速度更快，但必须先将 VHD 文件下载到本地文件夹：
  - 若要使用 **AzCopy**，请提供参数 `-AzCopyPath` ，并 `-VhdLocalFolder` () 将 vhd 复制到的路径。
  - 如果本地没有足够的空间，可以通过省略参数和来选择直接复制 VHD，无需 **AzCopy** `-AzCopyPath` `-VhdLocalFolder` 。 默认情况下，此命令使用 **AzStorageBlobCopy** 直接复制到 Azure Stack 中心存储帐户。

## <a name="step-2-generate-resource-manager-templates"></a>步骤2：生成资源管理器模板

复制磁盘后，使用 **AzSiteRecoveryVMFailBack** cmdlet 创建 `$templateFile` 并 `$parameterFile` 要求在 AZURE STACK Hub 上部署 VM：

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

- 此示例使用 `-SourceDiskVhdUris`)  (第1步中的返回值 `$uris` 。

- 此 cmdlet 支持两种方案：
  - 通过指定 `-TargetVM` ，假设 VM 在 Azure Stack 中心端处于活动状态，并且你想要将其磁盘替换为从 Azure 复制的最新磁盘。
  - 此脚本将生成一个资源管理器模板来部署此 VM，并从 Azure Stack 中心删除现有的 VM。
  
  > [!NOTE]
  > 删除 Azure Stack 中心 VM 本身并不会删除其他对象 (例如 VNET、资源组、Nsg) 。 它仅删除 VM 资源本身，然后将模板与参数一起部署 `-incremental` 。

  - 由于未提供 `-TargetVM` 参数，脚本假定 Azure Stack 中心端已不存在 VM，因此该脚本将创建一个资源管理器模板来部署全新的 vm。

- 生成的资源管理器模板文件位于下 `-ArmTemplateDestinationPath` ，并返回模板文件或参数文件的完整路径。

- 如果 `-TargetVM` 提供了参数，则该 cmdlet 将删除 VM，因此你可以继续执行以下步骤。

## <a name="step-3-deploy-the-resource-manager-template"></a>步骤3：部署资源管理器模板

此时，VHD 上载到 Azure Stack 集线器，并创建资源管理器模板和相应的参数文件。 剩下的就是在 Azure Stack 集线器上部署 VM。

在某些情况下，你可能需要编辑此模板并添加、删除或更改某些名称或资源。 这是允许的，因为您可以根据需要编辑和调整模板。

准备就绪后，确认资源管理器模板中的资源符合预期后，可以调用 **AzResourceGroupDeployment** cmdlet 来部署资源。 例如：

```powershell
New-AzResourceGroupDeployment `
  -Name "Failback" `
  -ResourceGroupName "failbackrg" `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile `
  -Mode Incremental
```

请注意以下事项：

- `-ResourceGroupName`参数应为现有资源组。
- `-TemplateFile`和 `-TemplateParameterFile` 参数来自步骤2中的返回值。

## <a name="next-steps"></a>后续步骤

- [Azure Stack Hub VM 功能](../user/azure-stack-vm-considerations.md)
- [在 Azure Stack 集线器中添加和删除自定义 VM 映像](azure-stack-add-vm-image.md)
- [在 Azure Stack Hub 中使用 PowerShell 创建 Windows VM](../user/azure-stack-quick-create-vm-windows-powershell.md)