---
title: Azure CLI 管理 Azure Stack 集线器 |Microsoft Docs
description: 了解如何使用跨平台命令行接口（CLI）管理和部署 Azure Stack 集线器上的资源。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: 8d6d02da1768f6cbcdaaecdfe9a1cf03d47ce0d6
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818853"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>通过 Azure CLI 管理和部署 Azure Stack 集线器的资源

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

按照本文中的步骤设置 Azure 命令行接口（CLI）来管理 Linux、Mac 和 Windows 客户端平台 Azure Stack 开发工具包（ASDK）资源。

## <a name="prepare-for-azure-cli"></a>准备 Azure CLI

如果使用的是 ASDK，则需要 Azure Stack 集线器的 CA 根证书，以便在开发计算机上使用 Azure CLI。 使用该证书可通过 CLI 管理资源。

 - 如果从 ASDK 之外的工作站使用 CLI，则需要**Azure Stack 中心 CA 根证书**。  

 - **虚拟机别名终结点**提供别名，如 "UbuntuLTS" 或 "Win2012Datacenter"。 部署 Vm 时，此别名引用映像发布者、产品/服务、SKU 和版本作为单一参数。  

以下部分介绍如何获取这些值。

### <a name="export-the-azure-stack-hub-ca-root-certificate"></a>导出 Azure Stack 中心 CA 根证书

如果使用的是集成系统，则无需导出 CA 根证书。 如果使用的是 ASDK，请在 ASDK 上导出 CA 根证书。

若要导出 PEM 格式的 ASDK 根证书：

