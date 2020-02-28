---
title: 更新 Azure Stack 集线器中的 MySQL 资源提供程序
description: 了解如何在 Azure Stack 中心更新 Azure Stack 集线器 MySQL 资源提供程序。
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: a617efab5516fb2f6bad96e36c99f1b84852237c
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698634"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>更新 Azure Stack 集线器中的 MySQL 资源提供程序

更新 Azure Stack 集线器版本时，可能会发布新的 MySQL 资源提供程序适配器。 当现有适配器继续工作时，我们建议尽快更新到最新版本。

从 MySQL 资源提供程序版本1.1.33.0 开始，更新是累积的，无论是从版本1.1.24.0 或更高版本开始，都无需按照它们发布的顺序进行安装。 例如，如果运行的是 MySQL 资源提供程序的版本1.1.24.0，则可以升级到1.1.33.0 或更高版本，而无需先安装版本1.1.30.0。 若要查看可用的资源提供程序版本以及支持它们的 Azure Stack 中心版本，请参阅[部署资源提供程序必备组件](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)中的版本列表。

若要更新资源提供程序，请使用**updatemysqlprovider.ps1**脚本。 此过程类似于用于安装资源提供程序的过程，如本文的部署资源提供程序部分中所述。 下载资源提供程序时，该脚本包括在内。 

 > [!IMPORTANT]
 > 升级资源提供程序之前，请查看发行说明，了解新功能、修复程序以及可能影响部署的任何已知问题。

## <a name="update-script-processes"></a>更新脚本进程

**Updatemysqlprovider.ps1**脚本使用最新的资源提供程序代码创建新的虚拟机（VM），并将这些设置从旧 vm 迁移到新 vm。 迁移的设置包括数据库和宿主服务器信息以及所需的 DNS 记录。

>[!NOTE]
>建议从 Marketplace 管理下载最新的 Windows Server 2016 Core 映像。 如果需要安装更新，可以将**单个**MSU 包置于本地依赖项路径中。 如果此位置有多个 MSU 文件，此脚本将失败。

脚本需要使用为 DeployMySqlProvider 脚本描述的相同参数。 在此处也提供证书。  


## <a name="update-script-parameters"></a>更新脚本参数 
运行**Updatemysqlprovider.ps1** PowerShell 脚本时，请从命令行指定以下参数。 如果不是，或者任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | 说明 | 注释或默认值 | 
| --- | --- | --- | 
| **CloudAdminCredential** | 访问特权终结点所需的云管理员凭据。 | _必需_ | 
| **AzCredential** | Azure Stack 中心服务管理员帐户的凭据。 使用与用于部署 Azure Stack 中心相同的凭据。 | _必需_ | 
| **VMLocalCredential** |SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ | 
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ | 
| **AzureEnvironment** | 用于部署 Azure Stack 中心的服务管理员帐户的 Azure 环境。 仅 Azure AD 部署中需要。 支持的环境名称为**AzureCloud**、 **AzureUSGovernment**或使用中国 Azure AD、 **AzureChinaCloud**。 | AzureCloud |
| **DependencyFilesLocalPath** | 还必须将证书 .pfx 文件放在此目录中。 | _可选_（多节点_必需_的） | 
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | _必需_ | 
| **MaxRetryCount** | 如果出现故障，要重试每个操作的次数。| 2 | 
| **RetryDuration** | 两次重试之间的超时间隔（秒）。 | 120 | 
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅以下注释）。 | 否 | 
| **DebugMode** | 防止在失败时自动清除。 | 否 | 
| **AcceptLicense** | 跳过提示以接受 GPL 许可证。  （ https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>更新脚本示例

> [!NOTE] 
> 更新过程仅适用于集成系统。

如果要将 MySQL 资源提供程序版本更新为1.1.33.0 或早期版本，则需要在 PowerShell 中安装特定版本的 AzureRm 和 Azure Stack 集线器模块。 如果要将 MySQL 资源提供程序更新到版本1.1.47.0，部署脚本将自动下载并安装所需的 PowerShell 模块，使你能够通过路径 C:\Program Files\SqlMySqlPsh。

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> 在断开连接的情况下，需要下载所需的 PowerShell 模块，并手动注册存储库。 可以在[部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)中获取详细信息

下面的示例演示了可从已提升权限的 PowerShell 控制台运行的*updatemysqlprovider.ps1*脚本。 请确保根据需要更改变量信息和密码：

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

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
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

资源提供程序更新脚本完成后，关闭当前 PowerShell 会话。

## <a name="next-steps"></a>后续步骤
[维护 MySQL 资源提供程序](azure-stack-mysql-resource-provider-maintain.md)
