---
title: 在 Azure Stack Hub 中更新 MySQL 资源提供程序
description: 了解如何在 Azure Stack Hub 中更新 Azure Stack Hub MySQL 资源提供程序。
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 61149b7eb89908b641fd995b10b22e3d05a5fb8d
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812701"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>在 Azure Stack Hub 中更新 MySQL 资源提供程序

> [!IMPORTANT]
> 在更新资源提供程序之前，请查看发行说明，了解新功能、修补程序以及任何可能影响部署的已知问题。 发行说明还指定资源提供程序所需的最低 Azure Stack Hub 版本。

更新 Azure Stack Hub 内部版本时，可能会发布新的 MySQL 资源提供程序适配器。 虽然现有的适配器可以继续使用，但仍建议尽快更新到最新的内部版本。

  |支持的 Azure Stack Hub 版本|MySQL RP 版本|RP 服务正在其上运行的 Windows Server
  |-----|-----|-----|
  |2008、2005|[MySQL RP 版本 1.1.93.0](https://aka.ms/azshmysqlrp11930)|Microsoft AzureStack 加载项 RP Windows Server（仅限内部）
  |2005、2002、1910|[MySQL RP 版本 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|Windows Server 2016 Datacenter - Server Core|
  |1908|[MySQL RP 版本 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|Windows Server 2016 Datacenter - Server Core|
  |     |     |     |

MySQL 资源提供程序更新是累积的。 从旧版本更新时，可以直接更新到最新版本。 

若要更新资源提供程序，请使用 UpdateMySQLProvider.ps1 脚本。 使用具有本地管理权限且是订阅的 **所有者** 的服务帐户。 资源提供程序的下载包中提供此更新脚本。 

更新过程类似于[部署资源提供程序](./azure-stack-mysql-resource-provider-deploy.md)时使用的过程。 更新脚本与 DeployMySqlProvider.ps1 脚本使用相同的参数，你需要提供证书信息。

## <a name="update-script-processes"></a>更新脚本过程

UpdateMySQLProvider.ps1 脚本使用最新的 OS 映像创建新的虚拟机 (VM)，部署最新的资源提供程序代码，并将设置从旧资源提供程序迁移到新资源提供程序。

>[!NOTE]
>建议从市场管理下载最新的 Windows Server 2016 Core 映像或 Microsoft AzureStack 加载项 RP Windows Server 映像。 如需安装更新，可以将 **单个** MSU 包放置在本地依赖项路径中。 如果此位置中有多个 MSU 文件，则脚本将失败。

UpdateMySQLProvider.ps1 脚本在创建新的 VM 后，会从旧的资源提供程序 VM 中迁移以下设置：

* 数据库信息
* 宿主服务器信息
* 必要的 DNS 记录

## <a name="update-script-parameters"></a>更新脚本参数 
运行 **UpdateMySQLProvider.ps1** PowerShell 脚本时，可在命令行中指定以下参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 | 
| --- | --- | --- | 
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ | 
| **AzCredential** | Azure Stack Hub 服务管理员帐户的凭据。 使用部署 Azure Stack Hub 时所用的相同凭据。 如果用于 AzCredential 的帐户需要多重身份验证 (MFA)，则脚本将失败。 | _必需_ | 
| **VMLocalCredential** |SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ | 
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ | 
| **AzureEnvironment** | 用于部署 Azure Stack Hub 的服务管理员帐户的 Azure 环境。 仅对于 Azure AD 部署是必需的。 支持的环境名称为 **AzureCloud**、 **AzureUSGovernment** 或使用中国 Azure AD、 **AzureChinaCloud**。 | AzureCloud |
| **DependencyFilesLocalPath** | 同样必须将证书 .pfx 文件放在此目录中。 | _可选_（对于多节点部署是 _必需_ 的） | 
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ | 
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 | 
| **RetryDuration** | 每两次重试的超时间隔（秒）。 | 120 | 
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅下面的注释）。 | 否 | 
| **DebugMode** | 防止在失败时自动清除。 | 否 | 
| **AcceptLicense** | 跳过接受 GPL 许可条款的提示。  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>更新脚本示例

如果要将 MySQL 资源提供程序版本更新为 1.1.33.0 或早期版本，则需要在 PowerShell 中安装特定版本的 AzureRm.BootStrapper 和 Azure Stack Hub 模块。 

如果要将 MySQL 资源提供程序更新到版本 1.1.47.0 或更高版本，可以跳过此步骤。 部署脚本会自动下载所需的 PowerShell 模块并将其安装到路径 C:\Program Files\SqlMySqlPsh。 

>[!NOTE]
>如果文件夹 C:\Program Files\SqlMySqlPsh 已存在并已下载 PowerShell 模块，建议在运行更新脚本之前清理此文件夹。 这是为了确保下载并使用正确版本的 PowerShell 模块。

```powershell 
# Run the following scripts when updating to version 1.1.33.0 only.
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> 在断开连接的情况下，需要下载所需的 PowerShell 模块并手动注册存储库，这是先决条件。 可以在[部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)中获取详细信息

以下示例演示可以通过权限提升的 PowerShell 控制台运行的 *UpdateMySQLProvider.ps1* 脚本。 请务必根据需要更改变量信息和密码：

```powershell 
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
.$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
-VMLocalCredential $vmLocalAdminCreds `
-CloudAdminCredential $cloudAdminCreds `
-PrivilegedEndpoint $privilegedEndpoint `
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass `
-DependencyFilesLocalPath $tempDir\cert `
-AcceptLicense
```  

资源提供程序更新脚本完成后，请关闭当前的 PowerShell 会话。

## <a name="next-steps"></a>后续步骤
[维护 MySQL 资源提供程序](azure-stack-mysql-resource-provider-maintain.md)
