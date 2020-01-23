---
title: 在 Azure Stack Hub 上部署 MySQL 资源提供程序 |Microsoft Docs
description: 了解如何在 Azure Stack 中心部署 MySQL 资源提供程序适配器和 MySQL 数据库即服务。
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: aecc96bc9e96c39ad1df1111b57bf17ca0d9b59a
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534951"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack-hub"></a>在 Azure Stack 集线器上部署 MySQL 资源提供程序

使用 MySQL 服务器资源提供程序将 MySQL 数据库作为 Azure Stack 中心服务公开。 MySQL 资源提供程序在 Windows Server 2016 Server Core 虚拟机（VM）上以服务的形式运行。

> [!IMPORTANT]
> 仅支持资源提供程序在托管 SQL 或 MySQL 的服务器上创建项。 在不是由资源提供程序创建的主机服务器上创建的项可能会导致不匹配的状态。

## <a name="prerequisites"></a>必备组件

部署 Azure Stack 中心 MySQL 资源提供程序之前，需要准备好几个先决条件。 若要满足这些要求，请在可以访问特权终结点 VM 的计算机上完成本文中所述的步骤。

* 如果尚未注册，请在 Azure 中[注册 Azure Stack 集线器](./azure-stack-registration.md)，以便下载 azure Marketplace 项。

* 通过下载**Windows server 2016 Datacenter-Server core**映像，将所需的 windows SERVER core VM 添加到 Azure Stack 集线器 Marketplace。

* 下载 MySQL 资源提供程序二进制文件，然后运行自解压缩程序，将内容提取到临时目录中。

  >[!NOTE]
  >若要在无法访问 internet 的系统上部署 MySQL 提供程序，请将[mysql-connector-net-mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi)文件复制到本地路径。 使用**DependencyFilesLocalPath**参数提供路径名。

