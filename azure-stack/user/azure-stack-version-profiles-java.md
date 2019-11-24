---
title: 在 Azure Stack 中将 API 版本配置文件与 Java 配合使用 | Microsoft Docs
description: 了解如何在 Azure Stack 中将 API 版本配置文件与 Java 配合使用。
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
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 7d2a98656671a5e6f59d46054e074e45bb9d3c89
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282889"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Java 配合使用

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

用于 Azure Stack 资源管理器的 Java SDK 提供了相关工具来帮助构建和管理基础结构。 该 SDK 中的资源提供程序包括了计算、网络、存储、应用服务和 [Key Vault](/azure/key-vault/key-vault-whatis)。

Java SDK 纳入了 API 配置文件，其方法是：在 Pom.xml 文件中包括依赖项，由后者加载 .java 文件中的正确模块。 不过，可以添加多个作为依赖项的配置文件，例如，添加 **2019-03-01-hybrid** 或 **latest** 作为 Azure 配置文件。 使用这些依赖项来加载正确的模块，这样在创建资源类型时，就可以从这些配置文件中选择要使用的 API 版本。 这样就可以使用 Azure 中的最新版本，同时使用 Azure Stack 的最新 API 版本进行开发。

使用 Java SDK 可以获得真正的混合云开发人员体验。 Java SDK 中的 API 配置文件可以用来在公有云 Azure 资源和 Azure Stack 中的资源之间进行切换，实现混合云开发。

## <a name="java-and-api-version-profiles"></a>Java 与 API 版本配置文件

API 配置文件是资源提供程序和 API 版本的组合。 可以使用 API 配置文件获取资源提供程序包中每个资源类型的最新且最稳定的版本。

- 若要使用所有服务的最新版本，请使用 **latest** 配置文件作为依赖项。

  - 若使用 latest 配置文件，则依赖项为 **com.microsoft.azure**。

  - 若要使用 Azure Stack 中提供的最新支持的服务，请使用 **com.microsoft.azure.profile\_2019\_03\_01\_hybrid** 配置文件。

    - 配置文件在**Pom .xml**文件中指定为依赖项，如果从下拉列表中选择正确的类（与 .net 相同），则会自动加载模块。

  - 依赖项如下所示：

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - 若要使用特定资源提供程序中某个资源类型的特定 API 版本，请使用通过 Intellisense 定义的特定 API 版本。

可以在同一应用中组合所有选项。

## <a name="install-the-azure-java-sdk"></a>安装 Azure Java SDK

按照以下步骤安装 Java SDK：

1. 按照正式说明安装 Git。 请参阅[入门-安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。

2. 按照说明安装 [Java SDK](https://zulu.org/download/) 和 [Maven](https://maven.apache.org/)。 正确的版本是 Java 开发人员工具包的版本 8。 Apache Maven 的正确版本为3.0 或更高版本。 若要完成本快速入门，必须将 `JAVA_HOME` 环境变量设置为 Java 开发工具包的安装位置。 有关详细信息，请参阅[使用 Java 和 Maven 创建第一个函数](/azure/azure-functions/functions-create-first-java-maven)。

3. 若要安装正确的依赖项包，请在 Java 应用中打开 Pom.xml 文件。 添加一个依赖项，如以下代码中所示：

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

6. 创建服务主体并保存客户端 ID 和客户端机密。 有关如何为 Azure Stack 创建服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限](../operator/azure-stack-create-service-principals.md)。 创建服务主体时的客户端 ID 也称为应用程序 ID。

7. 确保服务主体在订阅上具有“参与者/所有者”角色。 有关如何将角色分配到服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限](../operator/azure-stack-create-service-principals.md)。

## <a name="prerequisites"></a>先决条件

若要将 Azure Java SDK 与 Azure Stack 配合使用，必须提供以下值，然后使用环境变量来设置值。 若要设置环境变量，请参阅表后针对操作系统的说明。

