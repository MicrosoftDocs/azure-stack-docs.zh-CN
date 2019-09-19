---
title: 在 Azure Stack 上维护 SQL 资源提供程序 | Microsoft Docs
description: 了解如何在 Azure Stack 上维护 SQL 资源提供程序服务。
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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1bbfa7b07c0c671cbfaded1abef85b2a66ef93ff
ms.sourcegitcommit: 9f4c6e96f60b4c229316e7a4ab6e0e5ef0a9a232
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71061207"
---
# <a name="sql-resource-provider-maintenance-operations"></a>SQL 资源提供程序维护操作

SQL 资源提供程序在锁定的虚拟机上运行。 若要启用维护操作，需要更新虚拟机的安全性。 若要使用“最低特权”原则执行此操作，可以使用 [PowerShell Just Enough Administration (JEA)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) 终结点 *DBAdapterMaintenance*。 资源提供程序安装包包含此操作的脚本。

## <a name="patching-and-updating"></a>修补和更新

不能将 SQL 资源提供程序作为 Azure Stack 的一部分进行维护，因为它是一个加载项组件。 Microsoft 会根据需要为 SQL 资源提供程序提供更新。 发布更新的 SQL 适配器后，会提供一个脚本来应用更新。 此脚本创建新的资源提供程序 VM，并将旧提供程序 VM 的状态迁移到新 VM。 有关详细信息，请参阅[更新 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。

### <a name="provider-virtual-machine"></a>提供程序虚拟机

由于资源提供程序在用户虚拟机上运行，因此需要应用已发布的修补升级。 可以使用修补升级周期提供的 Windows 更新包将更新应用到 VM。

## <a name="updating-sql-credentials"></a>更新 SQL 凭据

你需要负责在 SQL 服务器上创建和维护 sysadmin 帐户。 资源提供程序需要拥有这些特权的帐户才能代表用户管理数据库，但无需访问用户的数据。 如果需要更新 SQL 服务器上的 sysadmin 密码，可以使用资源提供程序的管理员界面来更改存储的密码。 这些密码存储在 Azure Stack 实例上的 Key Vault 中。

若要修改设置，请选择“浏览”&gt;“管理资源”&gt;“SQL 宿主服务器”&gt;“SQL 登录”并选择用户名。 必须先在 SQL 实例上（必要时还需要在任何副本上）进行更改。在“设置”下，选择“密码”。

![更新管理密码](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>机密轮换

*这些说明仅适用于 Azure Stack 集成系统。*

在 Azure Stack 集成系统中使用 SQL 和 MySQL 资源提供程序时，Azure Stack 操作员负责轮换以下资源提供程序基础结构机密以确保它们不会过期：

- [部署期间提供的](azure-stack-pki-certs.md)外部 SSL 证书。
- 部署期间提供的资源提供程序 VM 本地管理员帐户密码。
- 资源提供程序诊断用户 (dbadapterdiag) 密码。

### <a name="powershell-examples-for-rotating-secrets"></a>用于轮换机密的 PowerShell 示例

**同时更改所有机密。**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**更改诊断用户密码。**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd
```

**更改 VM 本地管理员帐户密码。**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**更改 SSL 证书密码。**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider.ps1 参数

|参数|描述|
|-----|-----|
|AzCredential|Azure Stack 服务管理员帐户凭据。|
|CloudAdminCredential|Azure Stack 云管理域帐户凭据。|
|PrivilegedEndpoint|用于访问 Get-AzureStackStampInformation 的特权终结点。|
|DiagnosticsUserPassword|诊断用户帐户密码。|
|VMLocalCredential|MySQLAdapter VM 上的本地管理员帐户。|
|DefaultSSLCertificatePassword|默认 SSL 证书 (*pfx) 密码。|
|DependencyFilesLocalPath|依赖项文件本地路径。|
|     |     |

### <a name="known-issues"></a>已知问题

**问题**：机密轮换日志。<br>
如果机密轮换自定义脚本在运行时失败，则不会自动收集机密轮换的日志。

**解决方法**：<br>
使用 Get-AzsDBAdapterLogs cmdlet 收集所有资源提供程序日志，包括 C:\Logs 中保存的 AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log。

## <a name="update-the-virtual-machine-operating-system"></a>更新虚拟机操作系统

使用以下方法之一更新虚拟机操作系统。

- 使用当前进行了修补的 Windows Server 2016 Core 映像安装最新的资源提供程序包。
- 在安装或更新资源提供程序期间安装 Windows 更新包。

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>更新虚拟机 Windows Defender 定义

更新 Windows Defender 定义：

1. 从 [Windows Defender 定义](https://www.microsoft.com/en-us/wdsi/definitions)下载 Windows Defender 定义更新

   在定义更新页上，向下滚动到“手动下载并安装定义”。 下载“适用于 Windows 10 和 Windows 8.1 的 Windows Defender Antivirus”64 位文件。

   或者，使用[此直接链接](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64)下载/运行 fpam-fe.exe 文件。

2. 与 SQL 资源提供程序适配器虚拟机的维护终结点建立 PowerShell 会话。

3. 使用维护终结点会话将定义更新文件复制到虚拟机。

4. 在维护 PowerShell 会话中，运行 *Update-DBAdapterWindowsDefenderDefinitions* 命令。

5. 安装定义之后，我们建议使用 *Remove-ItemOnUserDrive* 命令删除定义更新文件。

**用于更新定义的 PowerShell 脚本示例。**

可以编辑并运行以下脚本来更新 Defender 定义。 将脚本中的值替换为环境中的值。

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="collect-diagnostic-logs"></a>收集诊断日志

若要从锁定的虚拟机收集日志，可以使用 PowerShell Just Enough Administration (JEA) 终结点 *DBAdapterDiagnostics*。 此终结点提供以下命令：

- **Get-AzsDBAdapterLog**。 此命令创建资源提供程序诊断日志的 zip 包，并将文件保存在会话的用户驱动器上。 可以不带任何参数运行此命令，收集过去四小时的日志。
- **Remove-AzsDBAdapterLog**。 此命令删除资源提供程序 VM 上的现有日志包。

### <a name="endpoint-requirements-and-process"></a>终结点要求和过程

安装或更新资源提供程序时，将创建 **dbadapterdiag** 用户帐户。 此帐户用于收集诊断日志。

>[!NOTE]
>dbadapterdiag 帐户密码与部署或更新提供程序期间在虚拟机上创建的本地管理员所用的密码相同。

若要使用 *DBAdapterDiagnostics* 命令，请与资源提供程序虚拟机建立远程 PowerShell 会话，然后运行 **Get-AzsDBAdapterLog** 命令。

使用 **FromDate** 和 **ToDate** 参数设置日志收集的时间跨度。 如果未指定上述一个或两个参数，将使用以下默认值：

- FromDate 为当前时间之前的四个小时。
- ToDate 为目前时间。

**用于收集日志的 PowerShell 脚本示例。**

以下脚本演示如何从资源提供程序 VM 收集诊断日志。

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Clean up the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>后续步骤

[添加 SQL Server 宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)
