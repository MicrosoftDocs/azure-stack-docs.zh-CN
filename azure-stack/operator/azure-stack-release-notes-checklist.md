---
title: Azure Stack 发行说明 - 更新活动清单 | Microsoft Docs
description: 为系统准备最新 Azure Stack 更新的快速清单。
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
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: fd76e862ec98de1a22caca96295173e494d8faff
ms.sourcegitcommit: c1e10ea5a9fdf1ffc62f84c30aad4d7518050bac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68352909"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack 更新活动清单

本文包含 Azure Stack 操作员的更新相关活动的清单。 如果准备将更新应用到 Azure Stack, 则可以查看此信息。

## <a name="prepare-for-azure-stack-update"></a>准备 Azure Stack 更新

| activities              | 详细信息                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| 查看已知问题   | [已知问题列表](azure-stack-release-notes-known-issues-1906.md)。                |
| 查看安全更新 | [安全更新列表](azure-stack-release-notes-security-updates-1906.md)。      |
| 应用最新的 OEM 包 | 联系你的 OEM 以确保你的系统满足你的系统正在更新到 Azure Stack 版本的最低 OEM 包要求。 |
| Run Test-AzureStack   | 运行 `Test-AzureStack -Group UpdateReadiness` 确定操作问题。      |
| 解决问题        | 解决 **Test-AzureStack** 确定的任何操作问题。                |
| 应用最新修补程序 | 应用适用于当前安装版本的最新修补程序。         |
| 运行 Capacity Planner 工具 | 请确保使用最新版本的 [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner)  工具来执行工作负荷规划和大小调整。 最新版本包含 bug 修复，并提供与每个 Azure Stack 更新一起发布的新功能。 |
| 可用更新       | 只有在联网场景中，Azure Stack 部署才会定期检查安全的终结点，并在已发布云更新的情况下自动通知你。 断开连接的客户可以使用[此处所述的过程](azure-stack-apply-updates.md)下载并导入新的1906包。               |

## <a name="during-azure-stack-update"></a>在 Azure Stack 更新期间

| activities              | 详细信息                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| 管理更新         | [使用运营商门户管理 Azure Stack 中的更新](azure-stack-updates.md)。 |
| 监视更新        | 如果运营商门户不可用，请[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。 |
| 恢复更新            | 在修复失败的更新后，[使用特权终结点恢复 Azure Stack 中的更新](azure-stack-monitor-update.md)。 |

> [!IMPORTANT]  
> 在更新期间不要运行 **Test-AzureStack**，因为这会导致更新停止。

## <a name="after-azure-stack-update"></a>Azure Stack 更新后

| activities              | 详细信息                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| 应用最新修补程序 | 应用适用于已更新版本的最新修补程序。                          |
| 检索加密密钥 | 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](azure-stack-security-bitlocker.md)操作。 |

## <a name="next-steps"></a>后续步骤

- [查看已知问题列表](azure-stack-release-notes-known-issues-1906.md)
- [查看安全更新列表](azure-stack-release-notes-security-updates-1906.md)
