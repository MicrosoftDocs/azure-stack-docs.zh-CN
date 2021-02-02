---
title: 将 VM 从 Azure 移动到 Azure Stack Hub
description: 了解如何将 VM 从 Azure 移动到 Azure Stack Hub。
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 89dc2d9dc55dcfe74565ad6c998880bd1c991126
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245723"
---
# <a name="move-a-vm-from-azure-to-azure-stack-hub"></a>将 VM 从 Azure 移动到 Azure Stack Hub

可以从 Azure 中创建的虚拟机 (VM) 将虚拟硬盘 (VHD) 上传到 Azure Stack Hub 实例。

## <a name="prepare-and-download-your-vhd-from-azure"></a>准备 VHD 并从 Azure 下载

在准备 VHD 时查找特定于你需求的部分。

#### <a name="windows---specialized"></a>[Windows - 专用](#tab/win-spec)

- 按照[使用 PowerShell 从专用磁盘创建 Windows VM](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) 一文中的步骤来准备 VHD。
- 若要部署 VM 扩展，请确保 VM 代理 .msi 可用。  
  有关信息和步骤，请参阅 [Azure 虚拟机代理概述](/azure/virtual-machines/extensions/agent-windows)。 移动 VM 之前，请确保已在 VM 上安装了扩展。 如果 VHD 中不存在 VM 代理，扩展部署将失败。 预配时无需设置 OS 配置文件或设置 `$vm.OSProfile.AllowExtensionOperations = $true`。

#### <a name="windows---generalized"></a>[Windows - 通用化](#tab/win-gen)

::: moniker range="<=azs-1910"
- 按照[从 Azure 下载 Windows VHD](/azure/virtual-machines/windows/download-vhd) 中的说明正确通用化并下载 VHD，然后将其移动到 Azure Stack Hub。
- 在 Azure 上预配 VM 时，请使用 PowerShell。 在没有 `-ProvisionVMAgent` 标志的情况下进行准备。
- 通用化 Azure 中的 VM 之前，先在该 VM 中使用 cmdlet 删除所有 VM 扩展。 可以转到 `Windows (C:) > WindowsAzure > Logs > Plugins` 查找已安装的 VM 扩展。

使用 Az PowerShell 模块：

```powershell  
Remove-AzVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

使用 AzureRM PowerShell 模块：

```powershell  
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```
::: moniker-end
::: moniker range=">=azs-2002"

按照[从 Azure 下载 Windows VHD](/azure/virtual-machines/windows/download-vhd) 中的说明正确通用化并下载 VHD，然后将其移动到 Azure Stack Hub。
::: moniker-end

#### <a name="linux---specialized"></a>[Linux - 专用](#tab/lin-spec)

- 下载 Linux VM 之前，请按照[使用 Azure CLI 从自定义磁盘创建 Linux VM](/azure/virtual-machines/linux/upload-vhd#prepare-the-vm) 一文的“准备 VM”部分中的指导进行操作
- 按照[从 Azure 下载 Linux VHD](/azure//virtual-machines/windows/download-vhd) 一文中的步骤来准备和下载 VHD。
- 对于专用 VHD，请确保通过 `-CreateOption Attach` 使用“附加”语义。 可以在[通过将现有托管 OS 磁盘与 PowerShell 配合使用来创建虚拟机 (Windows)](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks) 一文中找到示例。

#### <a name="linux---generalized"></a>[Linux - 通用](#tab/lin-gen)

1. 停止 **waagent** 服务：

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   适用于 Azure Stack Hub 的 Azure Linux 代理版本[在此处记述](../operator/azure-stack-linux.md#azure-linux-agent)。 确保在其上运行 sysprep 的映像包含与 Azure Stack Hub 兼容的 Azure Linux 代理版本。

2. 停止/解除分配 VM。

3. 下载 VHD。

   1. 若要下载 VHD 文件，请生成共享访问签名 (SAS) URL。 生成 URL 时，将为 URL 分配到期时间。

   1. 在 VM 的边栏选项卡菜单中选择“磁盘”。

   1. 为 VM 选择操作系统磁盘，然后选择“磁盘导出”。

   1. 将 URL 的过期时间设置为 36000。

   1. 选择“生成 URL”。

   1. 生成 URL。

   1. 在生成的 URL 下，选择“下载 VHD 文件”。

   1. 可能需要选择浏览器中的“保存”才能开始下载。 VHD 文件的默认名称为 **abcd**。

   1. 现在可以将此 VHD 移动到 Azure Stack Hub。

> [!IMPORTANT]  
> 可在[将 VHD 上传到 Azure 并创建新的 VM 的示例脚本](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)一文中查找脚本，以将 VHD 上传到 Azure Stack Hub 用户存储帐户并创建 VM。 请确保提供 `$urlOfUploadedImageVhd` 作为 Azure Stack Hub 存储帐户和容器 URL。 对于通用 VHD，请确保在设置 `-CreateOption FromImage` 使用 `FromImage` 值。

---

## <a name="verify-your-vhd"></a>验证 VHD

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>上传到存储帐户

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-vm"></a>创建 VM

自定义映像有两种形式：专用和通用 。

### <a name="specialized"></a>[专用](#tab/create-vm-spec)

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

### <a name="generalized"></a>[通用](#tab/create-vm-gen)

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]
---
## <a name="next-steps"></a>后续步骤

[将 VM 移动到 Azure Stack Hub 概述](vm-move-overview.md)
