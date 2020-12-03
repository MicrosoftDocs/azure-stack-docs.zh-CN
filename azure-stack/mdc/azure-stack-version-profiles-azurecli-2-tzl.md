---
title: Azure CLI 管理 Azure Stack 集线器 |Microsoft Docs
description: 了解如何使用跨平台命令行界面 (CLI) 在 Azure Stack Hub 上管理和部署资源。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: b9d40c83c31903a582d8130dcb249fa2fca0a49e
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525568"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>借助 Azure CLI 管理资源并将其部署到 Azure Stack Hub

*适用于：Azure Stack Hub 集成系统*

按照本文中的步骤设置 Azure Command-Line 接口 (CLI) ，以便从 Linux、Mac 和 Windows 客户端平台管理 Azure Stack 集线器资源。

## <a name="prepare-for-azure-cli"></a>准备 Azure CLI

**虚拟机别名终结点** 提供类似于“UbuntuLTS”或“Win2012Datacenter”的别名。 此别名引用映像发布者、套餐、SKU 和版本作为部署 VM 时的单个参数。  

以下部分介绍如何设置虚拟机别名终结点。

### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>设置虚拟机别名终结点

可以设置一个可公开访问的终结点用于托管 VM 别名文件。 VM 别名文件是一个 JSON 文件，提供映像的公用名称。 以 Azure CLI 参数形式部署 VM 时，将使用该名称。

1. 如果发布自定义映像，请记下发布过程中指定的发布者、产品/服务、SKU 和版本信息。 如果它是 Marketplace 中的映像，则可以使用 cmdlet 来查看信息 ```Get-AzureVMImage``` 。  

2. 从 GitHub 下载[示例文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。

3. 在 Azure Stack Hub 中创建存储帐户。 完成该操作后，将创建 Blob 容器。 将访问策略设置为“公开”。  

4. 将 JSON 文件上传到新容器。 完成该操作后，可以查看 blob 的 URL。 选择 blob 名称，然后从 blob 属性中选择该 URL。

### <a name="install-or-upgrade-cli"></a>安装或升级 CLI

登录到开发工作站并安装 CLI。 Azure Stack 中心需要 Azure CLI 版本2.0 或更高版本。 最新版本的 API 配置文件需要最新版本的 CLI。 使用[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 一文中所述的步骤安装 CLI。 

若要验证安装是否成功，请打开终端或命令提示符窗口，并运行以下命令：

```shell
az --version
```

应会看到 Azure CLI 的版本，以及计算机上安装的其他依赖库。
    
![Azure Stack 中心 Python 位置上的 Azure CLI](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)


## <a name="windowslinux-azure-ad"></a>Windows/Linux (Azure AD) 

本部分将指导你设置 CLI （如果你使用 Azure AD 作为标识管理服务），并在 Windows/Linux 计算机上使用 CLI。

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub

1. 通过运行命令注册 Azure Stack 中心环境 `az cloud register` 。

2. 注册环境。 在运行 `az cloud register` 时使用以下参数：

    | 值 | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> 集成系统中的 **ResourceManagerUrl** 是： `https://management.<region>.<fqdn>/` 如果你对集成系统终结点有疑问，请联系你的云操作员。 |
    | 存储终结点 | local.azurestack.external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> 对于集成系统，请使用适用于系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅[设置 VM 别名终结点](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. 使用以下命令设置活动环境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. 将环境配置更新为使用 Azure Stack Hub 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```
 
1. 使用命令登录到 Azure Stack 中心环境 `az login` 。 以用户或服务主体身份登录到 Azure Stack 中心环境。 

   - 以 *用户* 身份登录： 

     可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > 如果用户帐户已启用多重身份验证，请使用不带 `-u` 参数的 `az login` 命令。 运行此命令会提供一个 URL 以及身份验证时必须使用的代码。

   - 以 *服务主体* 身份登录： 
    
     在登录之前，请[通过 Azure 门户或 CLI 创建一个服务主体](../operator/azure-stack-create-service-principals.md)，并为其分配角色。 接下来，使用以下命令登录：

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/output.png)

## <a name="windowslinux-ad-fs"></a>Windows/Linux (AD FS) 

本部分将指导你设置 CLI （如果你使用的是 Active Directory 联合服务 (AD FS) 作为标识管理服务，并且在 Windows/Linux 计算机上使用 CLI）。


### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub

1. 通过运行命令注册 Azure Stack 中心环境 `az cloud register` 。

2. 注册环境。 在运行 `az cloud register` 时使用以下参数：

    | 值 | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> 集成系统中的 **ResourceManagerUrl** 是： `https://management.<region>.<fqdn>/` 如果你对集成系统终结点有疑问，请联系你的云操作员。 |
    | 存储终结点 | local.azurestack.external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> 对于集成系统，请使用适用于系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅[设置 VM 别名终结点](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. 使用以下命令设置活动环境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. 将环境配置更新为使用 Azure Stack Hub 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

1. 使用命令登录到 Azure Stack 中心环境 `az login` 。 你可以作为用户或服务主体登录到 Azure Stack 中心环境。 

   - 以 *用户* 身份登录：

     可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > 如果用户帐户已启用多重身份验证，请使用不带 `-u` 参数的 `az login` 命令。 运行此命令会提供一个 URL 以及身份验证时必须使用的代码。

   - 以 *服务主体* 身份登录： 
    
     准备要用于服务主体登录的 .pem 文件。

     在创建主体的客户端计算机上，使用位于 `cert:\CurrentUser\My` 的私钥将服务主体证书导出为 pfx。 证书名称与主体名称相同。

     将 pfx 转换为 pem（使用 OpenSSL 实用工具）。

     登录到 CLI：
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/output.png)


## <a name="known-issues"></a>已知问题

在 Azure Stack 集线器中使用 CLI 时存在一些已知问题：

 - CLI 交互模式。 例如， `az interactive` Azure Stack 集线器尚不支持该命令。
 - 若要获取 Azure Stack 中心可用的 VM 映像列表，请使用 `az vm image list --all` 命令，而不是 `az vm image list` 命令。 指定 `--all` 选项可确保响应仅返回 Azure Stack 中心环境中可用的映像。
 - Azure 中提供的 VM 映像别名可能不适用于 Azure Stack 中心。 使用 VM 映像时，必须使用整个 URN 参数 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0)，而不是映像别名。 此 URN 必须与派生自 `az vm images list` 命令的映像规范相匹配。

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 部署模板](../user/azure-stack-deploy-template-command-line.md)
- [为 Azure Stack 中心用户 (操作员启用 Azure CLI) ](../operator/azure-stack-cli-admin.md)
- [管理用户权限](../operator/azure-stack-manage-permissions.md) 