---
title: 将自定义 VM 映像添加到 Azure Stack Hub
description: 了解如何在 Azure Stack Hub 中添加或删除自定义 VM 映像。
author: sethmanheim
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2019
ms.openlocfilehash: 7bd8a93c50038b7a593136df44e94b8a8f2f43d0
ms.sourcegitcommit: c9737939f4e437f1d954e163db972d58b3f98ffd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2020
ms.locfileid: "84813801"
---
# <a name="add-a-custom-vm-image-to-azure-stack-hub"></a>将自定义 VM 映像添加到 Azure Stack Hub

在 Azure Stack Hub 中，可将虚拟机 (VM) 自定义映像添加到市场供用户使用。 可以通过管理员门户或 Windows PowerShell 将 VM 映像添加到 Azure Stack Hub 市场。 使用 Azure Marketplace 中的映像作为自定义映像的基础，或使用 Hyper-v 创建自己的映像。

## <a name="step-1-create-the-custom-vm-image"></a>步骤 1：创建自定义 VM 映像

### <a name="windows"></a>Windows

创建自定义的通用化 VHD。

**如果 VHD 来自 Azure 外部**，请遵循[上传通用化 VHD 并使用它在 Azure 中创建新 VM](/azure/virtual-machines/windows/upload-generalized-managed) 中的步骤，正确地对 VHD 执行 **Sysprep** 并使其通用化。

**如果 VHD 来自 Azure**，请在通用化 VM 之前确保：

