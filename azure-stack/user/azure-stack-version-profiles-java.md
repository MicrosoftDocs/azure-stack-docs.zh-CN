---
title: 在 Azure Stack 中通过 Java 使用 API 版本配置文件 |Microsoft Docs
description: 了解如何在 Azure Stack 中通过 Java 使用 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 755a4b373bcf305d7eb589acebca18f4ee4aafb8
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959408"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Java 配合使用

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

用于 Azure Stack 资源管理器的 Java SDK 提供了相关工具来帮助构建和管理基础结构。 SDK 中的资源提供程序包括计算、网络、存储、应用服务和[Key Vault](/azure/key-vault/key-vault-whatis)。 

Java SDK 合并了 API 配置文件, 方法是在该文件中加载正确的模块的*Pom*文件中包含依赖项。 但是, 可以将多个配置文件作为依赖项 (如**2019-03-01-混合**或**最新版本**) 添加为 Azure 配置文件。 使用这些依赖项会加载正确的模块, 以便在创建资源类型时, 可以从要使用的配置文件中选择要使用的 API 版本。 这样就可以使用 Azure 中的最新版本，同时使用 Azure Stack 的最新 API 版本进行开发。 

使用 Java SDK 可以获得真正的混合云开发人员体验。 Java SDK 中的 API 配置文件可以用来在公有云 Azure 资源和 Azure Stack 中的资源之间进行切换，实现混合云开发。

## <a name="java-and-api-version-profiles"></a>Java 与 API 版本配置文件

API 配置文件是资源提供程序和 API 版本的组合。 使用 API 配置文件来获取资源提供程序包中每种资源类型的最新、最稳定的版本。

- 若要使用所有服务的最新版本，请使用 **latest** 配置文件作为依赖项。

  - 若使用 latest 配置文件，则依赖项为 **com.microsoft.azure**。

  - 若要使用 Azure Stack 中提供的最新支持的服务，请使用 **com.microsoft.azure.profile\_2019\_03\_01\_hybrid** 配置文件。

    - 这将作为依赖项在*Pom .xml*文件中指定, 如果从下拉列表中选择正确的类 (与在 .net 中使用的一样), 则将自动加载模块。

  - 依赖项如下所示：

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - 若要使用特定资源提供程序中某个资源类型的特定 API 版本，请使用通过 Intellisense 定义的特定 API 版本。

可以组合同一应用中的所有选项。

## <a name="install-the-azure-java-sdk"></a>安装 Azure Java SDK

使用以下步骤安装 Java SDK：

