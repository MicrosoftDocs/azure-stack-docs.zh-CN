---
title: 设置 Azure Stack 开发工具包上的 Azure CLI 的证书 (ASDK) ？ | Microsoft Docs
description: 了解如何为 Azure Stack 开发工具包 Azure Stack 开发工具包上的 Azure CLI 设置证书。
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 7b408e28ff70e66c0460473f54304cbf50fa2c92
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524673"
---
# <a name="setting-up-certificates-for-azure-cli-on-azure-stack-development-kit"></a>为 Azure Stack 开发工具包上的 Azure CLI 设置证书

按照本文中的步骤设置 Azure 命令行接口 (CLI)，以从 Linux、Mac 和 Windows 客户端平台管理 Azure Stack 开发工具包 (ASDK) 资源。

本文介绍如何获取证书并将其信任到远程管理计算机。 若要安装 Azure CLI 并连接到你的环境，请参阅 [Azure Stack 集线器上安装 Azure CLI](/azure-stack/user/azure-stack-version-profiles-azurecli2)。

## <a name="prepare-for-azure-cli"></a>准备 Azure CLI

对于 ASDK，需要 Azure Stack 集线器的 CA 根证书，以便在开发计算机上使用 Azure CLI。 该证书用于通过 CLI 管理资源。

 - 如果从 ASDK 之外的工作站使用 CLI，则需要 **Azure Stack 中心 CA 根证书**。  

 - **虚拟机别名终结点** 提供类似于“UbuntuLTS”或“Win2012Datacenter”的别名。 此别名引用映像发布者、套餐、SKU 和版本作为部署 VM 时的单个参数。  

以下部分介绍如何获取这些值。

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>导出 Azure Stack 中心 CA 根证书

若要将 Azure CLI 与 ASDK 结合使用，请导出 CA 根证书。

导出 PEM 格式的 ASDK 根证书：

1. 获取 Azure Stack 中心根证书的名称：
    1. 登录到 Azure Stack 中心用户或管理员门户。
    2. 在地址栏附近选择 " **安全** "。
    3. 在弹出窗口中，选择 " **有效**"。
    4. 在 "证书" 窗口中，选择 " **证书路径** " 选项卡。
    5. 记下 Azure Stack 中心根证书的名称。

    ![Azure Stack 中心根证书](../user/media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [在 Azure Stack 集线器上创建 WINDOWS VM](../user/azure-stack-quick-windows-portal.md)。

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

## <a name="set-up-the-virtual-machine-alias-endpoint"></a>设置虚拟机别名终结点

可以设置一个可公开访问的终结点用于托管 VM 别名文件。 VM 别名文件是一个 JSON 文件，提供映像的公用名称。 以 Azure CLI 参数形式部署 VM 时，将使用该名称。

1. 如果发布自定义映像，请记下发布过程中指定的发布者、产品/服务、SKU 和版本信息。 如果映像来自市场，可以使用 ```Get-AzureVMImage``` cmdlet 查看信息。  

2. 从 GitHub 下载[示例文件](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。

3. 在 Azure Stack Hub 中创建存储帐户。 完成该操作后，将创建 Blob 容器。 将访问策略设置为“公开”。  

4. 将 JSON 文件上传到新容器。 完成该操作后，可以查看 blob 的 URL。 选择 blob 名称，然后从 blob 属性中选择该 URL。


## <a name="trust-the-certificate"></a>信任证书

若要将 Azure CLI 与 aSDK 一起使用，必须信任远程计算机上的 CA 根证书。

### <a name="windows"></a>[Windows](#tab/win)

1. 在计算机上找到证书位置。 该位置根据 Python 的安装位置而异。 打开命令提示符或权限提升的 PowerShell 提示符，然后键入以下命令：

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    记下证书位置。 例如，`~/lib/python3.5/site-packages/certifi/cacert.pem`。 具体的路径取决于 OS 和已安装的 Python 版本。

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

有关安装和连接的说明 Azure CLI 参阅 [Azure Stack 集线器上的安装 Azure CLI](/azure-stack/user/azure-stack-version-profiles-azurecli2)。

### <a name="linux"></a>[Linux](#tab/lin)

设置 CLI 时，通过将 Azure Stack 中心 CA 根证书追加到现有 Python 证书来信任该证书。

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

   - 对于 Azure Stack 中心环境中的 Linux 计算机：

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

有关安装和连接的说明 Azure CLI 参阅 [Azure Stack 集线器上的安装 Azure CLI](/azure-stack/user/azure-stack-version-profiles-azurecli2)。

---

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 集线器上安装 Azure CLI](../user/azure-stack-version-profiles-azurecli2.md)