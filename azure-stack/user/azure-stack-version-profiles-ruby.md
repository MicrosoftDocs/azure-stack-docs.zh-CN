---
title: 在 Azure Stack 中心使用带有 Ruby 的 API 版本配置文件
description: 了解如何在 Azure Stack 集线器中将 API 版本配置文件与 Ruby 一起使用。
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 5b79c676b922f0e76ed75e3ad043f53c1fb9d6a5
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77705162"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack-hub"></a>在 Azure Stack 中心使用带有 Ruby 的 API 版本配置文件

## <a name="ruby-and-api-version-profiles"></a>Ruby 和 API 版本配置文件

Azure Stack 中心的 Ruby SDK 资源管理器提供帮助你构建和管理基础结构的工具。 SDK 中的资源提供程序包括具有 Ruby 语言的计算、虚拟网络和存储。 Ruby SDK 中的 API 配置文件可帮助你在 Azure Stack 集线器上的全局 Azure 资源与资源之间切换，从而实现了混合云开发。

API 配置文件是资源提供程序和服务版本的组合。 你可以使用 API 配置文件来合并不同的资源类型。

- 若要使用所有服务的最新版本，请使用**最新**的 Azure SDK 汇总 gem 配置文件。
- 若要使用与 Azure Stack 中心兼容的服务，请使用 Azure SDK 汇总 gem 的**V2019_03_01_Hybrid**或**V2018_03_01**配置文件。
- 若要使用服务的最新**api 版本**，请使用特定 Gem 的**最新**配置文件。 例如，若要单独使用计算服务的最新**api 版本**，请使用**计算**gem 的**最新**配置文件。
- 若要对服务使用特定的**api 版本**，请使用 gem 内定义的特定 api 版本。

> [!NOTE]
> 可以组合同一应用中的所有选项。

## <a name="install-the-azure-ruby-sdk"></a>安装 Azure Ruby SDK

