---
title: 正在更新 Azure Stack SQL 资源提供程序 |Microsoft Docs
description: 了解如何更新 SQL 资源提供程序 Azure Stack。
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
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: b37e4c9f5e7b1aaa1a476b0665a9558e8e86365f
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955422"
---
# <a name="update-the-sql-resource-provider"></a>更新 SQL 资源提供程序

*适用于： Azure Stack 集成系统。*

将 Azure Stack 更新为新版本时，可能会发布新的 SQL 资源提供程序。 尽管现有的资源提供程序会继续工作，但建议尽快更新到最新版本。 

从 SQL 资源提供程序版本1.1.33.0 版本开始，更新是累积性的，无需按照它们的发布顺序进行安装;只要你从版本1.1.24.0 或更高版本开始。 例如，如果你运行的是 SQL 资源提供程序的版本1.1.24.0，则可以升级到1.1.33.0 或更高版本，而无需首先安装版本1.1.30.0。 若要查看可用的资源提供程序版本以及支持它们的 Azure Stack 版本，请参阅[部署资源提供程序必备组件](./azure-stack-sql-resource-provider-deploy.md#prerequisites)中的版本列表。

若要更新资源提供程序，请使用*updatesqlprovider.ps1*脚本。 此脚本包含在下载新的 SQL 资源提供程序中。 更新过程类似于用于[部署资源提供程序](./azure-stack-sql-resource-provider-deploy.md)的过程。 更新脚本使用与 Deploysqlprovider.ps1 脚本相同的参数，你将需要提供证书信息。

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

| 参数名称 | 描述 | 注释或默认值 |
| --- | --- | --- |
| **CloudAdminCredential** | 访问特权终结点所需的云管理员凭据。 | _必需_ |
| **AzCredential** | Azure Stack 服务管理员帐户的凭据。 使用用于部署 Azure Stack 的相同凭据。 | _必需_ |
| **VMLocalCredential** | SQL 资源提供程序 VM 的本地管理员帐户的凭据。 | _必需_ |
| **PrivilegedEndpoint** | 特权终结点的 IP 地址或 DNS 名称。 |  _必需_ |
| **AzureEnvironment** | 用于部署 Azure Stack 的服务管理员帐户的 Azure 环境。 仅 Azure AD 部署中需要。 支持的环境名称为**AzureCloud**、 **AzureUSGovernment**或使用中国 Azure AD、 **AzureChinaCloud**。 | AzureCloud |
| **DependencyFilesLocalPath** | 还必须将证书 .pfx 文件放在此目录中。 | _对于单一节点是可选的，但对于多节点是必需的_ |
| **DefaultSSLCertificatePassword** | .Pfx 证书的密码。 | _必需_ |
| **MaxRetryCount** | 如果出现故障，要重试每个操作的次数。| 2 |
| **RetryDuration** |两次重试之间的超时间隔（秒）。 | 120 |
| **卸载** | 删除资源提供程序和所有关联的资源。 | No |
| **DebugMode** | 防止在失败时自动清除。 | No |

## <a name="update-script-powershell-example"></a>更新脚本 PowerShell 示例
下面是使用*updatesqlprovider.ps1*脚本的示例，你可以从提升的 PowerShell 控制台中运行该脚本。 请确保根据需要更改变量信息和密码：  

> [!NOTE]
> 此更新过程仅适用于 Azure Stack 集成系统。

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
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

## <a name="next-steps"></a>后续步骤

[维护 SQL 资源提供程序](azure-stack-sql-resource-provider-maintain.md)
