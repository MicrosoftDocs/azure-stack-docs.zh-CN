---
title: 将自定义 VM 映像添加到 Azure Stack |Microsoft Docs
description: 了解如何在 Azure Stack 中添加或删除自定义 VM 映像。
services: azure-stack
documentationcenter: ''
author: Justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 9dc5039a2c8b74b14da59573758a4cf8d1a3657a
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282659"
---
# <a name="add-a-custom-vm-to-azure-stack"></a>将自定义 VM 添加到 Azure Stack

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

在 Azure Stack 中，你可以将自定义虚拟机（VM）映像添加到 marketplace，并使其可供用户使用。 使用用于 Azure Stack 的 Azure 资源管理器模板添加映像。 你还可以使用管理员门户或 Windows PowerShell 将 VM 映像添加到 Azure Marketplace UI，作为 Marketplace 项。 使用来自全局 Azure Marketplace 的映像，或使用自己的自定义 VM 映像。

## <a name="generalize-the-vm-image"></a>通用化 VM 映像

### <a name="windows"></a>Windows

创建自定义通用化 VHD。 如果 VHD 来自 Azure 外部，请按照[上传通用 VHD 并使用它在 azure 中创建新的 vm 中](/azure/virtual-machines/windows/upload-generalized-managed)的步骤，正确地**Sysprep** VHD 并使其通用化。

