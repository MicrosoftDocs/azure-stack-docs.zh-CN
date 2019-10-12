---
title: 在 Azure Stack 中将 API 版本配置文件与 Node.js 配合使用 | Microsoft Docs
description: 了解如何在 Azure Stack 中将 API 版本配置文件与 Node.js 配合使用。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/30/2019
ms.openlocfilehash: 7e2753ebb5bebad4f1ac2a7bbc17a27ccd87cbe9
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283043"
---
# <a name="use-api-version-profiles-with-nodejs-software-development-kit-sdk-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Node.js 软件开发工具包 (SDK) 配合使用

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="nodejs-and-api-version-profiles"></a>Node.js 与 API 版本配置文件

可以使用 Node.js SDK 来帮助构建和管理应用的基础结构。 Node.js SDK 中的 API 配置文件可帮助你在全局 Azure 资源与 Azure Stack 资源之间切换，从而帮助你的混合云解决方案。 你可以编写一次代码，然后将其作为全局 Azure 和 Azure Stack 目标。 

在本文中，可以使用 [Visual Studio Code](https://code.visualstudio.com/) 作为开发工具。 Visual Studio Code 可以调试 Node.js SDK，并可让你运行应用并将其推送到 Azure Stack 实例。 可以通过 Visual Studio Code 或者在终端窗口中运行 `node <nodefile.js>` 命令进行调试。

## <a name="the-nodejs-sdk"></a>Node.js SDK

Node.js SDK 提供 Azure Stack 资源管理器工具。 该 SDK 中的资源提供程序包括了计算、网络、存储、应用服务和 KeyVault。 可在 Node.js 应用程序中安装的资源提供程序客户端库有 10 个。 还可以下载指定要用于 **2018-03-01-hybrid** 或 **2019-03-01-profile** 的资源提供程序，以优化应用程序的内存。 每个模块包括资源提供程序、相应的 API 版本和 API 配置文件。 

API 配置文件是资源提供程序和 API 版本的组合。 可以使用 API 配置文件获取资源提供程序包中每个资源类型的最新且最稳定的版本。

  -   若要使用所有服务的最新版本，请使用包的 **latest** 配置文件。

  -   若要使用与 Azure Stack 兼容的服务，请使用 **\@azure/arm-resources-profile-hybrid-2019-03-01** 或 **\@azure/arm-storage-profile-2019-03-01-hybrid**

### <a name="packages-in-npm"></a>npm 中的包

每个资源提供程序都有自身的包。 可以从 [npm 注册表](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid)获取包。

可以找到以下包：

| 资源提供程序 | package |
| --- | --- |
| [应用服务](https://www.npmjs.com/package/@azure/arm-appservice-profile-2019-03-01-hybrid) | @azure/arm-appservice-profile-2019-03-01-hybrid |
| [Azure 资源管理器订阅](https://www.npmjs.com/package/@azure/arm-subscriptions-profile-hybrid-2019-03-01) | @azure/arm-subscriptions-profile-hybrid-2019-03-01  |
| [Azure 资源管理器策略](https://www.npmjs.com/package/@azure/arm-policy-profile-hybrid-2019-03-01) | @azure/arm-policy-profile-hybrid-2019-03-01
| [Azure 资源管理器 DNS](https://www.npmjs.com/package/@azure/arm-dns-profile-2019-03-01-hybrid) | @azure/arm-dns-profile-2019-03-01-hybrid  |
| [授权](https://www.npmjs.com/package/@azure/arm-authorization-profile-2019-03-01-hybrid) | @azure/arm-authorization-profile-2019-03-01-hybrid  |
| [计算](https://www.npmjs.com/package/@azure/arm-compute-profile-2019-03-01-hybrid) | @azure/arm-compute-profile-2019-03-01-hybrid |
| [存储](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid) | @azure/arm-storage-profile-2019-03-01-hybrid |
| [网络](https://www.npmjs.com/package/@azure/arm-network-profile-2019-03-01-hybrid) | @azure/arm-network-profile-2019-03-01-hybrid |
| [资源](https://www.npmjs.com/package/@azure/arm-resources-profile-hybrid-2019-03-01) | @azure/arm-resources-profile-hybrid-2019-03-01 |
 | [Keyvault](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) | @azure/arm-keyvault-profile-2019-03-01-hybrid |

若要使用某个服务的最新 API 版本，请使用特定客户端库的**最新**配置文件。 例如，若要单独使用资源服务的最新 API 版本，请使用**资源管理客户端库**包的 `azure-arm-resource` 配置 文件。

对于资源提供程序的特定 API 版本，请使用包中定义的特定 API 版本。

  > [!Note]  
  > 可以在同一应用程序中组合所有选项。

## <a name="install-the-nodejs-sdk"></a>安装 Node.js SDK

1. 安装 Git。 有关说明，请参阅[入门 - 安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。

2. 安装或升级到 [Node.js](https://nodejs.org/en/download/) 的最新版本。 Node.js 还包含 [npm](https://www.npmjs.com/) JavaScript 包管理器。

3. 安装或升级 [Visual Studio Code](https://code.visualstudio.com/)，并安装适用于 Visual Studio Code 的 [Node.js 扩展](https://code.visualstudio.com/docs/Node.js/nodejs-debugging)。

2.  安装 Azure Stack 资源管理器的客户端包。 有关详细信息，请参阅[如何安装客户端库](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid)。

3.  需安装的包取决于要使用的配置文件版本。 可以在 [npm 中的包](#packages-in-npm)部分找到资源提供程序列表。

4. 使用 npm 安装资源提供程序客户端库。 从命令行中运行：`npm install <package-name>`。 例如，可以运行 `npm install @azure/arm-authorization-profile-2019-03-01-hybrid` 来安装授权资源提供程序库。

5.  使用 SDK 时，请创建订阅并记下订阅 ID。 有关说明，请参阅[在 Azure Stack 中创建套餐的订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)。

6.  创建服务主体并保存客户端 ID 和客户端机密。 创建服务主体时的客户端 ID 也称为应用程序 ID。 有关说明，请参阅[为应用程序提供对 Azure Stack 的访问权限](../operator/azure-stack-create-service-principals.md)。

7.  确保服务主体在订阅上具有“参与者/所有者”角色。 有关如何将角色分配到服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限](../operator/azure-stack-create-service-principals.md)。

### <a name="nodejs-prerequisites"></a>Node.js 先决条件 

若要将 Node.js Azure SDK 与 Azure Stack 配合使用，必须提供以下值，然后使用环境变量来设置值。 若要设置环境变量，请参阅表后针对操作系统的说明。

| 值 | 环境变量 | 说明 |
| --- | --- | --- |
| 租户 ID | TENANT\_ID | Azure Stack [租户 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview) 的值。 |
| 客户端 ID | CLIENT\_ID | 在本文档上一部分创建服务主体时保存的服务主体应用程序 ID。  |
| 订阅 ID | AZURE\_SUBSCRIPTION\_ID：[订阅 ID](https://docs.microsoft.com/azure/azure-stack/service-plan-offer-subscription-overview#subscriptions) 用于访问 Azure Stack 中的套餐。  |
| 客户端机密 | APPLICATION\_SECRET | 创建服务主体时保存的服务主体应用程序机密。 |
| 资源管理器终结点 | ARM\_ENDPOINT | 请参阅 [Azure Stack 资源管理器终结点](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-resource-manager-endpoint)。 |

#### <a name="set-your-environmental-variables-for-nodejs"></a>设置 Node.js 的环境变量

若要定义环境变量：

  - **Microsoft Windows**  

    若要在 Windows 命令提示符中设置环境变量，请使用以下格式：
      
      `set Tenant_ID=<Your_Tenant_ID>`

  - **基于 macOS、Linux 和 Unix 的系统**  

    若要设置环境变量，请在 bash 提示符下使用以下格式：

    `export Azure_Tenant_ID=<Your_Tenant_ID>`

**Azure Stack 资源管理器终结点**

Microsoft Azure 资源管理器是一种管理框架，允许管理员部署、 管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

可以从资源管理器终结点获取元数据信息。 该终结点返回 JSON 文件，以及运行代码所需的信息。

> [!Note]  
> Azure Stack 开发工具包 (ASDK) 中的 **ResourceManagerUrl** 为：`https://management.local.azurestack.external`集成系统中的 **ResourceManagerUrl** 为：`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com` 检索所需的元数据：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

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

-  **\@azure/arm-resourceprovider-profile-2019-03-01-hybrid**

    为 Azure Stack 生成的最新配置文件。 请将此配置文件用于与 Azure Stack 最兼容的服务，前提是使用 1808 或更高的戳记。

-  **\@azure-arm-resource**

    配置文件包含所有服务的最新版本。 使用 Azure 中所有服务的最新版本。

有关 Azure Stack 和 API 配置文件的详细信息，请参阅 [API 配置文件的摘要](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles#summary-of-api-profiles)。

### <a name="azure-nodejs-sdk-api-profile-usage"></a>Azure Node.js SDK API 配置文件的用法

应该使用以下代码行来实例化配置文件客户端。 此参数只是 Azure Stack 或其他私有云所需要的。 默认情况下，全球 Azure 已有这些包含 @azure-arm-resource 或 @azure-arm-storage 的设置。

```Node.js  
var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;

var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
````

在 Azure Stack 上对服务主体进行身份验证需要以下代码。 它根据租户 ID 和特定于 Azure Stack 的身份验证基准创建令牌。

```Node.js  
var clientId = process.env['AZURE_CLIENT_ID'];
var tenantId = process.env['AZURE_TENANT_ID']; //"adfs"
var secret = process.env['AZURE_CLIENT_SECRET'];
var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
var base_url = process.env['ARM_ENDPOINT'];
var resourceClient, storageClient;
```

这样，便可以使用 API 配置文件客户端库将应用程序成功部署到 Azure Stack。

以下代码片段使用为 Azure Stack 实例指定的 Azure 资源管理器终结点，收集如上所示的数据，例如库终结点、图形终结点、受众和门户终结点。

```Node.js  
var map = {};
const fetchUrl = base_url + 'metadata/endpoints?api-version=1.0'
```

## <a name="environment-settings"></a>环境设置

若要通过 Azure Stack 环境对服务主体进行身份验证，请使用以下代码：使用此代码并在命令提示符中设置环境变量会自动为开发人员生成此映射。

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

可以参考以下示例使用 Node.js 和 Azure Stack API 配置文件来创建解决方案。 可从 GitHub 上的以下存储库中获取示例：

- [存储节点资源提供程序入门](https://github.com/sijuman/storage-node-resource-provider-getting-started)
- [计算节点管理](https://github.com/sijuman/compute-node-manage-vm)
- [资源管理器节点资源和组](https://github.com/sijuman/resource-manager-node-resources-and-groups)

### <a name="sample-create-storage-account"></a>示例 - 创建存储帐户 

1.  克隆存储库。

    ```bash  
    git clone https://github.com/sijuman/storage-node-resource-provider-getting-started.git
    ```

2.  创建 Azure 服务主体并分配用于访问订阅的角色。 有关说明，请参阅[使用 Azure PowerShell 创建具有证书的服务主体](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)。

3.  检索以下必需值：
    - 租户 ID
    - 客户端 ID
    - 客户端机密
    - Azure 订阅 ID
    - Azure Stack 资源管理器终结点

4.  使用命令提示符，根据从已创建的服务主体检索的信息设置以下环境变量：

    ```bash  
    export TENANT_ID=<your tenant id>
    export CLIENT_ID=<your client id>
    export APPLICATION_SECRET=<your client secret>K
    export AZURE_SUBSCRIPTION_ID=<your subscription id>
    export ARM_ENDPOINT=<your Azure Stack Resource manager URL>
    ```

    > [!Note]  
    > 在 Windows 上，请使用 **set** 而不是 **export**。

5.  打开示例应用程序的 `index.js` 文件。

6.  将位置变量设置为你的 Azure Stack 位置。 例如， `LOCAL = "local"` 。

7.  设置凭据，以便向 Azure Stack 进行身份验证。 此代码部分包含在本示例的 index.js 文件中。

    ```Node.js  
    var clientId = process.env['CLIENT_ID'];
    var tenantId = process.env['TENANT_ID']; //"adfs"
    var secret = process.env['APPLICATION_SECRET'];
    var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
    var base_url = process.env['ARM_ENDPOINT'];
    var resourceClient, storageClient;
    ```

8.  使用上面规定的客户端库组合，检查是否设置了正确的客户端库。 本示例使用了以下代码：

    ```Node.js
    var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;
    var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
    ```

9.  使用 [npm 模块搜索](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid)，找到 **2019-03-01-hybrid**，为计算、网络、存储、KeyVault 和应用服务资源提供程序安装与此配置文件相关联的包。

    为此，可打开命令提示符，将其重定向到存储库的根文件夹，然后针对使用的每个资源提供程序运行 `npm install @azure/arm-keyvault-profile-2019-03-01-hybrid`。

10.  在命令提示符下，运行 `npm install` 命令安装所有 Node.js 模块。

11.  运行示例。

        ```Node.js  
        node index.js
        ```

12.  若要在用完 index.js 之后进行清理，请运行清理脚本。

        ```Node.js  
        Node cleanup.js <resourceGroupName> <storageAccountName>
        ```

13.  本示例现已成功完成。 有关示例的详细信息，请参阅下文。

### <a name="what-does-indexjs-do"></a>index.js 有何用途？

本示例可创建新的存储帐户、列出订阅或资源组中的存储帐户、列出存储帐户密钥、重新生成存储帐户密钥、获取存储帐户属性、更新存储帐户 SKU，以及检查存储帐户名是否可用。

本示例首先使用你的服务主体登录，然后使用你的凭据和订阅 ID 创建 **ResourceManagementClient** 与 **StorageManagementClient** 对象。

然后，本示例设置要在其中创建新存储帐户的资源组。

```Node.js  
function createResourceGroup(callback) {
  var groupParameters = { location: location, tags: { sampletag: 'sampleValue' } };
  console.log('\nCreating resource group: ' + resourceGroupName);
  return resourceClient.resourceGroups.createOrUpdate(resourceGroupName, groupParameters, callback);
}
```

### <a name="create-a-new-storage-account"></a>创建新的存储帐户

接下来，本示例创建新的存储帐户，并将其与上一步骤中创建的资源组相关联。

在此情况下，系统会随机生成存储帐户名以确保唯一性。 但是，如果订阅中已有同名的帐户，则创建新存储帐户的调用仍会成功。

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

本示例列出指定订阅中的所有存储帐户：

```Node.js  
console.log('\\n--&gt;Listing storage accounts in the current subscription.');

return storageClient.storageAccounts.list(callback);

It also lists storage accounts in the resource group:

    console.log('\\n--&gt;Listing storage accounts in the resourceGroup : ' + resourceGroupName);

return storageClient.storageAccounts.listByResourceGroup(resourceGroupName, callback);
```

### <a name="read-and-regenerate-storage-account-keys"></a>读取和重新生成存储帐户密钥

本示例列出新建的存储帐户和资源组的存储帐户密钥：

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

本示例读取存储帐户的属性：

```Node.js  
console.log('\\n--&gt;Getting info of storage account: ' + storageAccountName);

return storageClient.storageAccounts.getProperties(resourceGroupName, storageAccountName, callback);
```

### <a name="check-storage-account-name-availability"></a>检查存储帐户名的可用性

本示例检查 Azure 中是否可使用给定的存储帐户名：

```Node.js  
console.log('\\n--&gt;Checking if the storage account name : ' + storageAccountName + ' is available.');

return storageClient.storageAccounts.checkNameAvailability(storageAccountName, callback);
```

### <a name="delete-the-storage-account-and-resource-group"></a>删除存储帐户和资源组

运行 cleanup.js 可删除本示例创建的存储帐户：

```Node.js  
console.log('\\nDeleting storage account : ' + storageAccountName);
return storageClient.storageAccounts.deleteMethod(resourceGroupName, storageAccountName, callback);
```

它还会删除本示例创建的资源组：

```Node.js  
console.log('\\nDeleting resource group: ' + resourceGroupName);

return resourceClient.resourceGroups.deleteMethod(resourceGroupName, callback);
```

## <a name="next-steps"></a>后续步骤

有关 API 配置文件的详细信息，请参阅：

- [在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles.md)
- [配置文件支持的资源提供程序 API 版本](azure-stack-profiles-azure-resource-manager-versions.md)
