---
title: Azure Stack 更新活动清单 |Microsoft Docs
description: 用于准备您的系统以获取最新 Azure Stack 更新的清单。
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
ms.date: 12/10/2019
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: a560b37051cf3b8b54e3cfec69dc6a9b28cc1cfb
ms.sourcegitcommit: d9430072dd96ae305101da6d8a47d6c23a0a64c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995563"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack 更新活动清单

*适用于：Azure Stack 集成系统*

查看此清单，以便准备更新 Azure Stack。 本文包含有关 Azure Stack 操作员的更新相关活动的清单。

## <a name="prepare-for-azure-stack-update"></a>准备 Azure Stack 更新

| 活动                     | 详细信息                                                   |
|------------------------------|-----------------------------------------------------------|
| 查看已知问题     | [已知问题列表](known-issues.md)。                |
| 查看安全更新 | [安全更新列表](release-notes-security-updates.md)。      |
| 应用最新的 OEM 包 | 与 OEM 联系以确保系统满足你的系统正在更新到 Azure Stack 版本的最低 OEM 包要求。 确保 OEM 包与要更新到 Azure Stack 版本兼容。 如果 OEM 包与要更新到 Azure Stack 版本不兼容，则需要在运行 Azure Stack 更新之前执行 OEM 包更新。 有关说明，请参阅 "应用 Azure Stack 原始设备制造商（OEM）更新"。 |
| 可选：配置自动日志收集 | 建议在 Azure Stack 中心环境上配置自动日志收集，以便在需要打开支持票证时，简化收集系统日志的过程。 若要配置自动日志收集，请参阅[配置自动 Azure Stack 诊断日志集合](azure-stack-configure-automatic-diagnostic-log-collection.md)中的说明。 |
| 应用最新修补程序 | 应用适用于当前安装的版本的最新修补程序。 有关最新修补程序的列表，请参阅发行说明修补程序部分。 |
| 运行容量规划器工具 | 请确保使用最新版本的[Azure Stack Capacity Planner 工具](azure-stack-capacity-planning-overview.md)来执行工作负荷规划和调整大小。 最新版本包含 bug 修复，并提供随每个 Azure Stack 更新一起发布的新功能。 |
| Run Test-AzureStack | 运行 `Test-AzureStack -Group UpdateReadiness` 以确定操作问题。 Cmdlet 可通过特权终结点会话（PEP）访问。 有关详细信息，请参阅[验证 Azure Stack 系统状态](azure-stack-diagnostic-test.md)。 |
| 解决问题 | 解决 `Test-AzureStack`标识的任何操作问题。 |
| 可用更新 | 仅在连接的方案中，Azure Stack 部署定期检查受保护的终结点，并在更新可用于你的云时自动向你发出通知。 断开连接的客户可以使用[此处所述的过程](azure-stack-apply-updates.md)来下载和导入新的包。 |
| 通知用户 | 你应将任何维护操作的用户通知给用户，并且如果可能，你应在非工作时间计划正常维护时段。 维护操作可能会影响租户工作负荷和门户操作。 |

## <a name="during-azure-stack-update"></a>Azure Stack 更新期间

| 活动 | 详细信息 |
|--------------------|------------------------------------------------------------------------------------------------------|
| 管理更新 |[使用操作员门户管理 Azure Stack 中的更新](azure-stack-updates.md)。 |
|  |  |
| 监视更新 | 如果操作员门户不可用，则[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。 |
|  |  |
| 恢复更新 | 修正失败的更新后，[使用特权终结点在 Azure Stack 中恢复更新](azure-stack-monitor-update.md)。 |

> [!IMPORTANT]  
> 请勿在更新过程中运行**test-azurestack** ，因为这将导致更新停止。

## <a name="after-azure-stack-update"></a>Azure Stack 更新后

| 活动 | 详细信息 |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 应用最新修补程序 | 应用适用于更新版本的最新修补程序。 |
| 检索加密密钥 | 检索静态数据加密密钥，并将其安全地存储在 Azure Stack 部署的外部。 按照有关[如何检索密钥的说明进行](azure-stack-security-bitlocker.md)操作。 |
|  |  |
| 重新启用多租户 | 如果有多租户 Azure Stack，请确保在成功更新后[配置所有来宾目录租户](azure-stack-enable-multitenancy.md#configure-guest-directory)。 |

## <a name="next-steps"></a>后续步骤

- [查看已知问题的列表](known-issues.md)
- [查看安全更新列表](release-notes-security-updates.md)
