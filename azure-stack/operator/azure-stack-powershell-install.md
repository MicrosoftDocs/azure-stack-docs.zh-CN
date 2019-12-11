---
title: 为 Azure Stack 安装 PowerShell |Microsoft Docs
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
ms.openlocfilehash: e20012ce8ca397a9e74b97e380753b8f3ad33e45
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993833"
---
# <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure Stack 的 PowerShell

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure PowerShell 提供了一组 cmdlet，这些 cmdlet 使用 Azure 资源管理器模型来管理你的 Azure Stack 资源。

若要使用云，需要安装 Azure Stack 兼容的 PowerShell 模块。 Azure Stack 使用**AzureRM**模块，而不是在全球 Azure 中使用的较新**AzureAZ**模块。 还需要使用*API 配置文件*来指定 Azure Stack 资源提供程序的兼容终结点。

API 配置文件提供了一种管理 Azure 与 Azure Stack 之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Azure 资源管理器 PowerShell 模块。 每个云平台都有一组受支持的 API 版本配置文件。 例如，Azure Stack 支持特定的配置文件版本，如**2019-03-01-混合**。 安装配置文件时，会安装与指定配置文件相对应的 Azure 资源管理器 PowerShell 模块。

可以在连接到 internet 的、部分连接或断开连接的情况下安装 Azure Stack 兼容的 PowerShell 模块。 本文将指导你完成这些方案的详细说明。

## <a name="1-verify-your-prerequisites"></a>1. 验证你的先决条件

开始使用 Azure Stack 和 PowerShell 之前，必须具备以下先决条件：

- **PowerShell 版本5。0** <br>
若要检查版本，请运行 $PSVersionTable，并比较**主要**版本**PSVersion** 。 如果没有 PowerShell 5.0，请遵循[安装 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)。

  > [!Note]
  > PowerShell 5.0 需要 Windows 计算机。

- **在提升的命令提示符下运行 Powershell**。

- **PowerShell 库访问** <br>
  需要对[PowerShell 库](https://www.powershellgallery.com)的访问权限。 库是 PowerShell 内容的中央存储库。 **PowerShellGet**模块包含用于发现、安装、更新和发布 PowerShell 项目的 cmdlet。 这些项目的示例包括来自 PowerShell 库和其他专用存储库的模块、DSC 资源、角色功能和脚本。 如果在断开连接的情况下使用 PowerShell，则必须从连接到 internet 的计算机中检索资源，并将其存储在断开连接的计算机可访问的位置。

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. 验证 PowerShell 库辅助功能

验证是否已将 PSGallery 注册为存储库。

> [!Note]  
> 此步骤需要 internet 访问权限。

打开提升权限的 PowerShell 提示符，并运行以下 cmdlet：

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

如果未注册存储库，请打开提升的 PowerShell 会话并运行以下命令：

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. 卸载 Azure Stack PowerShell 模块的现有版本

在安装所需的版本之前，请确保卸载以前安装的 Azure Stack AzureRM PowerShell 模块。 使用以下两种方法之一卸载模块：

1. 若要卸载现有的 AzureRM PowerShell 模块，请关闭所有活动的 PowerShell 会话，并运行以下 cmdlet：

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    如果遇到错误（如 "模块已在使用"），请关闭正在使用模块的 PowerShell 会话，然后重新运行上述脚本。

2. 从 `C:\Program Files\WindowsPowerShell\Modules` 和 `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` 文件夹中删除以 `Azure` 或 `Azs.` 开头的所有文件夹。 删除这些文件夹将删除任何现有的 PowerShell 模块。

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. 已连接：安装适用于 internet 连接的 Azure Stack 的 PowerShell

API 版本配置文件和所需 Azure Stack PowerShell 模块将取决于你正在运行 Azure Stack 的版本。

### <a name="install-azure-stack-powershell"></a>安装 Azure Stack PowerShell

运行以下 PowerShell 脚本，在开发工作站上安装这些模块：

- 对于 Azure Stack 1910 或更高版本：

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.8.0
    ```

- 对于 Azure Stack 1908 或1903之后：

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```
  
- 对于 Azure Stack 版本1903或更早版本，请仅安装以下两个模块：

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module -Name AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - Azure Stack 模块版本1.8.0 是一项重大更改版本。 有关详细信息，请参阅[发行说明](release-notes.md)。
    > - Azure Stack 模块版本1.7.2 是一个重大更改版本。 若要从 Azure Stack 1.6.0 进行迁移，请参阅[迁移指南](https://aka.ms/azspshmigration171)。
    > - AzureRM 模块版本2.4.0 附带了 cmdlet New-azurermstorageaccount 的重大更改。 此 cmdlet 需要指定 `-Force` 参数才能删除存储帐户，而无需确认。
    > - 不需要安装**AzureRM**来安装 Azure Stack 版本1901或更高版本的模块。
    > - 除了在 Azure Stack 版本1901或更高版本上使用上述 AzureRM 模块外，不要安装2018-03-01 混合配置文件。

### <a name="confirm-the-installation-of-powershell"></a>确认 PowerShell 的安装

通过运行以下命令确认安装：

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

如果安装成功，则 `AzureRM` 和 `AzureStack` 模块将显示在输出中。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. 已断开连接：在没有 internet 连接的情况下安装 PowerShell

在断开连接的情况下，先将 PowerShell 模块下载到具有 internet 连接的计算机。 然后，将它们传输到用于安装的 Azure Stack 开发工具包（ASDK）。

登录到具有 internet 连接的计算机，然后使用以下脚本下载 Azure 资源管理器和 Azure Stack 包，具体取决于你的 Azure Stack 版本。

安装分为四个步骤：

1. 将 Azure Stack PowerShell 安装到连接的计算机。
2. 启用其他存储功能。
3. 将 PowerShell 包传输到断开连接的工作站。
4. 在断开连接的工作站上手动启动 NuGet 提供程序。
5. 确认安装了 PowerShell。

### <a name="install-azure-stack-powershell"></a>安装 Azure Stack PowerShell

- Azure Stack 1910 或更高版本。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
    ```

- 对于 Azure Stack 1908 或1903之后：

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
    ```

- Azure Stack 1903 或更早版本。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - Azure Stack 模块版本1.8.0 是一项重大更改版本。 有关详细信息，请参阅[发行说明](release-notes.md)。
    > Azure Stack 模块版本1.7.1 是一项重大更改。 若要从 Azure Stack 1.6.0 进行迁移，请参阅[迁移指南](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack)。

    > [!NOTE]
    > 在没有 internet 连接的计算机上，我们建议执行以下 cmdlet 来禁用遥测数据收集。 在不禁用遥测数据收集的情况下，你可能会遇到 cmdlet 性能下降的情况。 这仅适用于没有 internet 连接的计算机
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="add-your-packages-to-your-workstation"></a>将包添加到工作站

1. 将下载的包复制到 USB 设备。

2. 登录到断开连接的工作站，并将设备上的包复制到工作站上的某个位置。

3. 在断开连接的工作站上手动启动 NuGet 提供程序。 有关说明，请参阅[在未连接到 internet 的计算机上手动启动 NuGet 提供程序](https://docs.microsoft.com/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet)。

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

通过运行以下命令确认安装：

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. 将 PowerShell 配置为使用代理服务器

在需要代理服务器访问 internet 的方案中，首先将 PowerShell 配置为使用现有的代理服务器：

1. 打开提升的 PowerShell 提示符。
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
