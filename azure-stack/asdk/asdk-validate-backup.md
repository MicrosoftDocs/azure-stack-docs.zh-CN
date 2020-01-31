---
title: 使用 ASDK 验证 Azure Stack 备份
description: 了解如何使用 ASDK 来验证 Azure Stack 集成系统备份。
author: justinha
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 2ff4ec010243b27406a2ebb3149bc123aa01947d
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873492"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>使用 ASDK 验证 Azure Stack 备份
部署 Azure Stack 和预配用户资源（例如产品/服务、计划、配额和订阅）后，应[启用 Azure Stack 基础结构备份](../operator/azure-stack-backup-enable-backup-console.md)。 如果出现灾难性的硬件或服务故障，则计划和运行定期基础结构备份将确保基础结构管理数据不会丢失。

> [!TIP]
> 建议在开始此过程之前[运行按需备份](../operator/azure-stack-backup-back-up-azure-stack.md)，以确保具有可用的最新基础结构数据的副本。 请确保在备份成功完成后捕获备份 ID。 云恢复期间需要此 ID。

Azure Stack 基础结构备份包含有关云的重要数据，这些数据可在 Azure Stack 重新部署期间还原。 你可以使用 ASDK 来验证这些备份，而不会影响你的生产云。 

以下方案支持在 ASDK 上验证备份：

|方案|用途|
|-----|-----|
|通过集成解决方案验证基础结构备份。|临时验证备份中的数据是否有效。|
|了解端到端恢复工作流。|使用 ASDK 验证整个备份和还原体验。|
|     |     |

验证 ASDK 上的备份时，**不**支持以下方案：

|方案|用途|
|-----|-----|
|用于生成备份和还原的 ASDK 生成。|将 ASDK 的早期版本中的备份数据还原到较新版本。|
|     |     |


## <a name="cloud-recovery-deployment"></a>云恢复部署
可以通过执行 ASDK 的云恢复部署来验证集成系统部署的基础结构备份。 在此类型的部署中，在主机计算机上安装 ASDK 后，将从备份还原特定服务数据。

### <a name="prereqs"></a>云恢复先决条件
在开始 ASDK 的云恢复部署之前，请确保你具有以下信息：

**UI 安装程序要求**

*当前 UI 安装程序仅支持加密密钥*

|先决条件|Description|
|-----|-----|
|备份共享路径|将用于恢复 Azure Stack 基础结构信息的最新 Azure Stack 备份的 UNC 文件共享路径。 此本地共享将在云恢复部署过程中创建。|
|要还原的备份 ID|"Xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 格式为 "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 的备份 ID，用于标识要在云恢复期间还原的备份。|
|时间服务器 IP|Azure Stack 部署需要有效的时间服务器 IP （如132.163.97.2）。|
|外部证书密码|Azure Stack 使用的外部证书的密码。 CA 备份包含需要用此密码还原的外部证书。|
|备份加密密钥|如果使用不推荐使用的加密密钥在中配置备份设置，则是必需的。 安装程序将支持至少三个版本的向后兼容模式下的加密密钥。 将备份设置更新为使用证书后，请参阅下表获取所需信息。|
|     |     | 

**PowerShell 安装程序要求**

*当前 PowerShell 安装程序支持加密密钥或解密证书*

|先决条件|Description|
|-----|-----|
|备份共享路径|将用于恢复 Azure Stack 基础结构信息的最新 Azure Stack 备份的 UNC 文件共享路径。 此本地共享将在云恢复部署过程中创建。|
|要还原的备份 ID|"Xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 格式为 "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 的备份 ID，用于标识要在云恢复期间还原的备份。|
|时间服务器 IP|Azure Stack 部署需要有效的时间服务器 IP （如132.163.97.2）。|
|外部证书密码|Azure Stack 使用的外部证书的密码。 CA 备份包含需要用此密码还原的外部证书。|
|解密证书密码|可选。 仅当使用证书对备份进行加密时才需要。 密码适用于自签名证书的（.pfx），其中包含解密备份数据所需的私钥。|
|备份加密密钥|可选。 如果备份设置仍配置有加密密钥，则是必需的。 安装程序将支持至少三个版本的向后兼容模式下的加密密钥。 将备份设置更新为使用证书后，必须提供解密证书的密码。|
|     |     | 

## <a name="prepare-the-host-computer"></a>准备主机计算机 
与在正常 ASDK 部署中一样，ASDK 主机系统环境必须为安装做好准备。 准备好 ASDK 主机计算机后，它将从 Cloudbuilder.vhdx VM 硬盘驱动器启动，以开始 ASDK 部署。

在 ASDK 主计算机上，下载与备份的同一版本 Azure Stack 相对应的新 cloudbuilder.vhdx，并按照[准备 ASDK 主机计算机](asdk-prepare-host.md)的说明进行操作。

