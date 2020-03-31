---
title: Azure Stack 集线器存储差异和注意事项
description: 了解 Azure Stack 中心存储与 Azure 存储之间的差异，以及 Azure Stack 中心部署注意事项。
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2020
ms.openlocfilehash: 04a89e89be033cb0372395580f2cf618d7857fb7
ms.sourcegitcommit: 8e480803ee0672eea328e4f14a8b75be94c8fc7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80391466"
---
# <a name="azure-stack-hub-storage-differences-and-considerations"></a>Azure Stack 中心存储：差异和注意事项

Azure Stack 中心存储是 Microsoft Azure Stack 中心中的存储云服务集。 Azure Stack 中心存储通过 Azure 一致性语义提供 blob、表、队列和帐户管理功能。

本文总结了 Azure 存储服务的已知 Azure Stack 集线器存储差异。 它还列出了在部署 Azure Stack 集线器时要考虑的事项。 若要了解全球 Azure 与 Azure Stack 中心之间的高级差异，请参阅[关键注意事项](azure-stack-considerations.md)一文。

## <a name="cheat-sheet-storage-differences"></a>备忘单：存储差异

| 功能 | Azure （全局） | Azure Stack Hub |
| --- | --- | --- |
|文件存储|支持基于云的 SMB 文件共享|尚不支持
|静态数据的 Azure 存储服务加密|256位 AES 加密。 使用 Key Vault 中的客户托管密钥支持加密。|BitLocker 128 位 AES 加密。 不支持使用客户托管的密钥进行加密。
|存储帐户类型|常规用途 V1、V2 和 Blob 存储帐户|仅限常规用途 V1。
|复制选项|本地冗余存储、异地冗余存储、读取访问异地冗余存储和区域冗余存储|本地冗余存储。
|高级存储|提供高性能和低延迟的存储。 仅支持高级存储帐户中的页 blob。|可以预配，但不能限制性能。 不会阻止使用块 blob，在高级存储帐户中追加 blob、表和队列。
|托管磁盘|支持高级和标准|当使用版本1808或更高版本时支持。
|托管磁盘快照|公开提供|支持。
|托管磁盘增量快照|公开提供|尚不支持。
|处于运行状态的 VM 的托管磁盘快照|公开提供|尚不支持。
|Blob 名称|1024 个字符（2048 个字节）|880 个字符（1760 个字节）
|块 blob 最大大小|4.75 TB（100 MB X 50,000 块）|对于1802更新或更新版本，4.75 TB （100 MB x 50000 块）。 对于以前的版本，50000 X 4 MB （约 195 GB）。
|页 blob 快照复制|备份已连接到正在运行的 VM 的 Azure 非托管 VM 磁盘|在 API 中支持[异步操作](azure-stack-acs-differences.md)。
|页 blob 增量快照复制|支持高级和标准 Azure 页 blob|尚不支持。
|页 blob 计费|对于唯一页，无论它们是在 blob 中还是在快照中，都会产生费用。 在更新基本 blob 之前，不会对与 blob 关联的快照产生额外费用。|基本 blob 和关联快照会产生费用。 对于每个单独的快照，将产生额外的费用。
|Blob 存储的存储层|热、冷和存档存储层。|尚不支持。
|Blob 存储的软删除|公开提供|尚不支持。
|页 blob 最大大小|8 TB|1 TB
|页 blob 页面大小|512 字节|4 KB
|表分区键和行键大小|1024 个字符（2048 个字节）|400个字符（800个字节）
|Blob 快照|一个 blob 的最大快照数没有限制。|一个 blob 的最大快照数为1000。
|用于存储的 Azure AD 身份验证|公开提供|尚不支持。
|不可变 Blob|公开提供|尚不支持。
|用于存储的防火墙和虚拟网络规则|公开提供|尚不支持。|
|将自定义域映射到 Blob 存储终结点|公开提供|尚不支持。|
|Blob 存储中托管的静态网站|公开提供|尚不支持。|
|通过客户托管的密钥加密静态存储数据|公开提供|尚不支持。|
|用 CRC64 哈希验证事务数据完整性|公开提供|尚不支持。|
|URL 中的数据服务器端同步副本|公开提供|尚不支持。|
|Blob 存储的批处理 API|公开提供|尚不支持。|

与存储指标之间也有一些差异：

* 存储度量值中的事务数据不区分内部或外部网络带宽。
* 存储指标中的事务数据不包括虚拟机对已装载磁盘的访问权限。

## <a name="api-version"></a>API 版本

Azure Stack 中心存储支持以下版本：

Azure 存储服务 Api：

2002更新或更新版本：

- [2019-02-02](https://docs.microsoft.com/rest/api/storageservices/version-2019-02-02)
- [2018-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2018-11-09)
- [2018-03-28](https://docs.microsoft.com/rest/api/storageservices/version-2018-03-28)
- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

以前的版本：

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure 存储服务管理 Api：

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-预览](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="powershell-version"></a>PowerShell 版本

对于存储模块 PowerShell，请注意与 REST API 兼容的版本。 

| 模块 | 支持的版本 | 用法 |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure.存储 | [4.5.0](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) | 管理 Azure Stack 中心存储帐户中的 blob、队列和表 |
| AzureRM.Storage | [5.0.4](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.4) | 在 Azure Stack 中心创建和管理存储帐户 |


有关 Azure Stack 中心支持的存储客户端库的详细信息，请参阅： [Azure Stack 中心存储开发工具入门](azure-stack-storage-dev.md)。

## <a name="next-steps"></a>后续步骤

* [Azure Stack 中心存储开发工具入门](azure-stack-storage-dev.md)
* [使用数据传输工具进行 Azure Stack 中心存储](azure-stack-storage-transfer.md)
* [Azure Stack 中心存储简介](azure-stack-storage-overview.md)
