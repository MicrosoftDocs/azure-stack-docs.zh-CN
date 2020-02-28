---
title: 使用 PowerShell 为 Azure Stack 中心启用备份
description: 了解如何使用 PowerShell 启用基础结构备份服务，以便在发生故障时可以还原 Azure Stack 中心。
author: justinha
ms.topic: article
ms.date: 04/25/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 2369ffbd1d3fc72c9d2df7ff87be6131f7aabcd7
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703309"
---
# <a name="enable-backup-for-azure-stack-hub-with-powershell"></a>使用 PowerShell 为 Azure Stack 中心启用备份

使用 Windows PowerShell 启用基础结构备份服务定期备份：
 - 内部标识服务和根证书。
 - 用户计划、产品/服务、订阅。
 - 计算、存储和网络用户配额。
 - 用户 Key Vault 密码。
 - 用户 RBAC 角色和策略。
 - 用户存储帐户。

可以访问 PowerShell cmdlet，启用备份、启动备份，并通过操作员管理终结点获取备份信息。

## <a name="prepare-powershell-environment"></a>准备 PowerShell 环境

有关配置 PowerShell 环境的说明，请参阅为[Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。 若要登录 Azure Stack 中心，请参阅[配置操作员环境和登录到 Azure Stack 中心](azure-stack-powershell-configure-admin.md)。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>提供备份共享、凭据和加密密钥以启用备份

在同一 PowerShell 会话中，通过为环境添加变量来编辑以下 PowerShell 脚本。 运行更新的脚本，为基础结构备份服务提供备份共享、凭据和加密密钥。

| 变量        | 说明   |
|---              |---                                        |
| `$username`       | 使用 "域" 和 "用户名" 为共享驱动器位置键入**用户名**，具有对读取和写入文件的足够访问权限。 例如，`Contoso\backupshareuser` 。 |
| `$password`       | 键入用户的**密码**。 |
| `$sharepath`      | 键入**备份存储位置**的路径。 必须使用通用命名约定（UNC）字符串作为指向单独设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 若要确保备份数据的可用性，设备应位于不同的位置。 |
| `$frequencyInHours` | 以小时为单位的频率决定了创建备份的频率。 默认值为 12。 计划程序最多支持12个，最多支持4个。|
| `$retentionPeriodInDays` | 保持期（天）决定在外部位置保留备份的天数。 默认值为 7。 计划程序最多支持14个，最多支持2个。 早于保留期的备份从外部位置自动删除。|
| `$encryptioncertpath` | 适用于1901及更高版本。 参数在 Azure Stack 集线器模块版本1.7 及更高版本中可用。 加密证书路径指定的文件路径。带有用于数据加密的公钥的 .CER 文件。 |
| `$encryptionkey` | 适用于 build 1811 或更早版本。 参数 Azure Stack 集线器模块版本1.6 或更早版本中可用。 加密密钥用于数据加密。 使用[AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64) cmdlet 生成新密钥。 |
|     |     |

### <a name="enable-backup-on-1901-and-later-using-certificate"></a>使用证书在1901和更高版本上启用备份
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certificate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>使用证书在1811或更早版本上启用备份
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $key = New-AzsEncryptionKeyBase64
    $Securekey = ConvertTo-SecureString -String ($key) -AsPlainText -Force

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $Securekey
```

   
##  <a name="confirm-backup-settings"></a>确认备份设置

在同一 PowerShell 会话中，运行以下命令：

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

结果应类似于以下示例输出：

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>更新备份设置
在同一 PowerShell 会话中，你可以更新保留期和备份频率的默认值。 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

结果应类似于以下示例输出：

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-hub-powershell"></a>Azure Stack 中心 PowerShell 
用于配置基础结构备份的 PowerShell cmdlet 是 AzsBackupConfiguration。 在以前的版本中，cmdlet 已设置-AzsBackupShare。 此 cmdlet 需要提供证书。 如果使用加密密钥配置基础结构备份，则无法更新加密密钥或查看属性。 需要使用版本1.6 的管理员 PowerShell。

如果在更新到1901之前配置了基础结构备份，则可以使用管理员 PowerShell 版本1.6 来设置和查看加密密钥。 版本1.6 不允许更新为证书文件的加密密钥。
有关安装正确版本的模块的详细信息，请参阅[安装 Azure Stack Hub PowerShell](azure-stack-powershell-install.md) 。


## <a name="next-steps"></a>后续步骤

了解如何运行备份，请参阅[备份 Azure Stack 中心](azure-stack-backup-back-up-azure-stack.md)。

了解如何验证备份是否已运行，请参阅[在管理门户中确认备份已完成](azure-stack-backup-back-up-azure-stack.md)。