- 在 Azure 上预配 VM 时，使用 PowerShell 并在不使用 `-ProvisionVMAgent` 标志的情况下对其进行预配。
- 通用化 Azure 中的 VM 之前，先在该 VM 中使用 **Remove-AzureRmVMExtension** cmdlet 删除所有 VM 扩展。 可以转到 `Windows (C:) > WindowsAzure > Logs > Plugins` 查找已安装的 VM 扩展。

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
   ```

按照[此文](/azure/virtual-machines/windows/download-vhd)中的说明正确通用化并下载 VHD，然后将其移植到 Azure Stack Hub。

### <a name="linux"></a>Linux

如果 VHD 来自 Azure 外部，请根据相应的说明将 VHD 通用化：

- [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES 或 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

**如果 VHD 来自 Azure**，请按照以下说明通用化并下载 VHD：

1. 停止 **waagent** 服务：

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   [此处介绍](azure-stack-linux.md#azure-linux-agent)了与 Azure Stack 中心一起使用的 Azure Linux 代理版本。 确保已执行 Sysprep 的映像包含与 Azure Stack Hub 兼容的 Azure Linux 代理版本。

2. 停止/解除分配 VM。

3. 下载 VHD。

   1. 若要下载 VHD 文件，请生成共享访问签名（SAS） URL。 生成 URL 时，将为 URL 分配到期时间。

   2. 在 VM 的边栏选项卡菜单中选择“磁盘”。 

   3. 为 VM 选择操作系统磁盘，然后选择“磁盘导出”  。

   4. 将 URL 的过期时间设置为 36000。

   5. 选择“生成 URL”  。

   6. 生成 URL。

   7. 在生成的 URL 下，选择“下载 VHD 文件”  。

   8. 可能需要选择浏览器中的“保存”才能开始下载。  VHD 文件的默认名称为 **abcd**。

### <a name="considerations"></a>注意事项

在上传映像之前，必须考虑以下因素：

- Azure Stack 集线器仅支持采用固定磁盘 VHD 格式的第1代 Vm。 固定格式在文件中将逻辑磁盘以线性方式布局，使磁盘偏移量 **X** 存储在 Blob 偏移量 **X** 的位置。在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 若要确认磁盘是否为固定格式，请使用 **Get-VHD** PowerShell cmdlet。

- Azure Stack Hub 不支持动态磁盘 VHD。

## <a name="step-2-upload-the-vm-image-to-a-storage-account"></a>步骤2：将 VM 映像上传到存储帐户

1. [安装适用于 Azure Stack Hub 的 PowerShell](azure-stack-powershell-install.md)。  

2. 以操作员身份登录到 Azure Stack Hub。 有关说明，请参阅[以操作员身份登录到 Azure Stack Hub](azure-stack-powershell-configure-admin.md)。

3. 必须通过 blob 存储 URI 引用映像。 以 VHD（不是 VHDX）格式准备 Windows 或 Linux 操作系统映像，然后将映像上传到 Azure Stack Hub 中的存储帐户。

   - 如果 VHD 位于 Azure 中，且你在联网的 Azure Stack Hub 上运行，可以使用 [Azcopy](/azure/storage/common/storage-use-azcopy) 等工具直接在 Azure 与 Azure Stack Hub 存储帐户之间传输 VHD。

   - 在断开连接的 Azure Stack 集线器上，如果 VHD 位于 Azure 中，则必须将 VHD 下载到同时连接到 Azure 和 Azure Stack 中心的计算机。 然后，在使用可跨 Azure 和 Azure Stack 中心使用的任何通用[存储数据传输工具](../user/azure-stack-storage-transfer.md)将 vhd 传输到 Azure Stack 集线器之前，请从 AZURE 将 vhd 复制到此计算机。

     此示例中使用的一个此类工具是将 VHD 上传到 Azure Stack 中心管理员门户中的存储帐户的**add-azurermvhd** cmdlet。  

     ```powershell
     Add-AzureRmVhd -Destination "https://bash.blob.redmond.azurestack.com/sample/vhdtestingmgd.vhd" -LocalFilePath "C:\vhd\vhdtestingmgd.vhd"
     ```

4. 记下上传映像的 blob 存储 URI。 Blob 存储 URI 的格式为 `<storageAccount>/<blobContainer>/<targetVHDName>.vhd` 。

5. 若要使 Blob 可供匿名访问，请转到已在其中上传了 VM 映像 VHD 的存储帐户 Blob 容器。 选择“Blob”  ，然后选择“访问策略”  。 也可生成容器的共享访问签名，然后将其作为 Blob URI 的一部分包括进去。 此步骤使 blob 可供使用。 如果无法以匿名方式访问 blob，则会在 "失败" 状态下创建 VM 映像。

   ![转到存储帐户 Blob](./media/azure-stack-add-vm-image/tca1.png)

   ![将 Blob 访问权限设置为公共](./media/azure-stack-add-vm-image/tca2.png)

   ![将 Blob 访问权限设置为公共](./media/azure-stack-add-vm-image/tca3.png)

## <a name="step-3-option-1-add-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>步骤3，选项1：使用门户将 VM 映像添加为 Azure Stack 中心操作员

1. 以操作员身份登录到 Azure Stack 中心门户。 选择 "**所有服务**" **Compute** ，然后在 " **VM 映像**  >  **添加**" 下计算。

   ![自定义映像旁加载 UI](./media/azure-stack-add-vm-image/tca4.png)

2. 在 "**创建映像**" 下，输入**发布者**、**产品/服务**、 **SKU**、**版本**和 OS 磁盘 blob URI。 然后选择“创建”，开始创建 VM 映像。 

   ![自定义映像旁加载 UI](./media/azure-stack-add-vm-image/tca5.png)

   成功创建映像后，VM 映像状态会更改为“已成功”。 

3. 添加映像时，它仅适用于基于 Azure 资源管理器的模板和 PowerShell 部署。 若要将映像作为市场项提供给用户，请使用[创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)一文中的步骤发布市场项 请确保记下**发布者**、**产品/服务**、 **SKU**和**版本**值。 在自定义 .azpkg 中编辑资源管理器模板和 Manifest.json 时，需要用到这些值。

## <a name="step-3-option-2-add-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>步骤3：选项2：使用 PowerShell 将 VM 映像作为 Azure Stack 中心操作员添加

1. [安装适用于 Azure Stack Hub 的 PowerShell](azure-stack-powershell-install.md)。  

2. 以操作员身份登录到 Azure Stack Hub。 有关说明，请参阅[以操作员身份登录到 Azure Stack Hub](azure-stack-powershell-configure-admin.md)。

3. 使用权限提升的提示符打开 PowerShell，并运行：

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<Offer>" `
      -sku "<SKU>" `
      -version "<#.#.#>" `
      -OSType "<OS type>" `
      -OSUri "<OS URI>"
   ```

   **Add-AzsPlatformimage** cmdlet 指定 Azure 资源管理器模板用来引用 VM 映像的值。 这些值包括：
   - **publisher**  
     例如： `Canonical`  
     VM 映像的**发布者**名称段，供用户在部署映像时使用。 此字段不得包含空格或其他特殊字符。  
   - **offer**  
     例如： `UbuntuServer`  
     VM 映像的**套餐**名称段，供用户在部署 VM 映像时使用。 此字段不得包含空格或其他特殊字符。  
   - **sku**  
     例如： `14.04.3-LTS`  
     VM 映像的 **SKU** 名称段，供用户在部署 VM 映像时使用。 此字段不得包含空格或其他特殊字符。  
   - **version**  
     例如： `1.0.0`  
     VM 映像的版本，供用户在部署 VM 映像时使用。 此版本的格式为 **#. #. #**。 此字段不得包含空格或其他特殊字符。  
   - **osType**  
     例如： `Linux`  
     映像的 **osType** 必须为 **Windows** 或 **Linux**。  
   - **OSUri**  
     例如： `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     可以指定 `osDisk` 的 Blob 存储 URI。  

     有关详细信息，请参阅 [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet 的 PowerShell 参考。

4. 添加映像时，它仅适用于基于 Azure 资源管理器的模板和 PowerShell 部署。 若要将映像作为市场项提供给用户，请使用[创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)一文中的步骤发布市场项 请务必记下“发布者”、“套餐”、“SKU”和“版本”的值。     在自定义 .azpkg 中编辑资源管理器模板和 Manifest.json 时，需要用到这些值。

## <a name="remove-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>以 Azure Stack Hub 操作员的身份使用门户删除 VM 映像

1. 打开 Azure Stack 中心 [管理员门户] `https://adminportal.local.azurestack.external` 。

