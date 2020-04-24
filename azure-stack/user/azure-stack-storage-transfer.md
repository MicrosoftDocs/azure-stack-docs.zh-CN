---
title: 在 Azure Stack Hub 存储中使用数据传输工具
description: 了解 Azure Stack Hub 存储数据传输工具。
author: mattbriggs
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: d61ce13c46e53f5eb2b9ed8fa544096db6ca8590
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81660157"
---
# <a name="use-data-transfer-tools-in-azure-stack-hub-storage"></a>在 Azure Stack Hub 存储中使用数据传输工具

Azure Stack Hub 提供一组存储服务，适用于磁盘、Blob、表、队列以及帐户管理功能。 如果需要通过 Azure Stack Hub 存储管理或移动数据，可以使用一些 Azure 存储工具。 本文概述了可用的工具。

你的需求决定了以下哪些工具最适合你：

* [AzCopy](#azcopy)

    一个特定于存储的命令行实用工具，下载后即可在存储帐户中将数据从一个对象复制到另一个对象，或者在存储帐户之间复制。

* [Azure PowerShell](#azure-powershell)

    一种基于任务的命令行 Shell 和脚本语言，专为系统管理而设计。

* [Azure CLI](#azure-cli)

    一种开源的跨平台工具，提供了一组适用于 Azure 和 Azure Stack Hub 平台的命令。

* [Microsoft 存储资源管理器](#microsoft-azure-storage-explorer)

    一个易于使用的独立应用，带有用户界面。

* [Blobfuse](#blobfuse)

    一个适用于 Azure Blob 存储的虚拟文件系统驱动程序，用于通过 Linux 文件系统访问存储帐户中的现有块 Blob 数据。

由于 Azure 和 Azure Stack Hub 之间具有存储服务差异，因此，以下部分中描述的每个工具可能存在一些特定的要求。 若要了解 Azure Stack Hub 存储和 Azure 存储之间的比较情况，请参阅 [Azure Stack Hub 存储：差异和注意事项](azure-stack-acs-differences.md)。

## <a name="azcopy"></a>AzCopy

AzCopy 是一个命令行实用程序，旨在使用具有最佳性能的简单命令将数据复制到 Microsoft Azure blob 和表存储以及从中复制数据。 可在存储帐户中将数据从一个对象复制到另一个对象，或者在存储帐户之间复制。

### <a name="download-and-install-azcopy"></a>下载并安装 AzCopy

::: moniker range=">=azs-1811"
* 对于1811更新或更高版本，请[下载 AzCopy V10 +](/azure/storage/common/storage-use-azcopy-v10#download-azcopy)。
::: moniker-end

::: moniker range="<azs-1811"
* 对于以前的版本（1802 到 1809 更新），请[下载 AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417)。
::: moniker-end

### <a name="azcopy-101-configuration-and-limits"></a>AzCopy 10.1 配置和限制

AzCopy 10.1 现在可以配置为使用旧版 API。 这样就可以为 Azure Stack Hub 提供（有限的）支持。
若要将 AzCopy 的 API 版本配置为支持 Azure Stack Hub，请将 `AZCOPY_DEFAULT_SERVICE_API_VERSION` 环境变量设置为 `2017-11-09`。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | 在命令提示符处使用 `set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`<br> 在 PowerShell 中使用 `$env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"`|
| **Linux** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |
| **MacOS** | `export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09` |

在 AzCopy 10.1 中，支持适用于 Azure Stack Hub 的以下功能：

| 功能 | 支持的操作 |
| --- | --- |
|管理容器|创建容器<br>列出容器的内容
|管理作业|显示作业<br>恢复作业
|删除 Blob|删除单个 Blob<br>删除整个或部分虚拟目录
|上传文件|上传文件<br>上传目录<br>上传目录的内容
|下载文件|下载文件<br>下载目录<br>下载目录的内容
|同步文件|将容器同步到本地文件系统<br>将本地文件系统同步到容器

   > [!NOTE]
   > * Azure Stack Hub 不支持使用 Azure Active Directory (AD) 向 AzCopy 提供授权凭据。 必须使用共享访问签名 (SAS) 令牌访问 Azure Stack Hub 上的存储对象。
   > * Azure Stack Hub 不支持在两个 Azure Stack Hub Blob 位置之间以及 Azure 存储和 Azure Stack Hub 之间进行同步数据传输。 不能直接在 AzCopy 10.1 中使用“azcopy cp”将数据从 Azure Stack Hub 移到 Azure 存储（反之亦然）。

### <a name="azcopy-command-examples-for-data-transfer"></a>针对数据传输的 AzCopy 命令示例

以下示例展示了将数据复制到 Azure Stack Hub Blob 以及从这些位置复制数据的典型方案。 若要了解详细信息，请参阅 [AzCopy 入门](/azure/storage/common/storage-use-azcopy-v10)。

### <a name="download-all-blobs-to-a-local-disk"></a>将所有 Blob 下载到本地磁盘

```
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

### <a name="upload-single-file-to-virtual-directory"></a>将单个文件上传到虚拟目录

```
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

### <a name="azcopy-known-issues"></a>AzCopy 已知问题

 - 在文件存储上执行的任何 AzCopy 操作都不可用，因为文件存储在 Azure Stack Hub 中不可用。
 - 如果要在两个 Azure Stack 集线器 blob 位置之间传输数据，或使用 AzCopy 10.1 在 Azure Stack 中心与 Azure 存储之间传输数据，则需要先将数据下载到本地位置，然后重新上传到 Azure Stack 中心或 Azure 存储上的目标目录。 也可使用 AzCopy 7.1 通过 **/SyncCopy** 选项来指定传输，以便复制数据。  
 - AzCopy 的 Linux 版本仅支持 1802 更新或更高版本，不支持表服务。
 - 如果要向/从 Azure 表存储服务复制数据，则[安装 AzCopy 版本 7.3.0](https://aka.ms/azcopyforazurestack20171109)
 
## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 是一个模块，它提供的 cmdlet 用于管理 Azure 和 Azure Stack Hub 上的服务。 这是一种基于任务的命令行 Shell 和脚本语言，专为系统管理而设计。

### <a name="install-and-configure-powershell-for-azure-stack-hub"></a>安装和配置适用于 Azure Stack Hub 的 PowerShell

需要安装与 Azure Stack Hub 兼容的 Azure PowerShell 模块才能使用 Azure Stack Hub。 有关详细信息，请参阅[安装适用于 Azure Stack Hub 的 PowerShell](../operator/azure-stack-powershell-install.md) 和[配置 Azure Stack Hub 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)。

### <a name="powershell-sample-script-for-azure-stack-hub"></a>适用于 Azure Stack Hub 的 PowerShell 示例脚本 

此示例假定你已成功[安装了适用于 Azure Stack Hub 的 PowerShell](../operator/azure-stack-powershell-install.md)。 此脚本会帮助你完成配置，然后要求你提供 Azure Stack Hub 租户凭据，以便将你的帐户添加到本地 PowerShell 环境。 然后，该脚本会设置默认的 Azure 订阅、在 Azure 中创建新的存储帐户、在此新的存储帐户中创建新容器，并将现有图像文件 (Blob) 上传到该容器。 在脚本列出该容器中的所有 Blob 后，它会在本地计算机中创建新的目标目录，并下载图像文件。

1. 安装 [Azure Stack Hub 兼容的 Azure PowerShell 模块](../operator/azure-stack-powershell-install.md)。
2. 下载[使用 Azure Stack Hub 所需的工具](../operator/azure-stack-powershell-download.md)。
3. 打开 **Windows PowerShell ISE**，选择“以管理员身份运行”，  然后单击“文件”   >   “新建”以创建新的脚本文件。
4. 复制下面的脚本并将其粘贴到新的脚本文件。
5. 根据配置设置更新脚本变量。
   > [!NOTE]
   > 此脚本必须在 **AzureStack_Tools** 的根目录中运行。

```powershell  
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack Hub environment name
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
# Register an AzureRM environment that targets your Azure Stack Hub instance
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

目前兼容的 Azure Stack Hub 的 Azure PowerShell 模块版本为 1.2.11，用于用户操作。 它不同于最新版本的 Azure PowerShell。 这种差异以下述方式影响存储服务操作：

在版本 1.2.11 中，`Get-AzureRmStorageAccountKey` 的返回值格式有两个属性：`Key1` 和 `Key2`，而当前的 Azure 版本返回的数组包含所有帐户密钥。

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

有关详细信息，请参阅 [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 是 Azure 的命令行体验，用于管理 Azure 资源。 可以将其安装在 macOS、Linux 和 Windows 上，然后从命令行运行。

Azure CLI 经过优化，可用于从命令行管理 Azure 资源，以及生成可以针对 Azure 资源管理器运行的自动化脚本。 它提供 Azure Stack Hub 门户所提供的许多功能，包括各种数据访问功能。

Azure Stack Hub 需要 Azure CLI 2.0 版或更高版本。 若要详细了解如何通过 Azure Stack Hub 来安装和配置 Azure CLI，请参阅[安装和配置 Azure Stack Hub CLI](azure-stack-version-profiles-azurecli2.md)。 有关如何使用 Azure CLI 来执行多个任务（使用 Azure Stack 中心存储帐户中的资源）的详细信息，请参阅[将 Azure CLI 与 Azure 存储配合使用](/azure/storage/storage-azure-cli)。

### <a name="azure-cli-sample-script-for-azure-stack-hub"></a>适用于 Azure Stack Hub 的 Azure CLI 示例脚本

完成 CLI 安装和配置后，可尝试以下步骤，使用一个小的 shell 示例脚本来与 Azure Stack Hub 存储资源交互。 此脚本完成以下操作：

* 在存储帐户中创建一个新容器。
* 将一个现有文件（作为 Blob）上传到该容器。
* 列出该容器中的所有 Blob。
* 将文件下载到本地计算机上的指定目标。

运行此脚本之前，请确保可以成功连接并登录到目标 Azure Stack Hub。

1. 打开偏好的文本编辑器，将前面的脚本复制并粘贴到编辑器中。
2. 更新脚本的变量，使之反映配置设置。
3. 更新所需的变量后，保存脚本并退出编辑器。 后续步骤假定已将脚本命名为 my_storage_sample.sh  。
4. 如果需要，请将脚本标记为可执行文件：`chmod +x my_storage_sample.sh`
5. 执行该脚本。 例如，在 Bash 中： `./my_storage_sample.sh`

```azurecli
#!/bin/bash
# A simple Azure Stack Hub storage example script

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

Azure 存储资源管理器是 Microsoft 提供的独立应用， 它可用来在 Windows、macOS 和 Linux 计算机上轻松处理 Azure 存储和 Azure Stack Hub 存储数据。 如果希望通过某种方式轻松管理 Azure Stack Hub 存储数据，请考虑使用 Microsoft Azure 存储资源管理器。

* 若要详细了解如何配置 Azure 存储资源管理器，使之能够用于 Azure Stack Hub，请参阅[将存储资源管理器连接到 Azure Stack Hub 订阅](azure-stack-storage-connect-se.md)。
* 若要详细了解 Microsoft Azure 存储资源管理器，请参阅[存储资源管理器入门](/azure/vs-azure-tools-storage-manage-with-storage-explorer)

## <a name="blobfuse"></a>Blobfuse 

[Blobfuse](https://github.com/Azure/azure-storage-fuse) 是适用于 Azure Blob 存储的虚拟文件系统驱动程序，用于通过 Linux 文件系统访问存储帐户中的现有块 Blob 数据。 Azure Blob 存储是一项对象存储服务，因此没有分层命名空间。 Blobfuse 使用虚拟目录方案提供这种命名空间，并使用正斜杠“`/`”作为分隔符。 Blobfuse 适用于 Azure 和 Azure Stack 中心。 

若要详细了解如何使用 Linux 上的 Blobfuse 将 Blob 存储装载为文件系统，请参阅[如何使用 Blobfuse 将 Blob 存储装载为文件系统](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)。 

对于 Azure Stack 中心，需要在配置存储帐户凭据时指定*blobEndpoint* ，同时配置 AccountName、AccountKey/SasToken 和容器名。

在 Azure Stack 开发工具包 (ASDK) 中，*blobEndpoint* 应当为 `myaccount.blob.local.azurestack.external`。 在 Azure Stack 集线器集成系统中，如果你不确定终结点，请与云管理员联系。

*accountKey* 和 *sasToken* 一次只能配置一个。 提供存储帐户密钥时，凭据配置文件采用以下格式：

```
accountName myaccount 
accountKey myaccesskey== 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

提供共享访问密钥时，凭据配置文件采用以下格式：

```  
accountName myaccount 
sasToken ?mysastoken 
containerName mycontainer 
blobEndpoint myaccount.blob.local.azurestack.external
```

## <a name="next-steps"></a>后续步骤

* [将存储资源管理器连接到 Azure Stack 中心订阅](azure-stack-storage-connect-se.md)
* [存储资源管理器入门](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [与 Azure 一致的存储：差异和注意事项](azure-stack-acs-differences.md)
* [Microsoft Azure 存储简介](/azure/storage/common/storage-introduction)