* 资源提供程序至少有一个对应的 Azure Stack 中心生成。

  |最低 Azure Stack 中心版本|MySQL RP 版本|
  |-----|-----|
  |版本1910（1.1910.0.58）|[MySQL RP 版本1.1.47。0](https://aka.ms/azurestackmysqlrp11470)|
  |版本1808（1.1808.0.97）|[MySQL RP 版本1.1.33。0](https://aka.ms/azurestackmysqlrp11330)|  
  |版本1808（1.1808.0.97）|[MySQL RP 版本1.1.30。0](https://aka.ms/azurestackmysqlrp11300)|
  |版本1804（1.0.180513.1）|[MySQL RP 版本1.1.24。0](https://aka.ms/azurestackmysqlrp11240)
  |     |     |
  
> [!IMPORTANT]
> 部署 MySQL 资源提供程序版本1.1.47.0 之前，应将 Azure Stack 中心系统升级到1910更新或更高版本。 以前不受支持的 Azure Stack 集线器版本上的 MySQL 资源提供程序版本1.1.47.0 不起作用。

* 确保满足数据中心集成先决条件：

    |先决条件|参考|
    |-----|-----|
    |已正确设置条件 DNS 转发。|[Azure Stack 中心数据中心集成-DNS](azure-stack-integrate-dns.md)|
    |资源提供程序的入站端口已打开。|[Azure Stack 中心数据中心集成-发布终结点](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI 证书使用者和 SAN 设置正确。|[Azure Stack 中心部署必需的 PKI 必备组件](azure-stack-pki-certs.md#mandatory-certificates)[Azure Stack 中心部署 PaaS 证书必备组件](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

在断开连接的情况下，请完成以下步骤以下载所需的 PowerShell 模块并手动注册存储库。

1. 登录到具有 internet 连接的计算机，然后使用以下脚本下载 PowerShell 模块。

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
```

2. 然后，将下载的包复制到 USB 设备。

3. 登录到断开连接的工作站，并将设备上的包复制到工作站上的某个位置。

4. 将此位置注册为本地存储库。

```powershell
# requires -Version 5
# requires -RunAsAdministrator
# requires -Module PowerShellGet
# requires -Module PackageManagement

$SourceLocation = "C:\temp\azs1.6.0"
$RepoName = "azs1.6.0"

Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

New-Item -Path $env:ProgramFiles -name "SqlMySqlPsh" -ItemType "Directory" 
```

### <a name="certificates"></a>证书

_仅适用于集成系统安装_。 必须提供[Azure Stack 中心部署 pki 要求](./azure-stack-pki-certs.md#optional-paas-certificates)的可选 PaaS 证书部分所述的 SQL PaaS pki 证书。 将 .pfx 文件放在**DependencyFilesLocalPath**参数指定的位置。 请勿为 ASDK systems 提供证书。

## <a name="deploy-the-resource-provider"></a>部署资源提供程序

安装所有先决条件后，可以运行**DeployMySqlProvider**脚本来部署 MySQL 资源提供程序。 DeployMySqlProvider 脚本将作为 MySQL 资源提供程序安装文件的一部分提取，这些文件是你为 Azure Stack 集线器版本下载的。

 > [!IMPORTANT]
 > 在部署资源提供程序之前，请查看发行说明，了解新功能、修复程序以及可能影响部署的任何已知问题。

若要部署 MySQL 资源提供程序，请打开一个新的权限提升的 PowerShell 窗口（而不是 PowerShell ISE），并更改为你在其中提取 MySQL 资源提供程序二进制文件的目录。 建议使用新的 PowerShell 窗口，以避免已加载的 PowerShell 模块导致的潜在问题。

运行**DeployMySqlProvider**脚本，该脚本完成以下任务：

* 将证书和其他项目上载到 Azure Stack 集线器上的存储帐户。
* 发布库包，以便可以使用库部署 MySQL 数据库。
* 发布库包以部署宿主服务器。
* 使用下载的 Windows Server 2016 core 映像部署 VM，然后安装 MySQL 资源提供程序。
* 注册映射到资源提供程序 VM 的本地 DNS 记录。
* 将资源提供程序注册到操作员帐户的本地 Azure 资源管理器。

> [!NOTE]
> MySQL 资源提供程序部署启动时，将创建**mysqladapter.dbadapter**资源组。 完成此资源组所需的部署最多可能需要75分钟。

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider 参数

你可以从命令行指定这些参数。 如果不是，或者任何参数验证失败，系统会提示提供所需的参数。

| 参数名称 | Description | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 中心服务管理员帐户的凭据。 使用用于部署 Azure Stack 集线器的相同凭据。 | _必需_ |
| **VMLocalCredential** | MySQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **AzureEnvironment** | 用于部署 Azure Stack 中心的服务管理员帐户的 Azure 环境。 仅 Azure AD 部署中需要。 支持的环境名称为**AzureCloud**、 **AzureUSGovernment**或使用中国 Azure AD、 **AzureChinaCloud**。 | AzureCloud |
| **DependencyFilesLocalPath** | 对于集成系统，必须将证书 .pfx 文件放在此目录中。 对于断开连接的环境，请将[mysql-connector-net-mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi)下载到此目录。 可以选择在此处复制一个 Windows 更新 MSU 包。 | _可选_（对于集成系统或断开连接的环境是_必需_的） |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 如果出现故障，要重试每个操作的次数。| 2 |
| **RetryDuration** | 两次重试之间的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源（请参阅以下注释）。 | 否 |
| **DebugMode** | 防止在失败时自动清除。 | 否 |
| **AcceptLicense** | 跳过提示以接受 GPL 许可证。  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>使用自定义脚本部署 MySQL 资源提供程序

如果要部署 MySQL 资源提供程序版本1.1.33.0 或早期版本，则需要在 PowerShell 中安装特定版本的 AzureRm 和 Azure Stack 集线器模块。 如果要部署 MySQL 资源提供程序版本1.1.47.0，部署脚本将自动下载并安装所需的 PowerShell 模块，以便你可以通过路径 C:\Program Files\SqlMySqlPsh。

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> 在断开连接的情况下，需要下载所需的 PowerShell 模块，并手动注册存储库。

若要在部署资源提供程序时消除任何手动配置，可以自定义以下脚本。 根据 Azure Stack 中心部署的需要，更改默认的帐户信息和密码。

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

# Set the credentials for the new resource provider VM local admin account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh,
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

资源提供程序安装脚本完成后，请刷新浏览器，确保可以看到最新更新并关闭当前 PowerShell 会话。

## <a name="verify-the-deployment-by-using-the-azure-stack-hub-portal"></a>使用 Azure Stack 中心门户验证部署

1. 以服务管理员身份登录到管理员门户。
2. 选择“资源组”。
3. 选择 " **\<位置"\>"mysqladapter.dbadapter** " 资源组。
4. 在资源组概述的 "摘要" 页上，应该没有失败的部署。
5. 最后，在管理员门户中选择 "**虚拟机**"，验证 MySQL 资源提供程序 VM 是否已成功创建且正在运行。

## <a name="next-steps"></a>后续步骤

[添加宿主服务器](azure-stack-mysql-resource-provider-hosting-servers.md)
