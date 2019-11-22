---
title: 安装适用于 Azure Stack 的 PowerShell | Microsoft Docs
description: 了解如何安装适用于 Azure Stack 的 PowerShell。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 813cfb72a2fad2b22dfce5baff8680b30d2c599d
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298808"
---
# <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure Stack 的 PowerShell

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure PowerShell 提供了一组使用 Azure 资源管理器模型管理 Azure Stack 资源的 cmdlet。

若要使用云，需要安装 Azure Stack 兼容的 PowerShell 模块。 Azure Stack 使用**AzureRM**模块，而不是在全球 Azure 中使用的较新**AzureAZ**模块。 你还需要使用 API 配置文件为 Azure Stack 资源提供程序指定兼容的终结点。

API 配置文件提供一种管理 Azure 与 Azure Stack 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Azure 资源管理器 PowerShell 模块。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack 支持特定的配置文件版本，例如 **2019-03-01-hybrid**。 安装配置文件时，会安装与指定的配置文件对应的 Azure 资源管理器 PowerShell 模块。

可在已连接到 Internet、部分联网或离线场景中安装与 Azure Stack 兼容的 PowerShell 模块。 本文将引导你完成适用于这些场景的详细说明。

## <a name="1-verify-your-prerequisites"></a>1. 验证你的先决条件

开始使用 Azure Stack 和 PowerShell 之前，必须具备以下先决条件：

- **PowerShell 版本 5.0** <br>
若要检查版本，请运行 **$PSVersionTable.PSVersion** 并比较**主**版本。 如果没有 PowerShell 5.0，请按照[安装 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) 中所述进行操作。

  > [!Note]
  > PowerShell 5.0 需要 Windows 计算机。

- **在权限提升的命令提示符下运行 Powershell**。

- **PowerShell 库访问权限** <br>
  你需要有权访问 [PowerShell 库](https://www.powershellgallery.com)。 该库是 PowerShell 内容的中心存储库。 **PowerShellGet** 模块包含用于发现、安装、更新和发布 PowerShell 项目的 cmdlet。 这些项目的示例包括来自 PowerShell 库和其他专用存储库的模块、DSC 资源、角色功能和脚本。 如果在离线场景中使用 PowerShell，则必须从已建立 Internet 连接的计算机检索资源，并将其存储在离线计算机可访问的位置。

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. 验证 PowerShell 库辅助功能

验证 PSGallery 是否已注册为存储库。

> [!Note]  
> 此步骤需要访问 Internet。

打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

如果未注册存储库，请打开权限提升的 PowerShell 会话并运行以下命令：

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. 卸载 Azure Stack PowerShell 模块的现有版本

在安装所需版本之前，请确保卸载以前安装的任何 Azure Stack AzureRM PowerShell 模块。 使用以下两种方法之一卸载模块：

1. 若要卸载现有的 AzureRM PowerShell 模块，请关闭所有活动的 PowerShell 会话，并运行以下 cmdlet：

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    如果遇到任何错误（例如“模块已在使用中”），请关闭正在使用模块的 PowerShell 会话，然后重新运行上述脚本。

2. 从 `Azure` 和 `Azs.` 文件夹中删除以 `C:\Program Files\WindowsPowerShell\Modules` 或 `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` 开头的所有文件夹。 删除这些文件夹会删除任何现有的 PowerShell 模块。

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. 已连接：安装适用于 internet 连接的 Azure Stack 的 PowerShell

需要的 API 版本配置文件和 Azure Stack PowerShell 模块将取决于所运行的 Azure Stack 版本。

### <a name="install-azure-stack-powershell"></a>安装 Azure Stack PowerShell

运行以下 PowerShell 脚本，在开发工作站上安装这些模块：

- 对于 Azure Stack 1904 或更高版本：

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```
  
- 对于 Azure Stack 版本 1903 或更低版本，仅安装以下两个模块：

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module -Name AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - Azure Stack 模块版本 1.7.1 是一个包含中断性变更的版本。 若要从 Azure Stack 1.6.0 迁移，请参阅[迁移指南](https://aka.ms/azspshmigration171)。
    > - AzureRM 模块版本 2.4.0 包含对 cmdlet Remove-AzureRmStorageAccount 的中断性变更。 此 cmdlet 需要指定 `-Force` 参数，这样就可以在不确认的情况下删除存储帐户。
    > - 无需安装 **AzureRM.BootStrapper** 即可安装 Azure Stack 版本 1901 或更高版本的模块。
    > - 如果在 Azure Stack 版本 1901 或更高版本上使用以上 AzureRM 模块，请勿安装 2018-03-01-hybrid 配置文件。

### <a name="confirm-the-installation-of-powershell"></a>确认 PowerShell 的安装

运行以下命令来确认安装：

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

如果安装成功，则 `AzureRM` 和 `AzureStack` 模块将显示在输出中。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. 已断开连接：在没有 internet 连接的情况下安装 PowerShell

在离线场景中，请先将 PowerShell 模块下载到已建立 Internet 连接的计算机。 然后，将它们传输到要安装的 Azure Stack 开发工具包 (ASDK)。

登录到已建立 Internet 连接的计算机，并根据 Azure Stack 的版本，使用以下脚本下载 Azure 资源管理器和 Azure Stack 程序包。

安装分为四步：

1. 将 Azure Stack PowerShell 安装到连接的计算机。
2. 启用其他存储功能。
3. 将 PowerShell 包传输到已断开连接的工作站。
4. 在断开连接的工作站上手动启动 NuGet 提供程序。
5. 确认已安装 PowerShell。

### <a name="install-azure-stack-powershell"></a>安装 Azure Stack PowerShell

- Azure Stack 1904 或更高版本。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
    ```

- Azure Stack 1903 或更低版本。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Azure Stack 模块版本 1.7.1 是一项中断性变更。 若要从 Azure Stack 1.6.0 迁移，请参阅[迁移指南](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack)。

    > [!NOTE]
    > 在没有 Internet 连接的计算机上，建议执行以下 cmdlet 以禁用遥测数据收集功能。 在不禁用遥测数据收集功能的情况下，可能会遇到 cmdlet 性能降级的问题。 这仅适用于没有 Internet 连接的计算机。
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="add-your-packages-to-your-workstation"></a>将包添加到工作站

1. 将下载的程序包复制到 USB 设备。

2. 登录到已断开连接的工作站，将包从 USB 设备复制到工作站中的某个位置。

3. 在断开连接的工作站上手动启动 NuGet 提供程序。 有关说明，请参阅[在未连接到 Internet 的计算机上手动启动 NuGet 提供程序](https://docs.microsoft.com/powershell/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet)。

4. 将此位置注册为默认存储库，并从此存储库安装 AzureRM 和 `AzureStack` 模块：

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>确认 PowerShell 的安装

运行以下命令来确认安装：

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. 将 PowerShell 配置为使用代理服务器

在需要代理服务器访问 Internet 的场景中，请先将 PowerShell 配置为使用现有的代理服务器：

1. 打开提升的 PowerShell 命令提示符。
2. 运行以下命令：

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>后续步骤

- [从 GitHub 下载 Azure Stack 工具](azure-stack-powershell-download.md)
- [配置 Azure Stack 用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)
- [配置 Azure Stack 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)
- [在 Azure Stack 中管理 API 版本配置文件](../user/azure-stack-version-profiles.md)
