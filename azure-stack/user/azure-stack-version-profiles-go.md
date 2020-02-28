---
title: 结合使用 API 版本配置文件 Azure Stack 中心
description: 了解如何在 Azure Stack 集线器中使用 API 版本配置文件。
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/26/2019
ms.openlocfilehash: d008a30991e41be6abc3f21f888acfbc8d46d69e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77705230"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack-hub"></a>结合使用 API 版本配置文件 Azure Stack 中心

## <a name="go-and-version-profiles"></a>中转和版本配置文件

配置文件是不同服务的不同版本的不同资源类型的组合。 使用配置文件可帮助你在不同的资源类型之间进行混合和匹配。 配置文件可提供以下优势：

- 通过锁定特定 API 版本来实现应用的稳定性。
- 与 Azure Stack 中心和区域 Azure 数据中心的应用的兼容性。

在 "前往" SDK 中，配置文件位于配置文件路径下。 配置文件版本号以**yyyy-mm-dd**格式标记。 最新 Azure Stack 中心 API 配置文件版本为**2019-03-01** ，适用于 Azure Stack 集线器版本1904或更高版本。 若要从配置文件导入给定的服务，请从配置文件导入其相应的模块。 例如，若要从**2019-03-01**配置文件导入**计算**服务，请使用以下代码：

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/compute/mgmt/compute"
```

## <a name="install-the-azure-sdk-for-go"></a>安装 Azure SDK for Go

1. 安装 Git。 有关说明，请参阅[入门-安装 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
2. 安装[中转编程语言](https://golang.org/dl)。 Azure 的 API 配置文件需要1.9 版或更高版本。
3. 通过运行以下 bash 命令安装 Azure 中转 SDK 及其依赖项：

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>中转 SDK

可在以下链接中找到有关 Azure 中转 SDK 的详细信息：

- Azure 中转 SDK[安装 Azure SDK for Go](/go/azure/azure-sdk-go-install)。
- Azure 中转 SDK 公开提供于[azure SDK](https://github.com/Azure/azure-sdk-for-go)即用存储库中的 GitHub。

### <a name="go-autorest-dependencies"></a>AutoRest 依赖项

中转 SDK 依赖于 Azure **AutoRest**模块将 REST 请求发送到 azure 资源管理器终结点。 必须从[GitHub 上的 Azure AutoRest](https://github.com/Azure/go-autorest)中导入 azure **AutoRest**模块依赖项。 可以在 "**安装**" 部分中找到 "安装 bash" 命令。

## <a name="how-to-use-go-sdk-profiles-on-azure-stack-hub"></a>如何在 Azure Stack 中心使用中转 SDK 配置文件

若要在 Azure Stack Hub 上运行 "代码示例"，请执行以下步骤：

1. 安装 Azure SDK for Go 及其依赖项。 有关说明，请参阅上一节[安装 Azure SDK for Go](#install-the-azure-sdk-for-go)。
2. 从资源管理器终结点获取元数据信息。 终结点返回 JSON 文件，其中包含运行你的 "开始" 代码所需的信息。

   > [!NOTE]  
   > Azure Stack 开发工具包中的**ResourceManagerUrl** （ASDK）是： `https://management.local.azurestack.external/`  
   > 集成系统中的**ResourceManagerUrl**为： `https://management.<region>.<fqdn>/`  
   > 若要检索所需的元数据： `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
   示例 JSON 文件：

   ```json
   { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
     "graphEndpoint": "https://graph.windows.net/",  
     "portal Endpoint": "https://portal.local.azurestack.external/",
     "authentication": {
       "loginEndpoint": "https://login.windows.net/",
       "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
     }
   }
   ```

3. 如果不可用，请创建订阅，并保存订阅 ID 供稍后使用。 有关创建订阅的详细信息，请参阅[在 Azure Stack Hub 中创建对产品/服务的订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。

4. 使用**订阅**范围和**所有者**角色创建使用客户端机密的服务主体。 保存服务主体 ID 和机密。 有关为 Azure Stack 中心创建服务主体的信息，请参阅[使用应用标识来访问资源](../operator/azure-stack-create-service-principals.md)。 现在已设置 Azure Stack 中心环境。

5. 从代码中的 "离开" SDK 配置文件导入服务模块。 Azure Stack 中心配置文件的当前版本为**2019-03-01**。 例如，若要从**2019-03-01**配置文件类型导入网络模块，请使用以下代码：

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   ```

6. 在函数中，使用**新**的客户端函数调用创建并验证客户端。 若要创建虚拟网络客户端，请使用以下代码：  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   将 `<baseURI>` 设置为在步骤2中使用的**ResourceManagerUrl**值。 将 `<subscriptionID>` 设置为在步骤3中保存的**SubscriptionID**值。

   若要创建令牌，请参阅下一节。  

7. 使用在上一步中创建的客户端调用 API 方法。 例如，若要使用上一步中的客户端创建虚拟网络，请参阅以下示例：

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

