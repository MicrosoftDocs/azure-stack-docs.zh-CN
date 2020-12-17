---
title: 安装适用于 Azure Stack Hub 的 PowerShell AzureRM 模块
description: 了解如何安装适用于 Azure Stack Hub 的 PowerShell。 了解如何安装 PowerShell AzureRM 模块和所需的 API 配置文件。
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/16/2020
ms.openlocfilehash: d11a439054fc013ae0b595a684f0d6760fa932a5
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620613"
---
# <a name="install-powershell-azurerm-module-for-azure-stack-hub"></a>安装适用于 Azure Stack Hub 的 PowerShell AzureRM 模块

Azure PowerShell Azure 资源管理器 (AzureRM) 提供了一组使用 Azure 资源管理器模型管理 Azure Stack Hub 资源的 cmdlet。

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> 你访问的是已过时的 Azure PowerShell 版本的网页。 Azure 资源管理器 (AzureRM) PowerShell 模块的所有版本均已过时，但并不缺乏支持。 Az PowerShell 模块现在是建议用于与 Azure 和 Azure Stack 中心交互的 PowerShell 模块。 若要开始使用 Az PowerShell 模块，请参阅[为 Azure Stack Hub 安装 PowerShell Az 预览模块](powershell-install-az-module.md)。 若要了解如何迁移到 Az PowerShell 模块， 请参阅[在 Azure Stack Hub 中从 AzureRM 迁移到 Azure PowerShell Az](migrate-azurerm-az.md)。
::: moniker-end

还需要使用 API 配置文件为 Azure Stack Hub 资源提供程序指定兼容的终结点。

API 配置文件提供一种管理 Azure 与 Azure Stack Hub 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Azure 资源管理器 PowerShell 模块。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack Hub 支持特定的配置文件版本，例如 **2019-03-01-hybrid**。 安装配置文件时，会安装与指定的配置文件对应的 Azure 资源管理器 PowerShell 模块。

可在已连接到 Internet、部分联网或离线场景中安装与 Azure Stack Hub 兼容的 PowerShell 模块。 本文将引导你完成适用于这些场景的详细说明。

还可以在 Docker 容器中运行 Azure Stack Hub 的 Azure 资源管理器模块。 有关说明，请参阅[使用 Docker 运行适用于 Azure Stack Hub 的 PowerShell](../user/azure-stack-powershell-user-docker.md)。

## <a name="1-verify-your-prerequisites"></a>1.验证先决条件

开始使用 Azure Stack Hub 和 PowerShell Azure 资源管理器模块之前，必须具备以下先决条件：

- **PowerShell 版本 5.1** <br>
若要检查版本，请运行 **$PSVersionTable.PSVersion** 并比较 **主** 版本。 如果没有 PowerShell 5.1，请按照[安装 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) 中所述进行操作。

  > [!Note]
  > PowerShell 5.1 需要 Windows 计算机。

- 在权限提升的命令提示符下运行 PowerShell。

