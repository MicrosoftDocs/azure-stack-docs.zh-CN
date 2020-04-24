---
title: Azure Stack Hub 存储差异和注意事项
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack Hub 存储与 Azure 存储之间的差异，以及 Azure Stack Hub 部署注意事项。
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2020
ms.openlocfilehash: 47e8935e67185b91b73591a28ba3e453e827e708
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81394971"
---
# <a name="azure-stack-hub-storage-differences-and-considerations"></a>Azure Stack Hub 存储：差异和注意事项

Azure Stack 中心存储是 Microsoft Azure Stack 中心中的存储云服务集。 Azure Stack Hub 存储使用与 Azure 一致的语义来提供 Blob、表、队列和帐户管理功能。

本文汇总了 Azure Stack Hub 存储与 Azure 存储服务之间的已知差异。 它还列出了部署 Azure Stack Hub 时要考虑的事项。 若要了解全球 Azure 与 Azure Stack Hub 之间的大致差异，请参阅[重要注意事项](azure-stack-considerations.md)一文。

## <a name="cheat-sheet-storage-differences"></a>速查表：存储差异

| 功能 | Azure（公有云） | Azure Stack Hub |
| --- | --- | --- |
|文件存储|支持基于云的 SMB 文件共享。 | 尚不支持。
|静态数据的 Azure 存储服务加密|256 位 AES 加密。 支持在密钥保管库中使用客户管理的密钥进行加密。|BitLocker 128 位 AES 加密。 不支持使用客户托管密钥进行加密。
|存储帐户类型|常规用途 V1、V2 和 Blob 存储帐户。 |仅常规用途 V1。
|复制选项|本地冗余存储、异地冗余存储、读取访问异地冗余存储和区域冗余存储。 |本地冗余存储。
|高级存储|提供高性能和低延迟的存储。 仅支持高级存储帐户中的页 blob。|可预配，但无性能限制或保证。 不会阻止使用块 blob，在高级存储帐户中追加 blob、表和队列。
|托管磁盘|支持高级和标准。 |使用版本 1808 或更高版本时支持。
|Blob 名称|1024个字符（2048个字节）。 |880个字符（1760个字节）。
|块 Blob 大小上限|4.75 TB （100 MB X 50000 块）。 |4.75 TB（100 MB x 50,000 块），适用于 1802 更新或更高版本。 50,000 X 4 MB（约 195 GB），适用于以前的版本。
|页 Blob 快照复制|备份附加到运行中的 VM 的 Azure 非托管 VM 磁盘。 |尚不支持。
|页 Blob 增量快照复制|支持高级和标准 Azure 页 blob。 |尚不支持。
|页 Blob 计费|对于唯一页，无论它们是在 blob 中还是在快照中，都会产生费用。 在更新基本 blob 之前，不会对与 blob 关联的快照产生额外费用。|收费针对基 Blob 及关联的快照。 会对每个单独的快照额外收费。
|适用于 Blob 存储的存储层|热存储层、冷存储层和存档存储层。|尚不支持。
|适用于 Blob 存储的软删除|公开提供。 |尚不支持。
|页 Blob 大小上限|8 TB。 |1 TB。 
|页 Blob 页面大小|512字节。 |4 KB。 
|表分区键和行键大小|1024个字符（2048个字节）。|400个字符（800个字节）。
|Blob 快照|一个 blob 的最大快照数不受限制。|一个 blob 的最大快照数为 1,000。
|用于存储的 Azure AD 身份验证|预览。 |尚不支持。
|不可变 Blob|公开提供。 |尚不支持。
|针对存储的防火墙和虚拟网络规则|公开提供。 |尚不支持。|

存储指标也有一些差异：

* 存储度量值中的事务数据不区分内部或外部网络带宽。
* 存储度量值中的事务数据不包括虚拟机对已装载磁盘的访问权限。

## <a name="api-version"></a>API 版本

使用 Azure Stack Hub 存储时支持以下版本：

Azure 存储服务 API：

1811 更新或更高版本：

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

以前的版本：

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure 存储服务管理 API：

1811 更新或更高版本：

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

以前的版本：

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="powershell-version"></a>PowerShell 版本

对于存储模块 PowerShell，请注意与 REST API 兼容的版本。

| 模块 | 支持的版本 | 使用情况 |
|---|---|---|
| Azure.Storage | [4.5.0](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) | 管理 Azure Stack 中心存储帐户中的 blob、队列和表。 |
| AzureRM.Storage | [5.0.4](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.4) | 在 Azure Stack 中心创建和管理存储帐户。 |

有关 Azure Stack Hub 支持的存储客户端库的详细信息，请参阅：[Azure Stack Hub 存储开发工具入门](azure-stack-storage-dev.md)。

## <a name="next-steps"></a>后续步骤

* [Azure Stack Hub 存储开发工具入门](azure-stack-storage-dev.md)
* [使用 Azure Stack Hub 存储的数据传输工具](azure-stack-storage-transfer.md)
* [Azure Stack Hub 存储简介](azure-stack-storage-overview.md)
