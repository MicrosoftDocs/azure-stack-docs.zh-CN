---
title: 如何备份 Azure Stack 上的存储帐户 |Microsoft Docs
description: 了解如何在 Azure Stack 上备份存储帐户。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 3f6ed5604bd2d32d9d030ceb4b5f67567362cc34
ms.sourcegitcommit: 4d7611d81da6f2f8ef50adab3c09f9122a75bc9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73145758"
---
# <a name="back-up-your-storage-accounts-on-azure-stack"></a>Azure Stack 上备份存储帐户

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍如何保护和恢复 Azure Stack 上的 Azure 存储帐户中的存储帐户。

## <a name="elements-of-the-solution"></a>解决方案的元素

本部分介绍解决方案的整体结构和主要部分。

![Azure Stack 存储备份](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>应用程序层

可以通过发出多个[PUT Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)或[put 块](https://docs.microsoft.com/rest/api/storageservices/put-block)操作将对象写入多个位置，在不同 Azure Stack 缩放单位上的存储帐户之间复制数据。 此外，应用程序还可以发出[复制 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作，将 blob 复制到主帐户的 Put 操作完成后在单独的扩展单元上承载的存储帐户。

### <a name="scheduled-copy-task"></a>计划的复制任务

AzCopy 是一个极佳的工具，可用于从本地文件系统、Azure 云存储、Azure Stack 存储和 s3 复制数据。 目前，AzCopy 无法在两个 Azure Stack 的存储帐户之间复制数据。 将对象从源 Azure Stack 存储帐户复制到目标 Azure Stack 存储帐户需要中间本地文件系统。

有关详细信息，请参阅在[Azure Stack 存储中使用数据传输工具](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer?view=azs-1908#azcopy)一文中的 AzCopy。

### <a name="azure-stack-source"></a>Azure Stack （源）

这是要备份的存储帐户数据的源。

需要源存储帐户 URL 和 SAS 令牌。 有关使用存储帐户的说明，请参阅[Azure Stack 存储开发工具入门](azure-stack-storage-dev.md)。

### <a name="azure-stack-target"></a>Azure Stack （目标）

这是将存储要备份的帐户数据的目标。 目标 Azure Stack 实例必须与目标 Azure Stack 位于不同位置。 源将需要能够连接到目标。

需要源存储帐户 URL 和 SAS 令牌。 有关使用存储帐户的说明，请参阅[Azure Stack 存储开发工具入门](azure-stack-storage-dev.md)。

### <a name="intermediary-local-filesystem"></a>中间本地文件系统

需要一个位置来运行 AzCopy，并在从源复制到目标 Azure Stack 时存储数据。 这是源 Azure Stack 中的中间服务器。

可以创建 Linux 或 Windows 服务器作为中间服务器。 服务器需要有足够的空间来存储源存储帐户容器中的所有对象。
- 有关设置 Linux 服务器的说明，请参阅[使用 Azure Stack 门户创建 linux 服务器 VM](azure-stack-quick-linux-portal.md)。  
- 有关设置 Windows Server 的说明，请参阅[使用 Azure Stack 门户创建 windows SERVER VM](azure-stack-quick-windows-portal.md)。  

设置 Windows Server 后，将需要安装[Azure Stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json)和[Azure Stack 工具](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json)。

## <a name="set-up-backup-for-storage-accounts"></a>为存储帐户设置备份

1. 检索源和目标存储帐户的 Blob 终结点。

    ![Azure Stack 存储备份](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. 为源和目标存储帐户创建并记录 SAS 令牌。

    ![Azure Stack 存储备份](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. 在中介服务器上安装[AzCopy](https://github.com/Azure/azure-storage-azcopy) ，并将 API 版本设置为 Azure Stack 存储帐户的帐户。

    - 对于 Windows server：

    ```PowerShell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09 PowerShell use: $env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"
    ```

    - 对于 Linux （Ubuntu）服务器：

    ```bash  
    export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    ```

4. 在中间服务器上，创建脚本。 请将此命令与你的**存储帐户**、 **SAS 密钥**和**本地目录路径**一起更新。 你将运行该脚本，以便以增量方式从源**存储帐户**复制数据。

    ```
    azcopy sync "https:/<storagaccount>/<container>?<SAS Key>" "C:\\myFolder" --recursive=true --delete-destination=true
    ```

5.  输入**存储帐户**、* * SAS 密钥 * * 和 * * 本地目录路径。  你将使用此数据以增量方式将数据复制到**目标**存储帐户
    
    ```
    azcopy sync "C:\\myFolder" "https:// <storagaccount>/<container>?<SAS Key>" --recursive=true --delete-destination=true
    ```

6.  使用 Cron 或 Windows 任务计划程序将源 Azure Stack 存储帐户的复制计划为中间服务器上的本地存储。 然后从中间服务器中的本地存储复制到目标 Azure Stack 存储帐户。

    可以使用此解决方案实现的 RPO 由/MO 参数值和源帐户与中介服务器以及中间服务器与目标帐户之间的网络带宽确定。

    - 对于 Linux （Ubuntu）服务器：

    ```bash  
    schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\\&lt;script name>.bat
    ```

    | 参数 | 说明 | 
    | ---- | ---- |
    | /SC | 使用分钟计划。 |
    | /月 | 间隔为*XX*分钟。 |
    | /TN | 任务名称。 |
    | /TR | `script.bat` 文件的路径。 |


    - 对于 Windows server：

    有关使用 Windows 任务计划的信息，请参阅面向[开发人员的任务计划程序](https://docs.microsoft.com/windows/win32/taskschd/task-scheduler-start-page)
    

## <a name="use-your-storage-account-in-a-disaster"></a>在灾难中使用存储帐户

每个 Azure Stack 存储帐户都拥有一个唯一的 DNS 名称，该名称派生自 Azure Stack 区域本身的名称，例如，`https://krsource.blob.east.asicdc.com/`。 当目标帐户（例如 `https://krtarget.blob.west.asicdc.com/` 需要在发生灾难期间使用）时，写入此 DNS 名称和从此 DNS 名称读取的应用程序将需要容纳存储帐户 DNS 名称更改。

在声明灾难后，可以修改应用程序连接字符串，以便考虑重定位对象，或者，如果在负载均衡器前面使用 CNAME 记录，并在源和目标存储帐户的前面结束，则可以配置负载均衡器使用手动故障转移算法，使管理员能够声明目标

如果应用程序使用 SAS 而不是 AAD 或 AD FS，上述方法将不起作用，并且需要使用目标存储帐户 URL 和为目标存储帐户生成的 SAS 密钥更新应用程序连接字符串。

## <a name="next-steps"></a>后续步骤

[Azure Stack 存储开发工具入门](azure-stack-storage-dev.md)