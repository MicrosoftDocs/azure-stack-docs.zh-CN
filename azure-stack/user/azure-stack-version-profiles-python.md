---
title: 在 Azure Stack Hub 中将 API 版本配置文件与 Python 配合使用
description: 了解如何在 Azure Stack Hub 中将 API 版本配置文件与 Python 配合使用。
author: sethmanheim
ms.topic: article
ms.date: 05/05/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 42a00b997031850a61077ec59a92d5df5b6776ef
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82835894"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack-hub"></a>在 Azure Stack Hub 中将 API 版本配置文件与 Python 配合使用

Python SDK 支持 API 版本配置文件以面向不同的云平台，如 Azure Stack 中心和全局 Azure。 使用 API 配置文件为混合云创建解决方案。

本文中的说明需要 Microsoft Azure 订阅。 如果没有帐户，可以获取一个[免费试用帐户](https://go.microsoft.com/fwlink/?LinkId=330212)。

## <a name="python-and-api-version-profiles"></a>Python 与 API 版本配置文件

Python SDK 支持以下 API 配置文件：

- **latest**  
    此配置文件以 Azure 平台中所有服务提供程序的最新 API 版本为目标。
- **2019-03-01-hybrid**  
    此配置文件针对版本 1904 或更高版本的 Azure Stack Hub 平台中所有资源提供程序的最新 API 版本。
- **2018-03-01-hybrid**  
    此配置文件针对 Azure Stack Hub 平台中所有资源提供程序的最兼容 API 版本。
- **2017-03-09-profile**  
    此配置文件以 Azure Stack Hub 支持的资源提供程序的大多数兼容 API 版本为目标。

   有关 API 配置文件和 Azure Stack Hub 的详细信息，请参阅[在 Azure Stack Hub 中管理 API 版本配置文件](azure-stack-version-profiles.md)。

## <a name="install-the-azure-python-sdk"></a>安装 Azure Python SDK

1. 从[官方站点](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)安装 Git。
2. 有关如何安装 Python SDK 的说明，请参阅[面向 Python 开发人员的 Azure](/python/azure/python-sdk-azure-install?view=azure-python)。
3. 如果此文不适用，请创建订阅，并保存订阅 ID 供以后使用。 有关创建订阅的说明，请参阅[在 Azure Stack Hub 中创建套餐的订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。
4. 创建服务主体并保存其 ID 和机密。 有关如何为 Azure Stack Hub 创建服务主体的说明，请参阅[提供对 Azure Stack Hub 的应用程序访问权限](../operator/azure-stack-create-service-principals.md)。
5. 确保服务主体在订阅上具有“参与者/所有者”角色。 有关如何为服务主体分配角色的说明，请参阅[提供对 Azure Stack Hub 的应用程序访问权限](../operator/azure-stack-create-service-principals.md)。

## <a name="prerequisites"></a>先决条件

若要将 Python Azure SDK 与 Azure Stack Hub 配合使用，必须提供以下值，然后使用环境变量来设置值。 若要设置环境变量，请参阅下表后面针对特定操作系统的说明。

| Value | 环境变量 | 说明 |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| 租户 ID | `AZURE_TENANT_ID` | Azure Stack Hub [租户 ID](../operator/azure-stack-identity-overview.md)。 |
| 客户端 ID | `AZURE_CLIENT_ID` | 在本文上一部分创建服务主体时保存的服务主体应用 ID。 |
| 订阅 ID | `AZURE_SUBSCRIPTION_ID` | 使用[订阅 ID](../operator/service-plan-offer-subscription-overview.md#subscriptions) 访问 Azure Stack Hub 中的套餐。 |
| 客户端机密 | `AZURE_CLIENT_SECRET` | 创建服务主体时保存的服务主体应用机密。 |
| 资源管理器终结点 | `ARM_ENDPOINT` | 参阅 [Azure Stack Hub 资源管理器终结点](azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint)一文。 |
| 资源位置 | `AZURE_RESOURCE_LOCATION` | Azure Stack Hub 环境的资源位置。

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>信任 Azure Stack Hub CA 根证书

如果使用的是 ASDK，则必须显式信任远程计算机上的 CA 根证书。 不需要信任 Azure Stack Hub 集成系统的 CA 根证书。

#### <a name="windows"></a>Windows

1. 在计算机上找到 Python 证书存储位置。 该位置可能因 Python 的安装位置而异。 打开命令提示符或权限提升的 PowerShell 提示符，然后键入以下命令：

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    记下证书存储位置，例如， **~/lib/python3.5/site-packages/certifi/cacert.pem**。 具体的路径取决于操作系统以及安装的 Python 版本。

2. 若要信任 Azure Stack Hub CA 根书，请将它附加到现有的 Python 证书：

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
> 如果通过 Python SDK 使用 **virtualenv** 进行开发（如下面的[运行 Python 示例](#run-the-python-sample)部分所述），则必须向虚拟环境证书存储添加上述证书。 路径看起来可能类似于：`..\mytestenv\Lib\site-packages\certifi\cacert.pem`。

## <a name="python-samples-for-azure-stack-hub"></a>适用于 Azure Stack Hub 的 Python 示例

下面是一些适用于 Azure Stack Hub 的、使用 Python SDK 的代码示例：

- [管理资源和资源组](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)
- [管理存储帐户](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)
- [管理虚拟机](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/)：此示例使用 **2019-03-01-hybrid** 配置文件，该配置文件适用于 Azure Stack 支持的最新 API 版本。

## <a name="manage-virtual-machine-sample"></a>管理虚拟机示例

使用以下 Python 代码示例对 Azure Stack Hub 中的虚拟机 (VM) 执行常见管理任务。 该代码示例演示如何执行以下操作：

- 创建 VM：
  - 创建 Linux VM
  - 创建 Windows VM
- 更新 VM：
  - 扩展驱动器
  - 标记 VM
  - 附加数据磁盘
  - 分离数据磁盘
- 操作 VM：
  - 启动 VM
  - 停止 VM
  - 重新启动 VM
- 列出 VM
- 删除 VM

若要查看执行这些操作的代码，请参阅 GitHub 存储库 [Hybrid-Compute-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM) 中的 Python 脚本 **example.py** 中的 **run_example()** 函数。

每个操作都清晰地带有注释和一个 print 函数。 这些示例不一定按此列表中显示的顺序执行。

## <a name="run-the-python-sample"></a>运行 Python 示例

1. [安装 Python](https://www.python.org/downloads/)（如果尚未安装）。 此示例（和 SDK）与 Python 2.7、3.4、3.5 和 3.6 兼容。

2. 对于 Python 开发，通常建议使用虚拟环境。 有关详细信息，请参阅 [Python 文档](https://docs.python.org/3/tutorial/venv.html)。

3. 在 Python 3 上，请使用 **venv** 模块安装并初始化虚拟环境（对于 Python 2.7，必须安装 [virtualenv](https://pypi.python.org/pypi/virtualenv)）：

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

5. 使用 **pip** 安装依赖项：

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. 创建要用于 Azure Stack Hub 的[服务主体](../operator/azure-stack-create-service-principals.md)。 确保服务主体在订阅上具有[“参与者/所有者”角色](../operator/azure-stack-create-service-principals.md#assign-a-role)。

7. 设置以下变量并将这些环境变量导出到当前 shell 中：

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. 若要运行此示例，Ubuntu 16.04-LTS 和 WindowsServer 2012-R2-DataCenter 映像必须存在于 Azure Stack Hub 市场中。 可以[从 Azure 下载](../operator/azure-stack-download-azure-marketplace-item.md)这些映像或者将其添加到[平台映像存储库](../operator/azure-stack-add-vm-image.md)。

9. 运行示例：

    ```python
    python example.py
    ```

## <a name="next-steps"></a>后续步骤

- [Azure Python 开发中心](https://azure.microsoft.com/develop/python/)
- [Azure 虚拟机文档](https://azure.microsoft.com/services/virtual-machines/)
- [虚拟机的学习路径](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
