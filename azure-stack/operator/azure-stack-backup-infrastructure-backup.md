---
title: 通过基础结构备份服务恢复 Azure Stack 集线器中的数据
description: 了解如何使用基础结构备份服务备份和还原 Azure Stack 集线器中的配置和服务数据。
author: justinha
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: a9ebb725bed0472eaacc80e821840746966104dd
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874325"
---
# <a name="recover-data-in-azure-stack-hub-with-the-infrastructure-backup-service"></a>通过基础结构备份服务恢复 Azure Stack 集线器中的数据

您可以使用 Azure Stack 中心基础结构备份服务来备份和还原配置和服务数据。 每个 Azure Stack 集线器安装都包含服务的实例。 可以使用服务创建的备份来重新部署 Azure Stack 中心云，以还原标识、安全性和 Azure 资源管理器数据。

准备好将云置于生产环境中时，启用备份。 如果你计划长时间执行测试和验证，请不要启用备份。

在启用备份服务之前，请确保[满足要求](#verify-requirements-for-the-infrastructure-backup-service)。

> [!Note]  
> 基础结构备份服务不包含用户数据和应用。 有关如何保护基于 IaaS VM 的应用的详细信息，请参阅[保护 Azure Stack 集线器上部署的 vm](../user/azure-stack-manage-vm-protect.md)。 有关如何保护 Azure Stack 集线器上的应用的全面了解，请参阅[业务连续性和灾难恢复白皮书中的 Azure Stack 中心注意事项](https://aka.ms/azurestackbcdrconsiderationswp)。

## <a name="the-infrastructure-backup-service"></a>基础结构备份服务

此服务包含以下功能：

| 功能                                            | Description                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 备份基础结构服务                     | 在 Azure Stack Hub 中跨基础结构服务的子集协调备份。 如果发生灾难，可以在重新部署过程中还原数据。 |
| 导出的备份数据的压缩和加密 | 在将备份数据导出到管理员提供的外部存储位置之前，系统会对其进行压缩和加密。                |
| 备份作业监视                              | 如果备份作业失败，则系统会通知你，以及如何解决此问题。                                                                                                |
| 备份管理体验                       | 备份 RP 支持启用备份。                                                                                                                         |
| 云恢复                                     | 如果发生灾难性数据丢失，则可以使用备份来还原作为部署一部分的核心 Azure Stack 中心信息。                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>验证基础结构备份服务的要求

- **存储位置**  
  需要可从 Azure Stack 中心访问的文件共享，该文件可以包含七个备份。 每个备份约为 10 GB。 共享应能存储 140 GB 的备份。 有关选择基础结构备份服务的存储位置的详细信息，请参阅[备份控制器要求](azure-stack-backup-reference.md#backup-controller-requirements)。
- **凭据**  
  你需要域用户帐户和凭据。 例如，可以使用 Azure Stack 中心管理员凭据。
- **加密证书**  
  使用证书中的公钥对备份文件进行加密。 请确保将此证书存储在安全的位置。 


## <a name="next-steps"></a>后续步骤

了解如何[从管理员门户启用 Azure Stack 集线器的备份](azure-stack-backup-enable-backup-console.md)。

了解如何[使用 PowerShell 为 Azure Stack 集线器启用备份](azure-stack-backup-enable-backup-powershell.md)。

了解如何[备份 Azure Stack 中心](azure-stack-backup-back-up-azure-stack.md)。

了解如何[从灾难性数据丢失中恢复](azure-stack-backup-recover-data.md)。
