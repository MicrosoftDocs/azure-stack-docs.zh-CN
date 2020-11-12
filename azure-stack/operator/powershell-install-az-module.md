---
title: 安装适用于 Azure Stack Hub 的 PowerShell Az 模块
description: 了解如何安装适用于 Azure Stack Hub 的 PowerShell。
author: mattbriggs
ms.topic: article
ms.date: 11/11/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2020
ms.openlocfilehash: de226383fc5109cd702c4883754766664ea9ab73
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546933"
---
# <a name="install-powershell-az-module-for-azure-stack-hub"></a>安装适用于 Azure Stack Hub 的 PowerShell Az 模块

本文介绍如何使用 PowerShellGet 安装 Azure PowerShell Az 和兼容的 Azure Stack Hub 管理员模块。 Az 模块可以在 Windows、macOS 和 Linux 平台上安装。

还可以在 Docker 容器中运行 Azure Stack Hub 的 Az 模块。 有关说明，请参阅[使用 Docker 运行适用于 Azure Stack Hub 的 PowerShell](../user/azure-stack-powershell-user-docker.md)。

若要安装 PowerShell 资源模块 (AzureRM) module for Azure Stack Hub，请参阅 [安装适用于 Azure Stack 集线器的 Powershell AzureRM 模块](azure-stack-powershell-install.md)。

> [!IMPORTANT]
> 可能不会有新的 Azure 资源模块模块版本。 Azure 资源模块模块仅支持关键修复程序。 今后 Azure Stack 中心将只有 Az 版本。

可以使用 API 配置文件为 Azure Stack Hub 资源提供程序指定兼容的终结点。

API 配置文件提供一种管理 Azure 与 Azure Stack Hub 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Azure 资源管理器 PowerShell 模块。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack Hub 支持特定的配置文件版本，例如 **2019-03-01-hybrid** 。 安装配置文件时，会安装与指定的配置文件对应的 Azure 资源管理器 PowerShell 模块。

可在已连接到 Internet、部分联网或离线场景中安装与 Azure Stack Hub 兼容的 PowerShell Az 模块。 本文将引导你完成适用于这些场景的详细说明。

## <a name="1-verify-your-prerequisites"></a>1.验证先决条件

