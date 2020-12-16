---
title: 从 Azure 下载 marketplace 项，并发布到 Azure Stack
description: 了解如何从 Azure 下载 marketplace 项，并发布到针对模块化数据中心 (MDC) 的 Azure Stack。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 2a3dec7699911988c4c9a958c31386c127f70694
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598226"
---
# <a name="download-marketplace-items-to-azure-stack-hub---modular-data-center-mdc"></a>将 Marketplace 项下载到 Azure Stack 集线器-模块化数据中心 (MDC)  

云操作员可从市场将项下载 Azure Stack Hub，并使这些项可供所有使用 Azure Stack Hub 环境的用户使用。 可以选择的项来自 Azure 市场项的有序列表，这些项已预先经过测试，支持与 Azure Stack 配合使用。 其他项会不断地添加到此列表中，因此请不时地返回查看新内容。

有两种下载市场产品的场景：

- **联网场景**：需将 Azure Stack Hub 环境连接到 Internet。 使用 Azure Stack Hub 管理员门户查找和下载项。
- **离线或部分联网场景**：需使用市场联合集成工具访问 Internet 来下载市场项。 然后，将下载内容传输到离线 Azure Stack 安装中。 此场景使用 PowerShell。

有关可下载的市场项的完整列表，请参阅 [Azure Stack 的 Azure 市场项](../../operator/azure-stack-marketplace-azure-items.md)。 有关 Azure Stack 市场的最新添加、删除和更新的列表，请参阅 [Azure Stack 市场更改](../../operator/azure-stack-marketplace-changes.md)一文。

> [!NOTE]
> 目录根据 Azure Stack Hub 系统连接到的云而异。 云环境由用于注册 Azure Stack Hub 的 Azure 订阅确定。

## <a name="connected-scenario"></a>联网场景

如果 Azure Stack Hub 连接到 Internet，则可以使用管理员门户下载市场项。

### <a name="prerequisites"></a>先决条件

Azure Stack 中心部署必须具有 internet 连接并 [已注册到 Azure](registration-tzl.md)。

### <a name="use-the-portal-to-download-marketplace-items"></a>使用门户下载市场项

1. 登录到 Azure Stack Hub 管理员门户。

