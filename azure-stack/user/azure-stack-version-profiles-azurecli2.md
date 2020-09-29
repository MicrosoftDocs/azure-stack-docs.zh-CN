---
title: 使用 Azure CLI 管理 Azure Stack Hub
description: 了解如何使用跨平台命令行接口 (CLI) 管理和部署 Azure Stack Hub 上的资源。
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: 1fb17516e5ef0b4e3a670703a34e1e895c847b52
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88818838"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>使用 Azure CLI 管理资源以及将资源部署到 Azure Stack Hub

按照本文中的步骤设置 Azure 命令行接口 (CLI)，以从 Linux、Mac 和 Windows 客户端平台管理 Azure Stack 开发工具包 (ASDK) 资源。

## <a name="prepare-for-azure-cli"></a>准备 Azure CLI

如果使用 ASDK，需要提供 Azure Stack Hub 的 CA 根证书才能在开发计算机上使用 Azure CLI。 该证书用于通过 CLI 管理资源。

 - 如果从 ASDK 外部的工作站使用 CLI，则必须准备好 **Azure Stack Hub CA 根证书**。  

 - **虚拟机别名终结点**提供类似于“UbuntuLTS”或“Win2012Datacenter”的别名。 此别名引用映像发布者、套餐、SKU 和版本作为部署 VM 时的单个参数。  

以下部分介绍如何获取这些值。

### <a name="export-the-azure-stack-hub-ca-root-certificate"></a>导出 Azure Stack Hub CA 根证书

如果使用集成系统，则无需导出 CA 根证书。 如果使用 ASDK，请导出 ASDK 中的 CA 根证书。

导出 PEM 格式的 ASDK 根证书：

