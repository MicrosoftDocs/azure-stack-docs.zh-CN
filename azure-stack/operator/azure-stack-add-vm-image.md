---
title: 向 Azure Stack 添加 VM 映像 | Microsoft Docs
description: 了解如何在 Azure Stack 中添加或删除 VM 映像。
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
ms.date: 09/17/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: fef815ec23655638bbe4df1bcdccae42aeee13e2
ms.sourcegitcommit: 9f4c6e96f60b4c229316e7a4ab6e0e5ef0a9a232
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71061163"
---
# <a name="add-a-vm-image-to-azure-stack"></a>将 VM 映像添加到 Azure Stack

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

在 Azure Stack 中，你可以将虚拟机（VM）映像添加到 marketplace，并使其可供用户使用。 使用用于 Azure Stack 的 Azure 资源管理器模板添加映像。 你还可以使用管理员门户或 Windows PowerShell 将 VM 映像添加到 Azure Marketplace UI，作为 Marketplace 项。 可以使用来自全球 Azure 市场的映像，也可以使用你自己的自定义 VM 映像。

## <a name="add-a-vm-image-through-the-portal"></a>通过门户添加 VM 映像

> [!NOTE]  
> 使用此方法时，必须单独创建市场项。

映像必须能够通过 Blob 存储 URI 进行引用。 以 VHD（不是 VHDX）格式准备 Windows 或 Linux 操作系统映像，然后将映像上传到 Azure 或 Azure Stack 中的存储帐户。 如果映像已上传到 Azure 或 Azure Stack 中的 Blob 存储，则可跳过步骤 1。

