---
title: 将自定义 VM 映像添加到 Azure Stack 集线器
description: 了解如何在 Azure Stack 集线器中添加或删除自定义 VM 映像。
author: sethmanheim
ms.topic: conceptual
ms.date: 02/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2019
ms.openlocfilehash: 4d2f76e1af47800331aac44715b5b8630baceae1
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701422"
---
# <a name="add-a-custom-vm-image-to-azure-stack-hub"></a>将自定义 VM 映像添加到 Azure Stack 集线器

在 Azure Stack 中心，你可以将自定义虚拟机（VM）映像添加到 marketplace，并使其可供用户使用。 可以通过管理员门户或 Windows PowerShell 将 VM 映像添加到 Azure Stack 中心 Marketplace。 使用 Azure Marketplace 中的映像作为自定义映像的基础，或使用 Hyper-v 创建自己的映像。

## <a name="step-1-create-the-custom-vm-image"></a>步骤1：创建自定义 VM 映像

### <a name="windows"></a>Windows

创建自定义通用化 VHD。

**如果 vhd 来自 Azure 外部**，请按照[上传通用 VHD 并使用它在 azure 中创建新的 vm 中](/azure/virtual-machines/windows/upload-generalized-managed)的步骤，正确地**Sysprep** VHD 并使其通用化。

**如果 VHD 来自 Azure**，则在通用化 VM 之前，请确保以下各项：

- 在 Azure 上预配 VM 时，请使用 PowerShell 并在不使用 `-ProvisionVMAgent` 标志的情况下对其进行设置。
- 在 Azure 中通用化 VM 之前，请从 VM 中删除所有 VM 扩展，**并使用 set-azurermvmextension** cmdlet。 可以通过转到 `Windows (C:) > WindowsAzure > Logs > Plugins`来查找已安装的 VM 扩展。

