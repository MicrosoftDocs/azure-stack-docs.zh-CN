---
title: Azure Stack Hub 的基础结构备份服务最佳做法
description: 在部署和管理 Azure Stack Hub 时，请遵循这些最佳做法，这样有助于在发生灾难性故障时减少数据丢失。
author: justinha
ms.topic: article
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: a141beed4df6b34175f37d9e1e60e694f3ab71f2
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77700504"
---
# <a name="infrastructure-backup-service-best-practices"></a>基础结构备份服务最佳做法

在部署和管理 Azure Stack Hub 时，请遵循这些最佳做法，这样有助于在发生灾难性故障时减少数据丢失。

定期复查最佳做法以验证当对操作流进行了更改时你的安装是否仍然符合最佳做法。 如果在实施这些最佳实践时遇到任何问题，请联系 Microsoft 支持部门以获得帮助。

## <a name="configuration-best-practices"></a>配置最佳实践

### <a name="deployment"></a>部署

在部署每个 Azure Stack Hub 云后启用基础结构备份。 可以使用 Azure Stack Hub PowerShell 计划从任何能够访问操作员管理 API 终结点的客户端/服务器进行的备份。

### <a name="networking"></a>网络

路径的通用命名约定 (UNC) 字符串必须使用完全限定的域名 (FQDN)。 如果无法进行名称解析，则可以使用 IP 地址。 UNC 字符串指定资源（例如共享文件或设备）的位置。

### <a name="encryption"></a>加密

#### <a name="version-1901-and-newer"></a>版本 1901 及更新版本

加密证书用来对导出到外部存储的备份数据进行加密。 证书可以是自签名证书，因为证书仅用于传输密钥。 有关如何创建证书的更多信息，请参阅 New-SelfSignedCertificate。
  
密钥必须存储在安全位置（例如，全局 Azure 密钥保管库证书）。 CER 格式的证书用于加密数据。 在对 Azure Stack Hub 进行云恢复部署期间，必须使用 PFX 格式来解密备份数据。

![将证书存储在安全位置。](media/azure-stack-backup/azure-stack-backup-encryption-store-cert.png)

#### <a name="1811-and-older"></a>1811 版及更低版本

加密密钥用来对导出到外部存储的备份数据进行加密。 此密钥在[使用 PowerShell 为 Azure Stack Hub 启用备份](azure-stack-backup-enable-backup-powershell.md)的过程中生成。

密钥必须存储在安全位置（例如，全局 Azure 密钥保管库机密）。 在重新部署 Azure Stack Hub 期间，必须使用此密钥。

![将密钥存储在一个安全位置。](media/azure-stack-backup/azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>操作最佳实践

### <a name="backups"></a>备份

 - 备份作业在系统正在运行时执行，因此管理体验或用户应用不会出现停机的情况。 对于负载合理的解决方案，备份作业预计要花费 20-40 分钟。
 - 根据 OEM 提供的说明，应将手动备份的网络交换机和硬件生命周期主机 (HLH) 存储在基础结构备份控制器存储控制平面备份数据的同一备份共享上。 请考虑将交换机和 HLH 配置存储在区域文件夹中。 如果在同一区域中有多个 Azure Stack Hub 实例，请考虑对属于某个缩放单元的每个配置使用一个标识符。

### <a name="folder-names"></a>文件夹名称

 - 基础结构会自动创建 MASBACKUP 文件夹。 这是由 Microsoft 管理的一个共享。 你可以在与 MASBACKUP 相同的级别创建共享。 建议不要在 Azure Stack Hub 未创建的 MASBACKUP 内创建文件夹或存储数据。
 -  在文件夹名称中使用 FQDN 和区域来区分来自不同云的备份数据。 Azure Stack Hub 部署和终结点的 FQDN 是区域参数和外部域名参数的组合。 有关详细信息，请参阅 [Azure Stack Hub 数据中心集成 - DNS](azure-stack-integrate-dns.md)。

例如，备份共享是 fileserver01.contoso.com 上托管的 AzSBackups。 在该文件共享中，每个 Azure Stack Hub 部署可能有一个使用外部域名的文件夹和一个使用区域名称的子文件夹。

FQDN：contoso.com  
区域：nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup 文件夹是 Azure Stack Hub 存储其备份数据的地方。 不要使用此文件夹来存储你自己的数据。 OEM 也不应使用此文件夹来存储任何备份数据。

建议 OEM 将其组件的备份数据存储在区域文件夹下。 每台网络交换机、硬件生命周期主机 (HLH) 等等可以存储在其自己的子文件夹中。 例如：

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>监视

系统支持以下警报：

| 警报                                                   | 说明                                                                                     | 补救                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 备份由于文件共享中的容量不足而失败。 | 文件共享中的容量不足，并且备份控制器无法将备份文件导出到此位置。 | 增加更多存储容量并重试备份。 删除现有的备份（从最旧的备份开始）以释放空间。                    |
| 备份由于连接问题而失败。             | Azure Stack Hub 与文件共享之间的网络出现了问题。                          | 解决网络问题，然后重试备份。                                                                                            |
| 备份由于路径中的错误而失败。                | 无法解析文件共享路径。                                                          | 从另一台计算机映射共享，以确保共享可供访问。 如果路径不再有效，可能需要更新路径。       |
| 备份由于身份验证问题而失败。               | 可能存在影响身份验证的凭据问题或网络问题。    | 从另一台计算机映射共享，以确保共享可供访问。 如果凭据不再有效，可能需要更新凭据。 |
| 备份由于一般错误而失败。                    | 请求失败可能是由间歇性问题导致的。 重试备份。                    | 致电支持人员。                                                                                                                               |

## <a name="next-steps"></a>后续步骤

查看[基础结构备份服务](azure-stack-backup-reference.md)的参考资料。

启用[基础结构备份服务](azure-stack-backup-enable-backup-console.md)。
