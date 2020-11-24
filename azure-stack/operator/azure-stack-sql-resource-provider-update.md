---
title: 更新 Azure Stack Hub SQL 资源提供程序
titleSuffix: Azure Stack Hub
description: 了解如何更新 Azure Stack Hub SQL 资源提供程序。
author: bryanla
ms.topic: article
ms.date: 8/19/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: a67b950f91d502378a151031e25a68f861494555
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812640"
---
# <a name="update-the-sql-resource-provider"></a>更新 SQL 资源提供程序

> [!IMPORTANT]
> 在更新资源提供程序之前，请查看发行说明，了解新功能、修补程序以及任何可能影响部署的已知问题。 发行说明还指定资源提供程序所需的最低 Azure Stack Hub 版本。

当 Azure Stack Hub 更新到新版本时，可能会发布新的 SQL 资源提供程序。 虽然现有的资源提供程序可以继续使用，但仍建议尽快更新到最新的内部版本。

|支持的 Azure Stack Hub 版本|SQL RP 版本|RP 服务正在其上运行的 Windows Server
  |-----|-----|-----|
  |2008、2005|[SQL RP 版本 1.1.93.0](https://aka.ms/azshsqlrp11930)|Microsoft AzureStack 加载项 RP Windows Server（仅限内部）
  |2005、2002、1910|[SQL RP 版本 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|Windows Server 2016 Datacenter - Server Core|
  |1908|[SQL RP 版本 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|Windows Server 2016 Datacenter - Server Core|
  |     |     |     |

SQL 资源提供程序更新是累积的。 从旧版本更新时，可以直接更新到最新版本。 

若要更新资源提供程序，请使用 **UpdateSQLProvider.ps1** 脚本。 使用具有本地管理权限且是订阅的 **所有者** 的服务帐户。 资源提供程序的下载包中提供此更新脚本。 

更新过程类似于[部署资源提供程序](./azure-stack-sql-resource-provider-deploy.md)时使用的过程。 更新脚本与 DeploySqlProvider.ps1 脚本使用相同的参数，你需要提供证书信息。

## <a name="update-script-processes"></a>更新脚本过程

UpdateSQLProvider.ps1 脚本使用最新的 OS 映像创建新的虚拟机 (VM)，部署最新的资源提供程序代码，并将设置从旧资源提供程序迁移到新资源提供程序。 

> [!NOTE]
>建议从市场管理下载最新的 Windows Server 2016 Core 映像或 Microsoft AzureStack 加载项 RP Windows Server 映像。 如需安装更新，可以将 **单个** MSU 包放置在本地依赖项路径中。 如果此位置中有多个 MSU 文件，则脚本将失败。

UpdateSQLProvider.ps1 脚本在创建新的 VM 后，会从旧的资源提供程序 VM 中迁移以下设置：

* 数据库信息
* 宿主服务器信息
* 必要的 DNS 记录

## <a name="update-script-parameters"></a>更新脚本参数

运行 **UpdateSQLProvider.ps1** PowerShell 脚本时，可在命令行中指定以下参数。 如果未指定参数或任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点时所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack Hub 服务管理员帐户的凭据。 使用部署 Azure Stack Hub 时所用的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **AzureEnvironment** | 用于部署 Azure Stack Hub 的服务管理员帐户的 Azure 环境。 仅对于 Azure AD 部署是必需的。 支持的环境名称为 **AzureCloud**、 **AzureUSGovernment** 或使用中国 Azure AD、 **AzureChinaCloud**。 | AzureCloud |
| **DependencyFilesLocalPath** | 同样必须将证书 .pfx 文件放在此目录中。 | 对单节点为可选，但对多节点为必选 |
| **DefaultSSLCertificatePassword** | .pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 操作失败时，想要重试每个操作的次数。| 2 |
| **RetryDuration** |每两次重试的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |

## <a name="update-script-powershell-example"></a>更新脚本 PowerShell 示例

如果要将 SQL 资源提供程序版本更新为 1.1.33.0 或早期版本，则需要在 PowerShell 中安装特定版本的 AzureRm.BootStrapper 和 Azure Stack Hub 模块。 

如果要将 SQL 资源提供程序更新到版本 1.1.47.0 或更高版本，可以跳过此步骤。 部署脚本会自动下载所需的 PowerShell 模块并将其安装到路径 C:\Program Files\SqlMySqlPsh。

>[!NOTE]
>如果文件夹 C:\Program Files\SqlMySqlPsh 已存在并已下载 PowerShell 模块，建议在运行更新脚本之前清理此文件夹。 这是为了确保下载并使用正确版本的 PowerShell 模块。

```powershell
# Run the following scripts when updating to version 1.1.33.0 only.
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> 在断开连接的情况下，需要下载所需的 PowerShell 模块并手动注册存储库，这是先决条件。 可以在[部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md)中获取详细信息

下面是 *UpdateSQLProvider.ps1* 脚本的使用示例，该脚本可以通过提升的 PowerShell 控制台来运行。 请务必根据需要更改变量信息和密码：  

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
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
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

资源提供程序更新脚本完成后，请关闭当前的 PowerShell 会话。

## <a name="next-steps"></a>后续步骤

[维护 SQL 资源提供程序](azure-stack-sql-resource-provider-maintain.md)