```powershell
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

按照[本文](/azure/virtual-machines/windows/download-vhd)中的说明进行操作，在将 VHD 移植到 Azure Stack 集线器之前，对其进行正确通用化和下载。

### <a name="linux"></a>Linux

**如果 vhd 来自 Azure 外部**，请按照相应说明通用化 vhd：

- [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES 或 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

**如果 vhd 来自 Azure**，请按照以下说明通用化和下载 vhd：

1. 停止**waagent**服务：

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   请记住与 Azure Stack 中心一起使用的 Azure Linux 代理版本，[如此处所述](azure-stack-linux.md#azure-linux-agent)。 请确保经过系统准备映像具有与 Azure Stack 中心兼容的 Azure Linux 代理版本。

2. 停止解除分配 VM。

3. 下载 VHD。

   1. 若要下载 VHD 文件，需要生成共享访问签名（SAS） URL。 生成 URL 时，将为 URL 分配到期时间。

   1. 在 VM 的边栏选项卡的菜单上，选择 "**磁盘**"。

   1. 为 VM 选择操作系统磁盘，并选择 "**磁盘导出**"。

   1. 将 URL 的到期时间设置为36000。

   1. 选择 "**生成 URL**"。

   1. 生成 URL。

   1. 在生成的 URL 下，选择 **"下载 VHD 文件**"。

   1. 你可能需要在浏览器中选择 "**保存**" 以开始下载。 VHD 文件的默认名称为 _abcd_。

### <a name="considerations"></a>注意事项

在上传映像之前，请务必考虑以下事项：

- Azure Stack 集线器仅支持采用固定磁盘 VHD 格式的第1代 VM。 固定格式在文件中以线性方式构造逻辑磁盘，使磁盘偏移量*x*存储在 blob 偏移量*x*的位置。Blob 末尾的小页脚描述了 VHD 的属性。 若要确认是否已修复磁盘，请使用 "**获取 VHD** PowerShell cmdlet"。

- Azure Stack 中心不支持动态磁盘 Vhd。

## <a name="step-2-upload-the-vm-image-to-a-storage-account"></a>步骤2：将 VM 映像上传到存储帐户

1. [为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。  

2. 以操作员身份登录到 Azure Stack 中心。 有关说明，请参阅[以操作员身份登录 Azure Stack 中心](azure-stack-powershell-configure-admin.md)。

3. 映像必须能够通过 blob 存储 URI 进行引用。 以 VHD 格式（而非 VHDX）准备 Windows 或 Linux 操作系统映像，并将映像上传到 Azure Stack 集线器中的存储帐户。

   - 如果 VHD 位于 Azure 中，则可以使用[Azcopy](/azure/storage/common/storage-use-azcopy)等工具在连接的 Azure Stack 中心上运行时，直接在 azure 与 Azure Stack 中心存储帐户之间传输 VHD。

   - 在断开连接的 Azure Stack 集线器上，如果 VHD 位于 Azure 中，则需要将 VHD 下载到同时连接到 Azure 和 Azure Stack 中心的计算机。 然后，在使用可跨 Azure 和 Azure Stack 中心使用的任何通用[存储数据传输工具](../user/azure-stack-storage-transfer.md)将 vhd 传输到 Azure Stack 集线器之前，先从 AZURE 将 vhd 复制到此计算机。

     在此示例中使用的一个此类工具是将 VHD 上传到 Azure Stack 中心管理员门户中的存储帐户的 Add-azurermvhd 命令。  

     ```powershell
     Add-AzureRmVhd -Destination "https://bash.blob.redmond.azurestack.com/sample/vhdtestingmgd.vhd" -LocalFilePath "C:\vhd\vhdtestingmgd.vhd"
     ```

4. 记下要在其中上传映像的 blob 存储 URI。 Blob 存储 URI 采用以下格式： *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* .vhd。

5. 若要使 blob 可匿名访问，请访问已上传 VM 映像 VHD 的存储帐户 blob 容器。 选择 " **Blob**"，然后选择 "**访问策略**"。 （可选）可以为容器生成共享访问签名，并将其包含为 blob URI 的一部分。 此步骤可确保 blob 可供使用。 如果无法以匿名方式访问 blob，则会在 "失败" 状态下创建 VM 映像。

   ![中转到存储帐户 blob](./media/azure-stack-add-vm-image/tca1.png)

   ![将 blob 访问权限设置为公共](./media/azure-stack-add-vm-image/tca2.png)

   ![将 blob 访问权限设置为公共](./media/azure-stack-add-vm-image/tca3.png)

## <a name="step-3-option-1-add-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>步骤3，选项1：使用门户将 VM 映像添加为 Azure Stack 中心操作员

1. 以操作员身份登录到 Azure Stack 集线器。 在菜单中，选择 "**所有服务**"，在 " **VM 映像**" 下 > **计算**" > **添加**"。

   ![自定义映像旁加载 UI](./media/azure-stack-add-vm-image/tca4.png)

2. 在 "**创建映像**" 下，输入发布者、产品/服务、SKU、版本和 OS 磁盘 blob URI。 然后，选择 "**创建**" 以开始创建 VM 映像。

   ![自定义映像旁加载 UI](./media/azure-stack-add-vm-image/tca5.png)

   成功创建映像后，VM 映像状态将更改为 "**成功**"。

3. 添加映像时，它仅适用于基于 Azure 资源管理器的模板和 PowerShell 部署。 若要将映像作为 marketplace 项提供给用户，请使用[创建和发布 marketplace 项一](azure-stack-create-and-publish-marketplace-item.md)文中的步骤来发布 marketplace 项。 请确保记下**发布者**、**产品/服务**、 **SKU**和**版本**值。 在 .azpkg 中编辑资源管理器模板和时，需要用到它们。

## <a name="step-3-option-2-add-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>步骤3：选项2：使用 PowerShell 将 VM 映像作为 Azure Stack 中心操作员添加

1. [为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。  

2. 以操作员身份登录到 Azure Stack 中心。 有关说明，请参阅[以操作员身份登录 Azure Stack 中心](azure-stack-powershell-configure-admin.md)。

3. 使用权限提升的提示符打开 PowerShell，并运行：

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<Offer>" `
      -sku "<SKU>" `
      -version "<#.#.#>" `
      -OSType "<OS type>" `
      -OSUri "<OS URI>"
   ```

   **AzsPlatformimage** Cmdlet 指定 Azure 资源管理器模板用于引用 VM 映像的值。 值包括：
   - **发布服务器**  
     例如： `Canonical`  
     用户在部署映像时使用的 VM 映像的**发布者**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **提供**  
     例如： `UbuntuServer`  
     用户在部署 VM 映像时使用的 VM 映像的**产品/服务**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **sku**  
     例如： `14.04.3-LTS`  
     用户在部署 VM 映像时使用的 VM 映像的**SKU**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **version**  
     例如： `1.0.0`  
     用户在部署 VM 映像时使用的 VM 映像的版本。 此版本的格式为 *\#.\#。\#* 。 不要在此字段中包含空格或其他特殊字符。  
   - **osType**  
     例如： `Linux`  
     映像的**osType**必须为**Windows**或**Linux**。  
   - **OSUri**  
     例如： `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     可以为 `osDisk`指定 blob 存储 URI。  

     有关详细信息，请参阅[AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) Cmdlet 的 PowerShell 参考。

4. 添加映像时，它仅适用于基于 Azure 资源管理器的模板和 PowerShell 部署。 若要将映像作为 marketplace 项提供给用户，请使用[创建和发布 marketplace 项一](azure-stack-create-and-publish-marketplace-item.md)文中的步骤来发布 marketplace 项。 请确保记下**发布者**、**产品/服务**、 **SKU**和**版本**值。 在自定义的 .azpkg 中编辑 resource manager 模板和 Manifest 时，需要用到它们。

## <a name="remove-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>使用门户将 VM 映像作为 Azure Stack 中心操作员删除

1. 打开 Azure Stack 中心[管理员门户](https://adminportal.local.azurestack.external)。

2. 如果 VM 映像具有关联的 Marketplace 项，请选择 " **marketplace 管理**"，然后选择要删除的 vm Marketplace 项。

3. 如果 VM 映像没有关联的 Marketplace 项，请导航到 "**所有服务" > 计算 vm 映像 >** ，然后选择 vm 映像旁边的省略号（ **...** ）。

4. 选择“删除”。

## <a name="remove-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>使用 PowerShell 将 VM 映像作为 Azure Stack 中心操作员删除

如果不再需要上传的 VM 映像，可以使用以下 cmdlet 从 Marketplace 中删除它：

1. [为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。

2. 以操作员身份登录到 Azure Stack 中心。

3. 使用权限提升的提示符打开 PowerShell，并运行：

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<Publisher>" `
    -offer "<Offer>" `
    -sku "<SKU>" `
    -version "<Version>" `
   ```

   **AzsPlatformImage** Cmdlet 指定 Azure 资源管理器模板用于引用 VM 映像的值。 值包括：
   - **发布服务器**  
     例如： `Canonical`  
     用户在部署映像时使用的 VM 映像的**发布者**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **提供**  
     例如： `UbuntuServer`  
     用户在部署 VM 映像时使用的 VM 映像的**产品/服务**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **sku**  
     例如： `14.04.3-LTS`  
     用户在部署 VM 映像时使用的 VM 映像的**SKU**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **version**  
     例如： `1.0.0`  
     用户在部署 VM 映像时使用的 VM 映像的版本。 此版本的格式为 *\#.\#。\#* 。 不要在此字段中包含空格或其他特殊字符。  

     有关**AzsPlatformImage** cmdlet 的详细信息，请参阅 Microsoft PowerShell [Azure Stack 中心操作员模块文档](/powershell/module/)。

## <a name="next-steps"></a>后续步骤

- [创建和发布自定义 Azure Stack 中心市场项](azure-stack-create-and-publish-marketplace-item.md)
- [预配虚拟机](../user/azure-stack-create-vm-template.md)
