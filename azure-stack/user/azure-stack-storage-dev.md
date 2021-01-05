---
title: 了解 Azure Stack 中心存储开发工具
description: 使用 Azure Stack Hub 存储开发工具的入门指南
author: mattbriggs
ms.author: mabrigg
ms.date: 12/16/2020
ms.topic: conceptual
ms.reviewer: jiahan
ms.lastreviewed: 08/12/2020
ms.openlocfilehash: 452a572dfccaaa4c33a2630bd8459f89ba8b26d6
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873837"
---
# <a name="get-started-with-azure-stack-hub-storage-development-tools"></a>Azure Stack Hub 存储开发工具入门

Microsoft Azure Stack 中心提供一组存储服务，其中包括 blob、表和队列存储。

使用本文作为使用 Azure Stack Hub 存储开发工具的入门指南。 可以在对应的 Azure 存储教程中，找到更多的详细信息和示例代码。

> [!NOTE]  
> Azure Stack Hub 存储和 Azure 存储之间存在一些差异，包括每个平台的特定要求。 例如，Azure Stack Hub 有特定的客户端库以及终结点后缀要求。 有关详细信息，请参阅 [Azure Stack Hub 存储：差异和注意事项](azure-stack-acs-differences.md)。

## <a name="azure-client-libraries"></a>Azure 客户端库

对于存储客户端库，请了解与 REST API 兼容的版本。 还必须在代码中指定 Azure Stack Hub 终结点。


::: moniker range=">=azs-2008"
### <a name="2008-update"></a>2008 更新

