---
title: 从 Azure 下载市场项并发布到 Azure Stack Hub
description: 了解如何从 Azure 下载市场项并发布到 Azure Stack Hub。
author: sethmanheim
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/16/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 3634862089f3811da314d3f85865fc1a29324f5f
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99534039"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>将市场项下载到 Azure Stack Hub

云操作员可从市场将项下载 Azure Stack Hub，并使这些项可供所有使用 Azure Stack Hub 环境的用户使用。 可以选择的项来自 Azure 市场项的有序列表，这些项已预先经过测试，支持与 Azure Stack Hub 配合使用。 其他项会不断地添加到此列表中，因此请不时地返回查看新内容。

有两种下载市场产品的场景：

- **离线或部分联网场景**：需使用市场联合集成工具访问 Internet 来下载市场项。 然后，将下载内容传输到离线 Azure Stack Hub 安装中。 此场景使用 PowerShell。
- **联网场景**：需将 Azure Stack Hub 环境连接到 Internet。 使用 Azure Stack Hub 管理员门户查找和下载项。

有关可下载的市场项的完整列表，请参阅 [Azure Stack Hub 的 Azure 市场项](azure-stack-marketplace-azure-items.md)。 有关 Azure Stack Hub 市场的最新添加、删除和更新的列表，请参阅 [Azure Stack Hub 市场更改](azure-stack-marketplace-changes.md)一文。

> [!NOTE]
> 目录根据 Azure Stack Hub 系统连接到的云而异。 云环境由用于注册 Azure Stack Hub 的 Azure 订阅确定。

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

::: zone pivot="state-connected"
通过连接的部署，可以使用管理员门户下载市场项。

## <a name="prerequisites"></a>先决条件

Azure Stack Hub 部署必须已建立 Internet 连接，并且已注册到 Azure。

## <a name="use-the-portal-to-download-marketplace-items"></a>使用门户下载市场项

1. 登录到 Azure Stack Hub 管理员门户。

