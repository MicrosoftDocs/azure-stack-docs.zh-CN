---
title: 从 Azure 下载 marketplace 项，并发布到 Azure Stack 中心
description: 了解如何从 Azure 下载 marketplace 项，并发布到 Azure Stack 中心。
author: sethmanheim
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/23/2018
ms.openlocfilehash: d909786e3cf3bacbf981fc2150f20084d283e81b
ms.sourcegitcommit: c653efe7cd5177bf61c1a321ba7f38c0cdf90346
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77575144"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>将 Marketplace 项下载到 Azure Stack 中心 

作为云操作员，你可以将项目从 Marketplace 下载到 Azure Stack 集线器，并使用 Azure Stack 中心环境将其提供给所有用户使用。 你可以选择的项来自特选列表，其中包含经过预测试和支持的 Azure Marketplace 项，可与 Azure Stack 中心一起使用。 其他项会频繁添加到此列表中，因此，请继续检查是否有新内容。

下载 Marketplace 产品的情况有两种：

- **连接方案**：要求将 Azure Stack 集线器环境连接到 internet。 使用 Azure Stack 中心管理员门户定位和下载项目。
- **断开连接或部分连接的方案**：要求你使用 marketplace 联合工具访问 internet 以下载 marketplace 项。 然后，将下载内容传输到断开连接的 Azure Stack 中心安装。 此方案使用 PowerShell。

有关可下载的 Marketplace 项的完整列表，请参阅[Azure Stack 集线器的 Azure Marketplace 项](azure-stack-marketplace-azure-items.md)。 有关 Azure Stack 中心市场的最新添加、删除和更新的列表，请参阅[Azure Stack 中心市场更改](azure-stack-marketplace-changes.md)一文。

> [!NOTE]
> 根据 Azure Stack 集线器系统连接到的云，目录将有所不同。 云环境由用于注册 Azure Stack 中心的 Azure 订阅决定。

## <a name="connected-scenario"></a>已连接的场景

如果 Azure Stack 中心连接到 internet，则可以使用管理员门户下载 marketplace 项。

### <a name="prerequisites"></a>先决条件

Azure Stack 中心部署必须具有 internet 连接并已注册到 Azure。

### <a name="use-the-portal-to-download-marketplace-items"></a>使用门户下载 marketplace 项

1. 登录到 Azure Stack 中心管理员门户。

