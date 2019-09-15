---
title: Azure Stack 的基础结构备份服务最佳做法 | Microsoft Docs
description: 部署和管理 Azure Stack 时，请遵循以下最佳做法，以便在发生灾难性故障时帮助减轻数据丢失。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 0b843018a90e0a07a1d1135099275288d4a4ce62
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974762"
---
# <a name="infrastructure-backup-service-best-practices"></a>基础结构备份服务最佳做法

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

部署和管理 Azure Stack 时，请遵循以下最佳做法，以便在发生灾难性故障时帮助减轻数据丢失。

定期查看最佳实践，验证在对操作流进行更改时你的安装是否仍符合要求。 如果在实施这些最佳实践时遇到任何问题，请联系 Microsoft 支持部门以获得帮助。

## <a name="configuration-best-practices"></a>配置最佳实践

### <a name="deployment"></a>部署

在部署每个 Azure Stack 云后启用基础结构备份 使用 Azure Stack PowerShell，你可以从任何客户端/服务器（具有对操作员管理 API 终结点的访问权限）计划备份。

### <a name="networking"></a>网络

路径的通用命名约定 (UNC) 字符串必须使用完全限定的域名 (FQDN)。 如果无法进行名称解析，则可以使用 IP 地址。 UNC 字符串指定资源（例如共享文件或设备）的位置。

### <a name="encryption"></a>加密

#### <a name="version-1901-and-newer"></a>版本 1901 及更新版本

加密证书用来对导出到外部存储的备份数据进行加密。 证书可以是自签名证书，因为证书仅用于传输密钥。 有关如何创建证书的详细信息，请参阅 New-selfsignedcertificate。
  
密钥必须存储在安全位置（例如，全局 Azure 密钥保管库证书）。 CER 格式的证书用于加密数据。 在 Azure Stack 的云恢复部署期间，必须使用 PFX 格式来解密备份数据。

![将证书存储在安全位置。](media/azure-stack-backup/azure-stack-backup-encryption-store-cert.png)

#### <a name="1811-and-older"></a>1811 版及更低版本

加密密钥用来对导出到外部存储的备份数据进行加密。 密钥将在[使用 PowerShell 为 Azure Stack 启用备份](azure-stack-backup-enable-backup-powershell.md)的过程中生成。

密钥必须存储在安全位置（例如，全局 Azure 密钥保管库机密）。 在重新部署 Azure Stack 期间，必须使用此密钥。

![将密钥存储在一个安全位置。](media/azure-stack-backup/azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>操作最佳实践

### <a name="backups"></a>备用

 - 备份作业在系统运行时执行，因此管理体验或用户应用无需停机。 对于合理负载下的解决方案，备份作业预计需要20-40 分钟。
 - 使用 OEM 提供的说明，手动备份的网络交换机和硬件生命周期主机（HLH）应存储在基础结构备份控制器存储控制平面备份数据的同一备份共享。 请考虑将交换机和 HLH 配置存储在区域文件夹中。 如果在同一区域中有多个 Azure Stack 实例，请考虑为属于某个缩放单元的每个配置使用一个标识符。

### <a name="folder-names"></a>文件夹名称

 - 基础结构会自动创建 MASBACKUP 文件夹。 这是由 Microsoft 管理的一个共享。 你可以在与 MASBACKUP 相同的级别创建共享。 不建议在 Azure Stack 不创建的 MASBACKUP 中创建文件夹或存储数据。
 -  在文件夹名称中使用 FQDN 和区域来区分来自不同云的备份数据。 Azure Stack 部署和终结点的 FQDN 是区域参数和外部域名参数的组合。 有关详细信息，请参阅[Azure Stack 数据中心集成-DNS](azure-stack-integrate-dns.md)。

例如，备份共享是 fileserver01.contoso.com 上托管的 AzSBackups。 在该文件共享中，每个 Azure Stack 部署可能有一个使用外部域名的文件夹和一个使用区域名称的子文件夹。

FQDN：contoso.com  
区域：nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup 文件夹是 Azure Stack 存储其备份数据的地方。 不要使用此文件夹存储您自己的数据。 Oem 不应使用此文件夹存储任何备份数据。

建议 OEM 将其组件的备份数据存储在区域文件夹下。 每个网络交换机、硬件生命周期主机（HLH）等都可能存储在其自身的子文件夹中。 例如：

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>监视

系统支持以下警报：

| 警报                                                   | 描述                                                                                     | 更正                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 备份失败，因为文件共享空间不足。 | 文件共享空间不足，备份控制器无法将备份文件导出到该位置。 | 增加更多存储容量并重试备份。 删除现有的备份（从最旧的备份开始）以释放空间。                    |
| 备份由于连接问题而失败。             | Azure Stack 与文件共享之间的网络出现了问题。                          | 解决网络问题，然后重试备份。                                                                                            |
| 由于路径中的错误，备份失败。                | 无法解析文件共享路径。                                                          | 从另一台计算机映射共享，以确保共享可供访问。 如果路径不再有效，可能需要更新该路径。       |
| 由于身份验证问题而导致备份失败。               | 可能存在影响身份验证的凭据问题或网络问题。    | 从另一台计算机映射共享，以确保共享可供访问。 如果凭据不再有效，可能需要更新凭据。 |
| 由于常规错误，备份失败。                    | 请求失败可能是由间歇性问题导致的。 再次尝试备份。                    | 致电支持人员。                                                                                                                               |

## <a name="next-steps"></a>后续步骤

查看[基础结构备份服务](azure-stack-backup-reference.md)的参考资料。

启用[基础结构备份服务](azure-stack-backup-enable-backup-console.md)。
