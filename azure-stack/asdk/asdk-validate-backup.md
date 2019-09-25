---
title: 使用 ASDK 验证 Azure Stack 备份 |Microsoft Docs
description: 了解如何使用 ASDK 来验证 Azure Stack 集成系统备份。
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 685f2d868314610ea7c19443fe47f29182561a51
ms.sourcegitcommit: 4e48f1e5af74712a104eda97757dc5f50a591936
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71225016"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>使用 ASDK 验证 Azure Stack 备份
部署 Azure Stack 和预配用户资源（例如产品/服务、计划、配额和订阅）后，应[启用 Azure Stack 基础结构备份](../operator/azure-stack-backup-enable-backup-console.md)。 如果出现灾难性的硬件或服务故障，则计划和运行定期基础结构备份将确保基础结构管理数据不会丢失。

> [!TIP]
> 建议在开始此过程之前[运行按需备份](../operator/azure-stack-backup-back-up-azure-stack.md)，确保有最新基础结构数据的副本可用。 确保在备份成功完成以后捕获备份 ID。 云恢复期间需要此 ID。

Azure Stack 基础结构备份包含有关云的重要数据，这些数据可以在重新部署 Azure Stack 的过程中还原。 可以使用 ASDK 来验证这些备份，不影响生产云。 

以下方案支持在 ASDK 上验证备份：

|应用场景|用途|
|-----|-----|
|通过集成解决方案验证基础结构备份。|短暂验证，验证备份中的数据是否有效。|
|了解端到端恢复工作流。|使用 ASDK 验证整个备份和还原体验。|
|     |     |

在 ASDK 上验证备份时，以下方案**不**受支持：

|应用场景|用途|
|-----|-----|
|ASDK 内部版本到内部版本备份和还原。|将备份数据从旧版 ASDK 还原到新版。|
|     |     |


## <a name="cloud-recovery-deployment"></a>云恢复部署
对 ASDK 执行云恢复部署即可验证从集成系统部署进行的基础结构备份。 在此类部署中，将 ASDK 安装在主机上以后，即可从备份还原特定的服务数据。

### <a name="prereqs"></a>云恢复先决条件
在开始 ASDK 的云恢复部署之前，请确保你具有以下信息：

**UI 安装程序要求**

*当前 UI 安装程序仅支持加密密钥*

|先决条件|描述|
|-----|-----|
|备份共享路径。|将用于恢复 Azure Stack 基础结构信息的最新 Azure Stack 备份的 UNC 文件共享路径。 此本地共享将在云恢复部署过程中创建。|
|要还原的备份 ID|备份 ID，采用的字母数字形式为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”，用于确定需要在云恢复过程中还原的备份。|
|时间服务器 IP|有效的时间服务器 IP（例如 132.163.97.2）是 Azure Stack 部署所需的。|
|外部证书密码|Azure Stack 使用的外部证书的密码。 CA 备份包含外部证书，这些证书需使用此密码来还原。|
|备份加密密钥|如果使用加密密钥配置备份设置（已弃用），则需要此项。 安装程序至少支持 3 个版本的后向兼容性模式下的加密密钥。 将备份设置更新为使用证书后，请参阅下表获取所需信息。|
|     |     | 

**PowerShell 安装程序要求**

*当前 PowerShell 安装程序支持加密密钥或解密证书*

|先决条件|描述|
|-----|-----|
|备份共享路径。|将用于恢复 Azure Stack 基础结构信息的最新 Azure Stack 备份的 UNC 文件共享路径。 此本地共享将在云恢复部署过程中创建。|
|要还原的备份 ID|备份 ID，采用的字母数字形式为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”，用于确定需要在云恢复过程中还原的备份。|
|时间服务器 IP|有效的时间服务器 IP（例如 132.163.97.2）是 Azure Stack 部署所需的。|
|外部证书密码|Azure Stack 使用的外部证书的密码。 CA 备份包含外部证书，这些证书需使用此密码来还原。|
|解密证书密码|可选。 只有在使用证书加密备份时才是必需的。 密码适用于自签名证书 (.pfx)，该证书包含解密备份数据所需的私钥。|
|备份加密密钥|可选。 如果仍使用加密密钥配置备份设置，则为必需。 安装程序至少支持 3 个版本的后向兼容性模式下的加密密钥。 将备份设置更新为使用证书后，必须提供解密证书的密码。|
|     |     | 

## <a name="prepare-the-host-computer"></a>准备主机 
与在正常的 ASDK 部署中一样，ASDK 主机系统环境必须进行安装准备。 准备好 ASDK 主机计算机后，它将从 Cloudbuilder.vhdx VM 硬盘驱动器启动，以开始 ASDK 部署。

在 ASDK 主机上下载新的 cloudbuilder.vhdx（对应于 Azure Stack 的已备份版本），然后按说明[准备 ASDK 主机](asdk-prepare-host.md)。