1. 按照正式说明安装 Git。 有关说明，请参阅[入门 - 安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。

2. 按照说明安装 [Java SDK](https://zulu.org/download/) 和 [Maven](https://maven.apache.org/)。 正确的版本是 Java 开发人员工具包的版本 8。 正确的 Apache Maven 是 3.0 或更高版本。 要完成快速入门, 必须将环境变量设置为Java开发工具包的安装位置。`JAVA_HOME` 有关详细信息, 请参阅[通过 Java 和 Maven 创建你的第一个函数](/azure/azure-functions/functions-create-first-java-maven)。

3. 若要安装正确的依赖项包, 请在 Java 应用中打开*Pom .xml*文件。 添加一个依赖项，如以下代码中所示：

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. 需要安装的包取决于你要使用的配置文件版本。 配置文件版本的包名如下：

   - **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **latest**

5. 如果不可用，请创建一个订阅，并保存订阅 ID 供以后使用。 有关如何创建订阅的说明，请参阅[在 Azure Stack 中创建套餐的订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。

6. 创建服务主体并保存客户端 ID 和客户端机密。 有关如何为 Azure Stack 创建服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限](../operator/azure-stack-create-service-principals.md)。 在创建服务主体时, 客户端 ID 也称为应用程序 ID。

7. 确保服务主体在订阅上具有“参与者/所有者”角色。 有关如何将角色分配到服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限](../operator/azure-stack-create-service-principals.md)。

## <a name="prerequisites"></a>先决条件

若要将 Azure Java SDK 与 Azure Stack 配合使用，必须提供以下值，然后使用环境变量来设置值。 若要设置环境变量，请参阅表后针对操作系统的说明。

| 值                     | 环境变量 | 说明                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 租户 ID                 | `AZURE_TENANT_ID`            | Azure Stack [租户 ID](../operator/azure-stack-identity-overview.md) 的值。                                                          |
| 客户端 ID                 | `AZURE_CLIENT_ID`             | 在上一部分中创建服务主体时保存的服务主体应用程序 ID。                                                                                              |
| 订阅 ID           | `AZURE_SUBSCRIPTION_ID`      | [订阅 ID](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) 用于访问 Azure Stack 中的套餐。                |
| 客户端机密             | `AZURE_CLIENT_SECRET`        | 创建服务主体时保存的服务主体应用程序机密。                                                                                                                                   |
| 资源管理器终结点 | `ARM_ENDPOINT`              | 请参阅 [Azure Stack 资源管理器终结点](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)。 |
| Location                  | `RESOURCE_LOCATION`    | 对于 Azure Stack 来说为 **Local**。                                                                                                                                                                                                |

若要查找你的 Azure Stack 的租户 ID，请参阅[此处](../operator/azure-stack-csp-ref-operations.md)的说明。 若要设置环境变量, 请使用以下过程:

### <a name="microsoft-windows"></a>Microsoft Windows

若要在 Windows 命令提示符中设置环境变量，请使用以下格式：

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>基于 MacOS、Linux 和 Unix 的系统

在基于 Unix 的系统中，使用以下命令：

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>信任 Azure Stack CA 根证书

如果使用的是 Azure Stack 开发工具包 (ASDK), 则必须信任远程计算机上的 CA 根证书。 不需要与集成系统信任 CA 根证书。

#### <a name="windows"></a>Windows

1. 将 Azure Stack 自签名证书导出到桌面。

1. 在命令提示符下, 将目录更改`%JAVA_HOME%\bin`为。

1. 运行下面的命令：

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack 资源管理器终结点

Microsoft Azure 资源管理器是一种管理框架, 管理员可通过它来部署、管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

可以从资源管理器终结点获取元数据信息。 终结点返回 JSON 文件, 其中包含运行代码所需的信息。

请注意以下事项：

- ASDK 中的**ResourceManagerUrl**为: `https://management.local.azurestack.external/`。

- 集成系统中的 **ResourceManagerUrl** 为：`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`。

检索所需的元数据：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`。

示例 JSON 文件：

```json
{
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication":
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>现有 API 配置文件

- **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**：为 Azure Stack 生成的最新配置文件。 只要你使用的是1904或更高的戳记, 就可以使用此配置文件与 Azure Stack 最兼容。

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**：为 Azure Stack 生成的配置文件。 使用此配置文件可以使服务与标记版本 1808 或更高版本的 Azure Stack 兼容。

- **com.microsoft.azure**：包含所有服务的最新版本的配置文件。 使用所有服务的最新版本。

有关 Azure Stack 和 API 配置文件的详细信息, 请参阅[API 配置文件摘要](../user/azure-stack-version-profiles.md#summary-of-api-profiles)。

## <a name="azure-java-sdk-api-profile-usage"></a>Azure Java SDK API 配置文件的使用

以下代码用于在 Azure Stack 上对服务主体进行身份验证。 它使用租户 ID 和特定于 Azure Stack 的身份验证基准创建令牌：

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

此代码使你可以使用 API 配置文件依赖关系来成功地将应用程序部署到 Azure Stack。

## <a name="define-azure-stack-environment-setting-functions"></a>定义 Azure Stack 环境设置函数

若要将 Azure Stack 云注册到正确的终结点，请使用以下代码：

```java
// Get Azure Stack cloud endpoints
final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);

AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

上面代码中的 `getActiveDirectorySettings` 调用从元数据终结点检索终结点。 它从发出的调用中指出环境变量:

```java
public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {

    HashMap<String, String> adSettings = new HashMap<String, String>();
    try {

        // create HTTP Client
        HttpClient httpClient = HttpClientBuilder.create().build();

        // Create new getRequest with below mentioned URL
        HttpGet getRequest = new HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
                             armEndpoint));

        // Add additional header to getRequest which accepts application/xml data
        getRequest.addHeader("accept", "application/xml");

        // Execute request and catch response
        HttpResponse response = httpClient.execute(getRequest);
        
        // Check for HTTP response code: 200 = success
        if (response.getStatusLine().getStatusCode() != 200) {
            throw new RuntimeException("Failed : HTTP error code : " + response.getStatusLine().getStatusCode());
        }
        
        String responseStr = EntityUtils.toString(response.getEntity());
        JSONObject responseJson = new JSONObject(responseStr);
        adSettings.put("galleryEndpoint", responseJson.getString("galleryEndpoint"));
        JSONObject authentication = (JSONObject) responseJson.get("authentication");
        String audience = authentication.get("audiences").toString().split("\"")[1];
        adSettings.put("login_endpoint", authentication.getString("loginEndpoint"));
        adSettings.put("audience", audience);
        adSettings.put("graphEndpoint", responseJson.getString("graphEndpoint"));

    } catch (ClientProtocolException cpe) {
        cpe.printStackTrace();
        throw new RuntimeException(cpe);
    } catch (IOException ioe) {
        ioe.printStackTrace();
        throw new RuntimeException(ioe);
    }
    return adSettings;
}
```

## <a name="samples-using-api-profiles"></a>使用 API 配置文件的示例

使用以下 GitHub 示例作为参考, 通过 .NET 和 Azure Stack API 配置文件创建解决方案:

- [管理资源组](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [管理存储帐户](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [管理虚拟机](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)(已更新为 2019-03-01-混合配置文件)。

### <a name="sample-unit-test-project"></a>示例单元测试项目

1. 使用以下命令克隆存储库：

   `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2. 创建 Azure 服务主体并分配用于访问订阅的角色。 有关如何创建服务主体的说明，请参阅[使用 Azure PowerShell 创建具有证书的服务主体](../operator/azure-stack-create-service-principals.md)。

3. 检索以下必需的环境变量值：

   - `AZURE_TENANT_ID`
   - `AZURE_CLIENT_ID`
   - `AZURE_CLIENT_SECRET`
   - `AZURE_SUBSCRIPTION_ID`
   - `ARM_ENDPOINT`
   - `RESOURCE_LOCATION`

4. 使用从使用命令提示符创建的服务主体检索到的信息设置以下环境变量:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Resource Manager URL}`
   - `export RESOURCE_LOCATION={location of Azure Stack}`

   在 Windows 中，请使用 **set** 而不是 **export**。

5. 使用 `getActiveDirectorySettings` 函数检索 Azure 资源管理器元数据终结点。

    ```java
    // Get Azure Stack cloud endpoints
    final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);
    ```

6. 在*Pom*文件中, 添加以下依赖项, 以便将**2019-03-01 混合**配置文件用于 Azure Stack。 此依赖关系为计算、网络、存储、Key Vault 和应用服务资源提供程序安装与此配置文件关联的模块:

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. 在打开的用于设置环境变量的命令提示符中，输入以下命令：

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>后续步骤

有关 API 配置文件的详细信息，请参阅：

- [Azure Stack 中的版本配置文件](azure-stack-version-profiles.md)
- [配置文件支持的资源提供程序 API 版本](azure-stack-profiles-azure-resource-manager-versions.md)