- **PowerShell 库访问权限** <br>
  你需要有权访问 [PowerShell 库](https://www.powershellgallery.com)。 该库是 PowerShell 内容的中心存储库。 **PowerShellGet** 模块包含用于发现、安装、更新和发布 PowerShell 项目的 cmdlet。 这些项目的示例包括来自 PowerShell 库和其他专用存储库的模块、DSC 资源、角色功能和脚本。 如果在离线场景中使用 PowerShell，则必须从已建立 Internet 连接的计算机检索资源，并将其存储在离线计算机可访问的位置。

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2.验证 PowerShell 库的可访问性

验证 PSGallery 是否已注册为存储库。

> [!Note]  
> 此步骤需要访问 Internet。

打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell
Install-module -Name PowerShellGet -Force
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

如果未注册存储库，请打开权限提升的 PowerShell 会话并运行以下命令：

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3.卸载 Azure Stack Hub PowerShell 模块的现有版本

在安装所需版本之前，请确保卸载以前安装的任何 Azure Stack Hub Azure 资源管理器 PowerShell 模块。 使用以下两种方法之一卸载模块：

1. 若要卸载现有的 Azure 资源管理器和 Az PowerShell 模块，请关闭所有活动的 PowerShell 会话，并运行以下 cmdlet：

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```

    如果遇到任何错误（例如“模块已在使用中”），请关闭正在使用模块的 PowerShell 会话，然后重新运行上述脚本。

2. 从 `C:\Program Files\WindowsPowerShell\Modules` 和 `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` 文件夹中删除以 `Azure`、`Az` 或 `Azs.` 开头的所有文件夹。 删除这些文件夹会删除任何现有的 PowerShell 模块。

## <a name="4-connected-install-powershell-for-azure-stack-hub-with-internet-connectivity"></a>4.已联网：在已建立 Internet 连接的情况下安装适用于 Azure Stack Hub 的 PowerShell

需要的 API 版本配置文件和 Azure Stack Hub PowerShell 模块将取决于所运行的 Azure Stack Hub 版本。

### <a name="install-azure-stack-hub-powershell"></a>安装 Azure Stack Hub PowerShell

运行以下 PowerShell 脚本，在开发工作站上安装这些模块：

::: moniker range=">=azs-2002"
对于 Azure Stack Hub 2002 或更高版本：

可以使用 AzureRm 模块或 Az 预览版模块。 使用 Az 模块需要 Azure Stack Hub 2002 和最新修补程序。

若要使用 Az 预览版模块，请按照[安装 PowerShell Az 模块](powershell-install-az-module.md)中的说明进行操作。

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.2
```

::: moniker-end
::: moniker range="azs-1910"
对于 Azure Stack Hub 1910：

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.0
```

> [!Note]  
> - Azure Stack Hub 模块版本 1.8.0 是一个包含中断性变更的版本。 有关详细信息，请参阅[发行说明](release-notes.md)。

::: moniker-end
::: moniker range="<=azs-1908"
对于 Azure Stack Hub 1908 或更低版本：

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.7.2
```

> [!Note]  
> Azure Stack Hub 模块版本 1.7.2 是一个包含中断性变更的版本。 若要从 Azure Stack Hub 1.6.0 迁移，请参阅[迁移指南](https://aka.ms/azspshmigration171)。

::: moniker-end

### <a name="confirm-the-installation-of-powershell"></a>确认已安装 PowerShell

运行以下命令来确认安装：

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

如果安装成功，输出中会显示 `AzureRm` 和 `AzureStack` 模块。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5.离线：在未建立 Internet 连接的情况下安装 PowerShell

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

可以使用 Azure 资源管理器或 Az 预览版模块。 有关 Az 模块，请参阅[安装 PowerShell Az 模块](powershell-install-az-module.md)中的说明。

```powershell

Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.2
```
::: moniker-end

::: moniker range="azs-1910"
Azure Stack Hub 1910。

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
```

> [!NOTE]  
> Azure Stack Hub 模块版本 1.8.0 是一个包含中断性变更的版本。 有关详细信息，请参阅[发行说明](release-notes.md)。

::: moniker-end
::: moniker range="<=azs-1908"
对于 Azure Stack Hub 1908 或更低版本：

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
```

> [!NOTE]  
> Azure Stack Hub 模块版本 1.7.1 是一项中断性变更。 若要从 Azure Stack Hub 1.6.0 迁移，请参阅[迁移指南](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack)。

::: moniker-end

> [!NOTE]  
> 在没有 Internet 连接的计算机上，建议执行以下 cmdlet 以禁用遥测数据收集功能。 在不禁用遥测数据收集功能的情况下，可能会遇到 cmdlet 性能降级的问题。 这仅适用于没有 Internet 连接的计算机。
> ```powershell
> Disable-AzureRmDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>将包添加到工作站

1. 将下载的程序包复制到 USB 设备。

2. 登录到已断开连接的工作站，将包从 USB 设备复制到工作站中的某个位置。

3. 在断开连接的工作站上手动启动 NuGet 提供程序。 有关说明，请参阅[在未连接到 Internet 的计算机上手动启动 NuGet 提供程序](/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet)。

4. 将此位置注册为默认存储库，并从此存储库安装 Azure 资源管理器和 `AzureStack` 模块：

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

### <a name="confirm-the-installation-of-powershell"></a>确认已安装 PowerShell

运行以下命令来确认安装：

```powershell
Get-Module -Name "Azure*" -ListAvailable
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

## <a name="known-issue"></a>已知问题

###  <a name="method-get_serializationsettings-error"></a>get_SerializationSettings 方法错误 

- 原因：PowerShell Az 模块与 PowerShell Azure 资源管理器模块不兼容。

    以下错误指示 Azure 资源管理器模块和 Az 模块在同一会话中加载： 

    ```powershell  
    >  Method 'get_SerializationSettings' in type 'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' does 
    not have an implementation.
    ```

- 补救措施：卸载冲突的模块。 

  若要使用 Azure 资源管理器模块，请卸载 Az 模块。 或者，若要使用 Az 模块，请卸载 Azure 资源管理器。 关闭 PowerShell 会话并卸载 Az 或 Azure 资源管理器模块。 
  
  可以在[卸载 Azure Stack Hub PowerShell 模块的现有版本](#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules)中找到相关说明。

## <a name="next-steps"></a>后续步骤

- [从 GitHub 下载 Azure Stack Hub 工具](azure-stack-powershell-download.md)
- [配置 Azure Stack Hub 用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)
- [配置 Azure Stack Hub 操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)
- [管理 Azure Stack Hub 中的 API 版本配置文件](../user/azure-stack-version-profiles.md)
