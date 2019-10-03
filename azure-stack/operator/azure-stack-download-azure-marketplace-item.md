---
title: 从 Azure 下载 marketplace 项，并发布到 Azure Stack |Microsoft Docs
description: 了解如何从 Azure 下载 marketplace 项，并发布到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: justinha
ms.reviewer: ihcherie
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: f4ffb166d4ae45ca8d7ef335d81e51e2775eb985
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829135"
---
# <a name="download-marketplace-items-from-azure-and-publish-to-azure-stack"></a>从 Azure 下载 marketplace 项，并发布到 Azure Stack

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

作为云操作员，你可以从 Azure Marketplace 下载项目并使其在 Azure Stack 中可用。 可以选择的项来自 Azure 市场项的有序列表，这些项已预先经过测试，支持与 Azure Stack 配合使用。 其他项会不断地添加到此列表中，因此请不时地返回查看新内容。

可以通过两种方案连接到 Azure Marketplace：

- **联网场景** - 需将 Azure Stack 环境连接到 Internet。 使用 Azure Stack 门户查找和下载项。
- **断开连接或部分连接的方案**-要求使用 marketplace 联合工具访问 internet，以便下载 marketplace 项。 然后，将下载内容传输到离线 Azure Stack 安装中。 此场景使用 PowerShell。

有关可下载的市场项的完整列表，请参阅 [Azure Stack 的 Azure 市场项](azure-stack-marketplace-azure-items.md)。 有关 Azure Stack Marketplace 的最新添加、删除和更新列表，请参阅[Azure Stack marketplace 更改](azure-stack-marketplace-changes.md)一文。

## <a name="connected-scenario"></a>已连接的场景

如果 Azure Stack 连接到 Internet，则可以使用管理员门户下载市场项。

### <a name="prerequisites"></a>先决条件

你的 Azure Stack 部署必须具有 internet 连接并[已注册到 Azure](azure-stack-registration.md)。

### <a name="use-the-portal-to-download-marketplace-items"></a>使用门户下载市场项
  
1. 登录到 Azure Stack 管理员门户。