2. 下载市场项之前，查看可用的存储空间。 稍后在选择要下载的项时，可将下载大小与可用存储容量进行比较。 如果容量有限，请考虑使用[管理可用空间](../../operator/azure-stack-manage-storage-shares.md#manage-available-space)的选项。

   若要查看可用空间，请在“区域管理”中选择要浏览的区域，然后转到“资源提供程序” > “存储”：  

   ![在 Azure Stack 管理员门户中查看存储空间](media/azure-stack-download-azure-marketplace-item-tzl/storage.png)

3. 打开 Azure Stack Hub 市场并连接到 Azure。 为此，请依次选择“市场管理”服务、“市场项”和“从 Azure 中添加”：

   ![从 Azure 添加市场项](media/azure-stack-download-azure-marketplace-item-tzl/marketplace.png)

4. 每个行项还显示当前可用版本。 如果某个市场项有多个可用版本，“版本”列会显示“多个”。  可以单击每个项查看其说明和附加信息，包括其下载大小：

   ![显示 "从 Azure 添加" 页的屏幕截图。](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure-1.png)

5. 如果某个项的版本显示为 **多个**，则可以选择该项目，然后从生成的版本选择器下拉列表中选择特定版本：

   ![显示 "管理" 页面并突出显示 "版本选择器" 下拉列表的 Screnshot。](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure-3.png)

6. 选择所需的项，然后选择“下载”。 下载时间根据网络连接性能而异。 下载完成后，可以 Azure Stack 操作员或用户的身份部署新市场项。

7. 若要部署下载的项，请选择“+ 创建资源”，在类别中搜索该新市场项。 接下来，选择该项以开始部署过程。 该过程根据市场项的不同而异。

## <a name="disconnected-or-a-partially-connected-scenario"></a>离线场景或部分联网场景

在断开连接的环境中，不能从 Azure 下载 marketplace 项。 必须使用脱机联合工具将 marketplace 项下载到本地驱动器，然后将这些项上传到 Azure Stack 中心。

可在 [此处下载脱机联合工具](https://aka.ms/azsSyndicationtool)。

### <a name="download-marketplace-items-from-azure"></a>从 Azure 下载 Marketplace 项

#### <a name="prerequisites"></a>先决条件

- 计算机上安装 Azure PowerShell。

- 用于注册 Azure Stack 集线器的 Azure 订阅 ID、注册资源组和注册名称。 可以在 Azure Stack 中心操作员门户的 "**区域管理**" 边栏选项卡的 "**属性**" 选项卡中找到此信息。

- 拥有 Azure 订阅 ID 权限的帐户的用户名和密码。

- 可向其写入脱机文件的本地驱动器或网络路径。

#### <a name="download-items"></a>下载项目



### <a name="az-modules"></a>[Az 模块](#tab/az1)

1. 打开 PowerShell 并中转到解压缩的文件夹。

2. 运行 **Invoke-AzSMarketplaceDownload.ps1** PowerShell 脚本：

    ```powershell
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationSubscriptionId '<subscription ID>' ` 
       -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' `
       -TenantName mytenant.onmicrosoft.com -DownloadFolder 'F:\offlineSyndication'
    ```

    或者，如果已通过 Azure PowerShell 登录，则可以传入 Azure 上下文：

    ```powershell
    Add-AzAccount -Environment AzureCloud -Tenant mytenant.onmicrosoft.com 
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' -DownloadFolder 'F:\offlineSyndication' -AzureContext $(Get-AzureRMContext)
    ```
    如果未传入 Azure 上下文，则会要求你登录。

3. 此时将显示一个窗口，您可以在其中选择要下载的产品。 可以按住 Ctrl 并单击来选择多个项。

4. 选择“确定”  。 这会下载 marketplace 项及其依赖项（如果有）。
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm1)

1. 打开 PowerShell 并中转到解压缩的文件夹。

2. 运行 **Invoke-AzSMarketplaceDownload.ps1** PowerShell 脚本：

    ```powershell
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationSubscriptionId '<subscription ID>' ` 
       -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' `
       -TenantName mytenant.onmicrosoft.com -DownloadFolder 'F:\offlineSyndication'
    ```

    或者，如果已通过 Azure PowerShell 登录，则可以传入 Azure 上下文：

    ```powershell
    Add-AzureRmAccount -Environment AzureCloud -Tenant mytenant.onmicrosoft.com 
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' -DownloadFolder 'F:\offlineSyndication' -AzureContext $(Get-AzureRMContext)
    ```
    如果未传入 Azure 上下文，则会要求你登录。

3. 此时将显示一个窗口，您可以在其中选择要下载的产品。 可以按住 Ctrl 并单击来选择多个项。

4. 选择“确定”  。 这会下载 marketplace 项及其依赖项（如果有）。

---

### <a name="upload-marketplace-items-to-azure-stack-hub"></a>将 Marketplace 项上载到 Azure Stack 中心

#### <a name="prerequisites"></a>先决条件

- Azure Stack 管理员资源管理器终结点和目录租户。

- 访问脱机 Marketplace 项。

#### <a name="upload-items"></a>上传项目

### <a name="az-modules"></a>[Az 模块](#tab/az2)

1. 打开 PowerShell 并中转到解压缩的文件夹。

2. 运行 **Invoke-AzSMarketplaceUpload.ps1** PowerShell 脚本：

    ```powershell
    .\Invoke-AzsMarketplaceUpload.ps1 -AzureStackCloudName "AzureStack-Admin" -AzureStackAdminARMEndpoint https://adminmanagement.<region>.<fqdn> -TenantName mytenant.onmicrosoft.com -DownloadFolder F:\offlineSyndication
    ```

    或者，你可以在 Azure PowerShell 中自行设置 Azure Stack 环境，对 admin 资源管理器终结点进行身份验证，并将上下文传递给脚本：

    ```powershell
    Add-AzEnvironment -Name Redmond-Admin -ARMEndpoint https://adminmanagement.redmond.azurestack.corp.microsoft.com

    Add-AzAccount -Environment Redmond-Admin

    .\Invoke-AzsMarketplaceUpload.ps1 -DownloadFolder F:\Downloads\offlining -AzureContext $(GetAzContext)
    ```

    此过程将 marketplace 项上载到指定的 Azure Stack 中心。

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm2)

1. 打开 PowerShell 并中转到解压缩的文件夹。

2. 运行 **Invoke-AzSMarketplaceUpload.ps1** PowerShell 脚本：

    ```powershell
    .\Invoke-AzsMarketplaceUpload.ps1 -AzureStackCloudName "AzureStack-Admin" -AzureStackAdminARMEndpoint https://adminmanagement.<region>.<fqdn> -TenantName mytenant.onmicrosoft.com -DownloadFolder F:\offlineSyndication
    ```

    或者，你可以在 Azure PowerShell 中自行设置 Azure Stack 环境，对 admin 资源管理器终结点进行身份验证，并将上下文传递给脚本：

    ```powershell
    Add-AzureRmEnvironment -Name Redmond-Admin -ARMEndpoint https://adminmanagement.redmond.azurestack.corp.microsoft.com

    Add-AzureRmAccount -Environment Redmond-Admin

    .\Invoke-AzsMarketplaceUpload.ps1 -DownloadFolder F:\Downloads\offlining -AzureContext $(Get-AzureRmContext)
    ```

    此过程将 marketplace 项上载到指定的 Azure Stack 中心。

---
