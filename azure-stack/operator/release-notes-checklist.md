---
title: Azure Stack Hub 更新活动清单
description: 为系统准备最新 Azure Stack Hub 更新的清单。
author: sethmanheim
ms.topic: article
ms.date: 02/18/2020
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: c966aa9689f97265b2bca2e9b246f266ac4a11c7
ms.sourcegitcommit: 21cdab346fc242b8848a04a124bc16c382ebc6f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777775"
---
# <a name="azure-stack-hub-update-activity-checklist"></a>Azure Stack Hub 更新活动清单

查看此清单，以便准备 Azure Stack Hub 更新。 本文包含 Azure Stack Hub 操作员的更新相关活动的清单。

## <a name="prepare-for-azure-stack-hub-update"></a>准备 Azure Stack Hub 更新

| &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 活动 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;                   | 详细信息                                                   |
|------------------------------|-----------------------------------------------------------|
| 查看已知问题     | [已知问题列表](known-issues.md)。                |
| 查看安全更新 | [安全更新列表](release-notes-security-updates.md)。      |
| 查看外接程序资源提供程序更新 | [应用服务](azure-stack-app-service-update.md)<br>[事件中心](resource-provider-apply-updates.md)<br> [MySQL](azure-stack-sql-resource-provider-update.md)<br>[SQL](azure-stack-mysql-resource-provider-update.md)<br>  |
| 应用最新的 OEM 包 | 与 OEM 联系以确保系统满足你的系统正在更新到的 Azure Stack 中心版本的最低 OEM 包要求。 确保 OEM 包与要更新到的 Azure Stack Hub 版本兼容。 如果 OEM 包与要更新到的 Azure Stack Hub 版本不兼容，则必须在运行 Azure Stack Hub 更新之前，先执行 OEM 包更新。 有关说明，请参阅“应用 Azure Stack Hub 原始设备制造商 (OEM) 更新”。 |
| 可选：配置自动日志收集 | 建议在 Azure Stack Hub 环境的基础上配置日志自动收集，以便在需要开具支持票证时简化收集系统日志的过程。 若要配置日志自动收集，请参阅[配置 Azure Stack Hub 诊断日志自动收集](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)中的说明。 |
| 应用最新修补程序 | 应用适用于当前安装版本的最新修补程序。 有关最新修补程序的列表，请参阅[发行说明修补程序](release-notes.md)部分。 |
| 运行 Capacity Planner 工具 | 请确保使用最新版本的 [Azure Stack Hub Capacity Planner 工具](azure-stack-capacity-planning-overview.md)来执行工作负荷规划和大小调整。 最新版本包含 bug 修复，并提供与每个 Azure Stack Hub 更新一起发布的新功能。 |
| 运行**test-azurestack** | 运行 `Test-AzureStack -Group UpdateReadiness` 确定操作问题。 可通过特权终结点会话 (PEP) 访问 Cmdlet。 有关详细信息，请参阅[验证 Azure Stack Hub 系统状态](azure-stack-diagnostic-test.md)。 |
| 解决问题 | 解决 `Test-AzureStack` 确定的任何操作问题。 |
| 可用更新 | 只有在联网场景中，Azure Stack Hub 部署才会定期检查安全的终结点，并在已发布云更新的情况下自动通知你。 断开连接的客户可以根据[此处所述的过程](azure-stack-apply-updates.md)下载和导入新包。 |
| 计划维护时段并通知用户 | 如果可能，你应该通知用户任何维护操作，并在非工作时间计划正常维护时段。 维护操作可能会影响现有的租户工作负荷，并导致新的租户操作（例如，创建、重新配置或删除 Vm）失败-无论操作是从门户启动还是以编程方式从 Azure 资源管理器 API 启动。 其他操作（例如备份）在更新完成之前也可能不可用。 对于 Azure Stack 集线器快速更新和完整更新，您可以查看[发行说明](release-notes.md)，了解在要应用的版本中预期更新所需的时间。 |

## <a name="during-azure-stack-hub-update"></a>在 Azure Stack Hub 更新期间

| 活动 | 详细信息 |
|--------------------|------------------------------------------------------------------------------------------------------|
| 管理更新 |[使用运营商门户管理 Azure Stack Hub 中的更新](azure-stack-updates.md)。 |
|  |  |
| 监视更新 | 如果运营商门户不可用，请[使用特权终结点监视 Azure Stack Hub 中的更新](azure-stack-monitor-update.md)。 |
|  |  |
| 恢复更新 | 在修复失败的更新后，[使用特权终结点恢复 Azure Stack Hub 中的更新](azure-stack-monitor-update.md)。 |

> [!IMPORTANT]  
> 在更新期间不要运行 **Test-AzureStack**，因为这会导致更新停止。

## <a name="after-azure-stack-hub-update"></a>Azure Stack Hub 更新后

| 活动 | 详细信息 |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 应用最新修补程序 | 应用适用于已更新版本的最新修补程序。 |
| 检索加密密钥 | 检索静态数据加密密钥，并将其安全存储在 Azure Stack Hub 部署的外部。 请遵照[有关如何检索密钥的说明](azure-stack-security-bitlocker.md)操作。 |
|  |  |
| 重新启用多租户 | 对于多租户 Azure Stack Hub，请确保在成功更新后[配置所有来宾目录租户](azure-stack-enable-multitenancy.md#configure-guest-directory)。 |

## <a name="next-steps"></a>后续步骤

- [查看已知问题列表](known-issues.md)
- [查看安全更新列表](release-notes-security-updates.md)
