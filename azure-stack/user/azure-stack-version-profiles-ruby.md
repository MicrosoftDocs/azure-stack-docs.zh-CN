---
title: 在 Azure Stack Hub 中将 API 版本配置文件与 Ruby 配合使用
description: 了解如何在 Azure Stack Hub 中将 API 版本配置文件与 Ruby 配合使用。
author: sethmanheim
ms.topic: article
ms.date: 12/2/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 1d4cc0887bac12c535f2ce0fe12a2cb855d5b3a5
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525585"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack-hub"></a>在 Azure Stack Hub 中将 API 版本配置文件与 Ruby 配合使用

## <a name="ruby-and-api-version-profiles"></a>Ruby 与 API 版本配置文件

用于 Azure Stack Hub 资源管理器的 Ruby SDK 提供了相关工具来帮助构建和管理基础结构。 该 SDK 中的资源提供程序包括了采用 Ruby 语言的计算、虚拟网络和存储提供程序。 Ruby SDK 中的 API 配置文件可以用来在公有云 Azure 资源和 Azure Stack Hub 上的资源之间进行切换，实现混合云开发。

API 配置文件是资源提供程序和服务版本的组合。 可以使用 API 配置文件来组合不同的资源类型。

- 若要使用所有服务的最新版本，请使用 Azure SDK 汇总 gem 的 **最新** 配置文件。
- 若要使用与 Azure Stack Hub 兼容的服务，请使用 Azure SDK 汇总 gem 的 **V2019_03_01_Hybrid** 或 **V2018_03_01** 配置文件。
- 若要使用某个服务的最新 **api-version**，请使用特定 gem 的 **最新** 配置文件。 例如，若要单独使用计算服务的最新 **api-version**，请使用 **计算** gem 的 **最新** 配置文件。
- 若要使用某个服务的特定 **api-version**，请使用在 gem 中定义的特定 API 版本。

> [!NOTE]
> 可以在同一应用中组合所有选项。

## <a name="install-the-azure-ruby-sdk"></a>安装 Azure Ruby SDK

