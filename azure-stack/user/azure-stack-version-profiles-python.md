---
title: 在 Azure Stack Hub 中使用 Python 的 API 版本配置文件 |Microsoft Docs
description: 了解如何在 Azure Stack 集线器中将 API 版本配置文件与 Python 配合使用。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 556933545af27b1fc22241ced3c83389072b3c8f
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883195"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack-hub"></a>在 Azure Stack 集线器中将 API 版本配置文件与 Python 配合使用

Python SDK 支持 API 版本配置文件以面向不同的云平台，如 Azure Stack 中心和全局 Azure。 在为混合云创建解决方案时使用 API 配置文件。

本文中的说明需要 Microsoft Azure 订阅。 如果没有帐户，可以获取一个[免费试用帐户](https://go.microsoft.com/fwlink/?LinkId=330212)。

## <a name="python-and-api-version-profiles"></a>Python 和 API 版本配置文件

Python SDK 支持以下 API 配置文件：

- **最近**  
    此配置文件面向 Azure 平台中所有服务提供程序的最新 API 版本。
- **2019-03-01-混合**  
    此配置文件面向版本1904或更高版本的 Azure Stack 中心平台中的所有资源提供程序的最新 API 版本。
- **2018-03-01-混合**  
    此配置文件面向 Azure Stack 中心平台中所有资源提供程序的最兼容 API 版本。
- **2017-03-09-配置文件**  
    此配置文件面向 Azure Stack 中心支持的资源提供程序的最兼容 API 版本。

   有关 API 配置文件和 Azure Stack 集线器的详细信息，请参阅[在 Azure Stack 中心管理 API 版本配置文件](azure-stack-version-profiles.md)。

## <a name="install-the-azure-python-sdk"></a>安装 Azure Python SDK

1. 从[官方站点](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)安装 Git。
2. 有关如何安装 Python SDK 的说明，请参阅[适用于 python 开发人员的 Azure](/python/azure/python-sdk-azure-install?view=azure-python)。
3. 如果不可用，请创建订阅并保存订阅 ID 以供以后使用。 有关创建订阅的说明，请参阅[在 Azure Stack Hub 中创建对产品/服务的订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。
4. 创建一个服务主体，并保存其 ID 和机密。 有关如何为 Azure Stack 中心创建服务主体的说明，请参阅为[应用程序提供对 Azure Stack 集线器的访问权限](../operator/azure-stack-create-service-principals.md)。
5. 确保服务主体在订阅上具有“参与者/所有者”角色。 有关如何向服务主体分配角色的说明，请参阅为[应用程序提供对 Azure Stack 集线器的访问权限](../operator/azure-stack-create-service-principals.md)。

## <a name="prerequisites"></a>必备组件

若要将 Python Azure SDK 与 Azure Stack 中心一起使用，必须提供以下值，并使用环境变量设置值。 若要设置环境变量，请参阅下表中针对特定操作系统的说明。

| 值 | 环境变量 | Description |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| 租户 ID | `AZURE_TENANT_ID` | Azure Stack 中心[租户 ID](../operator/azure-stack-identity-overview.md)。 |
| 客户端 ID | `AZURE_CLIENT_ID` | 在本文上一部分中创建服务主体时保存的服务主体应用 ID。 |
| 订阅 ID | `AZURE_SUBSCRIPTION_ID` | 使用[订阅 ID](../operator/service-plan-offer-subscription-overview.md#subscriptions)可以访问 Azure Stack 集线器中的产品/服务。 |
| 客户端机密 | `AZURE_CLIENT_SECRET` | 在创建服务主体时保存的服务主体应用程序机密。 |
| 资源管理器终结点 | `ARM_ENDPOINT` | 请参阅[Azure Stack 中心资源管理器终结点](azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint)一文。 |
| 资源位置 | `AZURE_RESOURCE_LOCATION` | Azure Stack 中心环境的资源位置。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack 中心 CA 根证书

如果使用的是 ASDK，则必须显式信任远程计算机上的 CA 根证书。 不需要信任 Azure Stack 集线器集成系统的 CA 根证书。

#### <a name="windows"></a>Windows

1. 在计算机上查找 Python 证书存储区位置。 位置可能会有所不同，具体取决于你安装 Python 的位置。 打开命令提示符或提升的 PowerShell 提示符，然后键入以下命令：

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    记下证书存储位置;例如， **~/lib/python3.5/site-packages/certifi/cacert.pem**。 具体的路径取决于你的操作系统和已安装的 Python 的版本。

2. 信任 Azure Stack 中心 CA 根证书，方法是将其附加到现有的 Python 证书：

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate; for ex: C:\Users\user1\Downloads\root.pem>"

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
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

> [!NOTE]  
> 如果使用**virtualenv**通过 python SDK 进行开发（如以下[运行 Python 示例](#run-the-python-sample)部分中所述），则必须将以前的证书添加到虚拟环境证书存储区。 路径可能如下所示： `..\mytestenv\Lib\site-packages\certifi\cacert.pem`。

## <a name="python-samples-for-azure-stack-hub"></a>Azure Stack 中心的 Python 示例

使用 Python SDK Azure Stack 中心提供的一些代码示例如下：

- [管理资源和资源组](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)
- [管理存储帐户](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)
- [管理虚拟机](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/)：此示例使用**2019-03-01 混合**配置文件，该配置文件面向 Azure Stack 集线器支持的最新 API 版本。

## <a name="manage-virtual-machine-sample"></a>管理虚拟机示例

使用以下 Python 代码示例对 Azure Stack 中心内的虚拟机（Vm）执行常见管理任务。 此代码示例演示如何执行以下操作：

- 创建 Vm：
  - 创建 Linux VM
  - 创建 Windows VM
- 更新 VM：
  - 展开驱动器
  - 标记一个 VM
  - 附加数据磁盘
  - 分离数据磁盘
- 操作 VM：
  - 启动 VM
  - 停止 VM
  - 重新启动 VM
- 列出 VM
- 删除 VM

若要查看用于执行这些操作的代码，请参阅 GitHub 存储库中的 Python 脚本**example.py**中的**run_example （）** [函数。](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM)

每个操作都用注释和打印函数清楚地标记。 示例不一定按此列表中显示的顺序进行。

## <a name="run-the-python-sample"></a>运行 Python 示例

1. 如果尚未安装 Python，请[安装](https://www.python.org/downloads/)它。 此示例（和 SDK）与 Python 2.7、3.4、3.5 和3.6 兼容。

2. Python 开发的一般建议是使用虚拟环境。 有关详细信息，请参阅[Python 文档](https://docs.python.org/3/tutorial/venv.html)。

3. 在 Python 3 上安装和初始化具有**venv**模块的虚拟环境（必须安装适用于 python 2.7 的[virtualenv](https://pypi.python.org/pypi/virtualenv) ）：

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. 克隆存储库：

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. 使用**pip**安装依赖关系：

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. 创建与 Azure Stack 中心一起工作的[服务主体](../operator/azure-stack-create-service-principals.md)。 确保你的服务主体在你的订阅上具有[参与者/所有者角色](../operator/azure-stack-create-service-principals.md#assign-a-role)。

7. 设置以下变量，并将这些环境变量导出到当前 shell：

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. 若要运行此示例，Ubuntu 16.04-LTS 和 WindowsServer 2012-R2-DataCenter 映像必须位于 Azure Stack Hub Marketplace 中。 这些映像可以[从 Azure 下载](../operator/azure-stack-download-azure-marketplace-item.md)，也可以添加到[平台映像存储库](../operator/azure-stack-add-vm-image.md)中。

9. 运行示例：

    ```python
    python example.py
    ```

## <a name="next-steps"></a>后续步骤

- [Azure Python 开发中心](https://azure.microsoft.com/develop/python/)
- [Azure 虚拟机文档](https://azure.microsoft.com/services/virtual-machines/)
- [虚拟机的学习路径](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