仅[带 Update 2002 的 Azure Stack Hub](./release-notes.md?view=azs-2002#2002-build-reference) 和最新[修补程序](./release-notes.md?view=azs-2002#hotfixes)支持 Az 模块。

Azure PowerShell 适用于 Windows 上的 PowerShell 5.1 或更高版本，或者所有平台上的 PowerShell Core 6.x 及更高版本。 你应该安装适用于你的操作系统的[最新版本的 PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)。 在 PowerShell Core 上运行时，Azure PowerShell 没有额外要求。

若要检查 PowerShell 版本，请运行以下命令：

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>Windows 先决条件
若要在 Windows 上的 PowerShell 5.1 中使用 Azure PowerShell，请执行以下操作：

1. 在必要时更新到 [Windows PowerShell 5.1](/powershell/scripting/windows-powershell/install/installing-windows-powershell#upgrading-existing-windows-powershell)。 如果是在 Windows 10 平台上，则已安装 PowerShell 5.1。
2. 安装 [.NET Framework 4.7.2 或更高版本](/dotnet/framework/install)。
3. 确保你具有最新版本的 PowerShellGet。 运行 `Install-Module PowerShellGet -MinimumVersion 2.2.3 -Force`。 

## <a name="2-prerequisites-for-linux-and-mac"></a>2.Linux 和 Mac 先决条件
需要 PowerShell Core 6.x 或更高版本。 请访问[链接](/powershell/scripting/install/installing-powershell-core-on-windows)获取说明

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3.卸载 Azure Stack Hub PowerShell 模块的现有版本

在安装所需的版本之前，请确保卸载任何以前安装的 Azure Stack 中心 Azure 资源模块或 Az PowerShell 模块。 使用以下两种方法之一卸载模块：

1. 若要卸载现有的 Azure 资源模块和 Az PowerShell 模块，请关闭所有活动的 PowerShell 会话，并运行以下 cmdlet：

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```
    如果遇到任何错误（例如“模块已在使用中”），请关闭正在使用模块的 PowerShell 会话，然后重新运行上述脚本。

2. 从 `C:\Program Files\WindowsPowerShell\Modules` 和 `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` 文件夹中删除以 `Azure`、`Az` 或 `Azs.` 开头的所有文件夹。 删除这些文件夹会删除任何现有的 PowerShell 模块。

## <a name="4-connected-install-with-internet-connectivity"></a>4.已联网：通过 Internet 连接安装

Azure Stack Az 模块将在 Azure Stack Hub 2002 或更高版本上运行。 此外，Azure Stack Az 模块将在 Windows 计算机上与 PowerShell 5.1 或更高版本配合使用，在 Linux 或 macOS 平台上与 PowerShell 6.x 或更高版本配合使用。 使用 PowerShellGet cmdlet 是首选的安装方法。 此方法在受支持平台上的工作原理相同。

从 PowerShell 会话运行以下命令：

```powershell  
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
Install-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
```

> [!Note]  
> Azure Stack Hub 模块版本 2.0.0 是一项中断性变更。 有关详细信息，请参阅[在 Azure Stack Hub 中从 AzureRM 迁移到 Azure PowerShell Az](migrate-azurerm-az.md)。

> [!WARNING]
> 你不能同时为适用于 Windows PowerShell 5.1 的 Azure 资源模块 (AzureRM) 和 Az 模块安装。 如果需要在系统中保留 Azure 资源模块，请安装适用于 PowerShell Core 1.x 或更高版本的 Az 模块。 为此，请[安装 PowerShell Core 6.x 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)，然后在 PowerShell Core 终端中按照这些说明操作。

## <a name="5-disconnected-install-without-internet-connection"></a>5.离线：不通过 Internet 连接安装

在离线场景中，请先将 PowerShell 模块下载到已建立 Internet 连接的计算机。 然后，将它们传输到要安装的 Azure Stack 开发工具包 (ASDK)。

登录到已建立 Internet 连接的计算机，并根据 Azure Stack Hub 的版本，使用以下脚本下载 Azure 资源管理器和 Azure Stack Hub 包。

安装分为五步：

1. 将 Azure Stack Hub PowerShell 安装到连接的计算机。
2. 启用其他存储功能。
3. 将 PowerShell 包传输到已断开连接的工作站。
4. 在断开连接的工作站上手动启动 NuGet 提供程序。
5. 确认已安装 PowerShell。

### <a name="install-azure-stack-hub-powershell"></a>安装 Azure Stack Hub PowerShell

::: moniker range=">=azs-2002"
Azure Stack Hub 2002 或更高版本。

可以使用 Azure 资源模块或 Az 模块。 有关 Azure 资源模块，请参阅 [安装 PowerShell AzureRM 模块](powershell-install-az-module.md)中的说明。 以下代码保存可信联机存储库 https://www.powershellgallery.com/ 中的模块。

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install-module -Name PowerShellGet -MinimumVersion 2.2.3 -Force
Import-Module -Name PackageManagement -ErrorAction Stop

$savedModulesPath = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $savedModulesPath -Force -RequiredVersion 0.10.0-preview
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $savedModulesPath -Force -RequiredVersion 2.0.2-preview
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

3. 在断开连接的工作站上手动启动 NuGet 提供程序。 有关说明，请参阅[在未连接到 Internet 的计算机上手动启动 NuGet 提供程序](/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet)。

4. 将此位置注册为默认存储库，并 `AzureRM` `AzureStack` 从此存储库安装和模块：

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureStack -Repository $RepoName -RequiredVersion 2.0.2-preview -AllowPrerelease -Scope AllUsers

   Install-Module -Name Az -Repository $RepoName -RequiredVersion 0.10.0-preview -AllowPrerelease -Scope AllUsers
   ```

### <a name="confirm-the-installation-of-powershell"></a>确认已安装 PowerShell

运行以下命令来确认安装：

```powershell
Get-Module -Name "Az*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6.配置 PowerShell 以使用代理服务器

在需要代理服务器访问 Internet 的场景中，请先将 PowerShell 配置为使用现有的代理服务器：

1. 打开提升的 PowerShell 命令提示符。
2. 运行以下命令：

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="7-use-the-az-module"></a>7.使用 Az 模块

可以使用基于 Azure 资源模块的 cmdlet 和代码示例。 但是，你需要更改模块和 cmdlet 的名称。 模块名称已更改，以便 `AzureRM` 和 Azure 变为 `Az`，对于 cmdlet 也是如此。 例如，`AzureRM.Compute` 模块已重命名为 `Az.Compute`.` New-AzureRMVM` 已变为 ` New-AzVM`，并且 `Get-AzureStorageBlob` 现在为 `Get-AzStorageBlob`。

有关将 AzurRM 脚本移到 Az 中和 Azure Stack Hub 的 Az 模块中的中断性变更的更详尽讨论和指导，请参阅[从 AzureRM 迁移到 Azure PowerShell Az](migrate-azurerm-az.md)。

## <a name="next-steps"></a>后续步骤

- [从 GitHub 下载 Azure Stack Hub 工具](azure-stack-powershell-download.md)
- [配置 Azure Stack Hub 用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)
- [配置 Azure Stack Hub 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)
- [管理 Azure Stack Hub 中的 API 版本配置文件](../user/azure-stack-version-profiles.md)