| 客户端库 | Azure Stack Hub 支持的版本 | 链接 | 终结点规范 |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 12.2.0 | Nuget 包：<br>通用：<https://www.nuget.org/packages/Azure.Storage.common/12.2.0><br>Blob：<https://www.nuget.org/packages/Azure.Storage.Blobs/12.2.0><br>队列：<https://www.nuget.org/packages/Azure.Storage.queues/12.2.0><br> <br>GitHub 版本：<br>通用：<https://github.com/Azure/azure-sdk-for-net/tree/Azure.Storage.Common_12.2.0/sdk/storage><br>Blob：<https://github.com/Azure/azure-sdk-for-net/tree/Azure.Storage.Blobs_12.2.0/sdk/storage><br>队列：<https://github.com/Azure/azure-sdk-for-net/tree/Azure.Storage.Queues_12.2.0/sdk/storage>  | app.config 文件 |
| Java | 12.4.0 | Maven 包：<br><https://mvnrepository.com/artifact/com.azure/azure-storage-common/12.4.0><br> <br>GitHub 版本：<br><https://github.com/Azure/azure-sdk-for-java/tree/azure-storage-common_12.4.0/sdk/storage> | 连接字符串设置 |
| Node.js | 2.8.3 | NPM 链接：<br><https://www.npmjs.com/package/azure-storage><br>（运行：`npm install azure-storage@2.8.3`）<br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3> | 服务实例声明 |
| C++ | 7.2.0 | GitHub 版本：<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v7.2.0> | 连接字符串设置 |
| PHP | 1.2.0 | GitHub 版本：<br>通用：<https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common><br>Blob：<https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob><br>队列：<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue><br>表：<https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table><br> <br>通过编辑器进行安装（若要了解更多内容，[请参阅下面的详细信息](#install-php-client-via-composer---current)。） | 连接字符串设置 |
| Python | 12.2.0 | GitHub 版本：<br>Blob：<br><https://github.com/Azure/azure-sdk-for-python/tree/azure-storage-blob_12.2.0/sdk/storage/azure-storage-blob><br>队列：<br><https://github.com/Azure/azure-sdk-for-python/tree/azure-storage-blob_12.2.0/sdk/storage/azure-storage-queue> | 服务实例声明 |
| Ruby | 1.0.1 | RubyGems 包：<br>常见：<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Blob：<https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>队列：<https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>表：<https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>GitHub 版本：<br>通用：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Blob：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>队列：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>表：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | 连接字符串设置 |

#### <a name="install-php-client-via-composer---current"></a>通过 Composer 安装 PHP 客户端 - 当前

若要通过编辑器进行安装，请执行以下操作（以 Blob 为例）：

1. 在项目的根目录中，使用以下代码创建一个名为 **composer.json** 的文件：

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. 将 [composer.phar](https://getcomposer.org/composer.phar) 下载到项目根目录。
3. 运行：`php composer.phar install`。
::: moniker-end

::: moniker range=">=azs-2005 <azs-2008"
### <a name="2005-update"></a>2005 更新

| 客户端库 | Azure Stack Hub 支持的版本 | 链接 | 终结点规范 |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 11.0.0 | Nuget 包：<br>通用：<https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/11.0.0><br>Blob：<https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/11.0.0><br>队列：<br><https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/11.0.0><br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-net/releases/tag/v11.0.0> | app.config 文件 |
| Java | 12.0.0-preview.3 | Maven 包：<br><https://mvnrepository.com/artifact/com.azure/azure-storage-blob/12.0.0-preview.3><br> <br>GitHub 版本：<br><https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage> | 连接字符串设置 |
| Node.js | 2.8.3 | NPM 链接：<br><https://www.npmjs.com/package/azure-storage><br>（运行：`npm install azure-storage@2.8.3`）<br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3> | 服务实例声明 |
| C++ | 7.1.0 | GitHub 版本：<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v7.1.0> | 连接字符串设置 |
| PHP | 1.2.0 | GitHub 版本：<br>通用：<https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common><br>Blob：<https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob><br>队列：<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue><br>表：<https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table><br> <br>通过编辑器进行安装（若要了解更多内容，[请参阅下面的详细信息](#install-php-client-via-composer---current)。） | 连接字符串设置 |
| Python | 2.1.0 | GitHub 版本：<br>常见：<br><https://github.com/Azure/azure-storage-python/releases/tag/v2.1.0-common><br>Blob：<br><https://github.com/Azure/azure-storage-python/releases/tag/v2.1.0-blob><br>队列：<br><https://github.com/Azure/azure-storage-python/releases/tag/v2.1.0-queue> | 服务实例声明 |
| Ruby | 1.0.1 | RubyGems 包：<br>常见：<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Blob：<https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>队列：<https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>表：<https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>GitHub 版本：<br>通用：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Blob：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>队列：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>表：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | 连接字符串设置 |

#### <a name="install-php-client-via-composer---current"></a>通过 Composer 安装 PHP 客户端 - 当前

若要通过编辑器进行安装，请执行以下操作（以 Blob 为例）：

1. 在项目的根目录中，使用以下代码创建一个名为 **composer.json** 的文件：

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. 将 [composer.phar](https://getcomposer.org/composer.phar) 下载到项目根目录。
3. 运行：`php composer.phar install`。
::: moniker-end

::: moniker range=">=azs-1811 <=azs-2005"
### <a name="previous-versions-1811-to-2002-update"></a>以前的版本（1811 到 2002 更新）

| 客户端库 | Azure Stack Hub 支持的版本 | 链接 | 终结点规范 |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 9.2.0 | Nuget 包：<br><https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0><br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0> | app.config 文件 |
| Java | 7.0.0 | Maven 包：<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/7.0.0><br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0> | 连接字符串设置 |
| Node.js | 2.8.3 | NPM 链接：<br><https://www.npmjs.com/package/azure-storage><br>（运行：`npm install azure-storage@2.8.3`）<br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3> | 服务实例声明 |
| C++ | 5.2.0 | Nuget 包：<br><https://www.nuget.org/packages/Microsoft.Azure.Storage.CPP.v140/5.2.0><br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0> | 连接字符串设置 |
| PHP | 1.2.0 | GitHub 版本：<br>通用：<https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common><br>Blob：<https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob><br>队列：<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.1.1-queue><br>表：<https://github.com/Azure/azure-storage-php/releases/tag/v1.1.0-table><br> <br>通过编辑器进行安装（若要了解更多内容，[请参阅下面的详细信息](#install-php-client-via-composer---current)。） | 连接字符串设置 |
| Python | 1.1.0 | GitHub 版本：<br>常见：<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-common><br>Blob：<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob><br>队列：<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-queue> | 服务实例声明 |
| Ruby | 1.0.1 | RubyGems 包：<br>常见：<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Blob：<https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>队列：<https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>表：<https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>GitHub 版本：<br>通用：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Blob：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>队列：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>表：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | 连接字符串设置 |

#### <a name="install-php-client-via-composer---current"></a>通过 Composer 安装 PHP 客户端 - 当前

若要通过编辑器进行安装，请执行以下操作（以 Blob 为例）：

1. 在项目的根目录中，使用以下代码创建一个名为 **composer.json** 的文件：

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. 将 [composer.phar](https://getcomposer.org/composer.phar) 下载到项目根目录。
3. 运行：`php composer.phar install`。
::: moniker-end

::: moniker range=">=azs-1802 <=azs-1809"
### <a name="previous-versions-1802-to-1809-update"></a>以前的版本（1802 到 1809 更新）

| 客户端库 | Azure Stack Hub 支持的版本 | 链接 | 终结点规范 |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Nuget 包：<br><https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0><br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0> | app.config 文件 |
| Java | 6.1.0 | Maven 包：<br><https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0><br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0> | 连接字符串设置 |
| Node.js | 2.7.0 | NPM 链接：<br><https://www.npmjs.com/package/azure-storage><br>（运行：`npm install azure-storage@2.7.0`）<br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0> | 服务实例声明 |
| C++ | 3.1.0 | Nuget 包：<br><https://www.nuget.org/packages/wastorage.v140/3.1.0><br> <br>GitHub 版本：<br><https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0> | 连接字符串设置 |
| PHP | 1.0.0 | GitHub 版本：<br>通用：<https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common><br>Blob：<https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob><br>队列：<br><https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue><br>表：<https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table><br> <br>通过编辑器安装（请参阅下面的详细信息）。 | 连接字符串设置 |
| Python | 1.0.0 | GitHub 版本：<br>常见：<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common><br>Blob：<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob><br>队列：<br><https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue> | 服务实例声明 |
| Ruby | 1.0.1 | RubyGems 包：<br>常见：<br><https://rubygems.org/gems/azure-storage-common/versions/1.0.1><br>Blob：<https://rubygems.org/gems/azure-storage-blob/versions/1.0.1><br>队列：<https://rubygems.org/gems/azure-storage-queue/versions/1.0.1><br>表：<https://rubygems.org/gems/azure-storage-table/versions/1.0.1><br> <br>GitHub 版本：<br>通用：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common><br>Blob：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob><br>队列：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue><br>表：<https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table> | 连接字符串设置 |

#### <a name="install-php-client-via-composer---previous"></a>通过编辑器安装 PHP 客户端 - 以前

通过 Composer 安装：（以 blob 为例）。

1. 在项目的根目录中，使用以下代码创建一个名为 **composer.json** 的文件：

   ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
   ```

2. 将 [composer.phar](https://getcomposer.org/composer.phar) 下载到项目根目录。
3. 运行：`php composer.phar install`。
:::moniker-end

## <a name="endpoint-declaration"></a>终结点声明

Azure Stack Hub 终结点包含两个部分：区域的名称和 Azure Stack Hub 域。
在 Azure Stack 开发工具包中，默认终结点是 **local.azurestack.external**。
如果不确定你的终结点，请与云管理员联系。

## <a name="examples"></a>示例

### <a name="net"></a>.NET

对于 Azure Stack Hub，在 app.config 文件中指定终结点后缀：

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

对于 Azure Stack Hub，在连接字符串的设置中指定终结点后缀：

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

对于 Azure Stack Hub，在声明实例中指定终结点后缀：

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

对于 Azure Stack Hub，在连接字符串的设置中指定终结点后缀：

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

对于 Azure Stack Hub，在连接字符串的设置中指定终结点后缀：

```php
$connectionString = 'BlobEndpoint=https://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=https:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=https:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

对于 Azure Stack Hub，在声明实例中指定终结点后缀：

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

对于 Azure Stack Hub，在连接字符串的设置中指定终结点后缀：

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob 存储

以下 Azure Blob 存储教程适用于 Azure Stack Hub。 请注意前面[示例](#examples)部分中所述的 Azure Stack Hub 特定终结点后缀需求。

* [通过 .NET 开始使用 Azure Blob 存储](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)
* [如何通过 Java 使用 Blob 存储](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [如何通过 Node.js 使用 Blob 存储](/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage)
* [如何通过 C++ 使用 Blob 存储](/azure/storage/blobs/storage-c-plus-plus-how-to-use-blobs)
* [如何通过 PHP 使用 Blob 存储](/azure/storage/blobs/storage-php-how-to-use-blobs)
* [如何通过 Python 使用 Azure Blob 存储](/azure/storage/blobs/storage-python-how-to-use-blob-storage)
* [如何通过 Ruby 使用 Blob 存储](/azure/storage/blobs/storage-ruby-how-to-use-blob-storage)

## <a name="queue-storage"></a>队列存储

以下 Azure 队列存储教程适用于 Azure Stack Hub。 请注意前面[示例](#examples)部分中所述的 Azure Stack Hub 特定终结点后缀需求。

* [通过 .NET 开始使用 Azure 队列存储](/azure/storage/queues/storage-dotnet-how-to-use-queues)
* [如何通过 Java 使用队列存储](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [如何通过 Node.js 使用队列存储](/azure/storage/queues/storage-nodejs-how-to-use-queues)
* [如何通过 C++ 使用队列存储](/azure/storage/queues/storage-c-plus-plus-how-to-use-queues)
* [如何通过 PHP 使用队列存储](/azure/storage/queues/storage-php-how-to-use-queues)
* [如何通过 Python 使用队列存储](/azure/storage/queues/storage-python-how-to-use-queue-storage)
* [如何通过 Ruby 使用队列存储](/azure/storage/queues/storage-ruby-how-to-use-queue-storage)

## <a name="table-storage"></a>表存储

以下 Azure 表存储教程适用于 Azure Stack Hub。 请注意前面[示例](#examples)部分中所述的 Azure Stack Hub 特定终结点后缀需求。

* [通过 .NET 开始使用 Azure 表存储](/azure/cosmos-db/table-storage-how-to-use-dotnet)
* [如何通过 Java 使用表存储](/azure/cosmos-db/table-storage-how-to-use-java)
* [如何通过 Node.js 使用 Azure 表存储](/azure/cosmos-db/table-storage-how-to-use-nodejs)
* [如何通过 C++ 使用表存储](/azure/cosmos-db/table-storage-how-to-use-c-plus)
* [如何通过 PHP 使用表存储](/azure/cosmos-db/table-storage-how-to-use-php)
* [如何在 Python 中使用表存储](/azure/cosmos-db/table-storage-how-to-use-python)
* [如何通过 Ruby 使用表存储](/azure/cosmos-db/table-storage-how-to-use-ruby)

## <a name="next-steps"></a>后续步骤

* [Microsoft Azure 存储简介](/azure/storage/common/storage-introduction)
