---
title: 通过管理员门户启用 Azure Stack 中心备份
description: 了解如何从管理员门户启用基础结构备份服务，以便在发生故障时可以还原 Azure Stack 中心。
author: justinha
ms.topic: article
ms.date: 08/21/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 08/21/2019
ms.openlocfilehash: 0aa5fca80cfc408088f34923a607d4200ece15cb
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76878201"
---
# <a name="enable-backup-for-azure-stack-hub-from-the-administrator-portal"></a>通过管理员门户启用 Azure Stack 中心备份

你可以从管理员门户启用基础结构备份服务，以便 Azure Stack 中心可以生成基础结构备份。 硬件合作伙伴可以使用这些备份在发生[灾难性故障时](./azure-stack-backup-recover-data.md)使用云恢复来还原环境。 云恢复的目的是确保操作员和用户在恢复完成后可以重新登录到门户。 用户将还原其订阅，包括：

- 基于角色的访问权限和角色。
- 原始计划和产品/服务。
- 以前定义的计算、存储和网络配额。
- Key Vault 密码。

但是，基础结构备份服务不会备份 IaaS Vm、网络配置和存储资源，例如存储帐户、blob、表等。 云恢复之后登录的用户将看不到这些以前存在的资源。 服务也不会备份 "平台即服务" （PaaS）资源和数据。

管理员和用户负责独立于基础结构备份过程来备份和还原 IaaS 和 PaaS 资源。 有关备份 IaaS 和 PaaS 资源的信息，请参阅以下链接：