- 遵照官方说明安装 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
- 遵照官方说明安装 [Ruby](https://www.ruby-lang.org/en/documentation/installation/)。
  - 在安装时选择“将 Ruby 添加到 PATH 变量”。
  - 在 Ruby 安装过程中出现提示时，安装开发工具包。
  - 接下来，使用以下命令安装捆绑程序： 

       ```ruby
       Gem install bundler
       ```

- 如果不可用，请创建订阅，并保存订阅 ID 供稍后使用。 有关创建订阅的说明，请参阅[在 Azure Stack Hub 中创建套餐的订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)一文。
- 创建服务主体并保存其 ID 和机密。 有关为 Azure Stack Hub 创建服务主体的说明，请参阅[使用应用标识访问资源](../operator/azure-stack-create-service-principals.md)一文。
- 确保服务主体在订阅上分配有“参与者/所有者”角色。 有关如何为服务主体分配角色的说明，请参阅[使用应用标识访问资源](../operator/azure-stack-create-service-principals.md)一文。

## <a name="install-the-rubygem-packages"></a>安装 RubyGem 包

可以直接安装 Azure RubyGem 包。

```ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
```

也可以在 Gemfile 中使用这些包。

```ruby
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Azure 资源管理器 Ruby SDK 为预览版，在即将推出的版本中可能会有重大界面更改。 在次要版本中，编号数字增加可能表示重大更改。

## <a name="use-the-azure_sdk-gem"></a>使用 azure_sdk gem

**azure_sdk** gem 汇总了 Ruby SDK 中所有受支持的 gem。 此 gem 包含的 **最新** 配置文件支持所有服务的最新版本。 它包括为 Azure Stack 中心构建 **V2017_03_09** 和 **V2019_03_01_Hybrid** 的版本控制配置文件。

可使用以下命令安装 azure_sdk 汇总 gem：  

```ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>先决条件

若要将 Ruby Azure SDK 与 Azure Stack Hub 配合使用，必须提供以下值，然后使用环境变量来设置值。 若要设置环境变量，请参阅表后针对特定操作系统的说明。

| Value | 环境变量 | 说明 |
| --- | --- | --- |
| 租户 ID | `AZURE_TENANT_ID` | Azure Stack Hub [租户 ID](../operator/azure-stack-identity-overview.md)。 |
| 客户端 ID | `AZURE_CLIENT_ID` | 在本文上一部分创建服务主体时保存的服务主体应用 ID。  |
| 订阅 ID | `AZURE_SUBSCRIPTION_ID` | 使用[订阅 ID](../operator/service-plan-offer-subscription-overview.md#subscriptions) 访问 Azure Stack Hub 中的套餐。 |
| 客户端机密 | `AZURE_CLIENT_SECRET` | 创建服务主体时保存的服务主体应用机密。 |
| 资源管理器终结点 | `ARM_ENDPOINT` | 参阅 [Azure Stack Hub 资源管理器终结点](#the-azure-stack-hub-resource-manager-endpoint)。  |

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Azure Stack Hub 资源管理器终结点

Microsoft Azure 资源管理器是一种管理框架，管理员可通过它来部署、管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

可以从资源管理器终结点获取元数据信息。 该终结点返回 JSON 文件，其中包含运行代码所需的信息。

 > [!NOTE]  
 > Azure Stack 开发工具包 (ASDK) 中的 **ResourceManagerUrl** 为：`https://management.local.azurestack.external/` 集成系统中的 **ResourceManagerUrl** 为 `https://management.region.<fqdn>/`，其中 `<fqdn>` 是完全限定的域名。  
 > 检索所需的元数据：`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
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

### <a name="set-environment-variables"></a>设置环境变量。

#### <a name="microsoft-windows"></a>Microsoft Windows

若要设置环境变量，请在 Windows 命令提示符下使用以下格式：

```console
set AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

#### <a name="macos-linux-and-unix-based-systems"></a>基于 macOS、Linux 和 Unix 的系统

在基于 Unix 的系统中，使用以下命令：

```bash
export AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

## <a name="existing-api-profiles"></a>现有 API 配置文件

**Azure_sdk** 汇总 gem 包含以下 3 个配置文件：

- **V2019_03_01_Hybrid**：为 Azure Stack Hub 生成的配置文件。 将此配置文件用于 Azure Stack Hub 版本 1904 或更高版本中提供的所有最新版服务。
- **V2017_03_09**：为 Azure Stack Hub 生成的配置文件。 使用此配置文件可以使服务与 Azure Stack Hub 版本 1808 或更早版本最兼容。
- **最新**：配置文件包含所有服务的最新版本。 使用所有服务的最新版本。

有关 Azure Stack Hub 和 API 配置文件的详细信息，请参阅 [API 配置文件的摘要](azure-stack-version-profiles.md#summary-of-api-profiles)。

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API 配置文件的使用

使用以下代码实例化配置文件客户端。 此参数只是 Azure Stack Hub 或其他私有云所需要的。 默认情况下，公有云 Azure 已经有这些设置。

```ruby  
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

可以使用配置文件客户端来访问单个资源提供程序，例如计算、存储和网络提供程序：

```ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2019_03_01_Hybrid::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-hub-environment-setting-functions"></a>定义 Azure Stack Hub 环境设置函数

若要通过 Azure Stack Hub 环境进行服务主体身份验证，请使用 `get_active_directory_settings()` 来定义终结点 此方法使用之前设置的 **ARM_Endpoint** 环境变量：

```ruby  
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

参考 GitHub 上的以下示例，使用 Ruby 和 Azure Stack Hub API 配置文件来创建解决方案：

- [使用 Ruby 管理 Azure 资源和资源组](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)。
- [使用 Ruby 管理虚拟机](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM)（使用 2019-03-01-hybrid 配置文件针对 Azure Stack Hub 支持的最新 API 版本的示例）。
- [在 Ruby 中使用模板部署启用 SSH 的 VM](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)。

### <a name="sample-resource-manager-and-groups"></a>示例资源管理器和组

若要运行示例，请确保已安装 Ruby。 如果使用 Visual Studio Code，也请下载 Ruby SDK 扩展。

> [!NOTE]  
> 示例的存储库是 [Hybrid-Resource-Manager-Ruby-Resources-And-Groups](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)。

1. 克隆存储库：

   ```console
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. 使用捆绑安装依赖项：

   ```console
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. 使用 PowerShell 创建 Azure 服务主体，然后检索所需的值。

   有关如何创建服务主体的说明，请参阅[使用 Azure PowerShell 创建具有证书的服务主体](../operator/azure-stack-create-service-principals.md)。

   所需值为：

   - 租户 ID
   - 客户端 ID
   - 客户端机密
   - 订阅 ID
   - 资源管理器终结点

   使用从已创建的服务主体检索的信息设置以下环境变量：

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`

   > [!NOTE]  
   > 在 Windows 上，使用 `set` 而不是 `export`。

4. 确保将位置变量设置为你的 Azure Stack Hub 位置，例如 `LOCAL="local"`。

5. 若要定位正确的活动目录终结点，如果你使用的是 Azure Stack Hub 或其他私有云，请添加以下代码行：

   ```ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. 在 `options` 变量中添加 Active Directory 设置和适用于 Azure Stack Hub 的基 URL：

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. 创建以 Azure Stack Hub 配置文件为目标的配置文件客户端：

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. 若要通过 Azure Stack Hub 进行服务主体身份验证，应使用 **get_active_directory_settings()** 来定义终结点。 此方法使用之前设置的 **ARM_Endpoint** 环境变量：

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

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>后续步骤

- [安装适用于 Azure Stack Hub 的 PowerShell](../operator/powershell-install-az-module.md)
- [配置 Azure Stack Hub 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)  
