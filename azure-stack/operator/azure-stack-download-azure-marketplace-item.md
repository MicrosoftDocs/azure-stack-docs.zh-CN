---
title: 从 Azure 下载市场项并发布到 Azure Stack Hub
description: 了解如何从 Azure 下载市场项并发布到 Azure Stack Hub。
author: sethmanheim
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: a37f8b14a1a21caa8f710afe54868f308971e422
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80979405"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>将市场项下载到 Azure Stack Hub 

云操作员可从市场将项下载 Azure Stack Hub，并使这些项可供所有使用 Azure Stack Hub 环境的用户使用。 可以选择的项来自 Azure 市场项的有序列表，这些项已预先经过测试，支持与 Azure Stack Hub 配合使用。 其他项会不断地添加到此列表中，因此请不时地返回查看新内容。

有两种下载市场产品的场景：

- **联网场景**：需将 Azure Stack Hub 环境连接到 Internet。 使用 Azure Stack Hub 管理员门户查找和下载项。
- **离线或部分联网场景**：需使用市场联合集成工具访问 Internet 来下载市场项。 然后，将下载内容传输到离线 Azure Stack Hub 安装中。 此场景使用 PowerShell。

有关可下载的市场项的完整列表，请参阅 [Azure Stack Hub 的 Azure 市场项](azure-stack-marketplace-azure-items.md)。 有关 Azure Stack Hub 市场的最新添加、删除和更新的列表，请参阅 [Azure Stack Hub 市场更改](azure-stack-marketplace-changes.md)一文。

> [!NOTE]
> 目录根据 Azure Stack Hub 系统连接到的云而异。 云环境由用于注册 Azure Stack Hub 的 Azure 订阅确定。

## <a name="connected-scenario"></a>联网场景

如果 Azure Stack Hub 连接到 Internet，则可以使用管理员门户下载市场项。

### <a name="prerequisites"></a>先决条件

Azure Stack Hub 部署必须已建立 Internet 连接，并且已注册到 Azure。

### <a name="use-the-portal-to-download-marketplace-items"></a>使用门户下载市场项

1. 登录到 Azure Stack Hub 管理员门户。

