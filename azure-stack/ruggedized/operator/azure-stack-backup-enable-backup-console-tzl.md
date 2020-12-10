---
title: 从管理员门户为 Azure Stack 启用备份 | Microsoft Docs
description: 了解如何通过管理员门户启用基础结构备份服务，以便出现故障时可以还原 Azure Stack。
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
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 3864183ecda856500db1fcbfe38df84d70f3bae9
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939472"
---
# <a name="configure-backup-for-azure-stack-hub-from-the-administrator-portal"></a>从管理员门户配置 Azure Stack 集线器的备份

*适用于：模块化数据中心、Azure Stack 中心耐用*

你可以从管理员门户配置基础结构备份服务，将基础结构备份导出到外部存储位置。 基础结构备份可由支持人员用来修复降级的服务。

## <a name="configure-backup"></a>配置备份

1. 打开 [Azure Stack 管理员门户](../../operator/azure-stack-manage-portals.md)。

2. 选择 " **所有服务**"，然后在 " **管理** " 类别下选择 " **基础结构备份**"。 在“基础结构备份”  边栏选项卡中选择“配置”  。

3. 键入 **备份存储位置** 的路径。 使用通用命名约定 (UNC) 字符串表示单独的设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 对于服务，可以使用 IP 地址。 若要确保在发生灾难后备份数据的可用性，设备应位于不同的位置。

    > [!NOTE]  
    > 如果环境支持从 Azure Stack 基础结构网络到企业环境的名称解析，则可以使用完全限定的域名 (FQDN) 而不是 IP。

4. 使用域和用户名键入用户名，该 **用户名** 具有对读取和写入文件的足够访问权限;例如， **Contoso\backupshareuser**。

5. 键入用户的 **密码**。

6. 再次键入密码以 **确认密码**。

7. “频率(小时)”  决定了以何频率创建备份。 默认值为 12。 计划程序支持的最小值为4，最大值为12。

8. “保留期(天)”  决定了备份在外部位置保留多少天。 默认值为 7。 计划程序支持的最小值为2，最大值为14。 超过保留期的备份会自动从外部位置删除。

   > [!NOTE]
   > 如果希望对超过保留期的备份进行存档，请确保在计划程序删除备份之前对这些文件进行备份。 如果缩短备份保留期 (例如，从7天到5天) ，计划程序将删除早于新保留期的所有备份。 在更新此值之前，请确保删除备份没有问题。

9. 在 " **加密设置**" 中，公共证书指纹适用于部署期间提供的证书。 无需更新现有证书。

10. 选择“确定”  以保存备份控制器设置。

    ![Azure Stack - 备份控制器设置](media/azure-stack-backup-enable-backup-console-tzl/backup-controller-settings-certificate.png)

## <a name="enable-or-disable-automatic-backups"></a>启用或禁用自动备份

在部署过程中会自动启用备份。 配置存储位置之后，自动备份会根据频率设置，将备份按计划导出到外部存储位置。 可以在 " **概要** " 边栏选项卡中查看下一个计划的备份时间。

![Azure Stack - 按需备份](media/azure-stack-backup-enable-backup-console-tzl/on-demand-backup.png)

如果需要禁用未来计划的备份，请选择 " **禁用自动备份**"。 禁用自动备份将保留所配置的备份设置，并将保留备份计划。 此操作指示计划程序跳过以后的备份。

![Azure Stack - 禁用计划的备份](media/azure-stack-backup-enable-backup-console-tzl/disable-auto-backup.png)

在“概要”  中确认已禁用了将来的已计划备份：

![Azure Stack - 确认备份已禁用](media/azure-stack-backup-enable-backup-console-tzl/confirm-disable.png)

选择 " **启用自动备份** "，通知计划程序在计划的时间启动未来的备份。

![Azure Stack - 启用计划的备份](media/azure-stack-backup-enable-backup-console-tzl/enable-auto-backup.png)

## <a name="update-backup-settings"></a>更新备份设置

若要更新用于加密备份数据的证书，请上传包含公钥部分的新 .CER 文件，然后选择“确定”以保存设置。

新的备份将开始使用新证书中的公钥。 使用旧证书创建的所有现有备份不会受到影响。 请确保将较旧的证书保存在安全的位置，以便在需要云恢复时使用。

![Azure Stack - 查看证书指纹](media/azure-stack-backup-enable-backup-console-tzl/encryption-settings-thumbprint.png)

## <a name="next-steps"></a>后续步骤

若要验证备份是否已运行，请参阅 [在管理员门户中确认备份已完成](../../operator/azure-stack-backup-back-up-azure-stack.md)。