| 值                     | 环境变量 | 说明                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 租户 ID                 | `AZURE_TENANT_ID`            | 你的 Azure Stack[租户 ID](../operator/azure-stack-identity-overview.md)。                                                          |
| 客户端 ID                 | `AZURE_CLIENT_ID`             | 在上一部分中创建服务主体时保存的服务主体应用程序 ID。                                                                                              |
| 订阅 ID           | `AZURE_SUBSCRIPTION_ID`      | 使用[订阅 ID](../operator/service-plan-offer-subscription-overview.md#subscriptions)可以访问 Azure Stack 中的产品/服务。                |
| 客户端机密             | `AZURE_CLIENT_SECRET`        | 创建服务主体时保存的服务主体应用程序机密。                                                                                                                                   |
| 资源管理器终结点 | `ARM_ENDPOINT`              | 请参阅[Azure Stack 资源管理器终结点](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)一文。 |
| 位置                  | `RESOURCE_LOCATION`    | 对于 Azure Stack 来说为 **Local**。                                                                                                                                                                                                |

若要查找你的 Azure Stack 的租户 ID，请参阅[此处](../operator/azure-stack-csp-ref-operations.md)的说明。 若要设置环境变量，请使用以下部分中的过程：

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

如果使用的是 Azure Stack 开发工具包（ASDK），则必须信任远程计算机上的 CA 根证书。 不需要信任 Azure Stack 集成系统的 CA 根证书。

#### <a name="windows"></a>Windows

1. 将 Azure Stack 自签名证书导出到桌面。

1. 在命令提示符下，将目录更改为 `%JAVA_HOME%\bin`。

1. 运行以下命令：

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack 资源管理器终结点

Azure 资源管理器是一种管理框架，它允许管理员部署、管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

可以从资源管理器终结点获取元数据信息。 该终结点返回 JSON 文件，其中包含运行代码所需的信息。

请注意以下事项：

- ASDK 中的 **ResourceManagerUrl** 为：`https://management.local.azurestack.external/`。

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

- **.com.\_2019\_03\_01\_混合**：为 Azure Stack 生成最新的配置文件。 只要你在1904或更高版本上，使用此服务配置文件与 Azure Stack 最兼容。

- **\_2018\_03\_01\_混合**：为 Azure Stack 生成的配置文件。 使用此配置文件来与 Azure Stack 1808 或更高版本兼容。

- **com.microsoft.azure**：包含所有服务的最新版本的配置文件。 使用所有服务的最新版本。

有关 Azure Stack 和 API 配置文件的详细信息，请参阅 [API 配置文件的摘要](../user/azure-stack-version-profiles.md#summary-of-api-profiles)。

## <a name="azure-java-sdk-api-profile-usage"></a>Azure Java SDK API 配置文件的使用

以下代码用于在 Azure Stack 上对服务主体进行身份验证。 它使用租户 ID 和特定于 Azure Stack 的身份验证基准创建令牌：

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

此代码可让你使用 API 配置文件依赖项将应用成功部署到 Azure Stack。

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

前面代码中的 `getActiveDirectorySettings` 调用将从元数据终结点检索终结点。 它从所做的调用声明环境变量：

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

若要使用 .NET 和 Azure Stack API 配置文件来创建解决方案，可以将以下 GitHub 示例用作参考：

- [管理资源组](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [管理存储帐户](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [管理虚拟机](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)（使用2019-03-01 混合配置文件进行更新）

### <a name="sample-unit-test-project"></a>示例单元测试项目

1. 使用以下命令克隆存储库：

   ```shell
   git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`
   ```

2. 创建 Azure 服务主体并分配用于访问订阅的角色。 有关如何创建服务主体的说明，请参阅[使用 Azure PowerShell 创建具有证书的服务主体](../operator/azure-stack-create-service-principals.md)。

3. 检索以下必需的环境变量：

   - `AZURE_TENANT_ID`
   - `AZURE_CLIENT_ID`
   - `AZURE_CLIENT_SECRET`
   - `AZURE_SUBSCRIPTION_ID`
   - `ARM_ENDPOINT`
   - `RESOURCE_LOCATION`

4. 使用命令提示符，根据从已创建的服务主体检索的信息设置以下环境变量：

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

6. 在 Pom.xml 文件中，添加以下依赖项，以便将 **2019-03-01-hybrid** 配置文件用于 Azure Stack。 此依赖项将为计算、网络、存储、Key Vault 和应用服务资源提供程序安装与此配置文件关联的模块：

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