2. 下载市场项之前，查看可用的存储空间。 稍后在选择要下载的项时，可将下载大小与可用存储容量进行比较。 如果容量有限，请考虑使用[管理可用空间](azure-stack-manage-storage-shares.md#manage-available-space)的选项。

   若要查看可用空间，请在“区域管理”中选择要浏览的区域，然后转到“资源提供程序” > “存储”：   

   ![在 Azure Stack Hub 管理员门户中查看存储空间](media/azure-stack-download-azure-marketplace-item/storage.png)

3. 打开 Azure Stack Hub 市场并连接到 Azure。 为此，请依次选择“市场管理”  服务、“市场项”  和“从 Azure 中添加”  ：

   ![从 Azure 添加市场项](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. 每个行项还显示当前可用版本。 如果某个市场项有多个可用版本，“版本”列会显示“多个”。   可以单击每个项查看其说明和附加信息，包括其下载大小：

   ![从 Azure 添加](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. 如果某个项的版本显示为**多个**，则可以选择该项目，然后从生成的版本选择器下拉列表中选择特定的版本。 请注意，Microsoft 现在能够添加一些属性，这些属性阻止管理员下载与其 Azure Stack 不兼容的 marketplace 产品，因为 Azure Stack 版本或计费模式等不同的属性。 只有 Microsoft 可以添加以下属性：

   [![从 Azure 添加](media/azure-stack-download-azure-marketplace-item/add-from-azure3sm.png "多个版本")](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png#lightbox)

6. 选择所需的项，然后选择“下载”****。 下载时间根据网络连接性能而异。 下载完成后，可以将新的 marketplace 项作为 Azure Stack 中心操作员或用户部署。

7. 若要部署下载的项，请选择“+ 创建资源”，在类别中搜索该新市场项。**** 接下来，选择该项以开始部署过程。 该过程根据市场项的不同而异。

## <a name="disconnected-or-a-partially-connected-scenario"></a>离线场景或部分联网场景

如果 Azure Stack Hub 受限或未建立 Internet 连接，可以使用 PowerShell 和*市场联合工具*，将市场项下载到已建立 Internet 连接的计算机。 然后，将这些项传输到 Azure Stack Hub 环境。 在离线环境中，无法使用 Azure Stack Hub 门户下载市场项。

也可以在联网场景中使用市场联合工具。

此方案包含两个部分：

- **第1部分**：从 Marketplace 项下载。 在能够访问 Internet 的计算机上配置 PowerShell，下载联合工具，然后从 Azure 市场下载项。
- **第2部分**：上传并发布到 Azure Stack 中心市场。 将下载的文件移动到 Azure Stack 中心环境，然后将其发布到 Azure Stack 中心市场。

### <a name="prerequisites"></a>必备条件

- 联网环境（不必是 Azure Stack Hub）。 需要建立连接才能获取 Azure 中的产品列表及其详细信息，并在本地下载所有项。 完成此操作后，剩余的过程无需建立 Internet 连接。 此过程将创建以前下载的项的目录，供你在离线环境中使用。

- 一个用于连接离线环境和传输所有必要项目的可移动媒体。

- 符合以下先决条件的离线 Azure Stack Hub 环境：

  - 你的 Azure Stack 中心部署必须已注册到 Azure。

  - 具有 internet 连接的计算机必须具有**Azure Stack 集线器 PowerShell 模块版本 1.2.11**或更高版本。 如果尚未安装，请[安装 Azure Stack 集线器特定的 PowerShell 模块](azure-stack-powershell-install.md)。

  - 若要启用下载的 marketplace 项的导入，必须配置[Azure Stack 中心操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)。

- 使用以下命令从 PowerShell 库下载 Azs 模块
  ```
  Install-Module -Name Azs.Syndication.Admin
  ```
  
- .NET Framework 4.7 或更高版本

注册 Azure Stack 后，你可以忽略 Marketplace 管理边栏选项卡上显示的以下消息，因为这与断开连接的用例无关：

![市场管理](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>使用市场联合工具下载市场项

> [!IMPORTANT]
> 每当在离线场景中下载市场项时，都请确保下载市场联合工具。 对此工具进行频繁的更改，并且每次下载都应使用最新版本。

1. 在已建立 Internet 连接的计算机上，以管理员身份打开 PowerShell 控制台。

2. 使用用于注册 Azure Stack 集线器的 Azure 帐户登录到相应的 Azure cloud and AzureAD Directory 租户。 若要添加该帐户，请在 PowerShell 中运行**add-azurermaccount**。 

   ```powershell  
   Login-AzureRmAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```
   系统会提示输入 Azure 帐户凭据。根据帐户的配置，可能需要使用双因素身份验证。

   > [!NOTE]
   > 如果会话过期，你的密码已更改，或者你只是想要切换帐户，请在使用**add-azurermaccount**： **add-azurermaccount 进程**登录之前运行以下 cmdlet。

3. 如果有多个订阅，请运行以下命令，选择已经用于注册的那个订阅：

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   ```

4. 如果你尚未在先决条件步骤中执行此操作，请下载最新版本的 marketplace 联合工具：

   ```powershell
   Install-Module -Name Azs.Syndication.Admin
   ```

5. 若要选择要下载的 Marketplace 项（例如 VM 映像、扩展或解决方案模板），请运行以下命令。 

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   这会显示一个表，其中列出了所选订阅中的所有 Azure Stack 注册。 选择与要下载其应用商店项的 Azure Stack 环境匹配的注册，然后选择 **"确定"**。

     ![选择 Azure Stack 注册](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   现在应会看到另一个表，其中列出了可供下载的所有 marketplace 项。 选择要下载的项，并记下**版本**。 可以按住 **Ctrl** 键选择多个映像。
     ![选择 Azure Stack 注册](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   也可通过“添加条件”选项来筛选映像的列表。****
   ![选择 Azure Stack 注册](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   做出选择后，选择 "确定"。

6. 选择用于下载的 marketplace 项的 Id 保存在`$products`变量中。 使用以下命令开始下载选定的项目。 请将 destination folder path 替换为从 Azure 市场下载的文件的存储位置：

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. 所需的下载时间取决于项的大小。 下载完成后，该项会出现在脚本中指定的文件夹内。 下载内容中包括一个 VHD 文件（适用于虚拟机）或 .zip 文件（适用于虚拟机扩展和资源提供程序）。 它还可能包含 *.azpkg*格式的库包，即 .zip 文件。

8. 如果下载失败，可以重新运行以下 PowerShell cmdlet 来重试下载：

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. 还应在本地导出**Azs**模块，以便你可以将其复制到要从其导入 marketplace 项 Azure Stack 集线器的计算机。

   > [!NOTE]
   > 用于导出此模块的目标文件夹应该不同于已将 marketplace 项导出到的位置。

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

### <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>使用 PowerShell 导入下载并发布到 Azure Stack 中心市场

1. 必须将[之前下载](#use-the-marketplace-syndication-tool-to-download-marketplace-items)的文件移动到已本地下载到 Azure Stack 中心环境的计算机。 Marketplace 联合工具还必须可用于 Azure Stack 中心环境，因为你需要使用该工具执行导入操作。

   下图显示了文件夹结构示例。 **D:\downloadfolder**包含所有已下载的 marketplace 项。 每个子文件夹都是一个以产品 ID 命名的 marketplace 项（例如， **2.0.3**）。 每个子文件夹包含市场项的下载内容。

   ![市场下载目录结构](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. 按照[本文](azure-stack-powershell-configure-admin.md)中的说明配置 Azure Stack 中心操作员 PowerShell 会话。

3. 使用有权访问 "默认提供程序订阅" 的身份登录到 Azure Stack 中心。

4. 导入联合模块，然后运行以下脚本来启动市场联合工具：

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. 脚本成功完成后，marketplace 项应在 Azure Stack Hub Marketplace 中可用。