2. 下载市场项之前，查看可用的存储空间。 稍后在选择要下载的项时，可将下载大小与可用存储容量进行比较。 如果容量有限，请考虑使用[管理可用空间](azure-stack-manage-storage-shares.md#manage-available-space)的选项。

    查看可用空间：在 "**区域管理**" 中，选择要浏览的区域，然后访问 "**资源提供程序**"  > **存储**：

    ![查看 Azure Stack 管理员门户中的存储空间](media/azure-stack-download-azure-marketplace-item/storage.png)

3. 打开 Azure Stack Marketplace 并连接到 Azure。 为此，请依次选择“市场管理”服务、“市场项”和“从 Azure 中添加”：

    ![从 Azure 添加 marketplace 项](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    门户会显示可从 Azure Marketplace 下载的项目列表。 您可以按名称、发布者和/或产品类型来筛选产品。 你还可以选择每个项目以查看其描述和其他信息，包括其下载大小：

    ![Azure Marketplace 项列表 ](media/azure-stack-download-azure-marketplace-item/image03.PNG)

4. 选择所需的项，然后选择“下载”。 下载时间会有差异。

    ![下载 Azure Marketplace 项](media/azure-stack-download-azure-marketplace-item/image04.png)

    下载完成后，可以将新的 marketplace 项作为 Azure Stack 操作员或用户部署。

5. 若要部署下载的项，请选择“+ 创建资源”，在类别中搜索该新市场项。 接下来，选择要开始部署过程的项。 该过程根据市场项的不同而异。

## <a name="disconnected-or-a-partially-connected-scenario"></a>离线场景或部分联网场景

如果 Azure Stack 处于断开连接模式下，则使用 PowerShell 和*marketplace 联合工具*将 marketplace 项下载到具有 internet 连接的计算机。 然后，将这些项传输到 Azure Stack 环境。 在断开连接的环境中，你无法使用 Azure Stack 门户下载 marketplace 项。

也可以在联网场景中使用市场联合工具。

此方案包含两个部分：

- **第 1 部分：** 从 Azure 市场下载。 在具有 internet 访问权限的计算机上配置 PowerShell，下载联合工具，然后从 Azure Marketplace 下载项。  
- **第 2 部分：** 上载并发布到 Azure Stack Marketplace。 将下载的文件移动到 Azure Stack 环境，将其导入到 Azure Stack，然后将其发布到 Azure Stack Marketplace。  

### <a name="prerequisites"></a>先决条件

- Azure Stack 部署必须[已注册到 Azure](azure-stack-registration.md)。

- 已建立 Internet 连接的计算机上必须已安装 **Azure Stack PowerShell 模块版本 1.2.11** 或更高版本。 如果未安装，请[安装 Azure Stack 特定的 PowerShell 模块](azure-stack-powershell-install.md)。  

- 若要启用已下载市场项的导入，必须配置 [Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)。  

- 必须在 Azure Stack 中拥有一个包含可公开访问的容器（存储 Blob）的[存储帐户](azure-stack-manage-storage-accounts.md)。 该容器将用作市场项库文件的临时存储。 如果你不熟悉存储帐户和容器，请参阅 Azure 文档中[的使用 blob-Azure 门户](/azure/storage/blobs/storage-quickstart-blobs-portal)。

- 在执行第一个过程期间，将下载市场联合工具。

- 可以安装[AzCopy](/azure/storage/common/storage-use-azcopy)以获得最佳下载性能，但这不是必需的。

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>使用市场联合工具下载市场项

1. 在具有 internet 连接的计算机上，以管理员身份打开 PowerShell 控制台。

2. 添加用于注册 Azure Stack 的 Azure 帐户。 若要添加帐户，请在 PowerShell 中不结合任何参数运行 `Add-AzureRmAccount`。 系统会提示输入 Azure 帐户凭据，并且可能需要使用双因素身份验证，具体取决于帐户的配置。

   [!include[Remove Account](../../includes/remove-account.md)]

3. 如果有多个订阅，请运行以下命令来选择注册所用的订阅：  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 使用以下脚本下载最新版本的市场联合工具：  

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. 通过运行以下命令导入联合模块并启动工具。 将 `Destination folder path` 替换为存储从 Azure Marketplace 下载的文件的位置。

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
   ```

   请注意，`Export-AzSOfflineMarketplaceItem` 有一个额外的 `-cloud` 标志，用于指定云环境。 默认情况下，它是**azurecloud**。

6. 运行工具后，应会看到下图所示的屏幕，其中列出了可用的市场项：

   [![Azure 市场项弹出窗口](media/azure-stack-download-azure-marketplace-item/image05.png "Azure 市场项")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. 选择要下载的项，并记下版本。 可以按住 **Ctrl** 键选择多个映像。 在下一过程中导入项时，将要引用该版本。

   也可通过“添加条件”选项来筛选映像的列表。

8. 选择“确定”，然后查看并接受法律条款。

9. 下载时间取决于项的大小。 下载完成后，该项会出现在脚本中指定的文件夹内。 下载内容中包括一个 VHD 文件（适用于虚拟机）或 .zip 文件（适用于虚拟机扩展）。 其中还可能包含一个 *.azpkg* 格式的库包（只是一个 .zip 文件）。

10. 如果下载失败，你可以通过重新运行以下 PowerShell cmdlet 来重试：

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    重试之前，请删除发生下载失败的产品文件夹。 例如，如果下载脚本在下载到 `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` 时失败，请删除 `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` 文件夹，然后重新运行该 cmdlet。

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>导入下载内容并发布到 Azure Stack 市场（1811 和更高版本）

1. 您必须将[以前下载](#use-the-marketplace-syndication-tool-to-download-marketplace-items)的文件移动到您的 Azure Stack 环境中。 Marketplace 联合工具还必须可用于你的 Azure Stack 环境，因为你需要使用该工具来执行导入操作。

   下图显示了文件夹结构示例。 `D:\downloadfolder` 包含所有已下载的市场项。 每个子文件夹都是由产品 ID 命名的 marketplace 项（例如 `microsoft.custom-script-linux-arm-2.0.3`）。 每个子文件夹内都是 marketplace 项的下载内容。

   [![市场下载目录结构](media/azure-stack-download-azure-marketplace-item/mp1sm.png "市场下载目录结构")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. 遵照[此文](azure-stack-powershell-configure-admin.md)中的说明配置 Azure Stack 操作员 PowerShell 会话。

3. 导入联合模块，然后运行以下脚本来启动市场联合工具：

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   @No__t 参数指定包含所有已下载产品的顶层文件夹;例如，`"D:\downloadfolder"`。

   `-AzsCredential` 参数是可选的。 如果该令牌已过期，则使用它来续订访问令牌。 如果未指定 `-AzsCredential` 参数并且令牌过期，则会提示输入操作员凭据。

    > [!NOTE]  
    > AD FS 仅支持通过用户标识进行交互式身份验证。 如果需要凭据对象，则必须使用服务主体 (SPN)。 若要详细了解如何在设置服务主体时将 Azure Stack 和 AD FS 作为标识管理服务，请参阅[管理 AD FS 服务主体](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal)。

4. 脚本成功完成后，Azure Stack Marketplace 中应提供此项。

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>导入下载内容并发布到 Azure Stack 市场（1809 和更低版本）

1. 你[之前下载](#use-the-marketplace-syndication-tool-to-download-marketplace-items)的虚拟机映像或解决方案模板的文件必须在本地可供 Azure Stack 环境使用。  

2. 使用管理员门户将 marketplace 项包（.azpkg 文件）和虚拟硬盘映像（.vhd 文件）上传到 Azure Stack Blob 存储。 上传该包和磁盘文件会使其可供 Azure Stack 使用，以便稍后能够将该项发布到 Azure Stack 市场。

   需要有一个包含可公开访问的容器的存储帐户才能完成上传（请参阅此场景的先决条件）。  
   1. 在 Azure Stack 管理员门户中，依次单击 "**所有服务**"、"**数据 + 存储**" 类别下的 "**存储帐户**"。  

   2. 从订阅中选择一个存储帐户，然后在“BLOB 服务”下选择“容器”。  
      [![选择存储帐户和 blob 服务](media/azure-stack-download-azure-marketplace-item/blob-service.png "blob 服务")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  

   3. 选择要使用的容器，然后选择“上传”打开“上传 Blob”窗格。  
      [![选择容器并上传 blob](media/azure-stack-download-azure-marketplace-item/container.png "容器")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  

   4. 在“上传 Blob”窗格中，浏览到要加载到存储中的包和磁盘文件，然后选择“上传”：[![上传 blob 窗格](media/azure-stack-download-azure-marketplace-item/uploadsm.png "上传")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. 上传的文件会显示在容器窗格中。 选择一个文件，然后复制“Blob 属性”窗格的 URL。 在下一步骤中将市场项导入 Azure Stack 时，将要使用此 URL。  在下图中，容器为 **blob-test-storage**，文件为 **Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg**。 文件 URL 为 **https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg** 。  
      [![Blob 属性](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "Blob 属性")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. 使用 **Add-AzsPlatformimage** cmdlet 将 VHD 映像导入到 Azure Stack。 使用此 cmdlet 时，请将 `publisher`、`offer` 和其他参数值替换为要导入的图像的值。

   可以从连同 .azpkg 文件一起下载的文本文件中，获取映像的 `publisher`、`offer` 和 `sku` 值。 该文本文件存储在目标位置中。 `version` 值是在上一过程中从 Azure 下载项目时记下的版本。

   以下示例脚本使用了 Windows Server 2016 Datacenter - Server Core 虚拟机的值。 `-Osuri` 的值是项目的 blob 存储位置的示例路径。

   除了使用此脚本以外，还可以使用[此文中所述的过程](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal)通过 Azure 门户导入 VHD 映像。

   ```powershell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **关于解决方案模板：** 某些模板可能包含名为 **fixed3.vhd** 的 3 MB VHD 小文件。 无需将该文件导入 Azure Stack。 Fixed3 文件随一些解决方案模板一起提供，以满足 Azure Marketplace 的发布要求。

   查看模板说明并下载，然后导入其他必需的项，例如，使用解决方案模板所需的 VHD。  

   **关于扩展：** 使用虚拟机映像扩展时，请使用以下参数：
   - *发布者*
   - 类型
   - *版本*  

   不使用*产品/服务*。

4. 在 PowerShell 中使用 **Add-AzsGalleryItem** cmdlet 将市场项发布到 Azure Stack。 例如：

    ```powershell
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     -Verbose
    ```

5. 发布库项后，即可使用它。 若要确认发布库项，请转到“所有服务”，然后在“常规”类别下，选择“市场”。  如果下载内容是解决方案模板，请确保为该解决方案模板添加任何依赖的 VHD 映像。  
  [![查看市场](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "查看市场")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

随着 Azure Stack PowerShell 1.3.0 的发布，现在可以添加虚拟机扩展。 例如：

```powershell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>后续步骤

[创建和发布市场项目](azure-stack-create-and-publish-marketplace-item.md)
