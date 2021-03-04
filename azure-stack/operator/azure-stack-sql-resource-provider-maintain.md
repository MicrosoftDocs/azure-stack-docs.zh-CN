---
title: SQL 资源提供程序维护操作
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack Hub 上的 SQL 资源提供程序维护操作。
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 05ff121ccba8076276c8dd7106d56992500b57d8
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840280"
---
# <a name="sql-resource-provider-maintenance-operations"></a>SQL 资源提供程序维护操作

SQL 资源提供程序在锁定的虚拟机 (VM) 上运行。 若要启用维护操作，需要更新 VM 的安全性。 若要使用“最低特权”原则执行此操作，请使用 [PowerShell Just Enough Administration (JEA)](/powershell/scripting/learn/remoting/jea/overview) 终结点 *DBAdapterMaintenance*。 资源提供程序安装包包含此操作的脚本。

## <a name="patching-and-updating"></a>修补和更新

不能将 SQL 资源提供程序作为 Azure Stack Hub 的一部分进行维护，因为它是一个加载项组件。 Microsoft 会根据需要为 SQL 资源提供程序提供更新。 发布更新的 SQL 适配器后，会提供一个脚本来应用更新。 此脚本创建新的资源提供程序 VM，并将旧提供程序 VM 的状态迁移到新 VM。 有关详细信息，请参阅[更新 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。

### <a name="provider-vm"></a>提供程序 VM

由于资源提供程序在用户 VM 上运行，因此需要应用已发布的所需修补和升级。  使用修补升级周期提供的 Windows 更新包将更新应用到 VM。

## <a name="updating-sql-credentials"></a>更新 SQL 凭据

你需要负责在 SQL 服务器上创建和维护 sysadmin 帐户。 资源提供程序需要拥有这些特权的帐户才能代表用户管理数据库，但无需访问用户的数据。 如果需要更新 SQL 服务器上的 sysadmin 密码，可以使用资源提供程序的管理员界面来更改存储的密码。 这些密码将存储在 Azure Stack Hub 实例上的 Key Vault 中。

若要修改设置，请选择“浏览”&gt;“管理资源”&gt;“SQL 宿主服务器”&gt;“SQL 登录”并选择用户名。     必须先在 SQL 实例上（必要时还需要在任何副本上）进行更改。在“设置”下，选择“密码”。  

![更新 SQL 管理员密码](./media/azure-stack-sql-rp-deploy/sql-rp-update-password.png)

## <a name="secrets-rotation"></a>机密轮换

*这些说明仅适用于 Azure Stack Hub 集成系统。*

在 Azure Stack Hub 集成系统中使用 SQL 和 MySQL 资源提供程序时，Azure Stack Hub 操作员负责轮换以下资源提供程序基础结构机密以确保它们不会过期：

- [部署期间提供的](azure-stack-pki-certs.md)外部 SSL 证书。
- 部署期间提供的资源提供程序 VM 本地管理员帐户密码。
- 资源提供程序诊断用户 (dbadapterdiag) 密码。
- （版本 >= 1.1.47.0）在部署过程中生成的 Key Vault 证书。

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
    -VMLocalCredential $localCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
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

**更改 Key Vault 证书密码。**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider.ps1 参数

|参数|说明|注释|
|-----|-----|-----|
|AzureEnvironment|用于部署 Azure Stack Hub 的服务管理员帐户的 Azure 环境。 仅对于 Azure AD 部署是必需的。 支持的环境名称为 **AzureCloud**、 **AzureUSGovernment** 或使用中国 Azure Active Directory、 **AzureChinaCloud**。|可选|
|AzCredential|Azure Stack Hub 服务管理员帐户凭据。 如果用于 AzCredential 的帐户需要多重身份验证 (MFA)，则脚本将失败。|必需|
|CloudAdminCredential|Azure Stack Hub 云管理域帐户凭据。|必需|
|PrivilegedEndpoint|用于访问 Get-AzureStackStampInformation 的特权终结点。|必需|
|DiagnosticsUserPassword|诊断用户帐户密码。|可选|
|VMLocalCredential|MySQLAdapter VM 上的本地管理员帐户。|可选|
|DefaultSSLCertificatePassword|默认 SSL 证书 (*.pfx) 密码。|可选|
|DependencyFilesLocalPath|依赖项文件本地路径。|可选|
|KeyVaultPfxPassword|用于为数据库适配器生成 Key Vault 证书的密码。|可选|
|     |     |     |

### <a name="known-issues"></a>已知问题

**问题**：<br>
机密轮换日志。 如果机密轮换自定义脚本在运行时失败，则不会自动收集机密轮换的日志。

**解决方法**：<br>
使用 Get-AzsDBAdapterLogs cmdlet 收集所有资源提供程序日志，包括 C:\Logs 中保存的 AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log。

## <a name="update-the-vm-operating-system"></a>更新 VM 操作系统

使用以下方法之一更新 VM 操作系统。

- 使用当前进行了修补的 VM 映像安装最新的资源提供程序包。
- 在安装或更新资源提供程序期间安装 Windows 更新包。

## <a name="update-the-vm-windows-defender-definitions"></a>更新 VM Windows Defender 定义

更新 Windows Defender 定义：

1. 从 [Windows Defender 的安全智能更新](https://www.microsoft.com/wdsi/definitions)下载 Windows Defender 定义更新。

   在定义更新页上，向下滚动到“手动下载更新”。 下载“适用于 Windows 10 和 Windows 8.1 的 Windows Defender Antivirus”64 位文件。

   也可使用[此直接链接](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64)下载/运行 fpam-fe.exe 文件。

2. 与 SQL 资源提供程序适配器 VM 的维护终结点建立 PowerShell 会话。

3. 使用维护终结点会话将定义更新文件复制到 VM。

4. 在维护 PowerShell 会话中，运行 *Update-DBAdapterWindowsDefenderDefinitions* 命令。

5. 安装定义之后，我们建议使用 *Remove-ItemOnUserDrive* 命令删除定义更新文件。

**用于更新定义的 PowerShell 脚本示例**

可以编辑并运行以下脚本来更新 Defender 定义。 将脚本中的值替换为环境中的值。

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString '<local admin user password>' -AsPlainText -Force
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

::: moniker range=">= azs-2008"

Azure Stack 中心提供多种方法来收集、保存诊断日志并将其发送到 Microsoft 支持部门。 从版本 1.1.93 开始，SQL 资源提供程序支持从 Azure Stack Hub 环境收集日志的标准方式。 有关详细信息，请参阅[诊断日志收集](diagnostic-log-collection.md)。

::: moniker-end

::: moniker range="< azs-2008"

从版本 1.1.93 开始，SQL 资源提供程序支持从 Azure Stack Hub 环境收集日志的标准方式。 如果使用的是较旧版本，则建议将 SQL 资源提供程序更新到最新版本。

若要从锁定的 VM 收集日志，请使用 PowerShell Just Enough Administration (JEA) 终结点 *DBAdapterDiagnostics*。 此终结点提供以下命令：

- **Get-AzsDBAdapterLog**。 此命令创建资源提供程序诊断日志的 zip 包，并将文件保存在会话的用户驱动器上。 可以不带任何参数运行此命令，收集过去四小时的日志。
- **Remove-AzsDBAdapterLog**。 此命令删除资源提供程序 VM 上的现有日志包。

### <a name="endpoint-requirements-and-process"></a>终结点要求和过程

安装或更新资源提供程序时，将创建 **dbadapterdiag** 用户帐户。 此帐户用于收集诊断日志。

>[!NOTE]
>dbadapterdiag 帐户密码与部署或更新提供程序期间在 VM 上创建的本地管理员所用的密码相同。

若要使用 *DBAdapterDiagnostics* 命令，请与资源提供程序 VM 建立远程 PowerShell 会话，然后运行 **Get-AzsDBAdapterLog** 命令。

使用 **FromDate** 和 **ToDate** 参数设置日志收集的时间跨度。 如果未指定上述一个或两个参数，将使用以下默认值：

- FromDate 为当前时间之前的四个小时。
- ToDate 为目前时间。

**用于收集日志的 PowerShell 脚本示例**

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

::: moniker-end

## <a name="configure-azure-diagnostics-extension-for-sql-resource-provider"></a>为 SQL 资源提供程序配置 Azure 诊断扩展
默认情况下，在 SQL 资源提供程序适配器 VM 上安装 Azure 诊断扩展。 以下步骤介绍如何为收集 SQL 资源提供程序操作事件日志和 IIS 日志自定义扩展，以便用于故障排除和审核。

1. 登录到 Azure Stack Hub 管理员门户。

2. 从左侧窗格中选择“虚拟机”，搜索 SQL 资源提供程序适配器 VM，然后选择该 VM。

3. 在 VM 的“诊断设置”中，转到“日志”选项卡，然后选择“自定义”，以自定义要收集的事件日志。
![转到诊断设置](media/azure-stack-sql-resource-provider-maintain/sqlrp-diagnostics-settings.png)

4. 添加 **Microsoft-AzureStack-DatabaseAdapter/Operational!\*** 用于收集 SQL 资源提供程序操作事件日志。
![添加事件日志](media/azure-stack-sql-resource-provider-maintain/sqlrp-event-logs.png)

5. 若要启用 IIS 日志收集，请选中“IIS 日志”和“失败请求日志”。
![添加 IIS 日志](media/azure-stack-sql-resource-provider-maintain/sqlrp-iis-logs.png)

6. 最后，选择“保存”以保存所有诊断设置。

为 SQL 资源提供程序配置事件日志和 IIS 日志收集后，即可在名为 **sqladapterdiagaccount** 的系统存储帐户中找到日志。

若要详细了解 Azure 诊断扩展，请参阅[什么是 Azure 诊断扩展](/azure/azure-monitor/platform/diagnostics-extension-overview)。

## <a name="next-steps"></a>后续步骤

[添加 SQL Server 宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)