- 按照正式说明安装[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
- 按照正式说明安装[Ruby](https://www.ruby-lang.org/en/documentation/installation/)。
  - 安装时，请选择 "**将 Ruby 添加到路径变量**"。
  - 在 Ruby 安装过程中出现提示时，请安装开发工具包。
  - 接下来，使用以下命令安装捆绑程序： 

       ```Ruby
       Gem install bundler
       ```

- 如果不可用，请创建订阅，并保存订阅 ID 供稍后使用。 有关创建订阅的说明，请使用[Azure Stack Hub 中的产品/服务创建订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。
- 创建一个服务主体，并保存其 ID 和机密。 有关为 Azure Stack 中心创建服务主体的说明，[请访问使用应用标识访问资源](../operator/azure-stack-create-service-principals.md)一文。
- 确保你的服务主体在你的订阅上分配了参与者/所有者角色。 有关如何向服务主体分配角色的说明，[请使用应用标识来访问资源](../operator/azure-stack-create-service-principals.md)。

## <a name="install-the-rubygem-packages"></a>安装 RubyGem 包

可以直接安装 Azure RubyGem 包。

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
```

或者，在 Gemfile 中使用它们。

```Ruby
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Azure 资源管理器 Ruby SDK 处于预览阶段，并且可能会在即将发布的版本中进行更改。 次版本中增加的数字可能表示存在重大更改。

## <a name="use-the-azure_sdk-gem"></a>使用 azure_sdk gem

**Azure_sdk** Gem 是 Ruby sdk 中所有受支持的 gem 的汇总。 此 gem 包含 **最新**的 配置文件，该配置文件支持所有服务的最新版本。 它包括为 Azure Stack 中心构建 **V2017_03_09**和**V2019_03_01_Hybrid**的版本控制配置文件。

可以通过以下命令安装 azure_sdk 汇总 gem：  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>必备条件

若要将 Ruby Azure SDK 与 Azure Stack 中心一起使用，必须提供以下值，并使用环境变量设置值。 若要设置环境变量，请参阅下表中针对特定操作系统的说明。

| 值 | 环境变量 | 说明 |
| --- | --- | --- |
| 租户 ID | `AZURE_TENANT_ID` | Azure Stack 中心[租户 ID](../operator/azure-stack-identity-overview.md)。 |
| 客户端 ID | `AZURE_CLIENT_ID` | 在本文上一部分中创建服务主体时保存的服务主体应用 ID。  |
| 订阅 ID | `AZURE_SUBSCRIPTION_ID` | 使用[订阅 ID](../operator/service-plan-offer-subscription-overview.md#subscriptions)可以访问 Azure Stack 集线器中的产品/服务。 |
| 客户端机密 | `AZURE_CLIENT_SECRET` | 在创建服务主体时保存的服务主体应用程序机密。 |
| 资源管理器终结点 | `ARM_ENDPOINT` | 请参阅[Azure Stack 中心资源管理器终结点](#the-azure-stack-hub-resource-manager-endpoint)。  |

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Azure Stack 中心资源管理器终结点

Microsoft Azure 资源管理器是一种管理框架，管理员可通过它来部署、管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

可以从资源管理器终结点获取元数据信息。 终结点返回 JSON 文件，其中包含运行代码所需的信息。

 > [!NOTE]  
 > Azure Stack 开发工具包**中的** **ResourceManagerUrl** （ASDK）是 `https://management.local.azurestack.external/`： `https://management.region.<fqdn>/`，其中 `<fqdn>` 是完全限定的域名。  
 > 若要检索所需的元数据： `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 示例 JSON 文件：

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environment-variables"></a>设置环境变量

#### <a name="microsoft-windows"></a>Microsoft Windows

若要设置环境变量，请在 Windows 命令提示符中使用以下格式：

```shell
set AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

#### <a name="macos-linux-and-unix-based-systems"></a>macOS、Linux 和基于 Unix 的系统

在基于 Unix 的系统中，使用以下命令：

```bash
export AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

## <a name="existing-api-profiles"></a>现有 API 配置文件

**Azure_sdk**汇总 gem 包含以下3个配置文件：

- **V2019_03_01_Hybrid**：为 Azure Stack 中心生成的配置文件。 将此配置文件用于 Azure Stack 集线器1904版或更高版本中提供的所有最新版本的服务。
- **V2017_03_09**：为 Azure Stack 中心生成的配置文件。 使用此配置文件来与 Azure Stack 中心版本1808或更低版本最兼容。
- **最新**：配置文件包含所有服务的最新版本。 使用所有服务的最新版本。

有关 Azure Stack 中心和 API 配置文件的详细信息，请参阅[API 配置文件摘要](azure-stack-version-profiles.md#summary-of-api-profiles)。

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API 配置文件使用情况

使用以下代码实例化配置文件客户端。 仅 Azure Stack 集线器或其他私有云需要此参数。 默认情况下，公有云 Azure 已经有这些设置。

```Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
  ENV['AZURE_TENANT_ID'],
  ENV['AZURE_CLIENT_ID'],
  ENV['AZURE_CLIENT_SECRET'],
  active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
  credentials: credentials,
  subscription_id: subscription_id,
  active_directory_settings: active_directory_settings,
  base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack Hub
client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
```

配置文件客户端可用于访问单个资源提供程序，例如计算、存储和网络：

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2019_03_01_Hybrid::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-hub-environment-setting-functions"></a>定义 Azure Stack 中心环境设置函数

若要对 Azure Stack 中心环境中的服务主体进行身份验证，请使用 `get_active_directory_settings()`定义终结点。 此方法使用之前设置的**ARM_Endpoint**环境变量：

```Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Hub Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>使用 API 配置文件的示例

使用 GitHub 上的以下示例作为参考，通过 Ruby 和 Azure Stack 中心 API 配置文件创建解决方案：

- [利用 Ruby 管理 Azure 资源和资源组](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)。
- [使用 Ruby 管理虚拟机](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM)（使用2019-03-01 混合配置文件的示例以面向 Azure Stack 集线器支持的最新 API 版本）。
- [在 Ruby 中使用模板部署启用 SSH 的 VM](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)。

### <a name="sample-resource-manager-and-groups"></a>示例资源管理器和组

若要运行该示例，请确保已安装 Ruby。 如果使用 Visual Studio Code，请同时下载 Ruby SDK 扩展。

> [!NOTE]  
> 示例的存储库是[混合资源管理器-Ruby 资源和组](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)。

1. 克隆存储库：

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. 使用捆绑包安装依赖项：

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. 使用 PowerShell 创建 Azure 服务主体，并检索所需的值。

   有关创建服务主体的说明，请参阅[使用 Azure PowerShell 使用证书创建服务主体](../operator/azure-stack-create-service-principals.md)。

   所需的值是：

   - 租户 ID
   - 客户端 ID
   - 客户端机密
   - 订阅 ID
   - 资源管理器终结点

   使用从创建的服务主体检索的信息设置以下环境变量：

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`

   > [!NOTE]  
   > 在 Windows 上，使用 `set` 而不是 `export`。

4. 确保将位置变量设置为 Azure Stack 中心位置;例如，`LOCAL="local"`。

5. 若要以正确的 active directory 终结点为目标，请在使用 Azure Stack 集线器或其他私有云时，添加以下代码行：

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. 在 `options` 变量中，添加 Active Directory 设置和基 URL 以使用 Azure Stack 集线器：

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. 创建以 Azure Stack 中心配置文件为目标的配置文件客户端：

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. 若要使用 Azure Stack 集线器对服务主体进行身份验证，应使用**get_active_directory_settings （）** 定义终结点。 此方法使用之前设置的**ARM_Endpoint**环境变量：

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Hub Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. 运行该示例。

   ```Ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>后续步骤

- [为 Azure Stack 集线器安装 PowerShell](../operator/azure-stack-powershell-install.md)
- [配置 Azure Stack 集线器用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)  
