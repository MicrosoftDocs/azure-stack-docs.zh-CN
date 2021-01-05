---
title: 使用基础结构备份服务恢复 Azure Stack Hub 数据
description: 了解如何使用基础结构备份服务在 Azure Stack Hub 中备份和还原配置和服务数据。
author: PatAltimore
ms.topic: article
ms.date: 05/16/2019
ms.author: patricka
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 9d2105138501bfabe1c1244576208d485ff2d875
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871661"
---
# <a name="recover-data-in-azure-stack-hub-with-the-infrastructure-backup-service"></a>使用基础结构备份服务恢复 Azure Stack Hub 中的数据

可以使用 Azure Stack Hub 基础结构备份服务备份和还原配置和服务数据。 每个 Azure Stack Hub 安装均包含该服务的实例。 可以使用该服务创建的备份重新部署 Azure Stack Hub 云，以还原标识、安全性和 Azure 资源管理器数据。

准备好将云投入生产后，启用备份。 如果计划执行测试和验证很长时间，则不要启用备份。

在启用备份服务之前，请确保已[符合要求](#verify-requirements-for-the-infrastructure-backup-service)。

> [!Note]  
> 基础结构备份服务不包括用户数据和应用。 有关如何保护基于 IaaS VM 的应用的详细信息，请参阅[保护 Azure Stack Hub 上部署的 VM](../user/azure-stack-manage-vm-protect.md)。 若要全面了解如何保护 Azure Stack Hub 上的应用，请参阅[有关业务连续性和灾难恢复的 Azure Stack Hub 注意事项白皮书](https://aka.ms/azurestackbcdrconsiderationswp)。

## <a name="the-infrastructure-backup-service"></a>基础结构备份服务

该服务包含以下功能：

| Feature                                            | 说明                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 备份基础结构服务                     | 协调 Azure Stack Hub 中基础结构服务的一个子集的备份。 如果发生灾难，可以在重新部署过程中还原数据。 |
| 压缩和加密导出的备份数据 | 将备份数据导出到管理员提供的外部存储位置之前，系统会对其进行压缩和加密。                |
| 备份作业监视                              | 当备份作业失败时，系统会通知你如何修复该问题。                                                                                                |
| 备份管理体验                       | 备份 RP 支持启用备份。                                                                                                                         |
| 云恢复                                     | 如果发生灾难性数据丢失，可以在部署过程中使用备份还原核心 Azure Stack Hub 信息。                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>验证基础结构备份服务的要求

- **存储位置**  
  需要可从 Azure Stack Hub 访问的文件共享，其中可以包含 14 个备份。 每个备份大约为 10 GB。 文件共享应能够存储 140 GB 的备份。 有关为基础结构备份服务选择存储位置的详细信息，请参阅[备份控制器要求](azure-stack-backup-reference.md#backup-controller-requirements)。
- **凭据**  
  你需要域用户帐户和凭据。 例如，可以使用 Azure Stack Hub 管理员凭据。
- **加密证书**  
  备份文件使用证书中的公钥加密。 请确保将此证书存储在安全位置。 


## <a name="next-steps"></a>后续步骤

了解如何[从管理员门户为 Azure Stack Hub 启用备份](azure-stack-backup-enable-backup-console.md)。

了解如何[使用 PowerShell 为 Azure Stack Hub 启用备份](azure-stack-backup-enable-backup-powershell.md)。

了解如何[备份 Azure Stack Hub](azure-stack-backup-back-up-azure-stack.md)。

了解如何[从灾难性数据丢失中恢复](azure-stack-backup-recover-data.md)。
