---
title: Azure Stack 的基础结构备份服务最佳做法 | Microsoft Docs
description: 在部署和管理 Azure Stack 时，请遵循这些最佳做法，以便在发生灾难性故障时帮助减少数据丢失。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: d54381c185b51b880d452ebf303581c068072f9a
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909831"
---
# <a name="infrastructure-backup-service-best-practices---ruggedized"></a>基础结构备份服务最佳做法-耐用

*适用于：模块化数据中心、Azure Stack 中心耐用*

定期查看这些最佳实践，验证在对操作流进行更改时你的安装是否仍符合要求。 如果在实施这些最佳实践时遇到任何问题，请联系 Microsoft 支持部门以获得帮助。

## <a name="configuration-best-practices"></a>配置最佳实践

默认情况下，在部署新系统的过程中会启用基础结构备份，并在内部存储。 使用 Azure Stack 门户或 PowerShell，你可以提供用于将备份导出到辅助位置的外部存储位置。

### <a name="networking"></a>网络

路径的通用命名约定 (UNC) 字符串必须使用完全限定的域名 (FQDN)。 如果名称解析不可行，可以使用 IP 地址 \' 。 UNC 字符串指定资源（例如共享文件或设备）的位置。

### <a name="encryption"></a>Encryption

加密证书用来对导出到外部存储的备份数据进行加密。 证书可以是自签名证书，因为证书仅用于传输密钥。 有关如何创建证书的更多信息，请参阅 New-SelfSignedCertificate。

此证书必须存储在安全的位置。 证书的 CER 格式仅用于加密数据，而不用于建立通信。

## <a name="operational-best-practices"></a>操作最佳实践

### <a name="backups"></a>备份

- 备份作业在系统运行时执行，因此不会导致 \' 管理体验或用户应用停机。 对于合理负载下的解决方案，备份作业预计需要20-40 分钟 \' 。

- 为了手动备份网络交换机和硬件生命周期主机而提供的其他说明 (HLH) 。

### <a name="folder-names"></a>文件夹名称

- 基础结构会自动创建 MASBACKUP 文件夹。 这是由 Microsoft 管理的一个共享。 你可以在与 MASBACKUP 相同的级别创建共享。 \'不建议在 Azure Stack 不创建的 MASBACKUP 中创建文件夹或存储数据 \' 。

- 在文件夹名称中使用 FQDN 和区域来区分来自不同云的备份数据。 Azure Stack 部署和终结点的 FQDN 是区域参数和外部域名参数的组合。 有关详细信息，请参阅 [Azure Stack 数据中心集成 - DNS](../../operator/azure-stack-integrate-dns.md)。

例如，备份共享是 fileserver01.contoso.com 上托管的 AzSBackups。 在该文件共享中，每个 Azure Stack 部署可能有一个使用外部域名的文件夹和一个使用区域名称的子文件夹。

- FQDN：contoso.com
- 区域：nyc

```shell
\\fileserver01.contoso.com\AzSBackups
\\fileserver01.contoso.com\AzSBackups\contoso.com
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup
```

`MASBackup`文件夹是 Azure Stack 存储其备份数据的位置。 不要使用此文件夹来存储你自己的数据。 Oem 也不应使用此文件夹存储任何备份数据。

建议 OEM 将其组件的备份数据存储在区域文件夹下。 每台网络交换机、硬件生命周期主机 (HLH) 等等可以存储在其自己的子文件夹中。 例如：

```shell
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration
```

### <a name="monitoring"></a>监视

系统支持以下警报：

| 警报                                                   | 说明                                                                                     | 补救                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 备份由于文件共享中的容量不足而失败。 | 文件共享中的容量不足，并且备份控制器无法将备份文件导出到此位置。 | 增加更多存储容量并重试备份。 删除现有的备份（从最旧的备份开始）以释放空间。                    |
| 备份由于连接问题而失败。             | Azure Stack 与文件共享之间的网络出现了问题。                          | 解决网络问题，然后重试备份。                                                                                            |
| 备份由于路径中的错误而失败。                | 无法解析文件共享路径。                                                          | 从另一台计算机映射共享，以确保共享可供访问。 如果路径不再有效，可能需要更新路径。       |
| 备份由于身份验证问题而失败。               | 可能存在影响身份验证的凭据问题或网络问题。    | 从另一台计算机映射共享，以确保共享可供访问。 如果凭据不再有效，可能需要更新凭据。 |
| 备份由于一般错误而失败。                    | 请求失败可能是由间歇性问题导致的。 重试备份。                    | 致电支持人员。                                                                                                                               |

### <a name="infrastructure-backup-service-components"></a>基础结构备份服务组件

基础结构备份服务包含以下组件：

- **基础结构备份控制器**：基础结构备份控制器的实例化，并驻留在每个 Azure Stack 云中。

- **备份资源提供程序**：备份资源提供程序 (备份 RP) 由用户界面和 api 公开 Azure Stack 基础结构的基本备份功能组成。

### <a name="infrastructure-backup-controller"></a>基础结构备份控制器

基础结构备份控制器是为 Azure Stack 云实例化的一项 Service Fabric 服务。 备份资源在区域级别创建，从 AD、CA、Azure 资源管理器、CRP、SRP、NRP、Key Vault 和 RBAC 捕获区域特定的服务数据。

### <a name="backup-resource-provider"></a>备份资源提供程序

