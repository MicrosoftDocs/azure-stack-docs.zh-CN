---
title: ASDK 的部署后配置
description: 了解安装 Azure Stack 开发工具包 (ASDK) 后要进行的建议配置更改。
author: justinha
ms.topic: article
ms.date: 11/14/2020
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/14/2020
ms.openlocfilehash: efb7d803d8f0c29269bae3e147fc48eec3e29eb3
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517304"
---
# <a name="post-deployment-configurations-for-asdk"></a>ASDK 的部署后配置

[安装 Azure Stack 开发工具包 (ASDK)](asdk-install.md) 之后，当你在 ASDK 主计算机上以 AzureStack\AzureStackAdmin 身份登录时，应进行一些建议的部署后配置更改。

## <a name="install-azure-stack-powershell"></a>安装 Azure Stack PowerShell

需要安装与 Azure Stack 兼容的 Azure PowerShell 模块才能使用 Azure Stack。

通过 PowerShell 库安装适用于 Azure Stack 的 PowerShell 命令。 若要注册 PSGallery 存储库，请打开权限提升的 PowerShell 会话并运行以下命令：

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

使用 API 版本配置文件指定 Azure Stack 兼容 Az 模块。  API 版本配置文件提供一种管理 Azure 与 Azure Stack 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Az PowerShell 模块。 通过 PowerShell 库提供的 **Az** 模块提供了使用 API 版本配置文件所需的 PowerShell cmdlet。

无论是否与 ASDK 主机建立了 Internet 连接，都可以安装最新 Azure Stack PowerShell 模块：

> [!IMPORTANT]
> 在安装所需版本之前，请务必[卸载任何现有 Azure PowerShell 模块](../operator/powershell-install-az-module.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules)。

- 已从 ASDK 主计算机 **建立 Internet 连接**：运行以下 PowerShell 脚本，以在 ASDK 安装中安装以下模块：

### <a name="az-modules"></a>[Az 模块](#tab/az1)

  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the Az.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name Az.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
  ```

如果安装成功，则会在输出中显示 Az 和 Test-azurestack 模块。

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm1)

  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.8.2
  ```

如果安装成功，则会在输出中显示 AureRM 和 Test-azurestack 模块。

---

- **未从 ASDK 主计算机建立 Internet 连接**：在离线场景中，必须先使用以下 PowerShell 命令，将 PowerShell 模块下载到已建立 Internet 连接的计算机：

### <a name="az-modules"></a>[Az 模块](#tab/az2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  接下来，将下载的包复制到 ASDK 计算机并将该位置注册为默认存储库，并从此存储库安装 Az 和 Test-azurestack 模块：

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module Az -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  接下来，将下载的包复制到 ASDK 计算机，将该位置注册为默认存储库，并从此存储库安装 AzureRM 和 AzureStack 模块：

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module AzureRM -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

---

## <a name="download-the-azure-stack-tools"></a>下载 Azure Stack 工具

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) 是托管 PowerShell 模块的 GitHub 存储库，可用于管理资源并将其部署到 Azure Stack。 使用 Az PowerShell 模块或 AzureRM 模块的工具。

### <a name="az-modules"></a>[Az 模块](#tab/az3)

若要获取这些工具，请从 `az` 分支克隆 GitHub 存储库，或运行以下脚本来下载 AzureStack-Tools 文件夹：

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile az.zip

# Expand the downloaded files.
expand-archive az.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-az

```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm3)

若要获取这些工具，请从 `master` 分支克隆 GitHub 存储库，或通过在提升的 PowerShell 提示符下运行以下脚本来下载 AzureStack-Tools 文件夹：

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
若要详细了解如何使用适用于 Azure Stack Hub 的 AzureRM 模块，请参阅[安装适用于 Azure Stack Hub 的 PowerShell AzureRM 模块](../operator/azure-stack-powershell-install.md)

---

## <a name="validate-the-asdk-installation"></a>验证 ASDK 安装

若要确保 ASDK 部署成功，可以遵循以下步骤使用 Test-AzureStack cmdlet：

1. 以 AzureStack\AzureStackAdmin 身份登录到 ASDK 主计算机。
2. 以管理员身份打开 PowerShell（非 PowerShell ISE）。
3. 运行： `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. 运行： `Test-AzureStack`

此项测试可能需要几分钟时间才能完成。 如果安装成功，将返回如下所示的输出：

![测试 Azure Stack - 安装成功](media/asdk-post-deploy/test-azurestack.png)

如果失败，请遵循故障排除步骤来获取帮助。

## <a name="enable-multi-tenancy"></a>启用多租户

对于使用 Azure AD 的部署，需要为 ASDK 安装[启用多租户](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy)。

> [!NOTE]
> 当使用非用于注册 Azure Stack 的域的管理员或用户帐户登录 Azure Stack 门户时，用于注册 Azure Stack 的域名必须追加到门户 URL 后面。 例如，如果已向 fabrikam.onmicrosoft.com 注册了 Azure Stack，并且登录的用户帐户为，则用于 admin@contoso.com 登录用户门户的 URL 应为： https \: //portal.local.azurestack.external/fabrikam.onmicrosoft.com。

## <a name="next-steps"></a>后续步骤

[将 ASDK 注册到 Azure](asdk-register.md)
