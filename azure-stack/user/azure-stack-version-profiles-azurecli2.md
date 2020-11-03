---
title: 使用 Azure CLI 管理 Azure Stack Hub
description: 了解如何使用跨平台命令行接口 (CLI) 管理和部署 Azure Stack Hub 上的资源。
author: mattbriggs
ms.topic: article
ms.date: 10/26/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 35378da825d9b2d9c7446148101f1d205a22b2c4
ms.sourcegitcommit: b960df16e84ec9fbccfce772102b91f0b7ae7060
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93291244"
---
# <a name="install-azure-cli-on-azure-stack-hub"></a>在 Azure Stack 集线器上安装 Azure CLI

可以使用 Windows 或 Linux 计算机安装 Azure CLI 来管理 Azure Stack 中心。 本文将指导你完成安装和设置 Azure CLI 的步骤。

## <a name="install-azure-cli"></a>安装 Azure CLI

1. 登录到开发工作站并安装 CLI。 Azure Stack 中心需要 Azure CLI 版本2.0 或更高版本。 

2. 可以使用[安装 Azure CLI](/cli/azure/install-azure-cli) 一文中所述的步骤安装 CLI。 

3. 若要验证安装是否成功，请打开终端或命令提示符窗口，并运行以下命令：

    ```shell
    az --version
    ```

    应会看到 Azure CLI 的版本，以及计算机上安装的其他依赖库。

    ![Azure Stack 中心 Python 位置上的 Azure CLI](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. 记下 CLI 的 Python 位置。 如果正在运行 ASDK，则需要使用此位置来添加证书。 有关设置用于在 ASDK 上安装 CLI 的证书的说明，请参阅 [Azure Stack 开发工具包上的 Azure CLI 设置证书](../asdk/asdk-cli.md)。

## <a name="set-up-azure-cli"></a>设置 Azure CLI

### <a name="azure-ad-on-windows"></a>[Windows 上的 Azure AD](#tab/ad-win)

如果使用 Azure AD 作为标识管理服务，并在 Windows 计算机上使用 CLI，可以参考本部分完成 CLI 设置过程。

#### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub

1. 如果使用的是 ASDK，请信任 Azure Stack 中心 CA 根证书。 有关说明，请参阅 [信任证书](../asdk/asdk-cli.md#trust-the-certificate)。

2. 通过运行命令注册 Azure Stack 中心环境 `az cloud register` 。

3. 注册环境。 在运行 `az cloud register` 时使用以下参数：

    | 值 | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | ASDK 中的 **ResourceManagerUrl** 是： `https://management.local.azurestack.external/` 集成系统中的 **ResourceManagerUrl** 是： `https://management.<region>.<fqdn>/` 如果你对集成系统终结点有疑问，请联系你的云操作员。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅 [设置虚拟机别名终结点](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. 使用以下命令设置活动环境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. 将环境配置更新为使用 Azure Stack Hub 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果在1808版本之前运行 Azure Stack 中心版本，则必须使用 API 版本配置文件 **2017-03-09-配置** 文件，而不是 api 版本配置文件 **2019-03-01-混合** 。 还需要使用最新版本的 Azure CLI。
 
6. 使用命令登录到 Azure Stack 中心环境 `az login` 。 以用户或 [服务主体](/azure/active-directory/develop/app-objects-and-service-principals)身份登录到 Azure Stack 中心环境。 

   - 以 *用户* 身份登录： 

     可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > 如果用户帐户已启用多重身份验证，请使用不带 `-u` 参数的 `az login` 命令。 运行此命令会提供一个 URL 以及身份验证时必须使用的代码。

   - 以 *服务主体* 身份登录： 
    
     在登录之前，请[通过 Azure 门户或 CLI 创建一个服务主体](../operator/azure-stack-create-service-principals.md?view=azs-2002)，并为其分配角色。 接下来，使用以下命令登录：

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

#### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-on-windows"></a>[Windows 上的 AD FS](#tab/adfs-win)

如果使用 Active Directory 联合身份验证服务 (AD FS) 作为标识管理服务，并在 Windows 计算机上使用 CLI，可以参考本部分完成 CLI 设置过程。

#### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub


1. 如果使用的是 ASDK，请信任 Azure Stack 中心 CA 根证书。 有关说明，请参阅 [信任证书](../asdk/asdk-cli.md#trust-the-certificate)。

2. 通过运行命令注册 Azure Stack 中心环境 `az cloud register` 。

3. 注册环境。 在运行 `az cloud register` 时使用以下参数：

    | 值 | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | ASDK 中的 **ResourceManagerUrl** 是： `https://management.local.azurestack.external/` 集成系统中的 **ResourceManagerUrl** 是： `https://management.<region>.<fqdn>/` 如果你对集成系统终结点有疑问，请联系你的云操作员。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅 [设置虚拟机别名终结点](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. 使用以下命令设置活动环境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. 将环境配置更新为使用 Azure Stack Hub 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果在1808版本之前运行 Azure Stack 中心版本，则必须使用 API 版本配置文件 **2017-03-09-配置** 文件，而不是 api 版本配置文件 **2019-03-01-混合** 。 还需要使用最新版本的 Azure CLI。

6. 使用命令登录到 Azure Stack 中心环境 `az login` 。 你可以作为用户或 [服务主体](/azure/active-directory/develop/app-objects-and-service-principals)登录到 Azure Stack 中心环境。 

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

#### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

### <a name="azure-ad-on-linux"></a>[Linux 上的 Azure AD](#tab/ad-lin)

如果使用 Azure AD 作为标识管理服务，并在 Linux 计算机上使用 CLI，可以参考本部分完成 CLI 设置过程。

#### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub

使用以下步骤连接到 Azure Stack 中心：


1. 如果使用的是 ASDK，请信任 Azure Stack 中心 CA 根证书。 有关说明，请参阅 [信任证书](../asdk/asdk-cli.md#trust-the-certificate)。

2. 通过运行命令注册 Azure Stack 中心环境 `az cloud register` 。

3. 注册环境。 在运行 `az cloud register` 时使用以下参数：

    | 值 | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | ASDK 中的 **ResourceManagerUrl** 是： `https://management.local.azurestack.external/` 集成系统中的 **ResourceManagerUrl** 是： `https://management.<region>.<fqdn>/` 如果你对集成系统终结点有疑问，请联系你的云操作员。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅 [设置虚拟机别名终结点](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. 设置活动的环境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. 将环境配置更新为使用 Azure Stack Hub 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果在1808版本之前运行 Azure Stack 中心版本，则必须使用 API 版本配置文件 **2017-03-09-配置** 文件，而不是 api 版本配置文件 **2019-03-01-混合** 。 还需要使用最新版本的 Azure CLI。

6. 使用命令登录到 Azure Stack 中心环境 `az login` 。 你可以作为用户或 [服务主体](/azure/active-directory/develop/app-objects-and-service-principals)登录到 Azure Stack 中心环境。 

   * 以 *用户* 身份登录：

     可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > 如果用户帐户已启用多重身份验证，则可以使用不带 `-u` 参数的 `az login` 命令。 运行此命令会提供一个 URL 以及身份验证时必须使用的代码。
   
   * 以服务主体身份登录
    
     在登录之前，请[通过 Azure 门户或 CLI 创建一个服务主体](../operator/azure-stack-create-service-principals.md?view=azs-2002)，并为其分配角色。 接下来，使用以下命令登录：

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

#### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
    az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-linux"></a>[AD FS Linux](#tab/adfs-lin)

如果使用 Active Directory 联合身份验证服务 (AD FS) 作为管理服务，并在 Linux 计算机上使用 CLI，可以参考本部分完成 CLI 设置过程。

#### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub

使用以下步骤连接到 Azure Stack 中心：

1. 如果使用的是 ASDK，请信任 Azure Stack 中心 CA 根证书。 有关说明，请参阅 [信任证书](../asdk/asdk-cli.md#trust-the-certificate)。

2. 通过运行命令注册 Azure Stack 中心环境 `az cloud register` 。

3. 注册环境。 在运行 `az cloud register` 时使用以下参数。

    | 值 | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | ASDK 中的 **ResourceManagerUrl** 是： `https://management.local.azurestack.external/` 集成系统中的 **ResourceManagerUrl** 是： `https://management.<region>.<fqdn>/` 如果你对集成系统终结点有疑问，请联系你的云操作员。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅 [设置虚拟机别名终结点](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. 设置活动的环境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. 将环境配置更新为使用 Azure Stack Hub 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果在1808版本之前运行 Azure Stack 中心版本，则必须使用 API 版本配置文件 **2017-03-09-配置** 文件，而不是 api 版本配置文件 **2019-03-01-混合** 。 还需要使用最新版本的 Azure CLI。

6. 使用命令登录到 Azure Stack 中心环境 `az login` 。 你可以作为用户或 [服务主体](/azure/active-directory/develop/app-objects-and-service-principals)登录到 Azure Stack 中心环境。 

7. 登录： 

   *  将 Web 浏览器与设备代码配合使用，以 **用户** 的身份登录：  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >运行此命令会提供一个 URL 以及身份验证时必须使用的代码。

   * 以服务主体的身份：
        
     准备要用于服务主体登录的 .pem 文件。

      * 在创建主体的客户端计算机上，使用位于 `cert:\CurrentUser\My` 的私钥将服务主体证书导出为 pfx。 证书名称与主体名称相同。
  
      * 将 pfx 转换为 pem（使用 OpenSSL 实用工具）。

     登录到 CLI：

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

#### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
  az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

### <a name="known-issues"></a>已知问题

在 Azure Stack 集线器中使用 CLI 时存在一些已知问题：

 - CLI 交互模式。 例如， `az interactive` Azure Stack 集线器尚不支持该命令。
 - 若要获取 Azure Stack 中心可用的 VM 映像列表，请使用 `az vm image list --all` 命令，而不是 `az vm image list` 命令。 指定 `--all` 选项可确保响应仅返回 Azure Stack 中心环境中可用的映像。
 - Azure 中提供的 VM 映像别名可能不适用于 Azure Stack 中心。 使用 VM 映像时，必须使用整个 URN 参数 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0)，而不是映像别名。 此 URN 必须与派生自 `az vm images list` 命令的映像规范相匹配。

---

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
- [为 Azure Stack 中心用户 (操作员启用 Azure CLI) ](../operator/azure-stack-cli-admin.md)
- [管理用户权限](azure-stack-manage-permissions.md) 
