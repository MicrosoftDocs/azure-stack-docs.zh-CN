---
title: 使用 Azure Stack 存储中的数据传输工具 |Microsoft Docs
description: 了解 Azure Stack 存储数据传输工具。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: e9b474a47c0ab80d34330aff463bcd9d8ada5ab8
ms.sourcegitcommit: 8203490cf3ab8a8e6d39b137c8c31e3baec52298
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73712745"
---
# <a name="use-data-transfer-tools-in-azure-stack-storage"></a>使用 Azure Stack 存储中的数据传输工具

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 提供了一组存储服务，适用于磁盘、blob、表、队列和帐户管理功能。 如果要在 Azure Stack 存储中管理或移入或移出数据，则可以使用一些 Azure 存储工具。 本文概述了可用的工具。

你的需求决定以下哪种工具最适合你：

* [AzCopy](#azcopy)

    一种特定于存储的命令行实用程序，可以下载它，以将数据从一个对象复制到存储帐户中或存储帐户之间的另一个对象。

* [Azure PowerShell](#azure-powershell)

    一种基于任务的命令行 shell 和脚本语言，专门用于系统管理。

* [Azure CLI](#azure-cli)

    一种开源的跨平台工具，提供一组用于使用 Azure 和 Azure Stack 平台的命令。

* [Microsoft 存储资源管理器](#microsoft-azure-storage-explorer)

    使用用户界面的易于使用的独立应用程序。

* [Blobfuse](#blobfuse)

    用于 Azure Blob 存储的虚拟文件系统驱动程序，它允许你通过 Linux 文件系统访问你的存储帐户中的现有块 Blob 数据。

由于 Azure 与 Azure Stack 之间的存储服务差异，以下各节中所述的每个工具可能有一些特定的要求。 有关 Azure Stack 存储与 Azure 存储之间的比较，请参阅[Azure Stack 存储：差异和注意事项](azure-stack-acs-differences.md)。

## <a name="azcopy"></a>AzCopy

AzCopy 是一个命令行实用程序，旨在使用具有最佳性能的简单命令将数据复制到 Microsoft Azure blob 和表存储以及从中复制数据。 你可以将数据从一个对象复制到存储帐户中的另一个对象。

### <a name="download-and-install-azcopy"></a>下载并安装 AzCopy

::: moniker range=">=azs-1811"
* 对于1811更新或更高版本，请[下载 AzCopy V10 +](/azure/storage/common/storage-use-azcopy-v10#download-azcopy)。
::: moniker-end

::: moniker range="<azs-1811"
* 对于先前版本（1802到1809更新），请[下载 AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417)。
::: moniker-end

### <a name="azcopy-101-configuration-and-limits"></a>AzCopy 10.1 配置和限制

AzCopy 10.1 现在可以配置为使用较旧的 API 版本。 这为 Azure Stack 启用（有限）支持。
若要配置 AzCopy 的 API 版本以支持 Azure Stack，请将 `AZCOPY_DEFAULT_SERVICE_API_VERSION` 环境变量设置为 `2017-11-09`。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | 在命令提示符下，使用： `set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`<br> 在 PowerShell 中使用： `$env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"`|
| **Linux** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |
| **MacOS** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |

在 AzCopy 10.1 中，Azure Stack 支持以下功能：

| Feature | 支持的操作 |
| --- | --- |
|管理容器|创建容器<br>列出容器的内容
|管理作业|显示作业<br>恢复作业
|删除 blob|删除单个 blob<br>删除整个或部分虚拟目录
|上传文件|上传文件<br>上传目录<br>上传目录的内容
|下载文件|下载文件<br>下载目录<br>下载目录的内容
|同步文件|将容器同步到本地文件系统<br>将本地文件系统同步到容器

   > [!NOTE]
   > * Azure Stack 不支持通过使用 Azure Active Directory （AD）向 AzCopy 提供授权凭据。 必须使用共享访问签名（SAS）令牌访问 Azure Stack 上的存储对象。
   > * Azure Stack 不支持两个 Azure Stack blob 位置之间以及 Azure 存储和 Azure Stack 之间的同步数据传输。 不能使用 "azcopy cp" 将数据从 Azure Stack 直接移到 Azure 存储（或其他方法），AzCopy 10.1。

### <a name="azcopy-command-examples-for-data-transfer"></a>用于数据传输的 AzCopy 命令示例

下面的示例遵循了将数据复制到 Azure Stack blob 和从中复制数据的典型方案。 若要了解详细信息，请参阅[AzCopy 入门](/azure/storage/common/storage-use-azcopy-v10)。

### <a name="download-all-blobs-to-a-local-disk"></a>将所有 blob 下载到本地磁盘

```
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

### <a name="upload-single-file-to-virtual-directory"></a>将单个文件上传到虚拟目录

```
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

### <a name="azcopy-known-issues"></a>AzCopy 的已知问题

 - 文件存储上的任何 AzCopy 操作都不可用，因为文件存储在 Azure Stack 中尚不可用。
 - 如果要在两个 Azure Stack blob 位置之间传输数据，或使用 AzCopy 10.1 在 Azure Stack 与 Azure 存储之间传输数据，则需要先将数据下载到本地位置，然后重新上传到 Azure Stack 或 Azure 存储上的目标目录。 或者，可以使用 AzCopy 7.1，并使用 **/SyncCopy**选项指定传输来复制数据。  
 - Linux 版本的 AzCopy 仅支持1802更新或更高版本，并且不支持表服务。
 - 如果要将数据复制到 Azure 表存储服务和从 Azure 表存储服务复制数据，请[安装 AzCopy 版本 7.3.0](https://aka.ms/azcopyforazurestack20171109)
 
## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 是一个模块，它提供用于管理 Azure 和 Azure Stack 上的服务的 cmdlet。 它是一种基于任务的命令行外壳和脚本语言，专门用于系统管理。

### <a name="install-and-configure-powershell-for-azure-stack"></a>为 Azure Stack 安装和配置 PowerShell

需要 Azure Stack 兼容 Azure PowerShell 模块才能处理 Azure Stack。 有关详细信息，请参阅[安装 powershell for Azure Stack](../operator/azure-stack-powershell-install.md)和[配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)。

### <a name="powershell-sample-script-for-azure-stack"></a>Azure Stack 的 PowerShell 示例脚本 

此示例假设已成功[为 Azure Stack 安装了 PowerShell](../operator/azure-stack-powershell-install.md)。 此脚本将帮助你完成配置，并请求你的 Azure Stack 租户凭据将你的帐户添加到本地 PowerShell 环境。 然后，该脚本将设置默认 Azure 订阅，在 Azure 中创建新的存储帐户，在此新存储帐户中创建新容器，并将现有图像文件（blob）上传到该容器。 脚本列出该容器中的所有 blob 后，它会在本地计算机上创建一个新的目标目录，并下载该映像文件。

1. 安装[兼容 Azure Stack 的 Azure PowerShell 模块](../operator/azure-stack-powershell-install.md)。
2. 下载[处理 Azure Stack 所需的工具](../operator/azure-stack-powershell-download.md)。
3. 打开**Windows PowerShell ISE**并**以管理员身份运行**，然后单击 "**文件**" > "**新建**" 以创建新的脚本文件。
4. 复制下面的脚本，并将其粘贴到新的脚本文件。
5. 根据你的配置设置更新脚本变量。
   > [!NOTE]
   > 此脚本必须在**AzureStack_Tools**的根目录下运行。

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environment name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudience = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\download" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>PowerShell 已知问题

Azure Stack 的当前兼容 Azure PowerShell 模块版本是用户操作的1.2.11。 它与最新版本的 Azure PowerShell 不同。 这种差异会按以下方式影响存储服务操作：

版本1.2.11 中 `Get-AzureRmStorageAccountKey` 的返回值格式具有两个属性： `Key1` 和 `Key2`，而当前 Azure 版本返回包含所有帐户密钥的数组。

```powershell
# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.4, and later versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Value[0]

# This command gets a specific key for a storage account, 
# and works for Azure PowerShell version 1.3.2, and previous versions.
(Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
-AccountName "MyStorageAccount").Key1
```

有关详细信息，请参阅[get-azurermstorageaccountkey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是 Azure 的命令行体验，用于管理 Azure 资源。 可以将其安装在 macOS、Linux 和 Windows 上，然后从命令行运行该命令。

Azure CLI 经过优化，可用于从命令行管理和管理 Azure 资源，以及生成适用于 Azure 资源管理器的自动化脚本。 它提供了许多在 Azure Stack 门户中找到的功能，包括丰富的数据访问。

Azure Stack 要求 Azure CLI 版本2.0 或更高版本。 有关安装和配置 Azure Stack Azure CLI 的详细信息，请参阅[安装和配置 AZURE STACK CLI](azure-stack-version-profiles-azurecli2.md)。 有关如何使用 Azure CLI 来执行多个任务（使用 Azure Stack 存储帐户中的资源）的详细信息，请参阅[将 Azure CLI 与 Azure 存储配合使用](/azure/storage/storage-azure-cli)。

### <a name="azure-cli-sample-script-for-azure-stack"></a>Azure Stack 的 Azure CLI 示例脚本

完成 CLI 安装和配置后，可以尝试执行以下步骤来使用小型 shell 示例脚本与 Azure Stack 存储资源进行交互。 此脚本将完成以下操作：

* 在存储帐户中创建一个新容器。
* 将现有文件（作为 blob）上传到该容器。
* 列出容器中的所有 blob。
* 将文件下载到你指定的本地计算机上的目标。

在运行此脚本之前，请确保可以成功连接并登录到目标 Azure Stack。

1. 打开最喜欢的文本编辑器，然后将前面的脚本复制并粘贴到编辑器中。
2. 更新脚本的变量以反映你的配置设置。
3. 更新必要的变量后，保存脚本并退出编辑器。 后续步骤假定已将脚本命名为 **my_storage_sample.sh**。
4. 如有必要，将脚本标记为可执行文件：`chmod +x my_storage_sample.sh`
5. 执行脚本。 例如，在 Bash 中：`./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 存储资源管理器

Azure 存储资源管理器是 Microsoft 的独立应用。 它可让你轻松地在 Windows、macOS 和 Linux 计算机上使用 Azure 存储和 Azure Stack 存储数据。 如果你需要一种简单的方法来管理 Azure Stack 存储数据，请考虑使用 Microsoft Azure 存储资源管理器。

* 若要详细了解如何配置 Azure 存储资源管理器以使用 Azure Stack，请参阅[将存储资源管理器连接到 Azure Stack 订阅](azure-stack-storage-connect-se.md)。
* 若要详细了解 Microsoft Azure 存储资源管理器，请参阅[存储资源管理器入门](/azure/vs-azure-tools-storage-manage-with-storage-explorer)

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse)是 Azure Blob 存储的虚拟文件系统驱动程序，它允许你通过 Linux 文件系统访问你的存储帐户中的现有块 Blob 数据。 Azure Blob 存储是一个对象存储服务，因此没有分层命名空间。 Blobfuse 使用虚拟目录方案提供此命名空间，使用正斜杠 `/` 作为分隔符。 Blobfuse 适用于 Azure 和 Azure Stack。 

若要详细了解如何将 blob 存储作为文件系统装载到 Linux 上的 Blobfuse，请参阅[如何使用 Blobfuse 将 blob 存储装载为文件系统](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)。 

对于 Azure Stack，需要在配置存储帐户凭据时指定*blobEndpoint* ，同时配置 AccountName、AccountKey/SasToken 和容器名。

在 Azure Stack 开发工具包（ASDK）中，应 `myaccount.blob.local.azurestack.external`*blobEndpoint* 。 在 Azure Stack 集成的系统中，如果你不确定终结点，请与云管理员联系。

*accountKey*和*sasToken*一次只能配置一个。 指定存储帐户密钥后，凭据配置文件的格式如下：

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

提供共享访问令牌时，凭据配置文件采用以下格式：

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>后续步骤

* [将存储资源管理器连接到 Azure Stack 订阅](azure-stack-storage-connect-se.md)
* [存储资源管理器入门](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure 一致性存储：差异和注意事项](azure-stack-acs-differences.md)
* [Microsoft Azure 存储简介](/azure/storage/common/storage-introduction)
