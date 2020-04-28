---
title: 为 Azure Stack 中心安装 PowerShell Az module
description: 了解如何安装适用于 Azure Stack 集线器的 PowerShell。
author: mattbriggs
ms.topic: article
ms.date: 04/14/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: d39bac1a99c2dc7d7a43f211a5fd3e5a7275de33
ms.sourcegitcommit: d930d52e27073829b8bf8ac2d581ec2accfa37e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "82174009"
---
# <a name="install-powershell-az-preview-module-for-azure-stack-hub"></a>为 Azure Stack 中心安装 PowerShell Az preview module

本文介绍如何使用 PowerShellGet 安装 Azure PowerShell Az 和兼容 Azure Stack 中心管理员模块。 Az 模块可安装在 Windows、macOS 和 Linux 平台上。

如果要为 Azure Stack 中心安装 PowerShell AzureRM 模块，请参阅[安装适用于 Azure Stack 中心的 Powershell AzureRM 模块](azure-stack-powershell-install.md)。

> [!IMPORTANT]
> PowerShell Az module 目前为公共预览版。
> 此预览版在即将发布的版本中可能存在重大更改。有关详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 可能不会有新的 AzureRM 模块版本。 AzureRM 模块仅支持严重修补程序。 今后将只有 Az 版本适用于 Test-azurestack

可以使用*API 配置文件*来指定 Azure Stack 中心资源提供程序的兼容终结点。

API 配置文件提供了一种管理 Azure 与 Azure Stack 中心之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Azure 资源管理器 PowerShell 模块。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack 集线器支持特定的配置文件版本，如**2019-03-01-混合**。 安装配置文件时，会安装与指定的配置文件对应的 Azure 资源管理器 PowerShell 模块。

可以在连接到 Internet 的、部分连接或断开连接的方案中安装 Azure Stack 集线器兼容的 PowerShell Az 模块。 本文将引导你完成适用于这些场景的详细说明。

## <a name="1-verify-your-prerequisites"></a>1. 验证你的先决条件