2. 下载 marketplace 项之前，请查看可用的存储空间。 稍后，当你选择要下载的项时，可以将下载大小与可用存储容量进行比较。 如果容量有限，请考虑用于[管理可用空间](azure-stack-manage-storage-shares.md#manage-available-space)的选项。

   查看可用空间：在 "**区域管理**" 中，选择要浏览的区域，然后在 "**资源提供程序**" > **存储**：

   ![查看 Azure Stack 中心管理员门户中的存储空间](media/azure-stack-download-azure-marketplace-item/storage.png)

3. 打开 Azure Stack 中心 Marketplace 并连接到 Azure。 为此，请选择 " **marketplace 管理**" 服务，选择 " **marketplace 项**"，然后选择 "**从 Azure 添加**"：

   ![从 Azure 添加 marketplace 项](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. 每个行项还显示当前可用的版本。 如果有多个版本的 Marketplace 项可用，"**版本**" 列会显示**多个**版本。 你可以单击每个项目以查看其描述和其他信息，包括其下载大小：

   ![从 Azure 添加](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. 如果某个项的版本显示为**多个**，则可以选择该项目，然后从生成的版本选择器下拉列表中选择特定版本：

   ![从 Azure 添加](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png)

6. 选择所需的项目，然后选择 "**下载**"。 下载时间各不相同，具体取决于网络连接。 下载完成后，可以将新的 marketplace 项作为 Azure Stack 中心操作员或用户部署。

7. 若要部署下载项，请选择 " **+ 创建资源**"，然后在新的 marketplace 项的类别中进行搜索。 接下来，选择要开始部署过程的项。 不同 marketplace 项目的过程有所不同。

## <a name="disconnected-or-a-partially-connected-scenario"></a>断开连接或部分连接的方案

如果 Azure Stack 集线器限制或没有 internet 连接，则可以使用 PowerShell 和*marketplace 联合工具*将 marketplace 项下载到具有 internet 连接的计算机。 然后，将项目传输到 Azure Stack 中心环境。 在断开连接的环境中，无法使用 Azure Stack 集线器门户下载 marketplace 项。

Marketplace 联合工具也可用于连接的方案中。

此方案包含两个部分：

- **第1部分**：从 Marketplace 项下载。 在具有 internet 访问权限的计算机上，配置 PowerShell，下载联合工具，然后从 Azure Marketplace 下载项。
- **第2部分**：上传并发布到 Azure Stack 中心市场。 将下载的文件移动到 Azure Stack 中心环境，然后将其发布到 Azure Stack 中心市场。

### <a name="prerequisites"></a>先决条件

- 连接的环境（不必 Azure Stack 集线器）。 需要连接才能从 Azure 获取产品列表及其详细信息，并在本地下载所有内容。 完成此操作后，该过程的其余部分不需要 internet 连接。 它会创建以前下载的项的目录，以便在断开连接的环境中使用。

- 可移动媒体，用于连接到断开连接的环境并传输所有必要的项目。

- 断开连接 Azure Stack 集线器环境满足以下先决条件：

  - 你的 Azure Stack 中心部署必须已注册到 Azure。

  - 具有 internet 连接的计算机必须具有**Azure Stack 集线器 PowerShell 模块版本 1.2.11**或更高版本。 如果尚未安装，请[安装 Azure Stack 集线器特定的 PowerShell 模块](azure-stack-powershell-install.md)。

  - 若要启用下载的 marketplace 项的导入，必须配置[Azure Stack 中心操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)。

- 使用以下命令从 PowerShell 库下载 Azs 模块
  ```
  Install-Module -Name Azs.Syndication.Admin
  ```

注册 Azure Stack 后，你可以忽略 Marketplace 管理边栏选项卡上显示的以下消息，因为这与断开连接的用例无关：

![Marketplace 管理](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>使用 marketplace 联合工具下载 marketplace 项

> [!IMPORTANT]
> 在断开连接的情况下，请务必下载 marketplace 联合工具。 对此工具进行频繁的更改，并且每次下载都应使用最新版本。

1. 在具有 Internet 连接的计算机上，以管理员身份打开 PowerShell 控制台。

2. 使用用于注册 Azure Stack 集线器的 Azure 帐户登录到相应的 Azure cloud and AzureAD Directory 租户。 若要添加该帐户，请在 PowerShell 中运行**add-azurermaccount**。 

   ```powershell  
   Login-AzureRmAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```
   系统将提示你输入 Azure 帐户凭据，并且你可能必须使用双因素身份验证，具体取决于你的帐户配置。

   > [!NOTE]
   > 如果会话过期，你的密码已更改，或者你只是想要切换帐户，请在使用**add-azurermaccount**： **add-azurermaccount 进程**登录之前运行以下 cmdlet。

3. 如果有多个订阅，请运行以下命令来选择注册所用的订阅：

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

   这会显示一个表，其中列出了所选订阅中的所有 Azure Stack 注册。 选择与要下载其应用商店项的 Azure Stack 环境匹配的注册，然后选择 **"确定"** 。

     ![选择 Azure Stack 注册](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   现在应会看到另一个表，其中列出了可供下载的所有 marketplace 项。 选择要下载的项目，并记下该**版本**。 您可以按住**Ctrl**键来选择多个图像。
     ![选择 Azure Stack 注册](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   还可以通过使用 "**添加条件**" 选项来筛选映像列表。
   ![选择 Azure Stack 注册](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   做出选择后，选择 "确定"。

6. 所选的用于下载的 marketplace 项的 Id 保存在 `$products` 变量中。 使用以下命令开始下载选定的项目。 将目标文件夹路径替换为用于存储从 Azure Marketplace 下载的文件的位置：

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. 下载所用的时间取决于项的大小。 下载完成后，该项目在脚本中指定的文件夹中可用。 下载内容包括 VHD 文件（用于虚拟机）或 .zip 文件（用于虚拟机扩展和资源提供程序）。 它还可能包含 *.azpkg*格式的库包，即 .zip 文件。

8. 如果下载失败，你可以通过重新运行以下 PowerShell cmdlet 重试：

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

   下图显示了一个文件夹结构示例。 **D:\downloadfolder**包含所有已下载的 marketplace 项。 每个子文件夹都是一个以产品 ID 命名的 marketplace 项（例如， **2.0.3**）。 每个子文件夹内都是 marketplace 项的下载内容。

   ![Marketplace 下载目录结构](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. 按照[本文](azure-stack-powershell-configure-admin.md)中的说明配置 Azure Stack 中心操作员 PowerShell 会话。

3. 使用有权访问 "默认提供程序订阅" 的身份登录到 Azure Stack 中心。

4. 导入联合模块，并通过运行以下脚本来启动 marketplace 联合工具：

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. 脚本成功完成后，marketplace 项应在 Azure Stack Hub Marketplace 中可用。
