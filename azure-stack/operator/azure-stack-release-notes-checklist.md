---
title: Azure Stack 发行说明-更新活动核对清单 |Microsoft Docs
description: 快速清单准备您的系统的最新的 Azure Stack 更新。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: sethm
ms.reviewer: ''
ms.lastreviewed: 05/02/2019
ms.openlocfilehash: 576ca42d231aa12d88ed307df610a769a7907622
ms.sourcegitcommit: 91c5056cb6d9bbd852132bebfbefa05b6b4d6cb3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64988123"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack 的更新活动清单

本文包含 Azure Stack 操作员与更新相关的活动的清单。 如果你已准备好将更新应用到 Azure Stack，你可以查看此信息。

## <a name="prepare-for-azure-stack-update"></a>准备 Azure Stack 更新

| 活动              | 详细信息                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| 查看已知的问题   | [已知问题列表](azure-stack-release-notes-known-issues-1904.md)。                |
| 查看安全更新 | [安全更新的列表](azure-stack-release-notes-security-updates-1904.md)。      |
| 运行 Test-AzureStack   | 运行`Test-AzureStack -Group UpdateReadiness`识别操作问题。      |
| 解决问题        | 解析标识的任何操作问题**Test-azurestack**。                |
| 应用最新修补程序 | 应用最新的修补程序应用于当前安装的版本。         |
| 运行 capacity planner 工具 | 请确保使用最新版[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner) 工具执行规划和选型工作负荷。 最新版本包含 bug 修复，并提供已发布的新功能与每个 Azure Stack 更新。 |
| 可用更新       | 只有在联网场景中，Azure Stack 部署才会定期检查安全的终结点，并在已发布云更新的情况下自动通知你。 断开连接的客户可以下载并导入新 1904年包使用[进程此处所述](azure-stack-apply-updates.md)。               |

## <a name="during-azure-stack-update"></a>在 Azure Stack 更新过程

| 活动              | 详细信息                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| 管理更新         | [在 Azure Stack 使用运营商门户中管理更新](azure-stack-updates.md)。 |
| 监视更新        | 如果运营商门户不可用，[监视使用特权终结点的 Azure Stack 中的更新](azure-stack-monitor-update.md)。 |
| 恢复更新            | 修正失败的更新之后,[恢复使用特权终结点的 Azure Stack 中的更新](azure-stack-monitor-update.md)。 |

> [!IMPORTANT]  
> 不会运行**Test-azurestack**在更新期间，与此操作将导致更新操作计划。

## <a name="after-azure-stack-update"></a>Azure Stack 更新后

| 活动              | 详细信息                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| 应用最新修补程序 | 应用最新的修补程序适用于更新版本。                          |
| 检索加密密钥 | 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](azure-stack-security-bitlocker.md)操作。 |

## <a name="next-steps"></a>后续步骤

- [查看已知问题列表](azure-stack-release-notes-known-issues-1904.md)
- [查看安全更新列表](azure-stack-release-notes-security-updates-1904.md)
