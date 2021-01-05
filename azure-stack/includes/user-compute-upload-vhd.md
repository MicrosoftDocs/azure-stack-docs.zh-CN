---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/16/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: b8c5c7f439c4526b44299e6e3b33b343b11b61ed
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867264"
---
可以使用门户上传 VHD，或是对于在门户中创建的容器使用 AzCopy。

### <a name="portal-to-generate-sas-url-and-upload-vhd"></a>用于生成 SAS URL 并上传 VHD 的门户

1. 登录到 Azure Stack Hub 用户门户。

2. 选择“存储帐户”，并选择现有存储帐户，或创建新存储帐户。

3. 在存储帐户的存储帐户边栏选项卡中选择“Blob”。 选择“容器”来新建容器。

4. 键入容器的名称，然后选择“Blob (仅匿名读取访问 blob)”。

5. 如果要使用 AzCopy 上传映像而不是门户，请创建 SAS 令牌。 在存储帐户中选择“共享访问签名”，然后选择“生成 SAS 和连接字符串” 。 复制并记下“Blob 服务 SAS URL”。 使用 AzCopy 上传 VHD 时，将使用此 URL。

6. 选择容器，然后选择“上传”。 上传 VHD。

### <a name="azcopy-vhd"></a>AzCopy VHD

使用 Azure 存储资源管理器或 AzCopy 可减少 VHD 在上传过程中损坏的可能性，并且上传速度会更快。 以下步骤在 Windows 10 计算机上使用 AzCopy。 AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。

1. 如果尚未安装 AzCopy，请安装 AzCopy。 [AzCopy 入门](/azure/storage/common/storage-use-azcopy-v10)一文中提供了用于下载和开始使用 AzCopy 的说明。 记下二进制文件的存储位置。 可以[将 AzCopy 添加到路径](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/)，以便从 PowerShell 命令行使用它。

2. 打开 PowerShell，以便从 shell 使用 AzCopy。

3. 使用 AzCopy 将 VHD 上传到存储帐户中的容器中。

    ```powershell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    azcopy cp "/path/to/file.vhd" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS] --blob-type=PageBlob
    ```

> [!NOTE]  
> 使用类似于将单个文件上传到虚拟目录的语法上传 VHD。 添加 `--blob-type=PageBlob` 以确保 VHD 作为“页 Blob”上传，而不是默认情况下的“块” 。

有关使用 AzCopy 和其他存储工具的详细信息，请参阅[在 Azure Stack Hub 存储中使用数据传输工具](../user/azure-stack-storage-transfer.md)。