1. 将[WINDOWS VM 映像上传到 Azure 资源管理器部署](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)，或者在 Linux 映像中，按照在[Azure Stack 上部署 Linux vm](azure-stack-linux.md)中所述的说明进行操作。 在上传映像之前，必须考虑以下因素：

   - Azure Stack 仅支持采用固定磁盘 VHD 格式的第一代 VM。 固定格式在文件内对逻辑磁盘采用线性结构，使磁盘偏移量 X 存储在 Blob 偏移量 X 的位置。在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 若要确认磁盘是否为固定格式，请使用 [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell 命令。  

     > [!IMPORTANT]  
     >  Azure Stack 不支持动态磁盘 Vhd。 调整附加到 VM 的动态磁盘大小会使 VM 处于失败状态。 若要解决此问题，请删除 VM，但不删除 VM 的磁盘（存储帐户中的 VHD Blob）。 然后，将 VHD 从动态磁盘转换为固定磁盘并重新创建 VM。

   - 将映像上传到 Azure Stack Blob 存储比上传到 Azure Blob 存储更高效，因为将映像推送到 Azure Stack 映像存储库的时间更短。

   - 当你上传[WINDOWS VM 映像](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)时，请确保将**登录名切换到 Azure**步骤，并[配置 Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)步骤。  

   - 记下在其中上传映像的 Blob 存储 URI。 Blob 存储 URI 的格式如下： *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* .vhd。

   - 若要使 Blob 可供匿名访问，请转到已在其中上传了 VM 映像 VHD 的存储帐户 Blob 容器。 选择“Blob”，然后选择“访问策略”。 也可生成容器的共享访问签名，然后将其作为 Blob URI 的一部分包括进去。 此步骤可确保 blob 可供使用。 如果无法以匿名方式访问 blob，则会在 "失败" 状态下创建 VM 映像。

     ![转到存储帐户 Blob](./media/azure-stack-add-vm-image/image1.png)

     ![将 Blob 访问权限设置为公共](./media/azure-stack-add-vm-image/image2.png)

2. 以操作员身份登录到 Azure Stack。 在菜单中的**计算** > “添加”下，选择“所有服务” > “映像”。

3. 在“创建映像”下，输入名称、订阅、资源组、位置、OS 磁盘、OS 类型、存储 Blob URI、帐户类型和主机缓存。 然后，选择 "**创建**" 以开始创建 VM 映像。

   ![开始创建映像](./media/azure-stack-add-vm-image/image4.png)

   成功创建映像后，VM 映像状态会更改为“已成功”。

4. 若要使 VM 映像更易于在用户界面中使用，最好[创建 Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)。

## <a name="remove-a-vm-image-through-the-portal"></a>通过门户删除 VM 映像

1. 在上[https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)打开管理员门户。

2. 选择“市场管理”，然后选择要删除的 VM。

3. 单击“删除”。

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>使用 PowerShell 将 VM 映像添加到市场

> [!Note]  
> 添加映像时，它将仅适用于基于 Azure 资源管理器的模板和 PowerShell 部署。 若要将映像作为 marketplace 项提供给用户，请使用本文中的步骤发布 marketplace 项：[创建和发布市场项目](azure-stack-create-and-publish-marketplace-item.md)

1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。  

2. 以操作员身份登录到 Azure Stack。 有关说明，请参阅[以操作员身份登录到 Azure Stack](azure-stack-powershell-configure-admin.md)。

3. 使用权限提升的提示符打开 PowerShell，并运行：

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>" `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   **Add-AzsPlatformimage** cmdlet 指定 Azure 资源管理器模板用来引用 VM 映像的值。 这些值包括：
   - **publisher**  
     例如： `Canonical`  
     VM 映像的发布者名称段，供用户在部署映像时使用。 例如，**Microsoft**。 不要在此字段中包含空格或其他特殊字符。  
   - **offer**  
     例如： `UbuntuServer`  
     VM 映像的产品/服务名称段，供用户在部署 VM 映像时使用。 例如，**WindowsServer**。 不要在此字段中包含空格或其他特殊字符。  
   - **sku**  
     例如： `14.04.3-LTS`  
     VM 映像的 SKU 名称段，供用户在部署 VM 映像时使用。 例如，**Datacenter2016**。 不要在此字段中包含空格或其他特殊字符。  
   - **version**  
     例如： `1.0.0`  
     VM 映像的版本，供用户在部署 VM 映像时使用。 此版本采用 *\#.\#.\#* 格式。 例如，**1.0.0**。 不要在此字段中包含空格或其他特殊字符。  
   - **osType**  
     例如： `Linux`  
     映像的 osType 必须为 **Windows** 或 **Linux**。  
   - **OSUri**  
     例如： `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     可以指定 `osDisk` 的 Blob 存储 URI。  

     有关详细信息，请参阅[AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) Cmdlet 和[DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet 的 PowerShell 参考。

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>使用 PowerShell 将自定义 VM 映像添加到市场
 
1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

   ```powershell
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

2. 如果使用**Active Directory 联合身份验证服务（AD FS）** ，请使用以下 cmdlet：

   ```powershell
   # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAudience endpoint for your environment>"

   # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external
    ```

3. 以操作员身份登录到 Azure Stack。 有关说明，请参阅[以操作员身份登录到 Azure Stack](azure-stack-powershell-configure-admin.md)。

4. 在全球 Azure 或 Azure Stack 中创建一个存储帐户来存储自定义 VM 映像。 有关说明，请参阅[快速入门：使用 Azure 门户上传、下载和列出 Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

5. 以 VHD 格式（而非 VHDX）准备一个 Windows 或 Linux 操作系统映像，将该映像上传到存储帐户，并获取可通过 PowerShell 从其中检索 VM 映像的 URI。  

   ```powershell
   Add-AzureRmAccount 
   -EnvironmentName "AzureStackAdmin" 
   -TenantId $TenantID
   ```
  
   >[!Note]
   > 如果会话过期，你的密码已更改，或者你只是想要切换帐户，请在使用 Add-azurermaccount 登录之前运行以下 cmdlet：`Remove-AzureRmAccount-Scope Process`

6. （可选）可以将数据磁盘阵列上传为 VM 映像的一部分。 使用 New-DataDiskObject cmdlet 创建数据磁盘。 通过权限提升的提示符打开 PowerShell，并运行：

   ```powershell
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. 使用权限提升的提示符打开 PowerShell，并运行：

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>" -OSType "<ostype>" -OSUri "<osuri>"
   ```

    有关 AzsPlatformimage cmdlet 和 DataDiskObject cmdlet 的详细信息，请参阅 Microsoft PowerShell [Azure Stack Operator 模块文档](https://docs.microsoft.com/powershell/module/)。

## <a name="remove-a-vm-image-by-using-powershell"></a>使用 PowerShell 删除 VM 映像

如果不再需要上传的 VM 映像，可以使用以下 cmdlet 从 Marketplace 中删除它：

1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

2. 以操作员身份登录到 Azure Stack。

3. 使用权限提升的提示符打开 PowerShell，并运行：

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   **Remove-AzsPlatformImage** cmdlet 指定 Azure 资源管理器模板用来引用 VM 映像的值。 这些值包括：
   - **publisher**  
     例如： `Canonical`  
     VM 映像的发布者名称段，供用户在部署映像时使用。 例如，**Microsoft**。 不要在此字段中包含空格或其他特殊字符。  
   - **offer**  
     例如： `UbuntuServer`  
     VM 映像的产品/服务名称段，供用户在部署 VM 映像时使用。 例如，**WindowsServer**。 不要在此字段中包含空格或其他特殊字符。  
   - **sku**  
     例如： `14.04.3-LTS`  
     VM 映像的 SKU 名称段，供用户在部署 VM 映像时使用。 例如，**Datacenter2016**。 不要在此字段中包含空格或其他特殊字符。  
   - **version**  
     例如： `1.0.0`  
     VM 映像的版本，供用户在部署 VM 映像时使用。 此版本采用 *\#.\#.\#* 格式。 例如，**1.0.0**。 不要在此字段中包含空格或其他特殊字符。  
    
     有关 AzsPlatformImage cmdlet 的详细信息，请参阅 Microsoft PowerShell [Azure Stack Operator 模块文档](https://docs.microsoft.com/powershell/module/)。

## <a name="next-steps"></a>后续步骤

[预配虚拟机](../user/azure-stack-create-vm-template.md)