1. 获取 Azure Stack Hub 根证书的名称：
    - 登录到 Azure Stack Hub 用户或管理员门户。
    - 单击地址栏附近的“安全”。 
    - 在弹出窗口中，单击“有效”。 
    - 在“证书”窗口中，单击“证书路径”选项卡。 
    - 记下 Azure Stack Hub 根证书的名称。

    ![Azure Stack Hub 根证书](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [在 Azure Stack Hub 上创建 Windows VM](azure-stack-quick-windows-portal.md)。

3. 登录到 VM，打开权限提升的 PowerShell 提示符，然后运行以下脚本：

    ```powershell  
      $label = "<the name of your Azure Stack Hub root cert from Step 1>"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

4. 将证书复制到本地计算机。


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>设置虚拟机别名终结点

可以设置一个可公开访问的终结点用于托管 VM 别名文件。 VM 别名文件是一个 JSON 文件，提供映像的公用名称。 以 Azure CLI 参数形式部署 VM 时，将使用该名称。

1. 如果发布自定义映像，请记下发布过程中指定的发布者、产品/服务、SKU 和版本信息。 如果映像来自市场，可以使用 ```Get-AzureVMImage``` cmdlet 查看信息。  

2. 从 GitHub 下载[示例文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。

3. 在 Azure Stack Hub 中创建存储帐户。 完成该操作后，将创建 Blob 容器。 将访问策略设置为“公开”。  

4. 将 JSON 文件上传到新容器。 完成该操作后，可以查看 blob 的 URL。 选择 blob 名称，然后从 blob 属性中选择该 URL。

### <a name="install-or-upgrade-cli"></a>安装或升级 CLI

登录到开发工作站并安装 CLI。 Azure Stack Hub 需要 Azure CLI 2.0 版或更高版本。 最新版本的 API 配置文件需要最新版本的 CLI。 使用[安装 Azure CLI](/cli/azure/install-azure-cli) 一文中所述的步骤安装 CLI。 

1. 若要验证安装是否成功，请打开终端或命令提示符窗口，并运行以下命令：

    ```shell
    az --version
    ```

    应会看到 Azure CLI 的版本，以及计算机上安装的其他依赖库。

    ![Azure Stack Hub Python 位置上的 Azure CLI](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. 记下 CLI 的 Python 位置。 如果正在运行 ASDK，则需要使用此位置来添加证书。


## <a name="windows-azure-ad"></a>Windows (Azure AD)

如果使用 Azure AD 作为标识管理服务，并在 Windows 计算机上使用 CLI，可以参考本部分完成 CLI 设置过程。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack Hub CA 根证书

如果使用的是 ASDK，则需要信任远程计算机上的 CA 根证书。 在集成系统中无需执行此步骤。

若要信任 Azure Stack Hub CA 根证书，请将其追加​​到随 Azure CLI 一起安装的 Python 版本的现有 Python 证书存储中。 你可能正在运行自己的 Python 实例。 Azure CLI 包括其自己的 Python 版本。

1. 在计算机上找到证书存储位置。  可以通过运行命令 `az --version` 查找位置。

2. 导航到包含 CLI Python 应用的文件夹。 你希望运行此版本的 python。 如果已在系统 PATH 中设置了 Python，则运行 Python 将执行你自己的 Python 版本。 但是，你希望运行 CLI 使用的版本并将证书添加到该版本。 例如，CLI Python 可能位于：`C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`。

    使用以下命令：

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    记下证书位置。 例如，`C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`。 特定的路径取决于 OS 和 CLI 安装。

2. 若要信任 Azure Stack Hub CA 根书，请将它附加到现有的 Python 证书。

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub

1. 运行 `az cloud register` 命令注册 Azure Stack Hub 环境。

2. 注册环境。 在运行 `az cloud register` 时使用以下参数：

    | Value | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | ASDK 中的 **ResourceManagerUrl** 为：`https://management.local.azurestack.external/`集成系统中的 **ResourceManagerUrl** 为：`https://management.<region>.<fqdn>/` 如果对集成系统终结点有疑问，请与云操作员联系。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
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

    >[!NOTE]  
    >如果正在运行的 Azure Stack Hub 版本低于 1808 版，则必须使用 API 版本配置文件 **2017-03-09-profile**，而不是 API 版本配置文件 **2019-03-01-hybrid**。 还需要使用最新版本的 Azure CLI。
 
1. 使用 `az login` 命令登录到 Azure Stack Hub 环境。 以用户身份或以[服务主体](/azure/active-directory/develop/app-objects-and-service-principals)的形式登录到 Azure Stack Hub 环境。 

   - 以用户  身份登录： 

     可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > 如果用户帐户已启用多重身份验证，请使用不带 `-u` 参数的 `az login` 命令。 运行此命令会提供一个 URL 以及身份验证时必须使用的代码。

   - 以服务主体  身份登录： 
    
     在登录之前，请[通过 Azure 门户或 CLI 创建一个服务主体](../operator/azure-stack-create-service-principals.md?view=azs-2002)，并为其分配角色。 接下来，使用以下命令登录：

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack Hub 中创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

如果使用 Active Directory 联合身份验证服务 (AD FS) 作为标识管理服务，并在 Windows 计算机上使用 CLI，可以参考本部分完成 CLI 设置过程。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack Hub CA 根证书

如果使用的是 ASDK，则需要信任远程计算机上的 CA 根证书。 在集成系统中无需执行此步骤。

1. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 打开命令提示符或权限提升的 PowerShell 提示符，然后键入以下命令：

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem`。 具体的路径取决于 OS 和已安装的 Python 版本。

2. 若要信任 Azure Stack Hub CA 根书，请将它附加到现有的 Python 证书。

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub

1. 运行 `az cloud register` 命令注册 Azure Stack Hub 环境。

2. 注册环境。 在运行 `az cloud register` 时使用以下参数：

    | Value | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | ASDK 中的 **ResourceManagerUrl** 为：`https://management.local.azurestack.external/`集成系统中的 **ResourceManagerUrl** 为：`https://management.<region>.<fqdn>/` 如果对集成系统终结点有疑问，请与云操作员联系。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
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

    >[!NOTE]  
    >如果正在运行的 Azure Stack Hub 版本低于 1808 版，则必须使用 API 版本配置文件 **2017-03-09-profile**，而不是 API 版本配置文件 **2019-03-01-hybrid**。 还需要使用最新版本的 Azure CLI。

1. 使用 `az login` 命令登录到 Azure Stack Hub 环境。 可以用户身份或以[服务主体](/azure/active-directory/develop/app-objects-and-service-principals)的形式登录到 Azure Stack Hub 环境。 

   - 以用户  身份登录：

     可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果帐户已启用多重身份验证，则必须采用后一种方法。

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > 如果用户帐户已启用多重身份验证，请使用不带 `-u` 参数的 `az login` 命令。 运行此命令会提供一个 URL 以及身份验证时必须使用的代码。

   - 以服务主体  身份登录： 
    
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

完成所有设置后，使用 CLI 在 Azure Stack Hub 中创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

如果使用 Azure AD 作为标识管理服务，并在 Linux 计算机上使用 CLI，可以参考本部分完成 CLI 设置过程。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack Hub CA 根证书

如果使用的是 ASDK，则需要信任远程计算机上的 CA 根证书。 在集成系统中无需执行此步骤。

若要信任 Azure Stack Hub CA 根书，请将它附加到现有的 Python 证书。

1. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 需要安装 pip 和 certifi 模块。 在 bash 提示符下使用以下 Python 命令：

    ```bash  
    az --version
    ```

    记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem`。 具体的路径取决于操作系统以及安装的 Python 版本。

2. 结合证书的路径运行以下 bash 命令。

   - 对于远程 Linux 计算机：

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - 对于 Azure Stack Hub 环境中的 Linux 计算机：

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub

使用以下步骤连接到 Azure Stack Hub：

1. 运行 `az cloud register` 命令注册 Azure Stack Hub 环境。

2. 注册环境。 在运行 `az cloud register` 时使用以下参数：

    | Value | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | ASDK 中的 **ResourceManagerUrl** 为：`https://management.local.azurestack.external/`集成系统中的 **ResourceManagerUrl** 为：`https://management.<region>.<fqdn>/` 如果对集成系统终结点有疑问，请与云操作员联系。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅[设置 VM 别名终结点](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. 设置活动的环境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. 将环境配置更新为使用 Azure Stack Hub 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果正在运行的 Azure Stack Hub 版本低于 1808 版，则必须使用 API 版本配置文件 **2017-03-09-profile**，而不是 API 版本配置文件 **2019-03-01-hybrid**。 还需要使用最新版本的 Azure CLI。

5. 使用 `az login` 命令登录到 Azure Stack Hub 环境。 可以用户身份或以[服务主体](/azure/active-directory/develop/app-objects-and-service-principals)的形式登录到 Azure Stack Hub 环境。 

   * 以用户  身份登录：

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

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack Hub 中创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
    az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

如果使用 Active Directory 联合身份验证服务 (AD FS) 作为管理服务，并在 Linux 计算机上使用 CLI，可以参考本部分完成 CLI 设置过程。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack Hub CA 根证书

如果使用的是 ASDK，则需要信任远程计算机上的 CA 根证书。 在集成系统中无需执行此步骤。

若要信任 Azure Stack Hub CA 根书，请将它附加到现有的 Python 证书。

1. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 需要安装 pip 和 certifi 模块。 在 bash 提示符下使用以下 Python 命令：

    ```bash  
    az --version 
    ```

    记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem`。 具体的路径取决于操作系统以及安装的 Python 版本。

2. 结合证书的路径运行以下 bash 命令。

   - 对于远程 Linux 计算机：

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - 对于 Azure Stack Hub 环境中的 Linux 计算机：

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack Hub

使用以下步骤连接到 Azure Stack Hub：

1. 运行 `az cloud register` 命令注册 Azure Stack Hub 环境。

2. 注册环境。 在运行 `az cloud register` 时使用以下参数。

    | Value | 示例 | 说明 |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 对于用户环境，请使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | `https://management.local.azurestack.external` | ASDK 中的 **ResourceManagerUrl** 为：`https://management.local.azurestack.external/`集成系统中的 **ResourceManagerUrl** 为：`https://management.<region>.<fqdn>/` 如果对集成系统终结点有疑问，请与云操作员联系。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | KeyVault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 适用于 ASDK。 对于集成系统，请使用适用于系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅[设置 VM 别名终结点](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. 设置活动的环境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. 将环境配置更新为使用 Azure Stack Hub 特定的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果正在运行的 Azure Stack Hub 版本低于 1808 版，则必须使用 API 版本配置文件 **2017-03-09-profile**，而不是 API 版本配置文件 **2019-03-01-hybrid**。 还需要使用最新版本的 Azure CLI。

5. 使用 `az login` 命令登录到 Azure Stack Hub 环境。 可以用户身份或以[服务主体](/azure/active-directory/develop/app-objects-and-service-principals)的形式登录到 Azure Stack Hub 环境。 

6. 登录： 

   *  将 Web 浏览器与设备代码配合使用，以**用户**的身份登录：  

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

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack Hub 中创建资源。 例如，可以创建应用的资源组并添加 VM。 使用以下命令创建名为“MyResourceGroup”的资源组：

```azurecli
  az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上述命令会输出新建资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>已知问题

在 Azure Stack Hub 中使用 CLI 时存在一些已知的问题：

 - CLI 交互模式。 例如，`az interactive` 命令在 Azure Stack Hub 中尚不受支持。
 - 若要获取 Azure Stack Hub 中可用的 VM 映像列表，请使用 `az vm image list --all` 命令，而不是 `az vm image list` 命令。 指定 `--all` 选项可确保响应只返回 Azure Stack Hub 环境中可用的映像。
 - Azure 中可用的 VM 映像别名可能不适用于 Azure Stack Hub。 使用 VM 映像时，必须使用整个 URN 参数 (Canonical:UbuntuServer:14.04.3-LTS:1.0.0)，而不是映像别名。 此 URN 必须与派生自 `az vm images list` 命令的映像规范相匹配。

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
- [为 Azure Stack Hub 用户启用 Azure CLI（操作员）](../operator/azure-stack-cli-admin.md)
- [管理用户权限](azure-stack-manage-permissions.md) 
