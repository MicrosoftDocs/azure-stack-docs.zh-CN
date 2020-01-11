---
title: 在 Azure Stack Hub 中通过 node.js 使用 API 版本配置文件 |Microsoft Docs
description: 了解如何在 Azure Stack 集线器中将 API 版本配置文件与 node.js 配合使用。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: e6e65ebf39ae08b56f02fca437369f4861613ade
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878299"
---
# <a name="use-api-version-profiles-with-nodejs-software-development-kit-sdk-in-azure-stack-hub"></a>在 Azure Stack Hub 中通过 node.js 软件开发工具包（SDK）使用 API 版本配置文件

## <a name="nodejs-and-api-version-profiles"></a>Node.js 和 API 版本配置文件

可以使用 node.js SDK 来帮助构建和管理应用程序的基础结构。 Node.js SDK 中的 API 配置文件可帮助你在全局 Azure 资源与 Azure Stack 中心资源之间切换，从而帮助你的混合云解决方案。 你可以编写一次代码，然后针对全球 Azure 和 Azure Stack 中心进行编码。 

本文介绍如何使用[Visual Studio Code](https://code.visualstudio.com/)作为开发工具。 Visual Studio Code 可以调试 node.js SDK，并允许运行应用并将应用推送到 Azure Stack 中心实例。 可以从 Visual Studio Code 或通过运行命令 `node <nodefile.js>`的终端窗口进行调试。

## <a name="the-nodejs-sdk"></a>Node.js SDK

Node.js SDK 提供 Azure Stack 中心资源管理器工具。 SDK 中的资源提供程序包括计算、网络、存储、应用服务和 KeyVault。 你可以在 node.js 应用程序中安装10个资源提供程序客户端库。 你还可以下载指定将用于**2018-03-01 混合**或**2019-03-01 配置文件**的资源提供程序，以便为你的应用程序优化内存。 每个模块都包含一个资源提供程序、各自的 API 版本和 API 配置文件。 

API 配置文件是资源提供程序和 API 版本的组合。 可以使用 API 配置文件获取资源提供程序包中每个资源类型的最新且最稳定的版本。

  -   若要使用所有服务的最新版本，请使用包的 **latest** 配置文件。

  -   若要使用与 Azure Stack 中心兼容的服务，请使用 **\@azure/arm-资源配置**--03-03 或 **\@azure/arm--01.txt-03-03**

### <a name="packages-in-npm"></a>Npm 中的包

每个资源提供程序都有自己的包。 可以从[npm 注册表](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid)获取包。

你可以找到以下包：

| 资源提供程序 | 程序包 |
| --- | --- |
| [应用服务](https://www.npmjs.com/package/@azure/arm-appservice-profile-2019-03-01-hybrid) | @azure/arm-appservice-profile-2019-03-01-hybrid |
| [Azure 资源管理器订阅](https://www.npmjs.com/package/@azure/arm-subscriptions-profile-hybrid-2019-03-01) | @azure/arm-subscriptions-profile-hybrid-2019-03-01  |
| [Azure 资源管理器策略](https://www.npmjs.com/package/@azure/arm-policy-profile-hybrid-2019-03-01) | @azure/arm-policy-profile-hybrid-2019-03-01
| [Azure 资源管理器 DNS](https://www.npmjs.com/package/@azure/arm-dns-profile-2019-03-01-hybrid) | @azure/arm-dns-profile-2019-03-01-hybrid  |
| [授权](https://www.npmjs.com/package/@azure/arm-authorization-profile-2019-03-01-hybrid) | @azure/arm-authorization-profile-2019-03-01-hybrid  |
| [计算](https://www.npmjs.com/package/@azure/arm-compute-profile-2019-03-01-hybrid) | @azure/arm-compute-profile-2019-03-01-hybrid |
| [存储](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid) | @azure/arm-storage-profile-2019-03-01-hybrid |
| [Network](https://www.npmjs.com/package/@azure/arm-network-profile-2019-03-01-hybrid) | @azure/arm-network-profile-2019-03-01-hybrid |
| [资源](https://www.npmjs.com/package/@azure/arm-resources-profile-hybrid-2019-03-01) | @azure/arm-resources-profile-hybrid-2019-03-01 |
 | [Keyvault](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) | @azure/arm-keyvault-profile-2019-03-01-hybrid |

若要使用服务的最新 API 版本，请使用特定客户端库的**最新**配置文件。 例如，如果你想要单独使用最新的 API 版本的资源服务，请使用**资源管理客户端库**的 `azure-arm-resource` 配置文件。 部署。

使用包中定义的特定 API 版本获取资源提供程序的特定 API 版本。

  > [!Note]  
  > 您可以组合相同应用程序中的所有选项。

## <a name="install-the-nodejs-sdk"></a>安装 node.js SDK

1. 安装 Git。 有关说明，请参阅[入门 - 安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。

2. 安装或升级[到 node.js 的当前版本。](https://nodejs.org/en/download/) Node.js 还包括[npm](https://www.npmjs.com/) JavaScript 包管理器。

3. 安装或升级[Visual Studio Code](https://code.visualstudio.com/)并安装 Visual Studio Code 的[node.js 扩展](https://code.visualstudio.com/docs/nodejs/nodejs-debugging)。

2.  安装 Azure Stack 中心资源管理器的客户端包。 有关详细信息，请参阅[如何安装客户端库](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid)。

3.  需安装的包取决于要使用的配置文件版本。 可以在 npm 部分的[包](#packages-in-npm)中找到资源提供程序的列表。

4. 使用 npm 安装资源提供程序客户端库。 在命令行中，运行： `npm install <package-name>`。 例如，你可以运行 `npm install @azure/arm-authorization-profile-2019-03-01-hybrid` 来安装授权资源提供程序库。

5.  使用 SDK 时，请创建订阅并记下订阅 ID。 有关说明，请参阅[在 Azure Stack Hub 中创建对产品/服务的订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)。

6.  创建一个服务主体，并保存客户端 ID 和客户端密码。 在创建服务主体时，客户端 ID 也称为应用程序 ID。 有关说明，请参阅为[应用程序提供对 Azure Stack 集线器的访问权限](../operator/azure-stack-create-service-principals.md)。

7.  确保你的服务主体在你的订阅上有参与者/所有者角色。 有关如何向服务主体分配角色的说明，请参阅为[应用程序提供对 Azure Stack 集线器的访问权限](../operator/azure-stack-create-service-principals.md)。

### <a name="nodejs-prerequisites"></a>Node.js 必备组件 

若要将 node.js Azure SDK 与 Azure Stack 中心一起使用，必须提供以下值，并使用环境变量设置值。 若要设置环境变量，请参阅表后针对操作系统的说明。

| 值 | 环境变量 | Description |
| --- | --- | --- |
| 租户 ID | 租户\_ID | Azure Stack 中心[租户 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview)的值。 |
| 客户端 ID | 客户端\_ID | 在本文档的上一节中创建服务主体时保存的服务主体应用程序 ID。  |
| 订阅 ID | AZURE\_订阅\_ID[订阅 id](/azure-stack/operator/service-plan-offer-subscription-overview#subscriptions)是指你如何访问 Azure Stack 集线器中的产品/服务。  |
| 客户端机密 | 应用程序\_机密 | 在创建服务主体时保存的服务主体应用程序机密。 |
| 资源管理器终结点 | ARM\_终结点 | 请参阅[Azure Stack 中心资源管理器终结点](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-hub-resource-manager-endpoint)。 |

#### <a name="set-your-environmental-variables-for-nodejs"></a>设置 node.js 的环境变量

设置环境变量：

  - **Microsoft Windows**  

    若要设置环境变量，请在 Windows 命令提示符下使用以下格式：
      
      `set Tenant_ID=<Your_Tenant_ID>`

  - **macOS、Linux 和基于 Unix 的系统**  

    若要在 bash 提示符下设置环境变量，请使用以下格式：

    `export Azure_Tenant_ID=<Your_Tenant_ID>`

**Azure Stack 中心资源管理器终结点**

Microsoft Azure 资源管理器是一种管理框架，允许管理员部署、 管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

可以从资源管理器终结点获取元数据信息。 该终结点返回 JSON 文件，以及运行代码所需的信息。

> [!Note]  
> Azure Stack 开发工具包中的**ResourceManagerUrl** （ASDK）是： `https://management.local.azurestack.external` 集成系统中的**ResourceManagerUrl** ： `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com` 来检索所需的元数据： `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

示例 JSON 文件：

```JSON  
{
    "galleryEndpoint": "https://portal.local.azurestack.external/",

    "graphEndpoint": "https://graph.windowsNode.js/",

    "portal Endpoint": "https://portal.local.azurestack.external/",

    "authentication": {

        "loginEndpoint": "https://login.windowsNode.js/",

        "audiences": ["https://management.<yourtenant>.onmicrosoft.com/"]

    }

}
```

### <a name="existing-api-profiles"></a>现有 API 配置文件

-  **\@azure/resourceprovider-2019-03-03-01.txt**

    为 Azure Stack 中心构建的最新配置文件。 只要你使用的是1808或更高的戳记，就可以使用此配置文件与 Azure Stack 集线器最兼容。

-  **\@的 azure arm 资源**

    配置文件包含所有服务的最新版本。 使用 Azure 中的所有服务的最新版本。

有关 Azure Stack 集线器和 API 配置文件的详细信息，请参阅[API 配置文件摘要](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles#summary-of-api-profiles)。

### <a name="azure-nodejs-sdk-api-profile-usage"></a>Azure Node.js SDK API 配置文件使用情况

应使用以下行来实例化配置文件客户端。 仅 Azure Stack 集线器或其他私有云需要此参数。 默认情况下，Global Azure 已将这些设置 @azure-arm-resource 或 @azure-arm-storage。

```Node.js  
var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;

var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
````

需要以下代码对 Azure Stack 集线器上的服务主体进行身份验证。 它通过租户 ID 和身份验证基准创建令牌，该令牌特定于 Azure Stack 中心。

```Node.js  
var clientId = process.env['AZURE_CLIENT_ID'];
var tenantId = process.env['AZURE_TENANT_ID']; //"adfs"
var secret = process.env['AZURE_CLIENT_SECRET'];
var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
var base_url = process.env['ARM_ENDPOINT'];
var resourceClient, storageClient;
```

这将允许你使用 API 配置文件客户端库将应用程序成功部署到 Azure Stack 中心。

下面的代码片段使用为 Azure Stack 中心实例指定的 Azure 资源管理器终结点，并收集上面显示的数据，例如库终结点、图形终结点、访问群体和门户终结点。

```Node.js  
var map = {};
const fetchUrl = base_url + 'metadata/endpoints?api-version=1.0'
```

## <a name="environment-settings"></a>环境设置

若要对 Azure Stack 中心环境中的服务主体进行身份验证，请使用以下代码：使用此代码，并在命令提示符下设置环境变量，会自动为开发人员生成此映射。

```Node.js  
function main() {
  var initializePromise = initialize();
  initializePromise.then(function (result) {
    var userDetails = result;
    console.log("Initialized user details");
    // Use user details from here
    console.log(userDetails)
    map["name"] = "AzureStack"
    map["portalUrl"] = userDetails.portalEndpoint 
    map["resourceManagerEndpointUrl"] = base_url 
    map["galleryEndpointUrl"] = userDetails.galleryEndpoint 
    map["activeDirectoryEndpointUrl"] = userDetails.authentication.loginEndpoint.slice(0, userDetails.authentication.loginEndpoint.lastIndexOf("/") + 1) 
    map["activeDirectoryResourceId"] = userDetails.authentication.audiences[0] 
    map["activeDirectoryGraphResourceId"] = userDetails.graphEndpoint 
    map["storageEndpointSuffix"] = "." + base_url.substring(base_url.indexOf('.'))  
    map["keyVaultDnsSuffix"] = ".vault" + base_url.substring(base_url.indexOf('.')) 
    map["managementEndpointUrl"] = userDetails.authentication.audiences[0] 
    map["validateAuthority"] = "false"
    Environment.Environment.add(map);
```

## <a name="samples-using-api-profiles"></a>使用 API 配置文件的示例

可以使用以下示例作为参考，通过 node.js 和 Azure Stack 集线器 API 配置文件来创建解决方案。 可以从以下存储库中获取 GitHub 的示例：

- [存储节点资源提供程序入门](https://github.com/sijuman/storage-node-resource-provider-getting-started)
- [计算节点管理](https://github.com/sijuman/compute-node-manage-vm)
- [资源管理器节点资源和组](https://github.com/sijuman/resource-manager-node-resources-and-groups)

### <a name="sample-create-storage-account"></a>示例创建存储帐户 

1.  克隆存储库。

    ```bash  
    git clone https://github.com/sijuman/storage-node-resource-provider-getting-started.git
    ```

2.  创建 Azure 服务主体并分配用于访问订阅的角色。 有关说明，请参阅[使用 Azure PowerShell 使用证书创建服务主体](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)。

3.  检索以下必需值：
    - 租户 ID
    - 客户端 ID
    - 客户端机密
    - Azure 订阅 ID
    - Azure Stack 中心资源管理器终结点

4.  使用命令提示符，根据从已创建的服务主体检索的信息设置以下环境变量：

    ```bash  
    export TENANT_ID=<your tenant id>
    export CLIENT_ID=<your client id>
    export APPLICATION_SECRET=<your client secret>K
    export AZURE_SUBSCRIPTION_ID=<your subscription id>
    export ARM_ENDPOINT=<your Azure Stack Hub Resource manager URL>
    ```

    > [!Note]  
    > 在 Windows 上，使用 "**设置**" 而不是 "**导出**"。

5.  打开示例应用程序的 `index.js` 文件。

6.  将位置变量设置为 Azure Stack 中心位置。 例如，`LOCAL = "local"` 。

7.  设置允许您对 Azure Stack 中心进行身份验证的凭据。 此部分代码包含在索引 .js 文件中。

    ```Node.js  
    var clientId = process.env['CLIENT_ID'];
    var tenantId = process.env['TENANT_ID']; //"adfs"
    var secret = process.env['APPLICATION_SECRET'];
    var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
    var base_url = process.env['ARM_ENDPOINT'];
    var resourceClient, storageClient;
    ```

8.  检查是否已设置了正确的客户端库，同时结合了上述规定的客户端库。 在此示例中，我们使用了以下内容：

    ```Node.js
    var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;
    var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
    ```

9.  使用[npm 模块搜索](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid)，找到**2019-03-01 混合**，并为计算、网络、存储、KeyVault 和应用服务资源提供程序安装与此配置文件关联的包。

    为此，可以打开命令提示符，将其重定向到存储库的根文件夹，并为使用的每个资源提供程序运行 `npm install @azure/arm-keyvault-profile-2019-03-01-hybrid`。

10.  在命令提示符下，运行命令 `npm install` 安装所有 node.js 模块。

11.  运行该示例。

        ```Node.js  
        node index.js
        ```

12.  若要在 index 之后进行清理，请运行清理脚本。

        ```Node.js  
        Node cleanup.js <resourceGroupName> <storageAccountName>
        ```

13.  示例已成功完成。 有关该示例的详细信息，请参阅下文。

### <a name="what-does-indexjs-do"></a>Index node.js 的作用是什么？

该示例创建一个新的存储帐户，列出订阅或资源组中的存储帐户，列出存储帐户密钥，重新生成存储帐户密钥，获取存储帐户属性，更新存储帐户 SKU，并检查存储帐户名称可用性。

该示例首先使用服务主体登录，并使用凭据和订阅 ID 创建**ResourceManagementClient**和**StorageManagementClient**对象。

然后，该示例将设置一个资源组，该资源组将在其中创建新的存储帐户。

```Node.js  
function createResourceGroup(callback) {
  var groupParameters = { location: location, tags: { sampletag: 'sampleValue' } };
  console.log('\nCreating resource group: ' + resourceGroupName);
  return resourceClient.resourceGroups.createOrUpdate(resourceGroupName, groupParameters, callback);
}
```

### <a name="create-a-new-storage-account"></a>新建存储帐户

接下来，该示例将创建一个与在上一步骤中创建的资源组关联的新存储帐户。

在这种情况下，存储帐户名称是随机生成的，以确保唯一性。 但是，如果订阅中已存在具有相同名称的帐户，则对创建新存储帐户的调用将成功。

```Node.js  
var createParameters = {
    location: location,
    sku: {
        name: accType,
    },
    kind: 'Storage',
    tags: {
        tag1: 'val1',
        tag2: 'val2'
    }
};


console.log('\\n--&gt;Creating storage account: ' + storageAccountName + ' with parameters:\\n' + util.inspect(createParameters));

return storageClient.storageAccounts.create(resourceGroupName, storageAccountName, createParameters, callback);
```

### <a name="list-storage-accounts-in-the-subscription-or-resource-group"></a>列出订阅或资源组中的存储帐户

此示例列出了给定订阅中的所有存储帐户：

```Node.js  
console.log('\\n--&gt;Listing storage accounts in the current subscription.');

return storageClient.storageAccounts.list(callback);

It also lists storage accounts in the resource group:

    console.log('\\n--&gt;Listing storage accounts in the resourceGroup : ' + resourceGroupName);

return storageClient.storageAccounts.listByResourceGroup(resourceGroupName, callback);
```

### <a name="read-and-regenerate-storage-account-keys"></a>读取和重新生成存储帐户密钥

此示例列出了新创建的存储帐户和资源组的存储帐户密钥：

```Node.js  
console.log('\\n--&gt;Listing storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.listKeys(resourceGroupName, storageAccountName, callback);
```

它还会重新生成帐户访问密钥：

```Node.js  
console.log('\\n--&gt;Regenerating storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.regenerateKey(resourceGroupName, storageAccountName, 'key1', callback);
```

### <a name="get-storage-account-properties"></a>获取存储帐户属性

此示例读取存储帐户的属性：

```Node.js  
console.log('\\n--&gt;Getting info of storage account: ' + storageAccountName);

return storageClient.storageAccounts.getProperties(resourceGroupName, storageAccountName, callback);
```

### <a name="check-storage-account-name-availability"></a>检查存储帐户名称可用性

此示例检查 Azure 中是否提供了给定的存储帐户名称：

```Node.js  
console.log('\\n--&gt;Checking if the storage account name : ' + storageAccountName + ' is available.');

return storageClient.storageAccounts.checkNameAvailability(storageAccountName, callback);
```

### <a name="delete-the-storage-account-and-resource-group"></a>删除存储帐户和资源组

运行 "node.js" 将删除该示例创建的存储帐户：

```Node.js  
console.log('\\nDeleting storage account : ' + storageAccountName);
return storageClient.storageAccounts.deleteMethod(resourceGroupName, storageAccountName, callback);
```

它还会删除该示例创建的资源组：

```Node.js  
console.log('\\nDeleting resource group: ' + resourceGroupName);

return resourceClient.resourceGroups.deleteMethod(resourceGroupName, callback);
```

## <a name="next-steps"></a>后续步骤

有关 API 配置文件的详细信息，请参阅：

- [在 Azure Stack 中心管理 API 版本配置文件](azure-stack-version-profiles.md)
- [配置文件支持的资源提供程序 API 版本](azure-stack-profiles-azure-resource-manager-versions.md)