如果 VHD 来自 Azure，请在将源 VM 的虚拟机迁移到 Azure Stack 之前，按照[使用 Sysprep 通用化此 VM](/azure/virtual-machines/windows/upload-generalized-managed#generalize-the-source-vm-by-using-sysprep)中的说明进行操作。

### <a name="linux"></a>Linux

如果 VHD 来自 Azure 外部，请按照相应说明通用化 VHD：

- [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES 或 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

如果 VHD 来自 Azure，请按照以下说明通用化 VHD：

1. 停止**waagent**服务：

   ```bash
   # sudo waagent -force -deprovision
   # export HISTSIZE=0
   # logout
   ```

2. 关闭 VM 并下载 VHD。 如果要从 Azure 中引入 VHD，可以使用磁盘导出来完成此操作，如[从 Azure 下载 WINDOWS VHD](/azure/virtual-machines/windows/download-vhd)中所示。

### <a name="common-steps-for-both-windows-and-linux"></a>适用于 Windows 和 Linux 的常见步骤

在上传映像之前，请务必考虑以下事项：

- Azure Stack 仅支持以固定磁盘 VHD 格式生成一（1）个 VM。 固定格式在文件中以线性方式构造逻辑磁盘，使磁盘偏移量*x*存储在 blob 偏移量*x*的位置。Blob 末尾的小页脚描述了 VHD 的属性。 若要确认是否已修复磁盘，请使用 "**获取 VHD** PowerShell cmdlet"。

- Azure Stack 不支持动态磁盘 VHD。 调整附加到 VM 的动态磁盘大小会使 VM 处于失败状态。 若要解决此问题，请删除 VM，但不删除 VM 的磁盘（存储帐户中的 VHD Blob）。 然后，将 VHD 从动态磁盘转换为固定磁盘并重新创建 VM。

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-the-portal"></a>使用门户将 VM 映像添加为 Azure Stack 运算符

1. 映像必须能够通过 Blob 存储 URI 进行引用。 以 VHD（不是 VHDX）格式准备 Windows 或 Linux 操作系统映像，然后将映像上传到 Azure 或 Azure Stack 中的存储帐户。

   - 如果 VHD 位于 Azure 中，则可以使用[Azcopy](/azure/storage/common/storage-use-azcopy)等工具在 azure 与 Azure Stack 存储帐户之间直接传输 vhd （如果在连接的 Azure Stack 上运行）。

   - 在断开连接的 Azure Stack 上，如果 VHD 位于 Azure 中，则需要将 VHD 下载到同时连接到 Azure 和 Azure Stack 的计算机。 然后，在使用可跨 Azure 和 Azure Stack 使用的任何通用[存储数据传输工具](../user/azure-stack-storage-transfer.md)将 vhd 传输到 Azure Stack 之前，先从 AZURE 将 vhd 复制到此计算机。

2. 可以按照[此示例](/powershell/module/azurerm.compute/add-azurermvhd?view=azurermps-6.13.0)将 VHD 上传到 Azure Stack 管理员门户中的存储帐户。

   - 将映像上传到 Azure Stack blob 存储比使用 Azure blob 存储更高效，因为将映像推送到 Azure Stack 映像存储库需要更少的时间。

   - 当你上传[WINDOWS VM 映像](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)时，请确保将**登录名切换到 Azure**步骤，并[配置 Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)步骤。  

3. 记下在其中上传映像的 Blob 存储 URI。 Blob 存储 URI 的格式如下： *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* .vhd。

4. 若要使 Blob 可供匿名访问，请转到已在其中上传了 VM 映像 VHD 的存储帐户 Blob 容器。 选择“Blob”，然后选择“访问策略”。 也可生成容器的共享访问签名，然后将其作为 Blob URI 的一部分包括进去。 此步骤可确保 blob 可供使用。 如果无法以匿名方式访问 blob，则会在 "失败" 状态下创建 VM 映像。

   ![转到存储帐户 Blob](./media/azure-stack-add-vm-image/image1.png)

   ![将 Blob 访问权限设置为公共](./media/azure-stack-add-vm-image/image2.png)

5. 以操作员身份登录到 Azure Stack。 在菜单中的**计算** > “添加”下，选择“所有服务” > “映像”。

6. 在“创建映像”下，输入名称、订阅、资源组、位置、OS 磁盘、OS 类型、存储 Blob URI、帐户类型和主机缓存。 然后，选择 "**创建**" 以开始创建 VM 映像。

   ![开始创建映像](./media/azure-stack-add-vm-image/image4.png)

   成功创建映像后，VM 映像状态会更改为“已成功”。

7. 添加映像时，它仅适用于基于 Azure 资源管理器的模板和 PowerShell 部署。 若要将映像作为 marketplace 项提供给用户，请使用[创建和发布 marketplace 项一](azure-stack-create-and-publish-marketplace-item.md)文中的步骤来发布 marketplace 项。 请确保记下**发布者**、**产品/服务**、 **SKU**和**版本**值。 在自定义的 .azpkg 中编辑 resource manager 模板和 Manifest 时，需要用到它们。

## <a name="remove-the-vm-image-as-an-azure-stack-operator-using-the-portal"></a>使用门户删除作为 Azure Stack 运算符的 VM 映像

1. 打开 Azure Stack[管理员门户](https://adminportal.local.azurestack.external)。

2. 如果 VM 映像具有关联的 Marketplace 项，请选择 " **marketplace 管理**"，然后选择要删除的 vm Marketplace 项。

3. 如果 VM 映像没有关联的 Marketplace 项，请导航到 "**所有服务" > 计算 vm 映像 >** ，然后选择 vm 映像旁边的省略号（ **...** ）。

4. 选择“删除”。

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>使用 PowerShell 将 VM 映像添加为 Azure Stack 运算符

1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。  

2. 以操作员身份登录到 Azure Stack。 有关说明，请参阅[以操作员身份登录到 Azure Stack](azure-stack-powershell-configure-admin.md)。

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
     用户在部署映像时使用的 VM 映像的**发布者**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **offer**  
     例如： `UbuntuServer`  
     用户在部署 VM 映像时使用的 VM 映像的**产品/服务**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **sku**  
     例如： `14.04.3-LTS`  
     用户在部署 VM 映像时使用的 VM 映像的**SKU**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **version**  
     例如： `1.0.0`  
     VM 映像的版本，供用户在部署 VM 映像时使用。 此版本采用以下格式 *\#。\#。\#* 不要在此字段中包含空格或其他特殊字符。  
   - **osType**  
     例如： `Linux`  
     映像的**osType**必须为**Windows**或**Linux**。  
   - **OSUri**  
     例如： `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     可以指定 `osDisk` 的 Blob 存储 URI。  

     有关详细信息，请参阅[AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage)和[DataDiskObject](/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet 的 PowerShell 参考。

## <a name="remove-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>使用 PowerShell 删除 VM 映像作为 Azure Stack 运算符

如果不再需要上传的 VM 映像，可以使用以下 cmdlet 从 Marketplace 中删除它：

1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

2. 以操作员身份登录到 Azure Stack。

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
     用户在部署映像时使用的 VM 映像的**发布者**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **offer**  
     例如： `UbuntuServer`  
     用户在部署 VM 映像时使用的 VM 映像的**产品/服务**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **sku**  
     例如： `14.04.3-LTS`  
     用户在部署 VM 映像时使用的 VM 映像的**SKU**名称段。 不要在此字段中包含空格或其他特殊字符。  
   - **version**  
     例如： `1.0.0`  
     VM 映像的版本，供用户在部署 VM 映像时使用。 此版本采用以下格式 *\#。\#。\#* 不要在此字段中包含空格或其他特殊字符。  

     有关**AzsPlatformImage** cmdlet 的详细信息，请参阅 Microsoft PowerShell [Azure Stack Operator 模块文档](/powershell/module/)。

## <a name="next-steps"></a>后续步骤

- [创建和发布自定义 Azure Stack Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)
- [预配虚拟机](../user/azure-stack-create-vm-template.md)