当主机服务器从 cloudbuilder.vhdx 重新启动后，您必须创建一个文件共享并将您的备份数据复制到中。 运行安装程序的帐户应可以访问文件共享;以下 PowerShell 命令示例中的管理员： 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

接下来，将最新 Azure Stack 备份文件复制到新创建的共享中。 请确保复制 `<BackupID>` 文件夹的父文件夹，这是创建备份的时间的时间戳。 共享内的文件夹结构应为： `\\<ComputerName>\AzSBackups\MASBackup\<TimeStamp>\<BackupID>\`。 

最后，将解密证书（.pfx）复制到证书目录： `C:\CloudDeployment\Setup\BackupDecryptionCert\` 并将文件重命名为 `BackupDecryptionCert.pfx`。

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>在云恢复模式下部署 ASDK

> [!IMPORTANT]
> 1. 当前安装程序 UI 仅支持加密密钥。 你只能验证来自继续使用加密密钥的系统的备份。 如果使用证书在集成系统或 ASDK 上加密备份，则必须使用 PowerShell 安装程序（**installazurestackpoc.ps1**）。 
> 2. PowerShell 安装程序（**installazurestackpoc.ps1**）支持加密密钥或证书。
> 3. ASDK 安装仅支持一个网络接口卡（NIC）用于网络。 如果有多个 Nic，请确保在运行部署脚本之前只启用了一个 Nic （和所有其他 Nic 已禁用）。

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>使用安装程序 UI 在恢复模式下部署 ASDK
本部分中的步骤说明如何使用通过下载并运行**Asdk-installer.ps1** PowerShell 脚本提供的图形用户界面（GUI）来部署 ASDK。

> [!NOTE]
> ASDK 的安装程序用户界面是一种基于 WCF 和 PowerShell 的开源脚本。

> [!IMPORTANT]
> 当前安装程序 UI 仅支持加密密钥。

1. 在将主机成功启动到 Cloudbuilder.vhdx 映像后，使用在为 ASDK 安装[准备 ASDK 主机](asdk-prepare-host.md)时指定的管理员凭据进行登录。 这些凭据应与 ASDK 主机本地管理员凭据相同。
2. 打开提升了权限的 PowerShell 控制台，并运行 **&lt;驱动器号 > \ AzureStack_Installer \Asdk-installer.ps1** PowerShell 脚本。 该脚本现在可能位于与 C：\ 不同的驱动器上在 Cloudbuilder.vhdx 映像中。 单击“**恢复**”。

    ![ASDK 安装程序脚本](media/asdk-validate-backup/1.PNG) 

3. 在 "标识提供者和凭据" 页上输入 ASDK 主计算机的 Azure AD 目录信息（可选）和本地管理员密码。 单击“下一步”。

    ![ASDK 标识和凭据页](media/asdk-validate-backup/2.PNG) 

4. 选择 ASDK 主计算机要使用的网络适配器，然后单击 "**下一步**"。 在 ASDK 安装过程中，将禁用所有其他网络接口。 

    ![ASDK 网络适配器接口](media/asdk-validate-backup/3.PNG) 

5. 在 "网络配置" 页上，提供有效的时间服务器和 DNS 转发器 IP 地址。 单击“下一步”。

    ![ASDK 网络配置页](media/asdk-validate-backup/4.PNG) 

6. 验证网络接口卡属性后，单击 "**下一步**"。 

    ![ASDK 网络卡设置验证](media/asdk-validate-backup/5.PNG) 

7. 提供前面在 "备份设置" 页上的 "[先决条件" 部分](#prereqs)中描述的必需信息，以及用于访问共享的用户名和密码。 单击 "**下一步**"： 

   !["ASDK 备份设置" 页](media/asdk-validate-backup/6.PNG) 

8. 在 "摘要" 页上查看用于部署 ASDK 的部署脚本。 单击 "**部署**" 以开始部署。 

    ![ASDK 摘要页](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>使用 PowerShell 在恢复模式下部署 ASDK

修改环境的以下 PowerShell 命令，并运行这些命令以在云恢复模式下部署 ASDK：

**使用 Installazurestackpoc.ps1 脚本启动包含加密密钥的云恢复。**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**使用 Installazurestackpoc.ps1 脚本启动带有解密证书的云恢复。**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>完成云恢复 
成功部署云恢复后，需要使用**test-azurestack** cmdlet 完成还原。 

以 Azure Stack 操作员身份登录后，[安装 Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)并运行以下命令，以指定从备份还原时要使用的证书和密码：

**包含证书文件的恢复模式**

> [!NOTE]
> 出于安全原因，Azure Stack 部署不会保留解密证书。 需要再次提供解密证书和关联的密码。

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**具有加密密钥的恢复模式**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

调用此 cmdlet 后，请等待60分钟，开始验证云恢复的 ASDK 上的备份数据。

## <a name="next-steps"></a>后续步骤
[注册 Azure Stack](asdk-register.md)