有关使用中转 SDK 配置文件在 Azure Stack 集线器上创建虚拟网络的完整示例，请参阅[示例](#example)。

## <a name="authentication"></a>Authentication

若要使用转 SDK Azure Active Directory 获取**授权**者属性，请安装**AutoRest**模块。 这些模块应该已经安装了 "转向 SDK" 安装。 如果未安装，请[从 GitHub 安装身份验证包](https://github.com/Azure/go-autorest/tree/master/autorest/adal)。

授权者必须设置为资源客户端的授权者。 使用客户端凭据可以通过不同的方式在 Azure Stack 中心获取授权者令牌：

1. 如果订阅上具有所有者角色的服务主体可用，请跳过此步骤。 否则，请参阅[使用应用标识来访问资源](../operator/azure-stack-create-service-principals.md)，获取有关创建使用客户端机密的服务主体的说明，以及如何为其分配属于订阅的 "所有者" 角色的帮助。 务必捕获服务主体应用程序 ID 和机密。

2. 从代码中的**AutoRest**导入**adal**包。

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. 通过从**adal**模块使用 NewOAuthConfig 方法创建**oauthConfig** 。

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   将 `<activeDirectoryEndpoint>` 设置为本文档上一部分中检索到的 `ResourceManagerUrl` 元数据的 `loginEndpoint` 属性的值。 将 `<tenantID>` 值设置为你的 Azure Stack 中心租户 ID。

4. 最后，使用**adal**模块中的 `NewServicePrincipalToken` 方法创建服务主体令牌：

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/adal"

   func CreateToken() (adal.OAuthTokenProvider, error) {
       var token adal.OAuthTokenProvider
       oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
       token, err = adal.NewServicePrincipalToken(
           *oauthConfig,
           clientID,
           clientSecret,
           activeDirectoryResourceID)
       return token, err
   ```

    从本文上一部分中检索到的**ResourceManagerUrl**元数据，将 `<activeDirectoryResourceID>` 设置为 "受众" 列表中的一个值。
    将 `<clientID>` 设置为在本文上一部分中创建服务主体时保存的服务主体应用程序 ID。
    将 `<clientSecret>` 设置为在本文上一部分中创建服务主体时保存的服务主体应用程序机密。

## <a name="example"></a>示例

此示例显示了在 Azure Stack 集线器上创建虚拟网络的 "开始代码" 示例。 有关中转 SDK 的完整示例，请参阅[Azure 中转 sdk 示例存储库](https://github.com/Azure-Samples/azure-sdk-for-go-samples)。 Azure Stack 中心示例位于存储库服务文件夹内的混合路径下。

> [!NOTE]  
> 若要运行此示例中的代码，请验证所使用的订阅是否将**网络**资源提供程序列为**已注册**。 若要验证，请在 Azure Stack 中心门户中查找该订阅，并选择 "**资源提供程序"。**

1. 在代码中导入所需的包。 使用 Azure Stack 集线器上的最新可用配置文件导入网络模块：

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. 定义环境变量。 若要创建虚拟网络，必须有一个资源组。

   ```go
   var (
       activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
       tenantID = "yourAzureStackTenantID"
       clientID = "yourServicePrincipalApplicationID"
       clientSecret = "yourServicePrincipalSecret"
       activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
       subscriptionID = "yourSubscriptionID"
       baseURI = "yourResourceManagerURL"
       resourceGroupName = "existingResourceGroupName"
   )
   ```

3. 现在，你已定义了环境变量，请添加方法以使用**adal**包创建身份验证令牌。 有关身份验证的详细信息，请参阅上一节。

   ```go
   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }
   ```

4. 添加 `main` 方法。 `main` 方法首先使用上一步中定义的方法获取令牌。 然后，它将使用配置文件中的网络模块来创建客户端。 最后，它将创建一个虚拟网络。

   ```go
   package main

   import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
   )

   var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
     subscriptionID = "yourSubscriptionID"
     baseURI = "yourResourceManagerURL"
     resourceGroupName = "existingResourceGroupName"
   )

   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }

   func main() {
      token, _ := CreateToken()
      vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
      future, _ := vnetClient.CreateOrUpdate(
          context.Background(),
          resourceGroupName,
          "sampleVnetName",
          network.VirtualNetwork{
              Location: to.StringPtr("local"),
              VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                  AddressSpace: &network.AddressSpace{
                      AddressPrefixes: &[]string{"10.0.0.0/8"},
                  },
                  Subnets: &[]network.Subnet{
                      {
                          Name: to.StringPtr("subnetName"),
                          SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                              AddressPrefix: to.StringPtr("10.0.0.0/16"),
                          },
                      },
                  },
              },
          })
      err := future.WaitForCompletionRef(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```

使用 "前往 SDK" Azure Stack 中心提供的一些代码示例如下：

- [创建虚拟机](https://github.com/Azure-Samples/Hybrid-Compute-Go-Create-VM)
- [存储数据平面](https://github.com/Azure-Samples/Hybrid-Storage-Go-Dataplane)
- [使用托管磁盘](https://github.com/Azure-Samples/Hybrid-Compute-Go-ManagedDisks)（一个示例，使用2019-03-01 配置文件，该配置文件面向 Azure Stack 集线器支持的最新 API 版本）

## <a name="next-steps"></a>后续步骤

- [为 Azure Stack 集线器安装 PowerShell](../operator/azure-stack-powershell-install.md)
- [配置 Azure Stack 集线器用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
