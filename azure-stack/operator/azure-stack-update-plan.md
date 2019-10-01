---
title: 规划 Azure Stack 更新 |Microsoft Docs
description: 了解计划 Azure Stack 更新。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 60515f257dc8ab04e351d1353919a749e9cf4224
ms.sourcegitcommit: 79ead51be63c372b23b7fca6ffeaf95fd44de786
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687962"
---
# <a name="plan-for-an-azure-stack-update"></a>规划 Azure Stack 更新

适用范围：*Azure Stack 集成系统*

您可以准备 Azure Stack，使更新过程尽可能顺利地进行，以便对用户的影响最小。 在本文中，请查看向用户通知可能发生的服务中断的步骤，以及准备更新的步骤。

## <a name="notify-your-users-of-maintenance-operations"></a>通知用户维护操作

你应将任何维护操作的用户通知给用户，并且如果可能，你应在非工作时间计划正常维护时段。 维护操作可能会同时影响租户工作负荷和门户操作。

## <a name="prepare-for-an-azure-stack-update"></a>准备 Azure Stack 更新

可以通过确保应用了所有修补程序、安全修补程序、OEM 更新、验证了 Azure Stack 实例的运行状况、检查可用容量并查看更新包，来准备更新。

1. 查看已知问题。 有关说明，请参阅[Azure Stack 已知问题](https://docs.microsoft.com/azure-stack/operator/release-notes)。

2. 查看安全更新。 有关更新列表，请参阅[Azure Stack 安全更新](https://docs.microsoft.com/azure-stack/operator/release-notes-security-updates)。

3. 在开始安装此更新之前，请运行 [Test-AzureStack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看处于活动状态的警报并解决任何需要采取操作的警报。

    1. 从可以通过网络访问 Azure Stack ERCS VM 的计算机打开特权终结点会话。 有关使用 PEP 的说明，请参阅[在 Azure Stack 中使用特权终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)。

    2. Test-azurestack-Group UpdateReadiness

    3. 查看输出并解决任何运行状况错误。 有关详细信息，请参阅[运行 Azure Stack 的验证测试](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test)。

4. 解决问题。 若要了解有关已标记问题的详细信息，可以参阅监视运行状况和警报主题。 有关说明，请参阅[在 Azure Stack 中监视运行状况和警报](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-health)。

5. 应用最新的修补程序。 有关最新修补程序的列表，请参阅发行说明修补程序部分。 应用最新的修补程序后，重复步骤3和4。

6. 确保 OEM 包与要更新到 Azure Stack 版本兼容。 如果 OEM 包与要更新到 Azure Stack 版本不兼容，则需要在运行 Azure Stack 更新之前执行 OEM 包更新。 有关说明，请参阅 "应用 Azure Stack 原始设备制造商（OEM）更新"。 在应用 OEM 包更新后，重复步骤3和4。

7. 运行容量规划器工具。 有关使用该工具的概述和说明，请参阅[Azure Stack 容量计划概述](https://docs.microsoft.com/azure-stack/operator/azure-stack-capacity-planning-overview)。

8. 查看更新包。 在规划维护时段时，请务必查看发布的特定类型的更新包，如发行说明中所述。

## <a name="next-steps"></a>后续步骤

[应用 Azure Stack 更新](azure-stack-apply-updates.md)。