1. 获取 Azure Stack 中心根证书的名称：
    - 登录到 Azure Stack 中心用户或管理员门户。
    - 单击地址栏附近的 "**安全**"。
    - 在弹出窗口中，单击 "**有效**"。
    - 在 "证书" 窗口中，单击 "**证书路径**" 选项卡。
    - 记下 Azure Stack 中心根证书的名称。

    ![Azure Stack 中心根证书](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [在 Azure Stack 集线器上创建 WINDOWS VM](azure-stack-quick-windows-portal.md)。

3. 登录到 VM，打开提升的 PowerShell 提示符，然后运行以下脚本：

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

你可以设置一个可公开访问的终结点，该终结点承载 VM 别名文件。 VM 别名文件是一个 JSON 文件，它提供映像的公用名称。 将 VM 作为 Azure CLI 参数部署时，将使用该名称。

1. 如果发布自定义映像，请记下在发布过程中指定的发布者、产品/服务、SKU 和版本信息。 如果它是 marketplace 中的映像，则可以使用 ```Get-AzureVMImage``` cmdlet 来查看信息。  

2. 从 GitHub 下载[示例文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。

3. 在 Azure Stack Hub 中创建存储帐户。 完成此操作后，创建一个 blob 容器。 将访问策略设置为 "公共"。  

4. 将 JSON 文件上传到新容器。 完成此操作后，可以查看 blob 的 URL。 选择 blob 名称，然后选择 "blob 属性" 中的 URL。

### <a name="install-or-upgrade-cli"></a>安装或升级 CLI

登录到开发工作站并安装 CLI。 Azure Stack 中心需要 Azure CLI 版本2.0 或更高版本。 最新版本的 API 配置文件需要 CLI 的最新版本。 您可以使用[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)一文中所述的步骤来安装 CLI。 

1. 若要验证安装是否成功，请打开终端或命令提示符窗口，并运行以下命令：

    ```shell
    az --version
    ```

    应会看到计算机上安装的 Azure CLI 和其他依赖库的版本。

    ![Azure Stack 中心 Python 位置上的 Azure CLI](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. 记下 CLI 的 Python 位置。 如果运行的是 ASDK，则需要使用此位置添加证书。


## <a name="windows-azure-ad"></a>Windows （Azure AD）

本部分介绍如何设置 CLI （如果使用 Azure AD 作为标识管理服务），并在 Windows 计算机上使用 CLI。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack 中心 CA 根证书

如果使用的是 ASDK，则需要信任远程计算机上的 CA 根证书。 集成系统无需执行此步骤。

若要信任 Azure Stack 中心 CA 根证书，请将其附加到随 Azure CLI 一起安装的 Python 版本的现有 Python 证书存储区。 你可能正在运行自己的 Python 实例。 Azure CLI 包括其自己的 Python 版本。

1. 查找计算机上的证书存储位置。  可以通过运行命令 `az --version`来查找位置。

2. 导航到包含 CLI Python 应用的文件夹。 要运行此版本的 python。 如果已在系统路径中设置 Python，运行 Python 将执行你自己的 Python 版本。 相反，你需要运行 CLI 使用的版本，并将证书添加到该版本。 例如，CLI Python 可能位于： `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`。

    使用以下命令：

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    记下证书位置。 例如，`C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem` 。 具体的路径取决于操作系统和 CLI 安装。

2. 信任 Azure Stack 中心 CA 根证书，方法是将其附加到现有的 Python 证书。

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

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack 集线器

1. 通过运行 `az cloud register` 命令注册 Azure Stack 中心环境。

2. 注册您的环境。 运行 `az cloud register`时，请使用以下参数：

    | 值 | 示例 | Description |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 为用户环境使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | https://management.local.azurestack.external | ASDK 中的**ResourceManagerUrl**是： `https://management.local.azurestack.external/` 集成系统中的**ResourceManagerUrl**是： `https://management.<region>.<fqdn>/` 如果对集成系统终结点有疑问，请与云操作员联系。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 用于 ASDK。 对于集成系统，请使用系统的终结点。  |
    | Keyvault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 用于 ASDK。 对于集成系统，请使用系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅[设置 VM 别名端点](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. 使用以下命令设置活动环境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. 更新环境配置，以使用特定于 Azure Stack 集线器的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果在1808版本之前运行 Azure Stack 中心版本，则必须使用 API 版本配置文件**2017-03-09-配置**文件，而不是 api 版本配置文件**2019-03-01-混合**。 还需要使用最新版本的 Azure CLI。
 
1. 使用 `az login` 命令登录到 Azure Stack 中心环境。 以用户或[服务主体](/azure/active-directory/develop/app-objects-and-service-principals)身份登录到 Azure Stack 中心环境。 

   - 以*用户*身份登录： 

     您可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果你的帐户已启用多重身份验证，则必须执行后一项操作：

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > 如果你的用户帐户已启用多重身份验证，请在不提供 `-u` 参数的情况下使用 `az login` 命令。 运行此命令将为你提供一个 URL 和一个必须用来进行身份验证的代码。

   - 以*服务主体*身份登录： 
    
     在登录之前，请[通过 Azure 门户或 CLI 创建一个服务主体](azure-stack-create-service-principals.md)，并为其分配一个角色。 现在，使用以下命令登录：

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，你可以为应用程序创建资源组并添加 VM。 使用以下命令创建名为 "MyResourceGroup" 的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上一个命令将输出新创建的资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows （AD FS）

本部分将指导你设置 CLI （如果你 Active Directory 使用的是联合身份验证服务（AD FS）作为标识管理服务，并且在 Windows 计算机上使用 CLI。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack 中心 CA 根证书

如果使用的是 ASDK，则需要信任远程计算机上的 CA 根证书。 集成系统无需执行此步骤。

1. 查找计算机上的证书位置。 位置可能因安装 Python 的位置而异。 打开 cmd 提示符或提升的 PowerShell 提示符，然后键入以下命令：

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem` 。 具体的路径取决于操作系统和已安装的 Python 版本。

2. 信任 Azure Stack 中心 CA 根证书，方法是将其附加到现有的 Python 证书。

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

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack 集线器

1. 通过运行 `az cloud register` 命令注册 Azure Stack 中心环境。

2. 注册您的环境。 运行 `az cloud register`时，请使用以下参数：

    | 值 | 示例 | Description |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 为用户环境使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | https://management.local.azurestack.external | ASDK 中的**ResourceManagerUrl**是： `https://management.local.azurestack.external/` 集成系统中的**ResourceManagerUrl**是： `https://management.<region>.<fqdn>/` 如果对集成系统终结点有疑问，请与云操作员联系。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 用于 ASDK。 对于集成系统，请使用系统的终结点。  |
    | Keyvault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 用于 ASDK。 对于集成系统，请使用系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅[设置 VM 别名端点](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. 使用以下命令设置活动环境。

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. 更新环境配置，以使用特定于 Azure Stack 集线器的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果在1808版本之前运行 Azure Stack 中心版本，则必须使用 API 版本配置文件**2017-03-09-配置**文件，而不是 api 版本配置文件**2019-03-01-混合**。 还需要使用最新版本的 Azure CLI。

1. 使用 `az login` 命令登录到 Azure Stack 中心环境。 你可以作为用户或[服务主体](/azure/active-directory/develop/app-objects-and-service-principals)登录到 Azure Stack 中心环境。 

   - 以*用户*身份登录：

     您可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果你的帐户已启用多重身份验证，则必须执行后一项操作：

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > 如果你的用户帐户已启用多重身份验证，请在不提供 `-u` 参数的情况下使用 `az login` 命令。 运行此命令将为你提供一个 URL 和一个必须用来进行身份验证的代码。

   - 以*服务主体*身份登录： 
    
     准备要用于服务主体登录名的 pem 文件。

     在创建主体的客户端计算机上，将服务主体证书导出为 pfx，其私钥位于 `cert:\CurrentUser\My`。 证书名称与主体名称相同。

     将 pfx 转换为 pem （使用 OpenSSL 实用程序）。

     登录到 CLI：
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，你可以为应用程序创建资源组并添加 VM。 使用以下命令创建名为 "MyResourceGroup" 的资源组：

```azurecli
az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上一个命令将输出新创建的资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux （Azure AD）

本部分介绍如何设置 CLI （如果使用 Azure AD 作为标识管理服务，并在 Linux 计算机上使用 CLI）。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack 中心 CA 根证书

如果使用的是 ASDK，则需要信任远程计算机上的 CA 根证书。 集成系统无需执行此步骤。

信任 Azure Stack 中心 CA 根证书，方法是将其附加到现有的 Python 证书。

1. 查找计算机上的证书位置。 位置可能因安装 Python 的位置而异。 需要安装 pip 和 certifi 模块。 在 bash 提示符下使用以下 Python 命令：

    ```bash  
    az --version
    ```

    记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem` 。 具体的路径取决于你的操作系统和已安装的 Python 的版本。

2. 用证书的路径运行以下 bash 命令。

   - 对于远程 Linux 计算机：

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - 对于 Azure Stack 中心环境中的 Linux 计算机：

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack 集线器

使用以下步骤连接到 Azure Stack 中心：

1. 通过运行 `az cloud register` 命令注册 Azure Stack 中心环境。

2. 注册您的环境。 运行 `az cloud register`时，请使用以下参数：

    | 值 | 示例 | Description |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 为用户环境使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | https://management.local.azurestack.external | ASDK 中的**ResourceManagerUrl**是： `https://management.local.azurestack.external/` 集成系统中的**ResourceManagerUrl**是： `https://management.<region>.<fqdn>/` 如果对集成系统终结点有疑问，请与云操作员联系。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 用于 ASDK。 对于集成系统，请使用系统的终结点。  |
    | Keyvault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 用于 ASDK。 对于集成系统，请使用系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅[设置 VM 别名端点](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. 设置活动环境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. 更新环境配置，以使用特定于 Azure Stack 集线器的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果在1808版本之前运行 Azure Stack 中心版本，则必须使用 API 版本配置文件**2017-03-09-配置**文件，而不是 api 版本配置文件**2019-03-01-混合**。 还需要使用最新版本的 Azure CLI。

5. 使用 `az login` 命令登录到 Azure Stack 中心环境。 你可以作为用户或[服务主体](/azure/active-directory/develop/app-objects-and-service-principals)登录到 Azure Stack 中心环境。 

   * 以*用户*身份登录：

     您可以直接在 `az login` 命令中指定用户名和密码，或使用浏览器进行身份验证。 如果你的帐户已启用多重身份验证，则必须执行后一项操作：

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > 如果你的用户帐户已启用多重身份验证，则可以使用 `az login` 命令，而无需提供 `-u` 参数。 运行此命令将为你提供一个 URL 和一个必须用来进行身份验证的代码。
   
   * 以*服务主体*身份登录
    
     在登录之前，请[通过 Azure 门户或 CLI 创建一个服务主体](azure-stack-create-service-principals.md)，并为其分配一个角色。 现在，使用以下命令登录：

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，你可以为应用程序创建资源组并添加 VM。 使用以下命令创建名为 "MyResourceGroup" 的资源组：

```azurecli
    az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上一个命令将输出新创建的资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux （AD FS）

本部分将指导你设置 CLI （如果你使用的是 Active Directory 联合服务（AD FS）作为管理服务，并在 Linux 计算机上使用 CLI。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack 中心 CA 根证书

如果使用的是 ASDK，则需要信任远程计算机上的 CA 根证书。 集成系统无需执行此步骤。

信任 Azure Stack 中心 CA 根证书，方法是将其附加到现有的 Python 证书。

1. 查找计算机上的证书位置。 位置可能因安装 Python 的位置而异。 需要安装 pip 和 certifi 模块。 在 bash 提示符下使用以下 Python 命令：

    ```bash  
    az --version 
    ```

    记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem` 。 具体的路径取决于你的操作系统和已安装的 Python 的版本。

2. 用证书的路径运行以下 bash 命令。

   - 对于远程 Linux 计算机：

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - 对于 Azure Stack 中心环境中的 Linux 计算机：

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack 集线器

使用以下步骤连接到 Azure Stack 中心：

1. 通过运行 `az cloud register` 命令注册 Azure Stack 中心环境。

2. 注册您的环境。 运行 `az cloud register`时，请使用以下参数。

    | 值 | 示例 | Description |
    | --- | --- | --- |
    | 环境名称 | AzureStackUser | 为用户环境使用 `AzureStackUser`。 如果你是操作员，请指定 `AzureStackAdmin`。 |
    | 资源管理器终结点 | https://management.local.azurestack.external | ASDK 中的**ResourceManagerUrl**是： `https://management.local.azurestack.external/` 集成系统中的**ResourceManagerUrl**是： `https://management.<region>.<fqdn>/` 如果对集成系统终结点有疑问，请与云操作员联系。 |
    | 存储终结点 | local.azurestack.external | `local.azurestack.external` 用于 ASDK。 对于集成系统，请使用系统的终结点。  |
    | Keyvault 后缀 | .vault.local.azurestack.external | `.vault.local.azurestack.external` 用于 ASDK。 对于集成系统，请使用系统的终结点。  |
    | VM 映像别名文档终结点- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | 包含 VM 映像别名的文档的 URI。 有关详细信息，请参阅[设置 VM 别名端点](#set-up-the-virtual-machine-aliases-endpoint)。 |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

3. 设置活动环境。 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. 更新环境配置，以使用特定于 Azure Stack 集线器的 API 版本配置文件。 若要更新配置，请运行以下命令：

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >如果在1808版本之前运行 Azure Stack 中心版本，则必须使用 API 版本配置文件**2017-03-09-配置**文件，而不是 api 版本配置文件**2019-03-01-混合**。 还需要使用最新版本的 Azure CLI。

5. 使用 `az login` 命令登录到 Azure Stack 中心环境。 你可以作为用户或[服务主体](/azure/active-directory/develop/app-objects-and-service-principals)登录到 Azure Stack 中心环境。 

6. 登录： 

   *  **用户**使用具有设备代码的 web 浏览器时：  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >运行命令将为你提供一个 URL 和一个必须用来进行身份验证的代码。

   * 作为服务主体：
        
     准备要用于服务主体登录名的 pem 文件。

      * 在创建主体的客户端计算机上，将服务主体证书导出为 pfx，其私钥位于 `cert:\CurrentUser\My`。 证书名称与主体名称相同。
  
      * 将 pfx 转换为 pem （使用 OpenSSL 实用程序）。

     登录到 CLI：

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>测试连接

完成所有设置后，使用 CLI 在 Azure Stack 中心内创建资源。 例如，你可以为应用程序创建资源组并添加 VM。 使用以下命令创建名为 "MyResourceGroup" 的资源组：

```azurecli
  az group create -n MyResourceGroup -l local
```

如果成功创建了资源组，则上一个命令将输出新创建的资源的以下属性：

![资源组创建输出](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>已知问题

在 Azure Stack 集线器中使用 CLI 时存在一些已知问题：

 - CLI 交互模式。 例如，Azure Stack 中心尚不支持 `az interactive` 命令。
 - 若要获取 Azure Stack 中心可用的 VM 映像列表，请使用 `az vm image list --all` 命令而不是 `az vm image list` 命令。 指定 `--all` 选项可确保响应仅返回 Azure Stack 中心环境中可用的映像。
 - Azure 中提供的 VM 映像别名可能不适用于 Azure Stack 中心。 使用 VM 映像时，必须使用整个 URN 参数（规范： UbuntuServer： 14.04.3-LTS：1.0.0）而不是映像别名。 此 URN 必须与 `az vm images list` 命令派生的映像规范匹配。

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
- [为 Azure Stack 中心用户（操作员）启用 Azure CLI](../operator/azure-stack-cli-admin.md)
- [管理用户权限](azure-stack-manage-permissions.md) 
