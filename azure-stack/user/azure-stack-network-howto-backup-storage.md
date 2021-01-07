---
title: 在 Azure Stack Hub 上备份存储帐户
description: 了解如何在 Azure Stack Hub 上备份存储帐户。
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 6bbca7a38e23a0dfadcd0e7654430430d55da3f2
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974058"
---
# <a name="back-up-your-storage-accounts-on-azure-stack-hub"></a>在 Azure Stack Hub 上备份存储帐户

本文探讨如何保护和恢复 Azure Stack Hub 上 Azure 存储帐户中的存储帐户。

## <a name="elements-of-the-solution"></a>解决方案的要素

本部分探讨解决方案的整体结构和主要组成部分。

![该图显示 Azure Stack Hub 存储备份的总体结构。](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>应用层

可以发出多个 [PUT Blob](/rest/api/storageservices/put-blob) 或 [PUT Block](/rest/api/storageservices/put-block) 操作将对象写入到多个位置，以在不同 Azure Stack Hub 缩放单元上的存储帐户之间复制数据。 或者，应用程序可以发出 [Copy Blob](/rest/api/storageservices/copy-blob) 操作，以便在主要帐户的放置操作完成后，将 Blob 复制到托管在不同缩放单元上的存储帐户。

### <a name="scheduled-copy-task"></a>计划的复制任务

AzCopy 是一个极佳的工具，可用于复制本地文件系统、Azure 云存储、Azure Stack Hub 存储和 s3 中的数据。 目前，AzCopy 无法在两个 Azure Stack Hub 存储帐户之间复制数据。 将对象从源 Azure Stack Hub 存储帐户复制到目标 Azure Stack Hub 存储帐户需有一个中间本地文件系统。

有关详细信息，请参阅[在 Azure Stack Hub 存储中使用数据传输工具](./azure-stack-storage-transfer.md#azcopy)一文中的“AzCopy”。

### <a name="azure-stack-hub-source"></a>Azure Stack Hub（源）

这是要备份的存储帐户数据的源。

需要提供源存储帐户 URL 和 SAS 令牌。 有关如何使用存储帐户的说明，请参阅 [Azure Stack Hub 存储开发工具入门](azure-stack-storage-dev.md)。

### <a name="azure-stack-hub-target"></a>Azure Stack Hub（目标）

这是用于存储所要备份帐户数据的目标。 目标 Azure Stack Hub 实例必须与目标 Azure Stack Hub 位于不同的位置。 源需要能够连接到目标。

需要提供源存储帐户 URL 和 SAS 令牌。 有关如何使用存储帐户的说明，请参阅 [Azure Stack Hub 存储开发工具入门](azure-stack-storage-dev.md)。

### <a name="intermediary-local-filesystem"></a>中间本地文件系统

需要提供一个位置来运行 AzCopy，在从源复制并写入到目标 Azure Stack Hub 时，还要使用此位置来存储数据。 这是源 Azure Stack Hub 中的中间服务器。

可以创建 Linux 或 Windows 服务器作为中间服务器。 该服务器需有足够的空间，以便能够存储源存储帐户容器中的所有对象。
- 有关设置 Linux 服务器的说明，请参阅[使用 Azure Stack Hub 门户创建 Linux 服务器 VM](azure-stack-quick-linux-portal.md)。  
- 有关设置 Windows 服务器的说明，请参阅[使用 Azure Stack Hub 门户创建 Windows 服务器 VM](azure-stack-quick-windows-portal.md)。  

设置 Windows 服务器之后，需要安装 [Azure Stack Hub PowerShell](../operator/powershell-install-az-module.md?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json) 和 [Azure Stack Hub 工具](../operator/azure-stack-powershell-download.md?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json)。

## <a name="set-up-backup-for-storage-accounts"></a>为存储帐户设置备份

1. 检索源和目标存储帐户的 Blob 终结点。

    ![该屏幕截图显示源和目标存储帐户的主 Blob 终结点。](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. 创建并记下源和目标存储帐户的 SAS 令牌。

    ![Azure Stack Hub 存储备份](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. 在中间服务器上安装 [AzCopy](https://github.com/Azure/azure-storage-azcopy)，并将“API 版本”设置为 Azure Stack Hub 存储帐户的帐户。

    - 对于 Windows 服务器：

    ```PowerShell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09 PowerShell use: $env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"
    ```

    - 对于 Linux (Ubuntu) 服务器：

    ```bash  
    export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    ```

4. 在中间服务器上创建脚本。 使用 **存储帐户**、**SAS 密钥** 和 **本地目录路径** 更新此命令。 稍后需要运行该脚本以增量方式从 **源** 存储帐户复制数据。

    ```
    azcopy sync "https:/<storagaccount>/<container>?<SAS Key>" "C:\\myFolder" --recursive=true --delete-destination=true
    ```

5.  输入 **存储帐户**、**SAS 密钥**和**本地目录路径。  稍后将使用此信息以增量方式将数据复制到 **目标** 存储帐户
    
    ```
    azcopy sync "C:\\myFolder" "https:// <storagaccount>/<container>?<SAS Key>" --recursive=true --delete-destination=true
    ```

6.  使用 Cron 或 Windows 任务计划程序，来计划从源 Azure Stack Hub 存储帐户复制到中间服务器上的本地存储。 然后，从中间服务器中的本地存储复制到目标 Azure Stack Hub 存储帐户。

    使用此解决方案可以实现的 RPO 取决于 /MO 参数值，以及源帐户与中间服务器和中间服务器与目标帐户之间的网络带宽。

    - 对于 Linux (Ubuntu) 服务器：

    ```bash  
    schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\\&lt;script name>.bat
    ```

    | 参数 | 注意 | 
    | ---- | ---- |
    | /SC | 使用分钟计划。 |
    | /MO | *XX* 分钟间隔。 |
    | /TN | 任务名称。 |
    | /TR | `script.bat` 文件的路径。 |


    - 对于 Windows 服务器：

    有关如何使用 Windows 任务计划的信息，请参阅[面向开发人员的任务计划程序](/windows/win32/taskschd/task-scheduler-start-page)
    

## <a name="use-your-storage-account-in-a-disaster"></a>发生灾难时使用存储帐户

每个 Azure Stack Hub 存储帐户都有唯一的 DNS 名称，此名称派生自 Azure Stack Hub 区域本身的名称，例如 `https://krsource.blob.east.asicdc.com/`。 如果在发生灾难期间需要使用目标帐户（例如 `https://krtarget.blob.west.asicdc.com/`），通过此 DNS 名称写入和读取数据的应用程序需要适应存储帐户的 DNS 名称更改。

在对帐户声明发生灾难之后可以修改应用程序连接字符串以便重新放置对象，或者，如果在对源和目标存储帐户进行前端处理的负载均衡器前面使用 CNAME 记录，则可以为负载均衡器配置手动故障转移算法，使管理员能够声明目标

如果是应用程序而不是 AAD 或 AD FS 在使用 SAS，则上述方法不适用，需要使用为目标存储帐户生成的目标存储帐户 URL 和 SAS 密钥来更新应用程序连接字符串。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 存储开发工具入门](azure-stack-storage-dev.md)
