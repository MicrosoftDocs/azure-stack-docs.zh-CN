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
ms.date: 10/10/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 095744322937a34dffd680b886fd4b06ca65d7d6
ms.sourcegitcommit: 20d1c0ab3892e9c4c71d5b039457f1e15b1c84c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73618273"
---
# <a name="download-existing-marketplace-items-from-azure-and-publish-to-azure-stack"></a>从 Azure 下载现有 marketplace 项并发布到 Azure Stack

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

作为云操作员，你可以从 Azure Marketplace 下载项目并使其在 Azure Stack 中可用。 你可以选择的项来自特选的 Azure Marketplace 项列表，这些项已预先测试且支持使用 Azure Stack。 其他项会频繁添加到此列表中，因此，请继续检查是否有新内容。

可以通过两种方案连接到 Azure Marketplace：

- **连接方案**-要求 Azure Stack 环境连接到 internet。 使用 Azure Stack 门户定位和下载项。
- **断开连接或部分连接的方案**-要求使用 marketplace 联合工具访问 internet，以便下载 marketplace 项。 然后，将下载内容传输到断开连接的 Azure Stack 安装。 此方案使用 PowerShell。

有关可下载的 Marketplace 项的完整列表，请参阅[适用于 Azure Stack 的 Azure marketplace 项](azure-stack-marketplace-azure-items.md)。 有关 Azure Stack Marketplace 的最新添加、删除和更新列表，请参阅[Azure Stack marketplace 更改](azure-stack-marketplace-changes.md)一文。

## <a name="connected-scenario"></a>已连接的场景

如果 Azure Stack 连接到 internet，则可以使用管理员门户下载 marketplace 项。

### <a name="prerequisites"></a>必备组件

你的 Azure Stack 部署必须具有 internet 连接并[已注册到 Azure](azure-stack-registration.md)。

### <a name="use-the-portal-to-download-marketplace-items"></a>使用门户下载 marketplace 项
  
1. 登录到 Azure Stack 管理员门户。

