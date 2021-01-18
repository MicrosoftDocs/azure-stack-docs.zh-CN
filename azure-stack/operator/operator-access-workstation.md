---
title: Azure Stack Hub 操作员访问工作站
description: 了解如何下载和配置 Azure Stack Hub 操作员访问工作站。
author: ashika789
ms.topic: article
ms.date: 1/15/2021
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 1/15/2021
ms.openlocfilehash: 011a75dcdbe8818af568eb5cc6bb6123a4f11c4e
ms.sourcegitcommit: 48a46142ea7bccd6c8a609e188dd7f3f6444f3c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561955"
---
# <a name="azure-stack-hub-operator-access-workstation"></a>Azure Stack Hub 操作员访问工作站 

操作员访问工作站 (OAW) 用于在运行版本2005 或更高版本的硬件生命周期主机 (HLH) 上部署 jumpbox 虚拟机 (VM)，以便 Azure Stack Hub 操作员可以访问特权终结点 (PEP) 和管理员门户以了解支持方案。 

当操作员执行新任务时，应创建 OAW VM。 VM 内的必需任务完成之后，应关闭并删除 VM，因为 Azure Stack Hub 不需要始终运行它。  

## <a name="oaw-scenarios"></a>OAW 方案

下表列出了 OAW 的常见方案，但这不是独有的。 建议使用远程桌面连接到 OAW。 

