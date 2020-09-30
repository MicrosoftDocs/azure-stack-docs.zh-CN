---
title: 将 VM 从 Azure 移到 Azure Stack 中心
description: 了解如何将 VM 从 Azure 迁移到 Azure Stack 中心到 Azure Stack 中心。
author: mattbriggs
ms.topic: how-to
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: c8c68a64f7a05e03c70e138cb4d8c95da7417ec9
ms.sourcegitcommit: 3e225b30a54159b6b8dbeb2f843a2e5a721b746e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91519401"
---
# <a name="move-a-vm-from-azure-to-azure-stack-hub"></a>将 VM 从 Azure 移到 Azure Stack 中心

可以从在 Azure 中创建的虚拟机 () VM (VHD) 上传到 Azure Stack 中心实例的虚拟硬盘。

## <a name="prepare-and-download-your-vhd-from-azure"></a>准备并从 Azure 下载 VHD

查找在准备 VHD 时特定于你的需求的部分。

#### <a name="windows---specialized"></a>[Windows - 专用](#tab/win-spec)

- 按照 [使用 PowerShell 从专用磁盘创建 WINDOWS VM](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) 一文中的步骤来准备 VHD。
- 若要部署 VM 扩展，请确保 VM 代理 .msi 可用。  
  有关信息和步骤，请参阅 [Azure 虚拟机代理概述](/azure/virtual-machines/extensions/agent-windows)。 在移动 VM 之前，请确保已在 VM 上安装了该扩展。 如果 VHD 中不存在 VM 代理，扩展部署将失败。 预配时无需设置 OS 配置文件或设置 `$vm.OSProfile.AllowExtensionOperations = $true`。

#### <a name="windows---generalized"></a>[Windows-通用化](#tab/win-gen)

::: moniker range="<=azs-1910"
- 请按照 [从 Azure 下载 WINDOWS VHD](/azure/virtual-machines/windows/download-vhd) 中的说明进行操作，在将 vhd 移动到 Azure Stack 集线器之前，对其进行正确通用化和下载。
- 在 Azure 上预配 VM 时，请使用 PowerShell。 无标志的情况下准备 `-ProvisionVMAgent` 。
- 通用化 Azure 中的 VM 之前，先在该 VM 中使用 **Remove-AzureRmVMExtension** cmdlet 删除所有 VM 扩展。 可以转到 `Windows (C:) > WindowsAzure > Logs > Plugins` 查找已安装的 VM 扩展。

```powershell  
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```
::: moniker-end
::: moniker range=">=azs-2002"

请按照 [从 Azure 下载 WINDOWS VHD](/azure/virtual-machines/windows/download-vhd) 中的说明进行操作，在将 vhd 移动到 Azure Stack 集线器之前，对其进行正确通用化和下载。
::: moniker-end

#### <a name="linux---specialized"></a>[Linux - 专用](#tab/lin-spec)

- 下载 Linux VM 之前，请遵循[使用 Azure CLI 从自定义磁盘创建 LINUX vm](/azure/virtual-machines/linux/upload-vhd#prepare-the-vm)一文中的 "准备 VM" 一节中的指南。
- 请按照 [从 Azure 下载 LINUX VHD 一](/azure//virtual-machines/windows/download-vhd) 文中的步骤来准备和下载 VHD。
- 对于专用的 VHD，请确保使用 "附加" 语义 `-CreateOption Attach` 。 可以在 [使用 PowerShell (Windows) 创建虚拟机 ](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks)一文中找到示例。

#### <a name="linux---generalized"></a>[Linux - 通用](#tab/lin-gen)

1. 停止 **waagent** 服务：

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   适用于 Azure Stack Hub 的 Azure Linux 代理版本[在此处记述](../operator/azure-stack-linux.md#azure-linux-agent)。 请确保运行 sysprep 的映像具有与 Azure Stack 中心兼容的 Azure Linux 代理版本。

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

   1. 你现在可以将此 VHD 移到 Azure Stack 中心。

> [!IMPORTANT]  
> 可在[将 VHD 上传到 Azure 并创建新的 VM 的示例脚本](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)一文中查找脚本，以将 VHD 上传到 Azure Stack Hub 用户存储帐户并创建 VM。 请确保提供 `$urlOfUploadedImageVhd` 作为 Azure Stack Hub 存储帐户和容器 URL。 对于通用 VHD，请确保在设置 `-CreateOption FromImage` 使用 `FromImage` 值。

---

## <a name="verify-your-vhd"></a>验证你的 VHD

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>上传到存储帐户

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-vm"></a>创建 VM

自定义映像分为两种形式： **专用** 化和 **一般化**。

### <a name="specialized"></a>[专用](#tab/create-vm-spec)

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

### <a name="generalized"></a>[通用](#tab/create-vm-gen)

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]
---
## <a name="next-steps"></a>后续步骤

[将 VM 移到 Azure Stack 集线器概述](vm-move-overview.md)
