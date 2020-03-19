---
title: 为 Azure Stack 中心安装 PowerShell Az module
description: 了解如何安装适用于 Azure Stack 集线器的 PowerShell。
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: c3e18ca850bb96791dbaa5d546864a11eb39dc62
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520481"
---
# <a name="install-powershell-az-module-for-azure-stack-hub"></a>为 Azure Stack 中心安装 PowerShell Az module

本文介绍如何使用 PowerShellGet 安装 Azure PowerShell Az 模块。 这些说明适用于 Windows、macOS 和 Linux 平台。

如果要为 Azure Stack 中心安装 PowerShell AzureRM 模块，请参阅[安装适用于 Azure Stack 中心的 Powershell AzureRM 模块](azure-stack-powershell-install.md)。

> [!IMPORTANT]
>  PowerShell Az module 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

还需要使用*API 配置文件*来指定 Azure Stack 中心资源提供程序的兼容终结点。
API 配置文件提供了一种管理 Azure 与 Azure Stack 中心之间版本差异的方式。 API 版本配置文件是一组具有特定 API 版本的 Azure 资源管理器 PowerShell 模块。 每个云平台都有一组受支持的 API 版本配置文件。 例如，Azure Stack 集线器支持特定的配置文件版本，如**2019-03-01-混合**。 安装配置文件时，会安装与指定配置文件相对应的 Azure 资源管理器 PowerShell 模块。

可以在连接到 internet 的、部分连接或断开连接的方案中安装 Azure Stack 集线器兼容的 PowerShell Az 模块。 本文将指导你完成这些方案的详细说明。

## <a name="1-verify-your-prerequisites"></a>1. 验证你的先决条件

Azure PowerShell 适用于 Windows 上的 PowerShell 5.1 或更高版本，或者所有平台上的 PowerShell Core 6.x 及更高版本。 你应安装适用于你的操作系统的[最新版本的 PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core) 。 在 PowerShell Core 上运行时，Azure PowerShell 没有额外的要求。

若要检查 PowerShell 版本，请运行以下命令：

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>Windows 必备组件
在 Windows 上的 PowerShell 5.1 中使用 Azure PowerShell：

1. 在必要时更新到 [Windows PowerShell 5.1](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)。 如果是在 Windows 10 平台上，则已安装 PowerShell 5.1。
2. 安装 [.NET Framework 4.7.2 或更高版本](/dotnet/framework/install)。
3. 请确保使用最新版本的 PowerShellGet。 运行 `Update-Module PowerShellGet -Force`。

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. 验证 PowerShell 库辅助功能

默认情况下，PowerShell 库未配置为 PowerShellGet 的受信任存储库。 首次使用 PSGallery 时会看到以下提示：

```Output
Untrusted repository

You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the `Set-PSRepository` cmdlet.

Are you sure you want to install the modules from 'PSGallery'?
[Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "N"):
```

请回答 `Yes` 或 `Yes to All` 继续安装。

Az 模块是 Azure PowerShell cmdlet 的汇总模块。 安装它时，系统会下载所有可用的 Azure 资源管理器模块并使其 cmdlet 可供使用。

首先，[安装 PowerShell Core 1.x 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)

然后，在 PowerShell Core 会话中，只为当前用户安装 Az 模块。 这是建议的安装范围。

```powershell  
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

为系统上的所有用户安装该模块需要提升的权限。 使用 Windows 中的 "以**管理员身份运行**" 启动 PowerShell 会话，或在 MacOS 或 Linux 上使用 `sudo` 命令：

```powershell  
Install-Module -Name Az -AllowClobber -Scope AllUsers
```

## <a name="3-connected-install-with-internet-connectivity"></a>3. 已连接：安装 internet 连接

Azure Stack AZ module 适用 Azure Stack 集线器2002或更高版本。 此外，Azure Stack Az module 适用于在 Windows 计算机上使用 PowerShell 5.1 或更高版本，或者在 Linux 或 macOS 平台上使用 PowerShell 1.x 或更高版本。 使用 PowerShellGet cmdlet 是首选的安装方法。 此方法在支持的平台上的工作方式相同。 

从 PowerShell 会话运行以下命令：

```powershell  
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.BootStrapper
Use-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.0-preview -AllowPrerelease
```

> [!Note]  
> Azure Stack 中心模块版本2.0.0 是一项重大更改。 有关详细信息，请参阅[从 AzureRM 迁移到 Azure Stack 中心 Azure PowerShell Az](migrate-azurerm-az.md) 。

> [!WARNING]  
> 不能同时为适用于 Windows 的 PowerShell 5.1 安装 AzureRM 和 Az 模块。 如果需要确保 AzureRM 在系统上可用，请为 PowerShell Core 6.x 或更高版本安装 Az 模块。 为此，请[安装 PowerShell Core 6.x 或更高版本](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)，然后在 PowerShell Core 终端中按照这些说明操作。


## <a name="5-disconnected-install-without-internet-connection"></a>5. 已断开连接：无 internet 连接安装

在某些环境中，无法连接到 PowerShell 库。 在这些情况下，仍然可以使用以下方法之一脱机安装：

* 将模块下载到你网络中的另一个位置，并将其用作安装源。
  这样，便可以在单个服务器或文件共享上缓存 PowerShell 模块，以将其部署到任何断开连接的系统。 了解如何设置本地存储库，并按照[使用本地 PowerShellGet 存储库](https://docs.microsoft.com/powershell/scripting/gallery/how-to/working-with-local-psrepositories)在断开连接的系统上进行安装。
* [将 Azure PowerShell MSI](https://docs.microsoft.com/powershell/azure/install-az-ps-msi) 下载到连接到网络的计算机，然后将安装程序复制到无法访问 PowerShell 库的系统。 请记住，MSI 安装程序仅适用于 Windows 上的 PowerShell 5.1。
* 将模块与 [Save-Module](https://docs.microsoft.com/powershell/module/PowershellGet/Save-Module) 一起保存到文件共享，或将其保存到其他源并手动将其复制到其他计算机：

  ```powershell  
  Save-Module -Name Az -Path '\\server\share\PowerShell\modules' -Force
  ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. 将 PowerShell 配置为使用代理服务器

在需要代理服务器访问 internet 的方案中，首先将 PowerShell 配置为使用现有的代理服务器：

1. 打开提升权限的 PowerShell 提示符。
2. 运行以下命令：

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="7-use-the-az-module"></a>7. 使用 Az 模块

可以使用基于 AzureRM 的 cmdlet 和代码示例。 但是，你将需要更改模块和 cmdlet 的名称。 模块名称已更改，以便 `AzureRM` 和 Azure 成为 `Az`，对于 cmdlet 是相同的。 例如，`AzureRM.Compute` 模块已重命名为 `Az.Compute`。` New-AzureRMVM` 已变为 ` New-AzVM`，并且 `Get-AzureStorageBlob` 现在为 `Get-AzStorageBlob`。

有关将 AzurRM 脚本移动到 Az 并 Azure Stack 集线器 AZ 模块中的重大更改的更详尽的讨论和指导，请参阅[从 AzureRM 迁移到 Azure PowerShell Az](migrate-azurerm-az.md)。

## <a name="next-steps"></a>后续步骤

- [从 GitHub 下载 Azure Stack 中心工具](azure-stack-powershell-download.md)
- [配置 Azure Stack 集线器用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)
- [配置 Azure Stack 集线器操作员的 PowerShell 环境](azure-stack-powershell-configure-admin.md)
- [在 Azure Stack 中心管理 API 版本配置文件](../user/azure-stack-version-profiles.md)

