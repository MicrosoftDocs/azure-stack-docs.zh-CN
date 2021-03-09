---
title: Azure Stack Hub 操作员访问工作站
description: 了解如何下载和配置 Azure Stack Hub 操作员访问工作站。
author: mattbriggs
ms.topic: article
ms.date: 03/05/2021
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 03/05/2021
ms.openlocfilehash: f4fc9ec002312432bd9f839026eb3ed4254991ea
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515611"
---
# <a name="azure-stack-hub-operator-access-workstation"></a>Azure Stack Hub 操作员访问工作站

操作员访问工作站 (OAW) 用于在 Azure Stack 中心--硬件生命周期主机 (HLH) 或运行 Microsoft Hyper-V 的任何其他计算机上部署虚拟机 (VM) 。 它需要与用于操作员或用户方案的 Azure Stack 中心终结点建立网络连接。

OAW VM 是一个可选虚拟机，Azure Stack 集线器运行不是必需的虚拟机。 它的目的是为操作员或用户提供最新的工具，因为它们与 Azure Stack 中心交互。

## <a name="oaw-scenarios"></a>OAW 方案

下表列出了 OAW 的常见方案。 使用远程桌面连接到 OAW。

| **方案**                                                                                                                                          | **说明**                                                                                                                                                                                                                                                                                                         |
|-------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [访问管理门户](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals?view=azs-2008)                    | 执行管理操作。                                                                                                                                                                                                                                                                                       |
| [访问 PEP](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint?view=azs-2008)                                     | 日志收集和上传：<br>-在 HLH 上创建用于从 Azure Stack 中心传输文件的[SMB 共享](https://docs.microsoft.com/azure-stack/operator/operator-access-workstation?view=azs-2008#transfer-files-between-the-hlh-and-oaw)。<br>-使用 Azure 存储资源管理器上载保存到 SMB 共享中的日志。 |
| [注册 Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-registration?view=azs-2008#renew-or-change-registration) | 对于重新注册，请从管理门户获取以前的注册名称和资源组。                                                                                                                                                                                                                   |
| [市场联合](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-2008)            | 在 HLH 上[创建一个 SMB 共享](https://docs.microsoft.com/azure-stack/operator/operator-access-workstation?view=azs-2008#transfer-files-between-the-hlh-and-oaw)以存储下载的映像或扩展。                                                                                                     |
| [创建虚拟机](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-create-vm-windows-cli?view=azs-2008)                    | 使用 CLI 创建虚拟机。                                                                                                                                                                                                                                                                                       |
| [管理 AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-scale?view=azs-2008)                                 | 管理 AKS 群集，例如，缩放或升级。                                                                                                                                                                                                                                                                        |

## <a name="pre-installed-software"></a>预安装的软件

下表列出 OAW VM 上的预安装软件。

| **软件名称**                                                                                              | **位置**                                                         |
|----------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [Microsoft Edge for Business](https://www.microsoft.com/edge/business/)                                        | [SystemDrive] \\ (x86) \\ Microsoft \\ Edge \\ 应用程序的程序文件     |
| [Az 模块](https://docs.microsoft.com/azure-stack/operator/powershell-install-az-module?view=azs-2008) | [SystemDrive] \\ProgramFiles \\ WindowsPowerShell \\ 模块              |
| [PowerShell 7](https://devblogs.microsoft.com/powershell/announcing-PowerShell-7-0/)                           | [SystemDrive] \\Program Files \\ PowerShell \\ 7                          |
| [Azure 命令行接口 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)        | [SystemDrive] \\程序文件 (x86) \\ Microsoft sdk \\ Azure \\ CLI2      |
| [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)                     | [SystemDrive] \\程序文件 (x86) \\ Microsoft Azure 存储资源管理器 |
| [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)                         | [SystemDrive] \\VMSoftware \\ azcopy_windows_amd64_10 3。4               |
| [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools/tree/az)                                          | [SystemDrive] \\VMSoftware \\ test-azurestack-工具                          |
## <a name="download-files"></a>下载文件
若要获取文件以创建 OAW VM，请在此处下载。 下载之前，请务必查看 [Microsoft 隐私声明](https://privacy.microsoft.com/privacystatement) 和 [法律条款](https://docs.microsoft.com/legal/azure-stack-hub/azure-stack-operator-access-workstation-legal-terms) 。

由于解决方案的无状态特性，OAW VM 没有任何更新。 对于每个里程碑，都会发布一个新版本的 VM 映像文件。 使用最新版本创建新 OAW VM。 映像文件基于最新 Windows Server 2019 版本。 安装之后，可以使用 Windows 更新来应用更新（包括所有关键更新）。

验证下载的 OAW.zip 文件的哈希，以确保在使用它创建 OAW VM 之前未对其进行修改。 运行下面的 PowerShell 脚本。 如果返回值为 True，则可以使用下载的 OAW.zip：


> [!NOTE]  
> 提取下载内容后取消阻止脚本文件。

```powershell
param( 
    [Parameter(Mandatory=$True)] 
    [ValidateNotNullOrEmpty()] 
    [ValidateScript({Test-Path $_ -PathType Leaf})] 
    [string] 
    $DownloadedOAWZipFilePath 
) 
$expectedHash = '2B268EFB113A3BEDA008FCF382A5EF2F2D4E5DCC7FD0D12DB061E37F9671D3A7' 
$actualHash = (Get-FileHash -Path $DownloadedOAWZipFilePath).Hash 
Write-Host "Expected hash: $expectedHash" 
if ($expectedHash -eq $actualHash) 
{ 
    Write-Host 'SUCCESS: OAW.zip file hash matches.' 
} 
else 
{ 
    Write-Error "ERROR: OAW.zip file hash does not match! It isn't safe to use it, please download it again." 
    Write-Error "Actual hash: $actualHash" 
} 
```

## <a name="check-hlh-version"></a>检查 HLH 版本

> [!NOTE]  
> 若要确定是否在使用 Microsoft 映像或 OEM 映像部署的 HLH 上部署 OAW，则必须执行此步骤。 如果在常规 Microsoft Hyper-V 上部署 OAW，则可以跳过此步骤。

1.  用凭据登录到 HLH。

2.  打开 PowerShell ISE 并运行以下脚本：
    ```powershell  
    C:\Version\Get-Version.ps1
    ```

    例如：

    ![用于检查 OAW VM 版本的 PowerShell cmdlet 的屏幕截图。](media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

> [!NOTE]  
> 此 PowerShell cmdlet 在使用 OEM 映像部署的 HLH 上不存在。

## <a name="create-the-oaw-vm-using-a-script"></a>使用脚本创建 OAW VM

以下脚本准备虚拟机作为操作员访问工作站 (OAW) ，该工作站用于访问 Microsoft Azure Stack 集线器。

1.  用凭据登录到 HLH。

2.  下载 OAW.zip 并提取文件。

3.  打开提升的 PowerShell 会话。

4.  导航到 OAW.zip 文件的已提取内容。

5.  运行 New-OAW.ps1 脚本。

### <a name="example-deploy-on-hlh-using-a-microsoft-image"></a>示例：使用 Microsoft 映像在 HLH 上进行部署

```powershell  
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString 
New-OAW.ps1 -LocalAdministratorPassword $securePassword 
```


### <a name="example-deploy-on-hlh-using-an-oem-image"></a>示例：使用 OEM 映像在 HLH 上进行部署

```powershell  
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString 
New-OAW.ps1 -LocalAdministratorPassword $securePassword -AzureStackCertificatePath 'F:\certroot.cer' -DeploymentDataFilePath 'F:\DeploymentData.json' -AzSStampInfoFilePath 'F:\AzureStackStampInformation.json'
```

如果 ` DeploymentData.json` 文件包括 OAW VM 的命名前缀，则此值将用于 `VirtualMachineName` 参数。 否则，默认名称为 `AzSOAW` 或指定的任何名称由用户指定。 `DeploymentData.json`在 HLH 上不存在时，可以使用[特权终结点](https://docs.microsoft.com/azure-stack/reference/pep-2002/get-azurestackstampinformation)重新创建。 

> [!NOTE]  
> `AzureStackCertificatePath`仅当使用企业证书颁发机构颁发的证书部署 Azure Stack 集线器时，才应使用参数。

### <a name="example-deploy-on-microsoft-hyper-v"></a>示例：在 Microsoft Hyper-V 上部署

运行 Microsoft Hyper-V 的计算机需要四个核心和 4 GB 可用内存。

```powershell  
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString 
New-OAW.ps1 -LocalAdministratorPassword $securePassword -AzureStackCertificatePath 'F:\certroot.cer' `-SkipNetworkConfiguration -VirtualSwitchName Example  
```

> [!NOTE]  
> `AzureStackCertificatePath`仅当使用企业证书颁发机构颁发的证书部署 Azure Stack 集线器时，才应使用参数。 将不使用网络配置部署 OAW 虚拟机。 可以通过 DHCP 配置静态 IP 地址或检索 IP 地址。

## <a name="user-account-policy"></a>用户帐户策略

以下用户帐户策略会应用于 OAW VM：
 - 内置管理员用户名：AdminUser
 - MinimumPasswordLength = 14
 - PasswordComplexity 已启用
 - MinimumPasswordAge = 1（天）
 - MaximumPasswordAge = 42（天）
 - NewGuestName = GUser（默认情况下禁用）

## <a name="new-oaw-cmdlet-parameters"></a>OAW cmdlet 参数

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

| **参数**              | **必需/可选** | **说明**                                                                                                                                                                                                                                                                                                                                     |
|----------------------------|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LocalAdministratorPassword | 必须              | 虚拟机本地管理员帐户 AdminUser 的密码。                                                                                                                                                                                                                                                                           |
| IPAddress                  | 必须              | 用于在虚拟机上配置 TCP/IP 的静态 IPv4 地址。                                                                                                                                                                                                                                                                                 |
| SubnetMask                 | 必须              | 用于在虚拟机上配置 TCP/IP 的 IPv4 子网掩码。                                                                                                                                                                                                                                                                                    |
| DefaultGateway             | 必须              | 用于在虚拟机上配置 TCP/IP 的默认网关的 IPv4 地址。                                                                                                                                                                                                                                                                     |
| DNS                        | 必须              | 用于在虚拟机上配置 TCP/IP 的 DNS 服务器。                                                                                                                                                                                                                                                                                           |
| ImageFilePath              | 可选              | Microsoft 提供的 OAW.vhdx 的路径。 默认值为此脚本的相同父文件夹下的 OAW.vhdx。                                                                                                                                                                                                                                  |
| VirtualMachineName         | 可选              | 要分配给虚拟机的名称。 如果可在 DeploymentData.json 文件中找到命名前缀，则将它用作默认名称。 否则， **AzSOAW** 将用作默认名称。 可以指定另一个名称以覆盖默认值。                                                                         |
| VirtualMachineMemory       | 可选              | 要分配给虚拟机的内存。 默认值为 **4 GB**。                                                                                                                                                                                                                                                                             |
| VirtualProcessorCount      | 可选              | 要分配给虚拟机的虚拟处理器数量。 默认值为 8。                                                                                                                                                                                                                                                         |
| VirtualMachineDiffDiskPath | 可选              | 管理 VM 处于活动状态期间用于存储临时差异磁盘文件的路径。 默认值为此脚本的相同父文件夹下的 DiffDisks 子目录。                                                                                                                                                                                |
| AzureStackCertificatePath  | 可选              | 要导入到虚拟机以进行 Azure Stack Hub 访问的证书的路径。                                                                                                                                                                                                                                                              |
| AzSStampInfoFilePath       | 可选              | AzureStackStampInformation.json 文件的路径，脚本可以从该文件中检索 ERCS VM 的 IP。                                                                                                                                                                                                                                                  |
| CertificatePassword        | 可选              | 要导入到虚拟机以进行 Azure Stack Hub 访问的证书的密码。                                                                                                                                                                                                                                                           |
| ERCSVMIP                   | 可选              | 要添加到虚拟机的受信任主机列表的 Azure Stack Hub ERCS VM 的 IP。 如果设置了 -SkipNetworkConfiguration，则不会生效。                                                                                                                                                                                                |
| SkipNetworkConfiguration   | 可选              | 跳过虚拟机的网络配置，使用户可以在以后配置。                                                                                                                                                                                                                                                                    |
| DeploymentDataFilePath     | 可选              | DeploymentData.json 的路径。 如果设置了 -SkipNetworkConfiguration，则不会生效。                                                                                                                                                                                                                                                             |
| PhysicalAdapterMACAddress  | 可选              | 用于将虚拟机连接到的主机网络适配器的 MAC 地址。<br>-如果只有一个物理网络适配器，则此参数不是必需的，并且将使用唯一的网络适配器。<br>-如果有多个物理网络适配器，则需要使用此参数来指定要使用哪一个。 |
| VirtualSwitchName          | 可选              | 需要在 Hyper-V 中为虚拟机配置的虚拟交换机的名称。<br>-如果具有所提供名称的 VMSwitch，则会选择此类 VMSwitch。<br>-如果没有具有所提供名称的 VMSwitch，将使用提供的名称创建 VMSwitch。                                                            |
| Re-Create                   | 可选              | 如果已存在具有相同名称的虚拟机，则删除并重新创建虚拟机。                                                                                                                                                                                                                                       |

## <a name="check-the-oaw-vm-version"></a>检查 OAW VM 版本

1.  用凭据登录到 OAW VM。

2.  打开 PowerShell ISE 并运行以下脚本：

    ```powershell  
    C:\\Version\\Get-Version.ps1
    ```

    例如：
    
    ![用于检查硬件生命周期主机版本的 PowerShell cmdlet 的屏幕截图。](media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

## <a name="transfer-files-between-the-hlh-and-oaw"></a>在 HLH 与 OAW 之间传输文件

如果需要在 HLH 与 OAW 之间传输文件，请使用 [New-SmbShare](https://docs.microsoft.com/powershell/module/smbshare/new-smbshare?view=win10-ps) cmdlet 创建 SMB 共享。
New-SmbShare 会将文件系统文件夹作为服务器消息块 (SMB) 共享公开给远程客户端。 例如：

若要删除通过此 cmdlet 创建的共享，请使用 [Remove-SmbShare](https://docs.microsoft.com/powershell/module/smbshare/remove-smbshare?view=win10-ps) cmdlet。

## <a name="remove-the-oaw-vm"></a>删除 OAW VM

以下脚本会删除用于访问 Azure Stack Hub 以进行管理和诊断的 OAW VM。 此脚本还会删除与 VM 关联的磁盘文件和保护者。

1. 用凭据登录到 HLH。

2.  打开提升的 PowerShell 会话。

3.  导航到已安装 OAW.zip 文件的已提取内容。

4.  通过运行 Remove-OAW.ps1 脚本来删除 VM：

    ```powershell  
    Remove-OAW.ps1 -VirtualMachineName \<name\>
    ```
    其中 \<name\> 是要删除的虚拟机的名称。 默认情况下，名称是 AzSOAW。

    例如：

    ```powershell  
    Remove-OAW.ps1 -VirtualMachineName AzSOAW
    ```

## <a name="next-steps"></a>后续步骤

[Azure Stack 管理任务](azure-stack-manage-basics.md)
