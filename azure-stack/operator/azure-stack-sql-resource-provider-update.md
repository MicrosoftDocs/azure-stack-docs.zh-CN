---
title: 更新 Azure Stack 中心 SQL 资源提供程序
titleSuffix: Azure Stack Hub
description: 了解如何更新 Azure Stack 中心 SQL 资源提供程序。
author: bryanla
ms.topic: article
ms.date: 11/11/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 43099dfb4bfb2ffe50e6c8ccdfc05633ff4f3a6e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697155"
---
# <a name="update-the-sql-resource-provider"></a>更新 SQL 资源提供程序

Azure Stack 中心更新到新版本时，可能会发布新的 SQL 资源提供程序。 尽管现有的资源提供程序会继续工作，但建议尽快更新到最新版本。

从 SQL 资源提供程序版本1.1.33.0 版本开始，更新是累积的，无论是从版本1.1.24.0 或更高版本开始，都无需按照它们发布的顺序进行安装。 例如，如果运行的是 SQL 资源提供程序的版本1.1.24.0，则可以升级到1.1.33.0 或更高版本，而无需先安装版本1.1.30.0。 若要查看可用的资源提供程序版本以及支持它们的 Azure Stack 中心版本，请参阅[部署资源提供程序必备组件](./azure-stack-sql-resource-provider-deploy.md#prerequisites)中的版本列表。

若要更新资源提供程序，请使用*updatesqlprovider.ps1*脚本。 使用具有本地管理权限的服务帐户，并且是订阅的**所有者**。 此脚本包含在下载新的 SQL 资源提供程序中。 更新过程类似于用于[部署资源提供程序](./azure-stack-sql-resource-provider-deploy.md)的过程。 更新脚本使用与 Deploysqlprovider.ps1 脚本相同的参数，你将需要提供证书信息。

 > [!IMPORTANT]
 > 升级资源提供程序之前，请查看发行说明，了解新功能、修复程序以及可能影响部署的任何已知问题。

## <a name="update-script-processes"></a>更新脚本进程

*Updatesqlprovider.ps1*脚本使用最新的资源提供程序代码创建新的虚拟机（VM）。

> [!NOTE]
> 建议从 Marketplace 管理下载最新的 Windows Server 2016 Core 映像。 如果需要安装更新，可以将**单个**MSU 包置于本地依赖项路径中。 如果此位置有多个 MSU 文件，此脚本将失败。

*Updatesqlprovider.ps1*脚本创建新的 vm 后，该脚本将从旧的提供程序 VM 迁移以下设置：

* 数据库信息
* 宿主服务器信息
* 所需的 DNS 记录

## <a name="update-script-parameters"></a>更新脚本参数

运行**Updatesqlprovider.ps1** PowerShell 脚本时，可以从命令行指定以下参数。 如果不是，或者任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 中心服务管理员帐户的凭据。 使用用于部署 Azure Stack 集线器的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **AzureEnvironment** | 用于部署 Azure Stack 中心的服务管理员帐户的 Azure 环境。 仅 Azure AD 部署中需要。 支持的环境名称为**AzureCloud**、 **AzureUSGovernment**或使用中国 Azure AD、 **AzureChinaCloud**。 | AzureCloud |
| **DependencyFilesLocalPath** | 还必须将证书 .pfx 文件放在此目录中。 | _对于单一节点是可选的，但对于多节点是必需的_ |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 如果出现故障，要重试每个操作的次数。| 2 |
| **RetryDuration** |两次重试之间的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |

## <a name="update-script-powershell-example"></a>更新脚本 PowerShell 示例
> [!NOTE]
> 此更新过程仅适用于 Azure Stack 集线器集成系统。

如果要将 SQL 资源提供程序版本更新为1.1.33.0 或早期版本，则需要在 PowerShell 中安装特定版本的 AzureRm 和 Azure Stack 集线器模块。 如果要更新到 SQL 资源提供程序版本1.1.47.0，部署脚本将自动下载并安装所需的 PowerShell 模块，使你能够通过路径 C:\Program Files\SqlMySqlPsh。

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> 在断开连接的情况下，需要下载所需的 PowerShell 模块，并手动注册存储库。 可以在[部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md)中获取详细信息

下面是使用*updatesqlprovider.ps1*脚本的示例，你可以从提升的 PowerShell 控制台中运行该脚本。 请确保根据需要更改变量信息和密码：  

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

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
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

资源提供程序更新脚本完成后，关闭当前 PowerShell 会话。

## <a name="next-steps"></a>后续步骤

[维护 SQL 资源提供程序](azure-stack-sql-resource-provider-maintain.md)
