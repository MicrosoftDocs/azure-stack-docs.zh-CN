---
title: 安装 Azure Stack 集线器的 PowerShell AzureRM 模块
description: 了解如何安装适用于 Azure Stack Hub 的 PowerShell。
author: mattbriggs
ms.topic: article
ms.date: 04/14/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: d2c40307daa37b8f522fde9010a3d285eebff0fc
ms.sourcegitcommit: 7b8e067cb449e67ca9c2935580684d78840ad495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106935"
---
# <a name="install-powershell-azurerm-module-for-azure-stack-hub"></a>安装 Azure Stack 集线器的 PowerShell AzureRM 模块

Azure PowerShell AzureRM 提供了一组 cmdlet，这些 cmdlet 使用 Azure 资源管理器模型来管理你的 Azure Stack 集线器资源。

还需要使用*API 配置文件*来指定 Azure Stack 中心资源提供程序的兼容终结点。

API 配置文件提供了一种管理 Azure 与 Azure Stack 中心之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Azure 资源管理器 PowerShell 模块。 每个云平台都有一组支持的 API 版本配置文件。 例如，Azure Stack 集线器支持特定的配置文件版本，如**2019-03-01-混合**。 安装配置文件时，会安装与指定的配置文件对应的 Azure 资源管理器 PowerShell 模块。

可以在连接到 internet 的、部分连接或断开连接的情况下安装 Azure Stack 集线器兼容的 PowerShell 模块。 本文将引导你完成适用于这些场景的详细说明。

## <a name="1-verify-your-prerequisites"></a>1. 验证你的先决条件

开始使用 Azure Stack 中心和 PowerShell AzureRM 模块之前，必须具备以下先决条件：

- **PowerShell 版本5。1** <br>
若要检查版本，请运行 **$PSVersionTable.PSVersion** 并比较**主**版本。 如果没有 PowerShell 5.1，请遵循[安装 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)。

  > [!Note]
  > PowerShell 5.1 需要 Windows 计算机。

- **在提升的命令提示符下运行 PowerShell**。

- **PowerShell 库访问权限** <br>
  你需要有权访问 [PowerShell 库](https://www.powershellgallery.com)。 该库是 PowerShell 内容的中心存储库。 **PowerShellGet** 模块包含用于发现、安装、更新和发布 PowerShell 项目的 cmdlet。 这些项目的示例包括来自 PowerShell 库和其他专用存储库的模块、DSC 资源、角色功能和脚本。 如果在离线场景中使用 PowerShell，则必须从已建立 Internet 连接的计算机检索资源，并将其存储在离线计算机可访问的位置。

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. 验证 PowerShell 库辅助功能

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

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. 卸载 Azure Stack 中心 PowerShell 模块的现有版本

在安装所需的版本之前，请确保卸载任何以前安装的 Azure Stack Hub AzureRM PowerShell 模块。 使用以下两种方法之一卸载模块：

1. 若要卸载现有的 AzureRM 和 Az PowerShell 模块，请关闭所有活动的 PowerShell 会话，并运行以下 cmdlet：

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```

    如果遇到任何错误（例如“模块已在使用中”），请关闭正在使用模块的 PowerShell 会话，然后重新运行上述脚本。

2. `Azure`删除以开头的所有文件夹， `Az`或`Azs.`从`C:\Program Files\WindowsPowerShell\Modules`和`C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`文件夹中删除。 删除这些文件夹会删除任何现有的 PowerShell 模块。

## <a name="4-connected-install-powershell-for-azure-stack-hub-with-internet-connectivity"></a>4. 已连接：安装适用于带 internet 连接的 Azure Stack 集线器的 PowerShell

API 版本配置文件和所需的 Azure Stack 中心 PowerShell 模块将取决于你正在运行的 Azure Stack 中心的版本。

### <a name="install-azure-stack-hub-powershell"></a>安装 Azure Stack 中心 PowerShell

运行以下 PowerShell 脚本，在开发工作站上安装这些模块：

::: moniker range=">=azs-2002"
对于 Azure Stack 集线器2002或更高版本：

可以是用户 AzureRm 模块或 Az preview 模块。 使用 Az 模块需要 Azure Stack 集线器2002和最新的修补程序。

若要使用 Az 预览模块，请按照[安装 PowerShell Az module 模块](powershell-install-az-module.md)中的说明进行操作。

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.1
```

::: moniker-end
::: moniker range="azs-1910"
对于 Azure Stack 集线器1910：

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
对于 Azure Stack 集线器1908或更早版本：

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.7.2
```

> [!Note]  
> Azure Stack 中心模块版本1.7.2 是一个重大更改版本。 若要从 Azure Stack 中心1.6.0 进行迁移，请参阅[迁移指南](https://aka.ms/azspshmigration171)。

::: moniker-end

### <a name="confirm-the-installation-of-powershell"></a>确认 PowerShell 的安装

运行以下命令来确认安装：

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

如果安装成功，输出中会显示 `AzureRm` 和 `AzureStack` 模块。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. 已断开连接：在没有 internet 连接的情况下安装 PowerShell

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

可以使用 AzureRM 或 Az preview 模块。 对于 Az 模块，请参阅[安装 PowerShell Az module 模块](powershell-install-az-module.md)。

```powershell

Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.1
```
::: moniker-end

::: moniker range="azs-1910"
Azure Stack 集线器1910。

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
```

> [!NOTE]  
> Azure Stack 中心模块版本1.8.0 是一项重大更改版本。 有关详细信息，请参阅[发行说明](release-notes.md)。

::: moniker-end
::: moniker range="<=azs-1908"
对于 Azure Stack 集线器1908或更早版本：

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
```

> [!NOTE]  
> Azure Stack 中心模块版本1.7.1 是一项重大更改。 若要从 Azure Stack 中心迁移1.6.0，请参阅[迁移指南](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack)。

::: moniker-end

> [!NOTE]  
> 在没有 Internet 连接的计算机上，建议执行以下 cmdlet 以禁用遥测数据收集功能。 在不禁用遥测数据收集功能的情况下，可能会遇到 cmdlet 性能降级的问题。 这仅适用于没有 Internet 连接的计算机。
> ```powershell
> Disable-AzureRmDataCollection
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

1. 打开提升的 PowerShell 提示符。
2. 运行以下命令：

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>后续步骤

- [从 GitHub 下载 Azure Stack 中心工具](azure-stack-powershell-download.md)
- [配置 Azure Stack 集线器用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)
- [配置 Azure Stack 集线器操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)
- [在 Azure Stack 中心管理 API 版本配置文件](../user/azure-stack-version-profiles.md)