- [保护 Azure Stack 集线器上部署的 Vm](../user/azure-stack-manage-vm-protect.md)
- [在 Azure 中备份应用](https://docs.microsoft.com/azure/app-service/manage-backup)
- [什么是 Azure Vm 上的 SQL Server？Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>启用或重新配置备份

1. 打开[Azure Stack 中心管理员门户](azure-stack-manage-portals.md)。
2. 选择 "**所有服务**"，然后在 "**管理**" 类别下选择 "**基础结构备份**"。 在 "**基础结构备份**" 边栏选项卡中选择 "**配置**"。
3. 键入**备份存储位置**的路径。 使用通用命名约定（UNC）字符串作为指向单独设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 对于服务，可以使用 IP 地址。 若要确保在发生灾难后备份数据的可用性，设备应位于不同的位置。

    > [!Note]  
    > 如果你的环境支持从 Azure Stack 中心基础结构网络到企业环境的名称解析，则可以使用完全限定的域名（FQDN）而不是 IP。

4. 使用域和用户名键入用户名，该**用户名**具有对读取和写入文件的足够访问权限。 例如，`Contoso\backupshareuser` 。
5. 键入用户的**密码**。
6. 再次键入密码以**确认密码**。
7. **以小时为单位的频率**决定了创建备份的频率。 默认值为 12。 计划程序最多支持12个，最多支持4个。 
8. **保持期（天**）决定在外部位置保留备份的天数。 默认值为7。 计划程序最多支持14个，最多支持2个。 早于保留期的备份将从外部位置自动删除。

    > [!Note]  
    > 如果要将备份存档早于保留期，请确保在计划程序删除备份之前备份这些文件。 如果将备份保持期（例如7天到5天）减少，计划程序将删除所有早于新保留期的备份。 请确保在更新此值之前已将备份删除。

9. 在 "加密设置" 中，在 "证书 .cer 文件" 框中提供证书。 使用证书中的此公钥对备份文件进行加密。 在配置备份设置时，提供仅包含公钥部分的证书。 首次设置此证书或在将来轮替证书时，只能查看证书的指纹。 无法下载或查看上传的证书文件。 若要创建证书文件，请运行以下 PowerShell 命令，以创建具有公钥和私钥的自签名证书，并导出只包含公钥部分的证书。 可以将证书保存到可从管理门户访问的任何位置。

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **1901 及更高**版本： Azure Stack 集线器接受用于对基础结构备份数据进行加密的证书。 请确保将公钥和私钥的证书存储在安全的位置。 出于安全原因，不建议将证书与公钥和私钥一起使用来配置备份设置。 有关如何管理此证书的生命周期的详细信息，请参阅[基础结构备份服务最佳实践](azure-stack-backup-best-practices.md)。
   > 
   > **1811 或更早版本**： Azure Stack 集线器接受对称密钥来加密基础结构备份数据。 使用[AzsEncryptionKey64 cmdlet 创建密钥](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64)。 从1811升级到1901后，备份设置将保留加密密钥。 建议将备份设置更新为使用证书。 加密密钥支持现已弃用。 至少有3个版本可更新设置以使用证书。

10. 选择 **"确定"** 保存备份控制器设置。

![Azure Stack 中心备份控制器设置](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>开始备份
若要开始备份，请单击 "**立即备份**" 以启动按需备份。 按需备份不会修改下一次计划备份的时间。 任务完成后，可以在**Essentials**中确认设置：

![Azure Stack 按需备份](media/azure-stack-backup/scheduled-backup.png)

你还可以在 Azure Stack 中心管理员计算机上运行 PowerShell cmdlet **start-azsbackup** 。 有关详细信息，请参阅[备份 Azure Stack 中心](azure-stack-backup-back-up-azure-stack.md)。

## <a name="enable-or-disable-automatic-backups"></a>启用或禁用自动备份
启用备份时，会自动安排备份。 你可以在**Essentials**中查看下一个计划备份时间。 

![Azure Stack 按需备份](media/azure-stack-backup/on-demand-backup.png)

如果需要禁用未来计划的备份，请单击 "**禁用自动备份**"。 禁用自动备份会保留配置备份设置，并保留备份计划。 此操作仅通知计划程序跳过以后的备份。

![Azure Stack 中心-禁用计划的备份](media/azure-stack-backup/disable-auto-backup.png)

确认已在**Essentials**中禁用未来计划的备份：

![Azure Stack 集线器-确认备份已禁用](media/azure-stack-backup/confirm-disable.png)

单击 "**启用自动备份**"，通知计划程序在计划的时间启动未来的备份。 

![Azure Stack 集线器-启用计划的备份](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> 如果在更新到1807之前配置了基础结构备份，将禁用自动备份。 这样，Azure Stack 中心启动的备份不会与外部任务计划引擎启动的备份发生冲突。 禁用任何外部任务计划程序后，请单击 "**启用自动备份**"。

## <a name="update-backup-settings"></a>更新备份设置
从1901，对加密密钥的支持已弃用。 如果是第一次在1901中配置备份，则必须使用证书。 只有在更新到1901之前配置了密钥，Azure Stack 中心才支持加密密钥。 向后兼容模式将继续进行三次发布。 之后，加密密钥将不再受支持。

### <a name="default-mode"></a>默认模式
在 "加密设置" 中，如果在安装或更新到1901后首次配置基础结构备份，则必须使用证书配置备份。 不再支持使用加密密钥。

若要更新用于加密备份数据的证书，请上传新的。带有公钥部分的 CER 文件，然后选择 "确定" 保存设置。

新的备份将开始使用新证书中的公钥。 对通过以前的证书创建的所有现有备份没有影响。 请确保将较旧的证书保存在安全的位置，以便在需要云恢复时使用。

![Azure Stack 中心-查看证书指纹](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>向后兼容模式
如果在更新到1901之前配置了备份，则会在不更改行为的情况下执行这些设置。 在这种情况下，支持对加密密钥进行向后兼容。 可以更新加密密钥或交换机以使用证书。 至少需要三个版本，才能继续更新加密密钥。 使用此时间转换为证书。 若要创建新的加密密钥，请使用[AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64)。

![Azure Stack 中心-使用向后兼容模式下的加密密钥](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> 从加密密钥更新到证书是一种单向操作。 做出此更改后，将无法切换回加密密钥。 所有现有备份都将保留以前的加密密钥。

![Azure Stack 中心-在向后兼容模式下使用加密证书](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>后续步骤

了解如何运行备份。 请参阅[备份 Azure Stack 中心](azure-stack-backup-back-up-azure-stack.md)。

了解如何验证备份是否已运行。 请参阅[在管理员门户中确认备份已完成](azure-stack-backup-back-up-azure-stack.md)。