只有[更新2002的 Azure Stack 集线器](https://docs.microsoft.com/azure-stack/operator/release-notes?view=azs-2002#2002-build-reference)和最新的[修补程序](https://docs.microsoft.com/azure-stack/operator/release-notes?view=azs-2002#hotfixes)支持 Az 模块。

Azure PowerShell 适用于 Windows 上的 PowerShell 5.1 或更高版本，或者所有平台上的 PowerShell Core 6.x 及更高版本。 你应安装适用于你的操作系统的[最新版本的 PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core) 。 在 PowerShell Core 上运行时，Azure PowerShell 没有额外的要求。

若要检查 PowerShell 版本，请运行以下命令：

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>Windows 必备组件
在 Windows 上的 PowerShell 5.1 中使用 Azure PowerShell：

1. 在必要时更新到 [Windows PowerShell 5.1](https://docs.microsoft.com//powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)。 如果是在 Windows 10 平台上，则已安装 PowerShell 5.1。
2. 安装 [.NET Framework 4.7.2 或更高版本](https://docs.microsoft.com//dotnet/framework/install)。
3. 请确保使用最新版本的 PowerShellGet。 运行 `Install-Module PowerShellGet -MinimumVersion 2.2.3 -Force`。 

## <a name="2-prerequisites-for-linux-and-mac"></a>2. 适用于 Linux 和 Mac 的先决条件
需要 PowerShell Core 4.x 或更高版本。 请单击[链接](https://docs.microsoft.com//powershell/scripting/install/installing-powershell-core-on-windows)获取说明

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. 卸载 Azure Stack 中心 PowerShell 模块的现有版本

在安装所需的版本之前，请确保卸载任何以前安装的 Azure Stack Hub AzureRM 或 Az PowerShell 模块。 使用以下两种方法之一卸载模块：

1. 若要卸载现有的 AzureRM 和 Az PowerShell 模块，请关闭所有活动的 PowerShell 会话，并运行以下 cmdlet：

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```
    如果遇到任何错误（例如“模块已在使用中”），请关闭正在使用模块的 PowerShell 会话，然后重新运行上述脚本。

2. `Azure`删除以开头的所有文件夹， `Az`或`Azs.`从`C:\Program Files\WindowsPowerShell\Modules`和`C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`文件夹中删除。 删除这些文件夹会删除任何现有的 PowerShell 模块。

## <a name="4-connected-install-with-internet-connectivity"></a>4. 已连接：安装 internet 连接

Azure Stack Az module 适用 Azure Stack 集线器2002或更高版本。 此外，Azure Stack Az 模块可在 Windows 计算机上使用 PowerShell 5.1 或更高版本，或者在 Linux 或 macOS 平台上使用 PowerShell 1.x 或更高版本。 使用 PowerShellGet cmdlet 是首选的安装方法。 此方法在支持的平台上的工作方式相同。

从 PowerShell 会话运行以下命令：

```powershell  
Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
Install-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.0-preview -AllowPrerelease
```

> [!Note]  
> Azure Stack 中心模块版本2.0.0 是一项重大更改。 有关详细信息，请参阅[从 AzureRM 迁移到 Azure Stack 中心 Azure PowerShell Az](migrate-azurerm-az.md) 。

> [!WARNING]
> 对于 Windows 版 PowerShell 5.1，不能同时安装 AzureRM 和 Az 模块。 如果需要确保 AzureRM 在系统上可用，请为 PowerShell Core 6.x 或更高版本安装 Az 模块。 为此，请[安装 PowerShell Core 6.x 或更高版本](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)，然后在 PowerShell Core 终端中按照这些说明操作。

## <a name="5-disconnected-install-without-internet-connection"></a>5. 已断开连接：无 internet 连接安装

在离线场景中，请先将 PowerShell 模块下载到已建立 Internet 连接的计算机。 然后，将它们传输到要安装的 Azure Stack 开发工具包 (ASDK)。

登录到具有 internet 连接的计算机，然后使用以下脚本下载 Azure 资源管理器和 Azure Stack 集线器包，具体取决于 Azure Stack 集线器的版本。

安装包括5个步骤：

1. 将 Azure Stack 集线器 PowerShell 安装到连接的计算机。
2. 启用其他存储功能。
3. 将 PowerShell 包传输到已断开连接的工作站。
4. 在断开连接的工作站上手动启动 NuGet 提供程序。
5. 确认已安装 PowerShell。

### <a name="install-azure-stack-hub-powershell"></a>安装 Azure Stack 中心 PowerShell

::: moniker range=">=azs-2002"
Azure Stack 集线器2002或更高版本。

可以使用 AzureRM 或 Az preview 模块。 对于 RM 模块，请参阅[安装 PowerShell AzureRM 模块](azure-stack-powershell-install.md)中的说明。

```powershell

Install-module -Name PowerShellGet -MinimumVersion 2.2.3 -Force
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $Path -Force -RequiredVersion 0.10.0-preview
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 2.0.0-preview
```
::: moniker-end

> [!NOTE]  
> 在没有 Internet 连接的计算机上，建议执行以下 cmdlet 以禁用遥测数据收集功能。 在不禁用遥测数据收集功能的情况下，可能会遇到 cmdlet 性能降级的问题。 这仅适用于没有 Internet 连接的计算机。
> ```powershell
> Disable-AzDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>将包添加到工作站

1. 将下载的程序包复制到 USB 设备。

2. 登录到已断开连接的工作站，将包从 USB 设备复制到工作站中的某个位置。

3. 在断开连接的工作站上手动启动 NuGet 提供程序。 有关说明，请参阅[在未连接到 Internet 的计算机上手动启动 NuGet 提供程序](https://docs.microsoft.com/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet)。

4. 将此位置注册为默认存储库，并从此存储库安装 AzureRM 和 `AzureStack` 模块：

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name Az.BootStrapper -Repository $RepoName -Scope AllUsers -AllowPrerelease

   Set-BootstrapRepo -Repo $RepoName

   Install-AzProfile -Profile '2019-03-01-hybrid' -Force -Scope AllUsers

   Install-Module -Name AzureStack -Repository $RepoName -RequiredVersion 2.0.0-preview -AllowPrerelease -Scope AllUsers
   ```

### <a name="confirm-the-installation-of-powershell"></a>确认 PowerShell 的安装

运行以下命令来确认安装：

```powershell
Get-Module -Name "Az*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. 将 PowerShell 配置为使用代理服务器

在需要代理服务器访问 Internet 的场景中，请先将 PowerShell 配置为使用现有的代理服务器：

1. 打开提升的 PowerShell 提示符。
2. 运行以下命令：

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="7-use-the-az-module"></a>7. 使用 Az 模块

可以使用基于 AzureRM 的 cmdlet 和代码示例。 但是，你将需要更改模块和 cmdlet 的名称。 模块名称已更改，以便`AzureRM`和 Azure 成为`Az`cmdlet，并对 cmdlet 具有相同的名称。 例如， `AzureRM.Compute`模块已重命名为`Az.Compute`。` New-AzureRMVM` 已变为 ` New-AzVM`，并且 `Get-AzureStorageBlob` 现在为 `Get-AzStorageBlob`。

有关将 AzurRM 脚本移动到 Az 并 Azure Stack 集线器 Az 模块中的重大更改的更详尽的讨论和指导，请参阅[从 AzureRM 迁移到 Azure PowerShell Az](migrate-azurerm-az.md)。

Azure Stack 中心内容中使用的 PowerShell 代码片段使用 AzureRM 模块。 Az module 是预览版本。 可以按照迁移指南中的[从 AzureRM 迁移到 Azure Stack 中心 Azure PowerShell Az 中](powershell-install-az-module.md)的指南，重构用于 Az 模块的代码段。

## <a name="next-steps"></a>后续步骤

- [从 GitHub 下载 Azure Stack 中心工具](azure-stack-powershell-download.md)
- [配置 Azure Stack 集线器用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)
- [配置 Azure Stack 集线器操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)
- [在 Azure Stack 中心管理 API 版本配置文件](../user/azure-stack-version-profiles.md)
