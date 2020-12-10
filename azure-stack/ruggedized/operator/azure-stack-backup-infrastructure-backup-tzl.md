---
title: 使用基础结构备份服务恢复 Azure Stack 中的数据 | Microsoft Docs
description: 了解如何使用基础结构备份服务在 Azure Stack 中备份和还原配置和服务数据。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/29/2020
ms.openlocfilehash: 1941a1347e7b6ce3943a8cfae02e07a6b41fa46f
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939389"
---
# <a name="recover-data-in-azure-stack-with-the-infrastructure-backup-service"></a>使用基础结构备份服务恢复 Azure Stack 中的数据

*适用于：模块化数据中心和 Azure Stack 集线器耐用*

你可以使用 Azure Stack 基础结构备份服务来备份基础结构服务元数据。 这些备份用于修正降低的基础结构服务。 备份仅包括系统内部的基础结构服务中的数据。 备份数据不包括用户数据和应用程序数据。 必须单独保护用户和应用程序数据。

默认情况下，基础结构备份在部署时启用。 这些备份存储在系统内部，只能在高级支持方案中访问。 如果系统有权访问外部存储位置，则可以指示基础结构备份服务将备份作为辅助副本导出到该存储位置。

在启用备份服务之前，请确保已[符合要求](../../operator/azure-stack-backup-reference.md#backup-controller-requirements)。

> [!NOTE]
> 基础结构备份服务不包括用户数据和应用。 有关如何保护基于 IaaS VM 的应用的详细信息，请参阅以下文章：
>
> - [保护 Azure Stack 上部署的 VM](../../user/azure-stack-manage-vm-protect.md)
> - 保护 Azure Stack 上的事件中心时序数据
> - 保护 Azure Stack 上的 blob 数据

## <a name="the-infrastructure-backup-service"></a>基础结构备份服务

该服务包含以下功能：

| Feature                                            | 说明                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 备份基础结构服务                     | 在 Azure Stack 中跨基础结构服务的子集协调备份。 |
| 备份数据的压缩和加密 | 在将备份数据 \' 内部存储或导出到操作员提供的外部存储位置之前，系统会对其进行压缩和加密。                |
| 备份作业监视                              | 当备份作业失败时，系统会通知你如何修复该问题。                                                                                                |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>验证基础结构备份服务的要求

- **存储位置** 如果可以可靠地访问外部存储位置，则需要可通过可存储多个备份的 Azure Stack 基础结构访问文件共享。 有关选择基础结构备份服务的存储位置的详细信息，请参阅 [备份控制器要求](../../operator/azure-stack-backup-reference.md#backup-controller-requirements)。

- **凭据** 你需要可访问存储位置的用户帐户凭据。

## <a name="next-steps"></a>后续步骤

- 了解如何[从管理员门户为 Azure Stack 启用备份](../../operator/azure-stack-backup-enable-backup-console.md)。

- 了解如何[使用 PowerShell 为 Azure Stack 启用备份](../../operator/azure-stack-backup-enable-backup-powershell.md)。
