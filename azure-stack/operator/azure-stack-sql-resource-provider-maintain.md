---
title: SQL 资源提供程序维护操作
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack 集线器上的 SQL 资源提供程序维护操作。
author: mattbriggs
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: ed76e3611fe0b7b57386a7b688f08ddbdc3c36d7
ms.sourcegitcommit: b7b86e875cf04cb0fd9d48a2b830588d3ff99b6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77125799"
---
# <a name="sql-resource-provider-maintenance-operations"></a>SQL 资源提供程序维护操作

SQL 资源提供程序在锁定的虚拟机（VM）上运行。 若要启用维护操作，需要更新 VM 的安全性。 若要使用最低权限的主体来实现此目的，请使用[PowerShell （JEA）](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview)终结点*DBAdapterMaintenance*。 资源提供程序安装包包含此操作的脚本。

## <a name="patching-and-updating"></a>修补和更新

SQL 资源提供程序不是作为 Azure Stack 中心的一部分来处理的，因为它是一个外接程序组件。 Microsoft 根据需要提供 SQL 资源提供程序的更新。 发布更新的 SQL 适配器后，将提供一个脚本来应用更新。 此脚本创建新的资源提供程序 VM，并将旧提供程序 VM 的状态迁移到新 VM。 有关详细信息，请参阅[更新 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。

### <a name="provider-vm"></a>提供程序 VM

由于资源提供程序在*用户*VM 上运行，因此需要在发布时应用所需的修补程序和更新。 使用修补和更新周期中提供的 Windows 更新包将更新应用到 VM。

## <a name="updating-sql-credentials"></a>正在更新 SQL 凭据

你负责在 SQL 服务器上创建和维护 sysadmin 帐户。 资源提供程序需要一个具有这些权限的帐户来管理用户的数据库，但不需要访问用户数据。 如果需要更新 SQL server 上的 sysadmin 密码，可以使用资源提供程序的管理员界面来更改已存储的密码。 这些密码存储在 Azure Stack 中心实例的 Key Vault 中。

若要修改设置，请选择 "**浏览**&gt;**管理资源**" &gt; **Sql 宿主服务器**"&gt; **sql 登录**名并选择用户名。 必须首先在 SQL 实例（和任何副本）上进行更改（如有必要）。在 "**设置**" 下，选择 "**密码**"。

![更新 SQL 管理员密码](./media/azure-stack-sql-rp-deploy/sql-rp-update-password.png)

## <a name="secrets-rotation"></a>秘密旋转

*这些说明仅适用于 Azure Stack 集线器集成系统。*

将 SQL 和 MySQL 资源提供程序与 Azure Stack 集线器集成系统一起使用时，Azure Stack 中心操作员负责旋转以下资源提供程序基础结构机密，以确保它们不会过期：

- [在部署过程中提供](azure-stack-pki-certs.md)的外部 SSL 证书。
- 部署期间提供的资源提供程序 VM 本地管理员帐户密码。
- 资源提供程序诊断用户（dbadapterdiag）密码。

### <a name="powershell-examples-for-rotating-secrets"></a>用于轮换机密的 PowerShell 示例

**同时更改所有密码。**

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

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider 参数

|参数|说明|
|-----|-----|
|AzCredential|Azure Stack 中心服务管理员帐户凭据。|
|CloudAdminCredential|Azure Stack 中心云管理域帐户凭据。|
|PrivilegedEndpoint|用于访问 AzureStackStampInformation 的特权终结点。|
|DiagnosticsUserPassword|诊断用户帐户密码。|
|VMLocalCredential|Mysqladapter.dbadapter VM 上的本地管理员帐户。|
|DefaultSSLCertificatePassword|默认 SSL 证书（* pfx）密码。|
|DependencyFilesLocalPath|依赖项文件本地路径。|
|     |     |

### <a name="known-issues"></a>已知问题

**问题**：<br>
机密轮换日志。 如果机密旋转自定义脚本在运行时失败，则不会自动收集机密轮换日志。

**解决方法**：<br>
使用 AzsDBAdapterLogs cmdlet 收集所有资源提供程序日志，包括 ps1_ Start-secretrotation * .log，并保存在 C:\Logs. 中。

## <a name="update-the-vm-operating-system"></a>更新 VM 操作系统

使用以下方法之一来更新 VM 操作系统。

- 使用当前修补的 Windows Server 2016 Core 映像安装最新的资源提供程序程序包。
- 在安装或更新资源提供程序的过程中安装 Windows 更新包。

## <a name="update-the-vm-windows-defender-definitions"></a>更新 VM Windows Defender 定义

更新 Windows Defender 定义：

1. 从[适用于 Windows defender 的安全智能更新](https://www.microsoft.com/wdsi/definitions)下载 windows defender 定义更新。

   在 "定义更新" 页上，向下滚动到 "手动下载更新"。 下载 "适用于 Windows 10 的 Windows Defender 防病毒并 Windows 8.1" 64 位文件。

   你还可以使用[此直接链接](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64)来下载/运行 fpam-fe 文件。

2. 创建与 SQL 资源提供程序适配器 VM 的维护终结点的 PowerShell 会话。

3. 使用维护终结点会话将定义更新文件复制到 VM。

4. 在维护 PowerShell 会话上，运行*DBAdapterWindowsDefenderDefinitions*命令。

5. 安装定义后，建议使用*ItemOnUserDrive*命令删除定义更新文件。

**用于更新定义的 PowerShell 脚本示例**

你可以编辑并运行以下脚本来更新 Defender 定义。 将脚本中的值替换为你的环境中的值。

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter VM.
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

若要从锁定的 VM 收集日志，请使用 PowerShell 刚好足够的管理（JEA）终结点*DBAdapterDiagnostics*。 此终结点提供以下命令：

- **AzsDBAdapterLog**。 此命令创建资源提供程序诊断日志的 zip 包，并将该文件保存在会话的用户驱动器上。 可以在不带任何参数的情况下运行此命令，并收集过去四小时的日志。
- **AzsDBAdapterLog**。 此命令删除资源提供程序 VM 上的现有日志包。

### <a name="endpoint-requirements-and-process"></a>终结点要求和过程

安装或更新资源提供程序时，会创建**dbadapterdiag**用户帐户。 你将使用此帐户来收集诊断日志。

>[!NOTE]
>Dbadapterdiag 帐户密码与提供程序部署或更新过程中创建的 VM 上的本地管理员密码相同。

若要使用*DBAdapterDiagnostics*命令，请创建与资源提供程序 VM 的远程 PowerShell 会话，并运行**AzsDBAdapterLog**命令。

使用**FromDate**和**ToDate**参数设置日志收集的时间跨度。 如果未指定这两个参数中的一个或两个，将使用下列默认值：

- FromDate 是当前时间之前的四个小时。
- ToDate 是当前时间。

**用于收集日志的 PowerShell 脚本示例**

以下脚本显示了如何从资源提供程序 VM 收集诊断日志。

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
## <a name="configure-azure-diagnostics-extension-for-sql-resource-provider"></a>为 SQL 资源提供程序配置 Azure 诊断扩展
默认情况下，在 SQL 资源提供程序适配器 VM 上安装 Azure 诊断扩展。 以下步骤说明了如何自定义扩展以收集 SQL 资源提供程序的操作事件日志和 IIS 日志，以便进行故障排除和审核目的。

1. 登录到 Azure Stack 中心管理员门户。

2. 从左侧窗格中选择 "**虚拟机**"，搜索 SQL 资源提供程序适配器 vm，并选择 vm。

3. 在 VM 的 "**诊断设置**" 中，请单击 "**日志**" 选项卡，然后选择 "**自定义**" 自定义要收集的事件日志。
![中转到 "诊断设置"](media/azure-stack-sql-resource-provider-maintain/sqlrp-diagnostics-settings.png)

4. 添加**test-azurestack-DatabaseAdapter/operation！\*** 以收集 SQL 资源提供程序的操作事件日志。
![添加事件日志](media/azure-stack-sql-resource-provider-maintain/sqlrp-event-logs.png)

5. 若要启用 IIS 日志的集合，请检查 " **iis 日志**" 和 "**失败的请求日志**"。
![添加 IIS 日志](media/azure-stack-sql-resource-provider-maintain/sqlrp-iis-logs.png)

6. 最后，选择 "**保存**" 以保存所有诊断设置。

为 SQL 资源提供程序配置事件日志和 IIS 日志收集后，可在名为**sqladapterdiagaccount**的系统存储帐户中找到日志。

若要详细了解 Azure 诊断扩展，请参阅[什么是 Azure 诊断扩展](/azure/azure-monitor/platform/diagnostics-extension-overview)。

## <a name="next-steps"></a>后续步骤

[添加 SQL Server 宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)