2. 下载市场项之前，查看可用的存储空间。 稍后在选择要下载的项时，可将下载大小与可用存储容量进行比较。 如果容量有限，请考虑使用[管理可用空间](azure-stack-manage-storage-shares.md#manage-available-space)的选项。

   若要查看可用空间，请在“区域管理”中选择要浏览的区域，然后转到“资源提供程序” > “存储”：  

   ![在 Azure Stack Hub 管理员门户中查看存储空间](media/azure-stack-download-azure-marketplace-item/storage.png)

3. 打开 Azure Stack Hub 市场并连接到 Azure。 为此，请依次选择“市场管理”服务、“市场项”和“从 Azure 中添加”：

   ![从 Azure 添加市场项](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. 每个行项还显示当前可用版本。 如果某个市场项有多个可用版本，“版本”列会显示“多个”。  可以单击每个项查看其说明和附加信息，包括其下载大小：

   ![屏幕截图，显示市场项的可用版本。](media/azure-stack-download-azure-marketplace-item/add-from-azure1.png)

5. 如果某个项的版本显示为“多个”，你可以选择该项，然后从显示的版本选择器下拉列表中选择特定的版本。 请注意，Microsoft 现在可以通过添加属性来阻止管理员下载由于各种属性（例如，Azure Stack 版本或计费模型）而与其 Azure Stack 不兼容的市场产品。 只有 Microsoft 才能添加以下属性：

   [![从 Azure 添加](media/azure-stack-download-azure-marketplace-item/add-from-azure3sm.png "多个版本")](media/azure-stack-download-azure-marketplace-item/add-from-azure3.png#lightbox)

6. 选择所需的项，然后选择“下载”。 下载时间根据网络连接性能而异。 下载完成后，可以 Azure Stack Hub 操作员或用户的身份部署新市场项。

7. 若要部署下载的项，请选择“+ 创建资源”，在类别中搜索该新市场项。 接下来，选择该项以开始部署过程。 该过程根据市场项的不同而异。
::: zone-end

::: zone pivot="state-disconnected"
Azure Stack Hub 受限或未建立 Internet 连接时，可以使用 PowerShell 和“市场联合工具”，将市场项下载到已建立 Internet 连接的计算机。 然后，将这些项传输到 Azure Stack Hub 环境。 在离线环境中，无法使用 Azure Stack Hub 门户下载市场项。

也可以在联网场景中使用市场联合工具。

此方案包含两个部分：

- **第 1 部分**：从市场项下载。 在能够访问 Internet 的计算机上配置 PowerShell，下载联合工具，然后从 Azure 市场下载项。
- **第 2 部分**：上传并发布到 Azure Stack Hub 市场。 将下载的文件移到 Azure Stack Hub 环境，然后将其发布到 Azure Stack Hub 市场。

## <a name="prerequisites"></a>先决条件

- 联网环境（不必是 Azure Stack Hub）。 需要建立连接才能获取 Azure 中的产品列表及其详细信息，并在本地下载所有项。 完成此操作后，剩余的过程无需建立 Internet 连接。 此过程将创建以前下载的项的目录，供你在离线环境中使用。

- 一个用于连接离线环境和传输所有必要项目的可移动媒体。

- 符合以下先决条件的离线 Azure Stack Hub 环境：

  - Azure Stack Hub 部署必须已注册到 Azure。

  - 已建立 Internet 连接的计算机必须已安装 **Azure Stack Hub PowerShell 模块版本 1.2.11** 或更高版本。 如果未安装，请[安装 Azure Stack Hub 特定的 PowerShell 模块](powershell-install-az-module.md)。

  - 为了能够导入已下载的市场项，必须配置 [Azure Stack Hub 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)。
  - .NET Framework 4.7 或更高版本。

使用以下命令从 PowerShell 库下载 Azs.Syndication.Admin 模块：

### <a name="az-modules"></a>[Az 模块](#tab/az1)

  ```powershell
  Install-Module -Name Azs.Syndication.Admin -AllowPrerelease -PassThru
  ```

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm1)

  ```powershell
  Install-Module -Name Azs.Syndication.Admin -RequiredVersion 0.1.140
  ```

---

注册 Azure Stack 后，可以忽略市场管理边栏选项卡上显示的以下消息，因为此消息与离线用例无关：

![市场管理](media/azure-stack-download-azure-marketplace-item/toolsmsg.png)

## <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>使用市场联合工具下载市场项

> [!IMPORTANT]
> 每当在离线场景中下载市场项时，都请确保下载市场联合工具。 此工具经常发生更改，每次下载都应使用最新版本。

### <a name="az-modules"></a>[Az 模块](#tab/az2)

1. 在已建立 Internet 连接的计算机上，以管理员身份打开 PowerShell 控制台。

2. 使用已用于注册 Azure Stack Hub 的 Azure 帐户登录到相应的 Azure 云和 AzureAD 目录租户。 若要添加该帐户，请在 PowerShell 中运行 `Connect-AzAccount`：

   ```powershell  
   Connect-AzAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```

   系统会提示输入 Azure 帐户凭据。根据帐户的配置，可能需要使用双因素身份验证。

   > [!NOTE]
   > 如果会话过期、密码已更改或你需要切换帐户，请在使用 `Connect-AzAccount` 登录之前先运行以下 cmdlet：`Remove-AzAccount -Scope Process`。

3. 如果有多个订阅，请运行以下命令，以选择已用于注册的订阅：

   ```powershell  
   Get-AzSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzSubscription
   ```

4. 如果尚未在先决条件步骤中完成此操作，请下载最新版本的市场联合工具：

   ```powershell
   Install-Module -Name Azs.Syndication.Admin -AllowPrerelease -PassThru
   ```

5. 若要选择要下载的市场项（如 VM 映像、扩展或解决方案模板），请运行以下命令：

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   随后会显示一个表格，其中列出了所选订阅中可用的所有 Azure Stack 注册。 选择与要下载其市场项的 Azure Stack 环境相匹配的注册，然后选择“确定”。

     ![屏幕截图，显示所选订阅中可用的所有 Azure Stack 注册的列表。](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   此时应会看到另一个表格，其中列出了所有可供下载的市场项。 选择要下载的项，并记下 **版本**。 可以按住 **Ctrl** 键选择多个映像。
     ![屏幕截图，显示所选订阅中可用的所有 Azure Stack 注册的另一个列表。](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   也可通过“添加条件”选项来筛选映像的列表。
   ![选择 Azure Stack 注册](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   做出选择后，选择“确定”。

6. 已经选择要下载的市场项的 ID 会保存在 `$products` 变量中。 使用以下命令开始下载选定的项。 请将 destination folder path 替换为从 Azure 市场下载的文件的存储位置：

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. 所需的下载时间取决于项的大小。 下载完成后，该项会出现在脚本中指定的文件夹内。 下载内容中包括一个 VHD 文件（适用于虚拟机）或 .zip 文件（适用于虚拟机扩展和资源提供程序）。 其中还可能包含一个 .azpkg 格式的库包（一个 .zip 文件）。

8. 如果下载失败，可以重新运行以下 PowerShell cmdlet 来重试下载：

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. 还应该将 **Azs.Syndication.Admin** 模块导出到本地，以便能够将其复制到要从中将市场项导入到 Azure Stack Hub 的计算机。

   > [!NOTE]
   > 用于导出此模块的目标文件夹应该不同于市场项的导出所在位置。

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm2)

1. 在已建立 Internet 连接的计算机上，以管理员身份打开 PowerShell 控制台。

2. 使用已用于注册 Azure Stack Hub 的 Azure 帐户登录到相应的 Azure 云和 AzureAD 目录租户。 若要添加该帐户，请在 PowerShell 中运行 `Add-AzureRMAccount`：

   ```powershell  
   Login-AzureRMAccount -Environment AzureCloud -Tenant '<mydirectory>.onmicrosoft.com'
   ```

   系统会提示输入 Azure 帐户凭据。根据帐户的配置，可能需要使用双因素身份验证。

   > [!NOTE]
   > 如果会话过期、密码已更改或你需要切换帐户，请在使用 `Add-AzureRMAccount` 登录之前先运行以下 cmdlet：`Remove-AzureRMAccount -Scope Process`。

3. 如果有多个订阅，请运行以下命令，以选择已用于注册的订阅：

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRMSubscription
   ```

4. 如果尚未在先决条件步骤中完成此操作，请下载最新版本的市场联合工具：

   ```powershell
   Install-Module -Name Azs.Syndication.Admin -RequiredVersion 0.1.140
   ```

5. 若要选择要下载的市场项（如 VM 映像、扩展或解决方案模板），请运行以下命令：

   ```powershell
   $products = Select-AzsMarketplaceItem
   ```

   随后会显示一个表格，其中列出了所选订阅中可用的所有 Azure Stack 注册。 选择与要下载其市场项的 Azure Stack 环境相匹配的注册，然后选择“确定”。

     ![屏幕截图，显示所选订阅中可用的所有 Azure Stack 注册的列表。](media/azure-stack-download-azure-marketplace-item/select-registration.png)

   此时应会看到另一个表格，其中列出了所有可供下载的市场项。 选择要下载的项，并记下 **版本**。 可以按住 **Ctrl** 键选择多个映像。
     ![屏幕截图，显示所选订阅中可用的所有 Azure Stack 注册的另一个列表。](media/azure-stack-download-azure-marketplace-item/select-products.png)
  
   也可通过“添加条件”选项来筛选映像的列表。
   ![选择 Azure Stack 注册](media/azure-stack-download-azure-marketplace-item/select-products-with-filter.png)

   做出选择后，选择“确定”。

6. 已经选择要下载的市场项的 ID 会保存在 `$products` 变量中。 使用以下命令开始下载选定的项。 请将 destination folder path 替换为从 Azure 市场下载的文件的存储位置：

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

7. 所需的下载时间取决于项的大小。 下载完成后，该项会出现在脚本中指定的文件夹内。 下载内容中包括一个 VHD 文件（适用于虚拟机）或 .zip 文件（适用于虚拟机扩展和资源提供程序）。 其中还可能包含一个 .azpkg 格式的库包（一个 .zip 文件）。

8. 如果下载失败，可以重新运行以下 PowerShell cmdlet 来重试下载：

    ```powershell
    $products | Export-AzsMarketplaceItem  -RepositoryDir "Destination folder path in quotes"
    ```

9. 还应该将 **Azs.Syndication.Admin** 模块导出到本地，以便能够将其复制到要从中将市场项导入到 Azure Stack Hub 的计算机。

   > [!NOTE]
   > 用于导出此模块的目标文件夹应该不同于市场项的导出所在位置。

    ```powershell
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azs.Syndication.Admin -Path "Destination folder path in quotes" -Force
    ```

---



## <a name="import-the-download-and-publish-to-azure-stack-hub-marketplace-using-powershell"></a>使用 PowerShell 导入下载内容并发布到 Azure Stack Hub 市场

1. 必须将[前面下载](#use-the-marketplace-syndication-tool-to-download-marketplace-items)到本地的文件移到已与 Azure Stack Hub 环境建立了连接的计算机。 市场联合工具也必须可供 Azure Stack Hub 环境使用，因为你需要使用该工具来执行导入操作。

   下图显示了文件夹结构示例。 **D:\downloadfolder** 包含所有已下载的市场项。 每个子文件夹都是一个市场项（例如 **microsoft.custom-script-linux-arm-2.0.3**），并按产品 ID 命名。 每个子文件夹包含市场项的下载内容。

   ![市场下载目录结构](media/azure-stack-download-azure-marketplace-item/mp1.png)

2. 按照[此文](azure-stack-powershell-configure-admin.md)中的说明配置 Azure Stack Hub 操作员 PowerShell 会话。

3. 使用对“默认提供程序订阅”拥有所有者访问权限的标识登录到 Azure Stack Hub。

4. 导入联合模块，然后运行以下脚本来启动市场联合工具：

    ```powershell
    Import-AzsMarketplaceItem -RepositoryDir "Source folder path in quotes"
    ```

5. 成功完成该脚本后，Azure Stack Hub 市场中应会提供这些市场项。
::: zone-end
