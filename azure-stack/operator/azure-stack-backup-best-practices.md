---
title: Azure Stack 中心基础结构备份服务最佳实践
description: 部署和管理 Azure Stack 中心时，请遵循以下最佳做法，以便在发生灾难性故障时帮助减轻数据丢失。
author: justinha
ms.topic: article
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: a141beed4df6b34175f37d9e1e60e694f3ab71f2
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700504"
---
# <a name="infrastructure-backup-service-best-practices"></a>基础结构备份服务最佳实践

部署和管理 Azure Stack 中心时，请遵循以下最佳做法，以便在发生灾难性故障时帮助减轻数据丢失。

定期查看最佳实践，验证在对操作流进行更改时你的安装是否仍符合要求。 如果在实施这些最佳实践时遇到任何问题，请联系 Microsoft 支持部门以获得帮助。

## <a name="configuration-best-practices"></a>配置最佳实践

### <a name="deployment"></a>部署

部署每个 Azure Stack 中心云后启用基础结构备份。 使用 Azure Stack 集线器 PowerShell，你可以从任何客户端/服务器（具有对操作员管理 API 终结点的访问权限）计划备份。

### <a name="networking"></a>网络

路径的通用命名约定（UNC）字符串必须使用完全限定的域名（FQDN）。 如果无法进行名称解析，则可以使用 IP 地址。 UNC 字符串指定资源（如共享文件或设备）的位置。

### <a name="encryption"></a>加密

#### <a name="version-1901-and-newer"></a>版本1901及更高版本

加密证书用于加密导出到外部存储的备份数据。 证书可以是自签名证书，因为该证书仅用于传输密钥。 有关如何创建证书的详细信息，请参阅 New-selfsignedcertificate。
  
密钥必须存储在安全的位置（例如，global Azure Key Vault 证书）。 证书的 CER 格式用于对数据进行加密。 在 Azure Stack 集线器的云恢复部署过程中，必须使用 PFX 格式来解密备份数据。

![将证书存储在安全的位置。](media/azure-stack-backup/azure-stack-backup-encryption-store-cert.png)

#### <a name="1811-and-older"></a>1811及更低版本

加密密钥用于对导出到外部存储的备份数据进行加密。 此密钥是在为[Azure Stack 集线器启用 PowerShell 备份](azure-stack-backup-enable-backup-powershell.md)的过程中生成的。

密钥必须存储在安全的位置（例如，global Azure Key Vault secret）。 重新部署 Azure Stack 中心期间，必须使用此密钥。

![将密钥存储在安全位置。](media/azure-stack-backup/azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>操作最佳实践

### <a name="backups"></a>备份

 - 备份作业在系统运行时执行，因此管理体验或用户应用无需停机。 对于合理负载下的解决方案，备份作业预计需要20-40 分钟。
 - 使用 OEM 提供的说明，手动备份的网络交换机和硬件生命周期主机（HLH）应存储在基础结构备份控制器存储控制平面备份数据的同一备份共享。 请考虑将开关和 HLH 配置存储在区域文件夹中。 如果同一区域中有多个 Azure Stack 集线器实例，请考虑对每个属于缩放单位的配置使用一个标识符。

### <a name="folder-names"></a>文件夹名称

 - 基础结构自动创建 MASBACKUP 文件夹。 这是一个 Microsoft 管理的共享。 可以在与 MASBACKUP 相同的级别上创建共享。 不建议在 Azure Stack Hub 不创建的 MASBACKUP 中创建文件夹或存储数据。
 -  文件夹名称中的用户 FQDN 和区域，以区分不同云的备份数据。 Azure Stack 中心部署和终结点的 FQDN 是区域参数和外部域名参数的组合。 有关详细信息，请参阅[Azure Stack 中心数据中心集成-DNS](azure-stack-integrate-dns.md)。

例如，备份共享在 fileserver01.contoso.com 上托管 AzSBackups。 在该文件共享中，每个 Azure Stack 中心部署的文件夹可能使用外部域名和使用区域名称的子文件夹。

FQDN： contoso.com  
区域： nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup 文件夹是 Azure Stack 中心存储其备份数据的位置。 不要使用此文件夹存储您自己的数据。 Oem 不应使用此文件夹存储任何备份数据。

建议 Oem 将其组件的备份数据存储在区域文件夹下。 每个网络交换机、硬件生命周期主机（HLH）等都可能存储在其自身的子文件夹中。 例如：

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>监视

系统支持以下警报：

| 警报                                                   | 说明                                                                                     | 补救                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 备份失败，因为文件共享空间不足。 | 文件共享空间不足，备份控制器无法将备份文件导出到该位置。 | 添加更多存储容量，并再次尝试备份。 删除现有的备份（从最早的备份开始）以释放空间。                    |
| 由于连接问题而导致备份失败。             | Azure Stack 中心和文件共享之间的网络出现问题。                          | 解决网络问题，然后重试备份。                                                                                            |
| 由于路径中的错误，备份失败。                | 无法解析文件共享路径。                                                          | 从另一台计算机映射共享以确保共享可访问。 如果路径不再有效，可能需要更新该路径。       |
| 由于身份验证问题而导致备份失败。               | 凭据或网络问题可能会影响身份验证。    | 从另一台计算机映射共享以确保共享可访问。 如果凭据不再有效，可能需要更新凭据。 |
| 由于常规错误，备份失败。                    | 失败的请求可能是间歇性问题导致的。 再次尝试备份。                    | 致电支持人员。                                                                                                                               |

## <a name="next-steps"></a>后续步骤

查看[基础结构备份服务](azure-stack-backup-reference.md)的参考资料。

启用[基础结构备份服务](azure-stack-backup-enable-backup-console.md)。
