---
title: 使用基础结构备份服务对 Azure Stack 进行备份和数据恢复 | Microsoft Docs
description: 可以使用基础结构备份服务备份和还原配置和服务数据。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 05bdf2fe5930777d44ec42eab8dede97b758cd15
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268838"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>使用基础结构备份服务对 Azure Stack 进行备份和数据恢复

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用基础结构备份服务备份和还原配置和服务数据。 每个 Azure Stack 安装均包含该服务的实例。 通过重新部署 Azure Stack 云的服务创建的备份可用于还原标识、 安全性和 Azure 资源管理器数据。 

准备好将云投入生产后，可以启用备份。 如果计划执行测试和验证很长时间，则不要启用备份。

在启用备份服务之前，请确保已[符合要求](#verify-requirements-for-the-infrastructure-backup-service)。

> [!Note]  
> 基础结构备份服务不包括用户数据和应用程序。 有关详细信息，请参阅[保护在 Azure Stack 上部署的 Vm](../user/azure-stack-manage-vm-protect.md)详细了解如何保护 IaaS VM 基于应用程序。 有关如何保护 Azure Stack 上的应用程序的全面地理解，请参阅[业务连续性和灾难恢复白皮书的 Azure Stack 注意事项](https://aka.ms/azurestackbcdrconsiderationswp)。

## <a name="the-infrastructure-backup-service"></a>基础结构备份服务

这些服务包含以下功能。

| Feature                                            | 描述                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 备份基础结构服务                     | 协调 Azure Stack 中基础结构服务的一个子集的备份。 如果发生灾难，可以在重新部署过程中还原数据。 |
| 压缩和加密导出的备份数据 | 将备份数据导出到管理员提供的外部存储位置之前，系统会对其进行压缩和加密。                |
| 备份作业监视                              | 当备份作业失败和需要修正步骤时，系统会发出通知。                                                                                                |
| 备份管理体验                       | 备份 RP 支持启用备份。                                                                                                                         |
| 云恢复                                     | 如果发生灾难性数据丢失，可以在部署过程中使用备份还原核心 Azure Stack 信息。                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>验证基础结构备份服务的要求

- **存储位置**  
  需要可从 Azure Stack 访问的文件共享，其中可以包含七个备份。 每个备份大约为 10 GB。 共享应能够存储 140 GB 的备份。 有关为 Azure Stack 基础结构备份服务选择存储位置的详细信息，请参阅[备份控制器要求](azure-stack-backup-reference.md#backup-controller-requirements)。
- **凭据**  
  需要域用户帐户和凭据，例如，可以使用 Azure Stack 管理员凭据。
- **加密证书**  
  备份文件使用证书中的公钥加密。 请确保将此证书存储在安全位置。 


## <a name="next-steps"></a>后续步骤

了解如何[从管理门户为 Azure Stack 启用备份](azure-stack-backup-enable-backup-console.md)。

了解如何[使用 PowerShell 为 Azure Stack 启用备份](azure-stack-backup-enable-backup-powershell.md)。

了解如何[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )

了解如何[从灾难性数据丢失中恢复](azure-stack-backup-recover-data.md)