2. 如果 VM 映像有关联的市场项，请选择“市场管理”，然后选择要删除的 VM 市场项。 

3. 如果 VM 映像没有关联的市场项，请导航到“所有服务”>“计算”>“VM 映像”，然后选择 VM 映像旁边的省略号 ( **...** )。 

4. 选择“删除”  。

## <a name="remove-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>以 Azure Stack Hub 操作员的身份使用 PowerShell 删除 VM 映像

如果不再需要上传的 VM 映像，可以使用**AzsPlatformImage** cmdlet 将其从 Marketplace 中删除：

1. [安装适用于 Azure Stack Hub 的 PowerShell](azure-stack-powershell-install.md)。

2. 以操作员身份登录到 Azure Stack Hub。

3. 使用权限提升的提示符打开 PowerShell，并运行：

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<Publisher>" `
    -offer "<Offer>" `
    -sku "<SKU>" `
    -version "<Version>" `
   ```

   **Remove-AzsPlatformImage** cmdlet 指定 Azure 资源管理器模板用来引用 VM 映像的值。 这些值包括：
   - **publisher**  
     例如： `Canonical`  
     VM 映像的**发布者**名称段，供用户在部署映像时使用。 此字段不得包含空格或其他特殊字符。  
   - **offer**  
     例如： `UbuntuServer`  
     VM 映像的**套餐**名称段，供用户在部署 VM 映像时使用。 此字段不得包含空格或其他特殊字符。  
   - **sku**  
     例如： `14.04.3-LTS`  
     VM 映像的 **SKU** 名称段，供用户在部署 VM 映像时使用。 此字段不得包含空格或其他特殊字符。  
   - **version**  
     例如： `1.0.0`  
     用户在部署 VM 映像时使用的 VM 映像的版本。 此版本的格式为 **#. #. #**。 此字段不得包含空格或其他特殊字符。  

     有关**AzsPlatformImage** cmdlet 的详细信息，请参阅 Microsoft PowerShell [Azure Stack 中心操作员模块文档](/powershell/module/)。

## <a name="next-steps"></a>后续步骤

- [创建并发布自定义 Azure Stack Hub 市场项](azure-stack-create-and-publish-marketplace-item.md)
- [预配虚拟机](../user/azure-stack-create-vm-template.md)