备份资源提供程序在 Azure Stack 门户中提供了用于进行基本配置并列出备份资源的用户界面。 操作员可以在用户界面中执行以下操作：

- 通过提供外部存储位置、凭据和加密密钥首次启用备份。
- 查看已完成创建的备份资源和正在创建的资源。
- 修改备份控制器在其中放置备份数据的存储位置。
- 修改备份控制器用来访问外部存储位置的凭据。
- 修改备份控制器用来加密备份的加密证书。

## <a name="backup-controller-requirements"></a>备份控制器要求

本部分介绍基础结构备份服务的重要要求。 建议你在为 Azure Stack 实例启用备份之前仔细查看此信息，然后在部署和后续操作过程中根据需要返回该信息。

这些要求包括：

- **软件要求**：介绍支持的存储位置和大小调整指南。
- **网络要求**：描述不同存储位置的网络要求。

### <a name="software-requirements"></a>软件要求

#### <a name="supported-storage-locations"></a>支持的存储位置

| 存储位置                                                                  | 详细信息                                                                                                                                                  |
|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| 在可信网络环境中的存储设备上托管的 SMB 文件共享。 | 位于部署了 Azure Stack 的数据中心内或位于其他数据中心内的 SMB 共享。 多个 Azure Stack 实例可以使用同一个文件共享。 |
| Azure 上的 SMB 文件共享。                                                          | 目前不支持。                                                                                                                                 |
| Azure 上的 Blob 存储。                                                            | 目前不支持。                                                                                                                                 |

#### <a name="supported-smb-versions"></a>支持的 SMB 版本

|SMB  |版本  |
|---------|---------|
|SMB     |   3.x      |

#### <a name="smb-encryption"></a>SMB 加密

基础结构备份服务支持将备份数据传输到在服务器端启用了 SMB 加密的外部存储位置。 如果服务器不支持 SMB 加密或未启用该功能，则基础结构备份服务将回退到未加密的数据传输。 放置在外部存储位置上的备份数据始终是加密的，并且不依赖于 SMB 加密。

#### <a name="storage-location-sizing"></a>存储位置大小调整

建议一天至少备份两次，并保留最多7天的备份。 在 Azure Stack 上启用基础结构备份时，这是默认行为。


|环境规模  |预计的备份大小  |所需的空间总量  |
|---------|---------|---------|
|4-16 个节点     |  20 GB       |  280 GB       |
<!-- TZLASDKFIX 
|ASDK     |   10 GB      |   140 GB     |
-->
### <a name="network-requirements"></a>网络要求

|存储位置  |详细信息  |
|---------|---------|
|在可信网络环境中的存储设备上托管的 SMB 文件共享。     |  如果 Azure Stack 实例驻留在具有防火墙的环境中，则端口 445 是必需的。 基础结构备份控制器将通过端口 445 启动到 SMB 文件服务器的连接。       |
| 若要使用文件服务器的 FQDN，该名称必须可从 PEP 解析。     |         |

> [!NOTE]
> 无需打开任何入站端口。

### <a name="encryption-requirements"></a>加密要求

基础结构备份服务使用带有公钥 ( 的证书。CER) 对备份数据进行加密。 该证书用于传输密钥，而不会用于建立经过身份验证的安全通信。 出于此原因，该证书可以是自签名证书。 Azure Stack 无需验证此证书的根或信任，因此不需要外部 internet 访问。

自签名证书有两个部分，一个部分包含公钥，另一个部分包含私钥：

- 加密备份数据：包含公钥的证书（导出到 .CER 文件）用于加密备份数据。
- 解密备份数据：包含私钥的证书（导出到 .PFX 文件）用于解密备份数据。

内部机密轮换不会管理包含公钥的证书 (.CER)。 若要轮替证书，必须创建新的自签名证书，并使用新文件 ( 更新备份设置。CER) 。

所有现有备份将使用以前的公钥保持加密状态。 新备份将使用新的公钥。

出于安全原因，在云恢复过程中使用私钥 ( 的证书。Azure Stack 不会保留 PFX) 。

## <a name="infrastructure-backup-limits"></a>基础结构备份限制

在规划、部署和操作 Microsoft Azure Stack 实例时，请考虑这些限制。 下表介绍了这些限制。

|限制标识符  |限制  |注释  |
|---------|---------|---------|
|备份类型     | 仅限完整        | 基础结构备份控制器仅支持完整备份。 不支持增量备份。        |
|计划的备份     | 计划和手动        | 备份控制器支持计划备份和按需备份。        |
|最大并发备份作业数      | 1        | 备份控制器的每个实例仅支持一个活动备份作业。        |
|网络交换机配置     | 不在范围内         | 管理员必须使用 OEM 工具备份网络交换机配置。 请参阅每个 OEM 供应商提供的 Azure Stack 文档。        |
|硬件生命周期主机     | 不在范围内         | 管理员必须使用 OEM 工具备份硬件生命周期主机。 请参阅每个 OEM 供应商提供的 Azure Stack 文档。        |
|最大文件共享数     | 1        | 只能使用一个文件共享来存储备份数据。        |
|备份值-添加资源提供程序     | 范围内         | 基础结构备份包括事件中心 RP、IoT 中心 RP、Data Box Edge RP 的备份。        |

## <a name="next-steps"></a>后续步骤

- 了解有关 [基础结构备份服务](../../operator/azure-stack-backup-reference.md)的详细信息。
