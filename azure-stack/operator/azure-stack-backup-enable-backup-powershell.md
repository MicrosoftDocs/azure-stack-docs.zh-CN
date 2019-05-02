---
title: 使用 PowerShell 为 Azure Stack 启用备份 | Microsoft Docs
description: 使用 Windows PowerShell 启用基础结构备份服务，以便出现故障时可以还原 Azure Stack。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/08/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: cf1a040a14bab3c25ab81773578838ec00ae5ded
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490082"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>使用 PowerShell 为 Azure Stack 启用备份

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用 Windows PowerShell 启用基础结构备份服务，以便定期备份以下内容：
 - 内部标识服务和根证书
 - 用户计划、产品/服务、订阅
 - 计算配额、存储配额和网络用户配额
 - 用户密钥保管库机密
 - 用户 RBAC 角色和策略
 - 用户存储帐户

可以访问 PowerShell cmdlet 以启用备份、启动备份，以及通过操作员管理终结点获取备份信息。

## <a name="prepare-powershell-environment"></a>准备 PowerShell 环境

有关配置 PowerShell 环境的说明，请参阅[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 若要登录到 Azure Stack，请参阅[配置操作员环境并登录到 Azure Stack](azure-stack-powershell-configure-admin.md)。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>提供备份共享、凭据和加密密钥以启用备份

在同一 PowerShell 会话中，通过添加环境变量编辑以下 PowerShell 脚本。 运行更新的脚本，以向基础结构备份服务提供备份共享、凭据和加密密钥。

| 变量        | 描述   |
|---              |---                                        |
| $username       | 使用共享驱动器位置具有足够访问权限的域和用户名输入**用户名**，以便读取和写入文件。 例如，`Contoso\backupshareuser`。 |
| $password       | 键入用户的**密码**。 |
| $sharepath      | 键入**备份存储位置**的路径。 必须使用通用命名约定 (UNC) 字符串表示单独的设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 若要确保备份数据的可用性，设备应放置在单独的位置。 |
| $frequencyInHours | “频率(小时)”决定了以何频率创建备份。 默认值为 12。 计划程序支持的最大值为 12，最小值为 4。|
| $retentionPeriodInDays | “保留期(天)”决定了备份在外部位置保留多少天。 默认值为 7。 计划程序支持的最大值为 14，最小值为 2。 超过保留期的备份会自动从外部位置删除。|
| $encryptioncertpath | 适用于 1901 及更高版本。  参数在 Azure Stack 模块 1.7 及更高版本中提供。 加密证书路径指定 .CER 文件的文件路径，文件中的公钥用于数据加密。 |
| $encryptionkey | 适用于 1811 或更低版本。 参数在 Azure Stack 模块 1.6 或更低版本中提供。 用于数据加密的加密密钥。 使用 [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64) cmdlet 来生成新密钥。 |
|     |     |

### <a name="enable-backup-on-1901-and-beyond-using-certificate"></a>使用证书在 1901 及更高版本上启用备份
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

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>使用证书在 1811 或更低版本上启用备份
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
在同一 PowerShell 会话中，可以更新备份的保留期和频率的默认值。 

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

### <a name="azure-stack-powershell"></a>Azure Stack PowerShell 
用于配置基础结构备份的 PowerShell cmdlet 为 Set-AzsBackupConfiguration。 在以前的版本中，此 cmdlet 为 Set-AzsBackupShare。 此 cmdlet 要求提供一个证书。 如果为基础结构备份配置了加密密钥，则不能更新加密密钥或查看属性。 需使用 1.6 版管理员 PowerShell。 

如果基础结构备份在更新到 1901 之前已进行配置，则可使用 1.6 版管理员 PowerShell 来设置和查看加密密钥。 1.6 版不允许从加密密钥更新到证书文件。
请参阅[安装 Azure Stack PowerShell](azure-stack-powershell-install.md)，详细了解如何安装模块的正确版本。 


## <a name="next-steps"></a>后续步骤

若要了解如何运行备份，请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md)

若要了解如何验证备份是否已运行，请参阅[在管理门户中确认已完成的备份](azure-stack-backup-back-up-azure-stack.md)
