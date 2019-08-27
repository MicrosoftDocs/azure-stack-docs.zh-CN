---
title: 部署 ASDK 的部署配置 |Microsoft Docs
description: 了解安装 Azure Stack 开发工具包 (ASDK) 后建议进行的配置更改。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 111e8e6cb72baac64229e4808003818efece54cd
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025887"
---
# <a name="post-deployment-configurations-for-asdk"></a>ASDK 的部署后配置

[安装 Azure Stack 开发工具包 (ASDK)](asdk-install.md)后, 在 ASDK 主机计算机上以 AzureStack\AzureStackAdmin 身份登录时, 应进行一些建议的部署后配置更改。

## <a name="install-azure-stack-powershell"></a>安装 Azure Stack PowerShell

需要安装与 Azure Stack 兼容的 Azure PowerShell 模块才能使用 Azure Stack。

通过 PowerShell 库安装适用于 Azure Stack 的 PowerShell 命令。 若要注册 PSGallery 存储库，请打开权限提升的 PowerShell 会话并运行以下命令：

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

使用 API 版本配置文件指定与 Azure Stack 兼容的 AzureRM 模块。  API 版本配置文件提供一种管理 Azure 与 Azure Stack 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本 AzureRM PowerShell 模块。 通过 PowerShell 库提供的**AzureRM**模块提供了使用 API 版本配置文件所需的 PowerShell cmdlet。

无论是否与 ASDK 主机建立了 Internet 连接，都可以安装最新 Azure Stack PowerShell 模块：

> [!IMPORTANT]
> 在安装所需版本之前，请务必[卸载任何现有 Azure PowerShell 模块](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules)。

- 使用来自 ASDK 主计算机的**internet 连接**:运行以下 PowerShell 脚本, 在 ASDK 安装中安装这些模块:


  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.7.2
  ```

  如果安装成功，输出中会显示 AzureRM 和 AzureStack 模块。

- 如果没有从 ASDK 主计算机**建立 internet 连接**:在离线场景中，必须先使用以下 PowerShell 命令，将 PowerShell 模块下载到已建立 Internet 连接的计算机：

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

## <a name="download-the-azure-stack-tools"></a>下载 Azure Stack 工具

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) 是托管 PowerShell 模块的 GitHub 存储库，可用于管理资源并将其部署到 Azure Stack。 若要获取这些工具，请克隆 GitHub 存储库，或运行以下脚本来下载 AzureStack-Tools 文件夹：

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  Invoke-WebRequest `
    -Uri https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  Expand-Archive -Path master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>验证 ASDK 安装

若要确保 ASDK 部署成功, 请按以下步骤使用 Test-azurestack cmdlet:

1. 在 ASDK 主机计算机上以 AzureStack\AzureStackAdmin 的身份登录。
2. 以管理员身份 (而不是 PowerShell ISE) 打开 PowerShell。
3. 运行：`Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. 运行：`Test-AzureStack`

此项测试可能需要几分钟时间才能完成。 如果安装成功，将返回如下所示的输出：

![测试 Azure Stack-安装成功](media/asdk-post-deploy/test-azurestack.png)

如果失败，请遵循故障排除步骤来获取帮助。

## <a name="enable-multi-tenancy"></a>启用多租户

对于使用 Azure AD 的部署，需要为 ASDK 安装[启用多租户](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy)。

> [!NOTE]
> 当用于注册 Azure Stack 的域以外的其他域中的管理员或用户帐户登录到 Azure Stack 门户时, 用于注册 Azure Stack 的域名必须追加到门户 URL。 例如, 如果已向 fabrikam.onmicrosoft.com 注册了 Azure Stack, 并且登录的用户帐户为admin@contoso.com, 则用于登录用户门户的 URL 将为:。 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com

## <a name="next-steps"></a>后续步骤

[将 ASDK 注册到 Azure](asdk-register.md)