2. 下载 marketplace 项之前，请查看可用的存储空间。 稍后，当你选择要下载的项时，可以将下载大小与可用存储容量进行比较。 如果容量有限，请考虑用于[管理可用空间](azure-stack-manage-storage-shares.md#manage-available-space)的选项。

    查看可用空间：在 "**区域管理**" 中，选择要浏览的区域，然后在 "**资源提供程序**" > **存储**：

    ![查看 Azure Stack 管理员门户中的存储空间](media/azure-stack-download-azure-marketplace-item/storage.png)

3. 打开 Azure Stack Marketplace 并连接到 Azure。 为此，请选择 " **marketplace 管理**" 服务，选择 " **marketplace 项**"，然后选择 "**从 Azure 添加**"：

    ![从 Azure 添加 marketplace 项](media/azure-stack-download-azure-marketplace-item/marketplace.png)

4. 门户会显示可从 Azure Marketplace 下载的项目列表。 您可以按名称、发布者和/或产品类型来筛选产品。 您还可以单击每个项目以查看其描述和其他信息，包括其下载大小：

    ![Azure Marketplace 项列表 ](media/azure-stack-download-azure-marketplace-item/image03.PNG)

5. 选择所需的项目，然后选择 "**下载**"。 下载时间各不相同。

    ![下载 Azure Marketplace 项](media/azure-stack-download-azure-marketplace-item/image04.png)

    下载完成后，可以将新的 marketplace 项作为 Azure Stack 操作员或用户部署。

6. 若要部署下载项，请选择 " **+ 创建资源**"，然后在新的 marketplace 项的类别中进行搜索。 接下来，选择要开始部署过程的项。 不同 marketplace 项目的过程有所不同。

## <a name="disconnected-or-a-partially-connected-scenario"></a>断开连接或部分连接的方案

如果 Azure Stack 处于断开连接模式下，则使用 PowerShell 和*marketplace 联合工具*将 marketplace 项下载到具有 internet 连接的计算机。 然后，将项目传输到 Azure Stack 环境。 在断开连接的环境中，你无法使用 Azure Stack 门户下载 marketplace 项。

Marketplace 联合工具也可用于连接的方案中。

此方案包含两个部分：

- **第1部分：** 从 Azure Marketplace 下载。 在具有 internet 访问权限的计算机上配置 PowerShell，下载联合工具，然后从 Azure Marketplace 下载项。  
- **第2部分：** 上载并发布到 Azure Stack Marketplace。 将下载的文件移动到 Azure Stack 环境，将其导入到 Azure Stack，然后将其发布到 Azure Stack Marketplace。  

### <a name="prerequisites"></a>必备组件

- 连接的环境（不必 Azure Stack）。 需要连接才能从 Azure 获取产品列表及其详细信息，并在本地下载所有内容。 完成此操作后，该过程的其余部分不需要 internet 连接。 它会创建以前下载的项的目录，以便在断开连接的环境中使用。

- USB 密钥或外部驱动器连接到断开连接的环境，并传输所有必要的项目。

- 已断开连接的 Azure Stack 环境满足以下先决条件：
  - 你的 Azure Stack 部署必须[已注册到 Azure](azure-stack-registration.md)。
  - 具有 internet 连接的计算机必须具有**Azure Stack PowerShell 模块版本 1.2.11**或更高版本。 [安装 Azure Stack 特定的 PowerShell 模块](azure-stack-powershell-install.md)（如果尚未存在）。
  - 若要启用下载的 marketplace 项的导入，必须配置[Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)。
  - 克隆[Azure Stack 工具](https://github.com/Azure/AzureStack-Tools)GitHub 存储库。

- Azure Stack 中必须有一个具有可公开访问的容器的[存储帐户](azure-stack-manage-storage-accounts.md)（这是一个存储 blob）。 使用容器作为 marketplace 项库文件的临时存储。 如果你不熟悉存储帐户和容器，请参阅 Azure 文档中[的使用 blob-Azure 门户](/azure/storage/blobs/storage-quickstart-blobs-portal)。

- 在第一个过程中，将下载 marketplace 联合工具。

- 可以安装[AzCopy](/azure/storage/common/storage-use-azcopy)以获得最佳下载性能，但这不是必需的。

注册后，你可以忽略 Marketplace 管理边栏选项卡上显示的以下消息，因为这与断开连接的用例无关：

[![未注册消息](media/azure-stack-download-azure-marketplace-item/toolsmsgsm.png "未注册消息")](media/azure-stack-download-azure-marketplace-item/toolsmsg.png#lightbox)

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>使用 marketplace 联合工具下载 marketplace 项

> [!IMPORTANT]
> 在断开连接的情况下，请务必下载 marketplace 联合工具。 对此脚本进行频繁的更改，并且每次下载时都应该使用最新版本。

1. 在具有 Internet 连接的计算机上，以管理员身份打开 PowerShell 控制台。

2. 添加用于注册 Azure Stack 的 Azure 帐户。 若要添加该帐户，请在 PowerShell 中运行不带任何参数的 `Add-AzureRmAccount`。 系统会提示输入 Azure 帐户凭据，并且可能需要使用双因素身份验证，具体取决于帐户的配置。

   [!include[Remove Account](../../includes/remove-account.md)]

3. 如果有多个订阅，请运行以下命令来选择注册所用的订阅：  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID 'Your Azure Subscription GUID' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. 使用以下脚本下载最新版本的 marketplace 联合工具：  

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master
   ```

5. 导入联合模块，并通过运行以下命令来启动该工具。 将 `Destination folder path` 替换为存储从 Azure Marketplace 下载的文件的位置。

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
   ```

   请注意，`Export-AzSOfflineMarketplaceItem` 有一个指定云环境的附加 `-cloud` 标志。 默认情况下，它是**azurecloud**。

6. 当该工具运行时，应会看到类似于下图的屏幕，其中包含可用的 Azure marketplace 项列表：

   [![Azure Marketplace 项弹出窗口](media/azure-stack-download-azure-marketplace-item/image05.png "Azure Marketplace 项")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. 如果尚未安装 Azure 存储工具，你将看到以下消息。 若要安装这些工具，请确保下载[AzCopy](/azure/storage/common/storage-use-azcopy#download-azcopy)：

   ![存储工具](media/azure-stack-download-azure-marketplace-item/vmnew1.png)

8. 选择要下载的项目，并记下该**版本**。 您可以按住**Ctrl**键来选择多个图像。 在下一过程中导入项时，引用该*版本*。

   还可以通过使用 "**添加条件**" 选项来筛选映像列表。

9. 选择 **"确定"** ，然后查看并接受法律条款。

10. 下载所用的时间取决于项的大小。 下载完成后，该项目在脚本中指定的文件夹中可用。 下载内容包括 VHD 文件（用于虚拟机）或 .zip 文件（用于虚拟机扩展）。 它还可能包含 *.azpkg*格式的库包，该格式只是一个 .zip 文件。

11. 如果下载失败，你可以通过重新运行以下 PowerShell cmdlet 重试：

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    在重试之前，请删除下载失败的产品文件夹。 例如，如果下载脚本在下载到 `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`时失败，请删除 `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` 文件夹，然后重新运行 cmdlet。

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-using-powershell"></a>使用 PowerShell 导入下载并发布到 Azure Stack Marketplace

1. 您必须将[以前下载](#use-the-marketplace-syndication-tool-to-download-marketplace-items)的文件移动到您的 Azure Stack 环境中。 Marketplace 联合工具还必须可用于你的 Azure Stack 环境，因为你需要使用该工具来执行导入操作。

   下图显示了一个文件夹结构示例。 `D:\downloadfolder` 包含所有下载的 marketplace 项。 每个子文件夹都是由产品 ID 命名的 marketplace 项（例如 `microsoft.custom-script-linux-arm-2.0.3`）。 每个子文件夹内都是 marketplace 项的下载内容。

   [![Marketplace 下载目录结构](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Marketplace 下载目录结构")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. 按照[本文](azure-stack-powershell-configure-admin.md)中的说明配置 Azure Stack Operator PowerShell 会话。

3. 导入联合模块，并通过运行以下脚本来启动 marketplace 联合工具：

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   `-origin` 参数指定包含所有已下载产品的顶层文件夹;例如，`"D:\downloadfolder"`。

   `-AzsCredential` 参数是可选的。 如果该令牌已过期，则使用它来续订访问令牌。 如果未指定 `-AzsCredential` 参数并且令牌过期，则会提示输入操作员凭据。

    > [!NOTE]  
    > AD FS 仅支持具有用户标识的交互式身份验证。 如果需要 credential 对象，则必须使用服务主体（SPN）。 有关使用 Azure Stack 和 AD FS 作为标识管理服务设置服务主体的详细信息，请参阅[管理 AD FS 服务主体](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal)。

4. 脚本成功完成后，Azure Stack Marketplace 中应提供此项。

## <a name="next-steps"></a>后续步骤

- [添加自定义虚拟机映像](azure-stack-add-vm-image.md)
- [创建和发布自定义 Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)
