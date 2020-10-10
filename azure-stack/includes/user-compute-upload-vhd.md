---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 28fe06ca182b2df8104c2d8b23ce9f5c27448064
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91936223"
---
你可以使用门户或在门户中创建的容器上传 VHD，请使用 AzCopy。

### <a name="portal-to-generate-sas-url-and-upload-vhd"></a>用于生成 SAS URL 并上传 VHD 的门户

1. 登录到 Azure Stack Hub 用户门户。

2. 选择 " **存储帐户** "，并选择现有的存储帐户或创建新的存储帐户。

3. 在存储帐户边栏选项卡中选择存储帐户的 **blob** 。 选择“容器”来新建容器。

4. 键入容器的名称，然后选择 " **blob (仅) 的 blob 的匿名读取访问 **。

5. 如果要使用 AzCopy 来上载映像而不是门户，请创建 SAS 令牌。 在存储帐户中选择 " **共享访问签名** "，然后选择 " **生成 SAS 和连接字符串**"。 复制并记下 " **Blob 服务 SAS URL**"。 使用 AzCopy 上传 VHD 时，将使用此 URL。

6. 选择容器，然后选择 " **上传**"。 上传 VHD。

### <a name="azcopy-vhd"></a>AzCopy VHD

使用 Azure 存储资源管理器或 AzCopy 减少 VHD 在上载过程中将会损坏的可能性，并且上载速度会更快。 以下步骤将在 Windows 10 计算机上使用 AzCopy。 AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。

1. 如果尚未安装 AzCopy，请安装 AzCopy。 可在 [AzCopy 入门](/azure/storage/common/storage-use-azcopy-v10)一文中找到有关下载和开始处理 AzCopy 的说明。 记下二进制文件的存储位置。 你可以 [将 AzCopy 添加到你的路径](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/) ，以便在 PowerShell 命令行中使用它。

2. 打开 PowerShell，使用 shell 中的 AzCopy。

3. 使用 AzCopy 将 VHD 上传到存储帐户中的容器。

    ```powershell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    azcopy cp "/path/to/file.vhd" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS] --blob-type=PageBlob
    ```

> [!NOTE]  
> 使用类似于将单个文件上传到虚拟目录的语法上传 VHD。 添加 `--blob-type=PageBlob` 以确保将 VHD 上传为 **页 Blob**，而不是默认情况下的 **块** 。

有关使用 AzCopy 和其他存储工具的详细信息，请参阅 [在 Azure Stack 中心存储中使用数据传输工具](../user/azure-stack-storage-transfer.md)。