在主机服务器从 cloudbuilder.vhdx 重启以后，必须创建一个文件共享并将备份数据复制到其中。 文件共享应该可供运行设置的帐户（即下述示例 PowerShell 命令中的管理员）访问： 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

接下来，将最新的 Azure Stack 备份文件复制到新创建的共享。 请确保复制文件夹的`<BackupID>`父文件夹，这是创建备份的时间的时间戳。 共享中的文件夹结构应该是：`\\<ComputerName>\AzSBackups\MASBackup\<TimeStamp>\<BackupID>\`。 

最后，将解密证书 (.pfx) 复制到证书目录：`C:\CloudDeployment\Setup\BackupDecryptionCert\`，并将文件重命名为 `BackupDecryptionCert.pfx`。

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>在云恢复模式下部署 ASDK

> [!IMPORTANT]
> 1. 当前安装程序 UI 仅支持加密密钥。 只能从继续使用加密密钥的系统验证备份。 如果使用证书在集成系统或 ASDK 中加密了备份，则必须使用 PowerShell 安装程序 (**InstallAzureStackPOC.ps1**)。 
> 2. PowerShell 安装程序 (**InstallAzureStackPOC.ps1**) 支持加密密钥或证书。
> 3. ASDK 安装支持使用一个网络接口卡 (NIC) 进行网络连接。 如果有多个 NIC，请确保只启用一个（所有其他 NIC 均禁用），然后再运行部署脚本。

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>使用安装程序 UI 在恢复模式下部署 ASDK
本部分中的步骤说明如何使用图形用户界面 (GUI)（可通过下载并运行 **asdk-installer.ps1** PowerShell 脚本来获取）部署 ASDK。

> [!NOTE]
> ASDK 的安装程序用户界面是一种基于 WCF 和 PowerShell 的开源脚本。

> [!IMPORTANT]
> 当前安装程序 UI 仅支持加密密钥。

1. 在将主机成功启动到 Cloudbuilder.vhdx 映像后，使用在为 ASDK 安装[准备 ASDK 主机](asdk-prepare-host.md)时指定的管理员凭据进行登录。 这些凭据应与 ASDK 主机本地管理员凭据相同。
2. 打开权限提升的 PowerShell 控制台并运行 **&lt;驱动器号>\AzureStack_Installer\asdk-installer.ps1** PowerShell 脚本。 该脚本现在可能位于与 CloudBuilder.vhdx 映像中的 C:\ 不同的驱动器上。 单击“**恢复**”。

    ![ASDK 安装程序脚本](media/asdk-validate-backup/1.PNG) 

3. 在 "标识提供者和凭据" 页上输入 ASDK 主计算机的 Azure AD 目录信息（可选）和本地管理员密码。 单击“下一步”。

    ![ASDK 标识和凭据页](media/asdk-validate-backup/2.PNG) 

4. 选择 ASDK 主机使用的网络适配器，然后单击“下一步”。 在 ASDK 安装期间，将禁用其他所有网络接口。 

    ![ASDK 网络适配器接口](media/asdk-validate-backup/3.PNG) 

5. 在“网络配置”页上，提供有效的时间服务器和 DNS 转发站 IP 地址。 单击“下一步”。

    ![ASDK 网络配置页](media/asdk-validate-backup/4.PNG) 

6. 检查网络接口卡的属性后，单击“下一步”。 

    ![ASDK 网络卡设置验证](media/asdk-validate-backup/5.PNG) 

7. 提供前面在 "备份设置" 页上的 "[先决条件" 部分](#prereqs)中描述的必需信息，以及用于访问共享的用户名和密码。 单击“下一步”： 

   !["ASDK 备份设置" 页](media/asdk-validate-backup/6.PNG) 

8. 在“摘要”页上查看用于部署 ASDK 的部署脚本。 单击“部署”以开始部署。 

    ![ASDK 摘要页](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>使用 PowerShell 在恢复模式下部署 ASDK

针对环境修改以下 PowerShell 命令，然后运行它们，以便在云恢复模式下部署 ASDK：

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
成功地进行云恢复部署以后，需使用 **Restore-AzureStack** cmdlet 完成还原操作。 

以 Azure Stack 操作员身份登录，[安装 Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)，然后运行以下命令以指定从备份恢复时要使用的证书和密码：

**使用证书文件的恢复模式**

> [!NOTE]
> 出于安全原因，Azure Stack 部署不会保留解密证书。 需要再次提供解密证书和关联的密码。

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**使用加密密钥的恢复模式**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

调用此 cmdlet 后，先等待 60 分钟，然后开始在进行云恢复的 ASDK 上验证备份数据。

## <a name="next-steps"></a>后续步骤
[注册 Azure Stack](asdk-register.md)