|方案                                                                                                                          |说明                 |
|----------------------------------------------------------------------------------------------------------------------------------|-----------------------------|
|[访问管理门户](./azure-stack-manage-portals.md)                     |执行管理操作                                                                           |
|[访问 PEP](./azure-stack-privileged-endpoint.md)                                     |日志收集和上传：<br>在 HLH 上-[创建 SMB 共享](#transfer-files-between-the-hlh-and-oaw)以便从 Azure Stack Hub 进行文件传输<br>-使用 Azure 存储资源管理器上传保存到 SMB 共享中的日志 |
|[注册 Azure Stack Hub](./azure-stack-registration.md#renew-or-change-registration) |对于重新注册，从管理门户获取以前的注册名称和资源组                               |
|[市场联合](./azure-stack-download-azure-marketplace-item.md)            |在 HLH 上[创建 SMB 共享](#transfer-files-between-the-hlh-and-oaw)以存储下载的映像或扩展                                                        |

## <a name="download-files"></a>下载文件

若要获取文件以创建 OAW VM，请[在此处下载](https://aka.ms/OAWDownload)。 下载之前，请务必查看 [Microsoft 隐私声明](https://privacy.microsoft.com/privacystatement) 和 [法律条款](/legal/azure-stack-hub/azure-stack-operator-access-workstation-legal-terms) 。

由于解决方案的无状态性质，没有适用于 OAW VM 的更新。 对于每个里程碑，都会发行 VM 映像文件的新版本。 使用最新版本创建新 OAW VM。 映像文件基于最新 Windows Server 2019 版本。 安装之后，可以使用 Windows 更新来应用更新（包括所有关键更新）。 

验证下载的 OAW.zip 文件的哈希，以确保在使用它创建 OAW VM 之前未对它进行修改。 运行下面的 PowerShell 脚本。 如果返回值为 True，则可以使用下载的 OAW.zip：

```powershell
param(
    [Parameter(Mandatory=$True)]
    [ValidateNotNullOrEmpty()]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [string]
    $DownloadedOAWZipFilePath
)

$expectedHash = '73E16995B79433E79F9EFA9A292443296D112B24B4D86A060FCB4C9403B8D014'
$actualHash = (Get-FileHash -Path $DownloadedOAWZipFilePath).Hash

Write-Host "Expected hash: $expectedHash"

if ($expectedHash -eq $actualHash)
{
    Write-Host 'SUCCESS: OAW.zip file hash matches.'
}
else
{
    Write-Error 'ERROR: OAW.zip file hash does not match! It is not safe to use it, please download it again.'
    Write-Error "Actual hash: $actualHash"
}
```

## <a name="user-account-policy"></a>用户帐户策略 
以下用户帐户策略会应用于 OAW VM：

- 内置管理员用户名：AdminUser
- MinimumPasswordLength = 14
- PasswordComplexity 已启用
- MinimumPasswordAge = 1（天）
- MaximumPasswordAge = 42（天）
- NewGuestName = GUser（默认情况下禁用）

## <a name="pre-installed-software"></a>预安装的软件
下表列出 OAW VM 上的预安装软件。

| 软件名称           | 位置                                                                                       |
|--------------------------|------------------------------------------------------------------------------------------------|
| [Microsoft Edge for Business](https://www.microsoft.com/edge/business/)                                            | \[SystemDrive\]\Program Files (x86)\Microsoft\Edge\Application                                                                                        |
| [Az 模块](./powershell-install-az-module.md)                         | \[SystemDrive\]\ProgramFiles\WindowsPowerShell\Modules                                         |  
| [PowerShell 7](https://devblogs.microsoft.com/powershell/announcing-PowerShell-7-0/)| \[SystemDrive\]\Program Files\PowerShell\7                                                                       |
| [Azure 命令行接口 (CLI)](/cli/azure/?view=azure-cli-latest) | \[SystemDrive\]\Program Files (x86)\Microsoft SDKs\Azure\CLI2 |
| [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)   | \[SystemDrive\]\Program Files (x86)\Microsoft Azure Storage Explorer                                                                       |
| [AzCopy](/azure/storage/common/storage-use-azcopy-v10)                             | \[SystemDrive\]\VMSoftware\azcopy_windows_amd64_10.3.4                                         |
| [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools/tree/az)                  | \[SystemDrive\]\VMSoftware\AzureStack-Tools                                                    |

## <a name="check-hlh-version"></a>检查 HLH 版本

1. 使用凭据登录 HLH。
1. 打开 PowerShell ISE 并运行以下脚本：

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   例如：

   ![用于检查 OAW VM 版本的 PowerShell cmdlet 的屏幕截图](./media/operator-access-workstation/check-hardware-lifecycle-host-version.png)

## <a name="create-the-oaw-vm-using-a-script"></a>使用脚本创建 OAW VM

下面的脚本将虚拟机准备为操作员访问工作站 (OAW) ，该工作站用于访问用于访问 Microsoft Azure Stack 集线器进行管理和诊断。

1. 使用凭据登录 HLH。
1. 下载 OAW.zip 并提取文件。
1. 打开提升的 PowerShell 会话。
1. 导航到 OAW.zip 文件的已提取内容。
1. 运行 New-OAW.ps1 脚本。 

例如，若要使用 Azure Stack Hub 版本 2005 或更高版本在 HLH 上创建 OAW VM 而不进行任何自定义，请只使用 -LocalAdministratorPassword 参数运行 New-OAW.ps1 脚本：

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword  
```

若要在具有与 Azure Stack Hub 的网络连接的主机上创建 OAW VM：

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -IPAddress '192.168.0.20' `
   -SubnetMask '255.255.255.0' `
   -DefaultGateway '192.168.0.1' `
   -DNS '192.168.0.10'
```

若要从 AzureStackStampInformation.json 文件检索 ERCS VM 的 IP 地址，请执行以下代码：

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -AzureStackCertificatePath 'F:\certroot.cer' `
   -DeploymentDataFilePath 'F:\DeploymentData.json' `
   -AzSStampInfoFilePath 'F:\AzureStackStampInformation.json'
```

若要使用 DeploymentData.json 在 HLH 上创建 OAW VM：

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -DeploymentDataFilePath 'D:\AzureStack\DeploymentData.json'
```

如果 DeploymentData.json 文件包含 OAW VM 的命名前缀，则该值将用于 VirtualMachineName 参数。 否则，默认名称是 AzSOAW 或用户指定的任何名称。

New-OAW 可以使用两个参数集。 可选参数显示在括号中。

```powershell
New-OAW 
-LocalAdministratorPassword <Security.SecureString> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-DNS <String[]>] `
[-DeploymentDataFilePath <String>] `
[-SkipNetworkConfiguration] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

```powershell
New-OAW
-LocalAdministratorPassword <Security.SecureString> `
-IPAddress <String> `
-SubnetMask <String> `
-DefaultGateway <String> `
-DNS <String[]> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

下表列出了每个参数的定义。

| 参数   | 必需/可选  | 说明       |
|-------------|--------------------|-------------------|
| LocalAdministratorPassword | 必须 | 虚拟机本地管理员帐户 AdminUser 的密码。 |
| IPAddress                  | 必须 | 用于在虚拟机上配置 TCP/IP 的静态 IPv4 地址。                                                |
| SubnetMask                 | 必须 | 用于在虚拟机上配置 TCP/IP 的 IPv4 子网掩码。                                                   |
| DefaultGateway             | 必须 | 用于在虚拟机上配置 TCP/IP 的默认网关的 IPv4 地址。                                    |
| DNS                        | 必须 | 用于在虚拟机上配置 TCP/IP 的 DNS 服务器。                                                          |
| ImageFilePath              | 可选 | Microsoft 提供的 OAW.vhdx 的路径。 默认值为此脚本的相同父文件夹下的 OAW.vhdx。 |
| VirtualMachineName         | 可选 | 要分配给虚拟机的名称。 如果可在 DeploymentData.json 文件中找到命名前缀，则将它用作默认名称。 否则，AzSOAW 将用作默认名称。 可以指定另一个名称以覆盖默认值。 |
| VirtualMachineMemory       | 可选 | 要分配给虚拟机的内存。 默认值为 4GB。                            |
| VirtualProcessorCount      | 可选 | 要分配给虚拟机的虚拟处理器数量。 默认值为 8。        |
| VirtualMachineDiffDiskPath | 可选 | 管理 VM 处于活动状态期间用于存储临时差异磁盘文件的路径。 默认值为此脚本的相同父文件夹下的 DiffDisks 子目录。 |
| AzureStackCertificatePath  | 可选 | 要导入到虚拟机以进行 Azure Stack Hub 访问的证书的路径。 |
| AzSStampInfoFilePath       | 可选 | AzureStackStampInformation.json 文件的路径，脚本可以从该文件中检索 ERCS VM 的 IP。 |
| CertificatePassword        | 可选 | 要导入到虚拟机以进行 Azure Stack Hub 访问的证书的密码。 |
| ERCSVMIP                   | 可选 | 要添加到虚拟机的受信任主机列表的 Azure Stack Hub ERCS VM 的 IP。 如果设置了 -SkipNetworkConfiguration，则不会生效。 |
SkipNetworkConfiguration     | 可选 | 跳过虚拟机的网络配置，使用户可以在以后配置。 |
| DeploymentDataFilePath     | 可选 | DeploymentData.json 的路径。 如果设置了 -SkipNetworkConfiguration，则不会生效。            |
| PhysicalAdapterMACAddress  | 可选 | 用于将虚拟机连接到的主机网络适配器的 MAC 地址。<br>- 如果只有一个物理网络适配器，则不需要此参数，将使用唯一的网络适配器。<br>- 如果有多个物理网络适配器，则需要使用此参数来指定要使用的适配器。<br> |
| VirtualSwitchName          | 可选 | 需要在 Hyper-V 中为虚拟机配置的虚拟交换机的名称。<br>- 如果存在具有所提供名称的 VMSwitch，则会选择此类 VMSwitch。<br>- 如果不存在具有所提供名称的 VMSwitch，则会使用提供的名称创建 VMSwitch。<br> |
| ReCreate                   | 可选 | 如果已存在具有相同名称的虚拟机，则删除并重新创建虚拟机。 |

## <a name="check-the-oaw-vm-version"></a>检查 OAW VM 版本

1. 使用凭据登录 OAW VM。
1. 打开 PowerShell ISE 并运行以下脚本：

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   例如：

   ![用于检查硬件生命周期主机版本的 PowerShell cmdlet 的屏幕截图](./media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

## <a name="transfer-files-between-the-hlh-and-oaw"></a>在 HLH 与 OAW 之间传输文件

如果需要在 HLH 与 OAW 之间传输文件，请使用 [New-SmbShare](/powershell/module/smbshare/new-smbshare?view=win10-ps) cmdlet 创建 SMB 共享。 New-SmbShare 会将文件系统文件夹作为服务器消息块 (SMB) 共享公开给远程客户端。 例如：

若要删除通过此 cmdlet 创建的共享，请使用 [Remove-SmbShare](/powershell/module/smbshare/remove-smbshare?view=win10-ps) cmdlet。 例如：

## <a name="remove-the-oaw-vm"></a>删除 OAW VM

以下脚本会删除用于访问 Azure Stack Hub 以进行管理和诊断的 OAW VM。 此脚本还会删除与 VM 关联的磁盘文件和保护者。

1. 使用凭据登录 HLH。
1. 打开提升的 PowerShell 会话。 
1. 导航到已安装 OAW.zip 文件的已提取内容。
1. 通过运行 Remove-OAW.ps1 脚本来删除 VM： 

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName <name>
   ```

   其中 \<name\> 是要删除的虚拟机的名称。 默认情况下，名称是 AzSOAW。

   例如：

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName AzSOAW
   ```

## <a name="next-steps"></a>后续步骤

[Azure Stack 管理任务](azure-stack-manage-basics.md)
