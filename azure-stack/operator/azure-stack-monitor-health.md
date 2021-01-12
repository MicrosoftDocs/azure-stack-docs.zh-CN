---
title: 在 Azure Stack Hub 中监视运行状况和警报
description: 了解如何在 Azure Stack Hub 中监视运行状况和警报。
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 00357a7b16ee1ec6b158ea881d9f4a2c77d69e72
ms.sourcegitcommit: a90b146769279ffbdb09c68ca0506875a867e177
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98123726"
---
# <a name="monitor-health-and-alerts-in-azure-stack-hub"></a>在 Azure Stack Hub 中监视运行状况和警报

Azure Stack Hub 包含基础结构监视功能，有助于查看 Azure Stack Hub 区域的运行状况和警报。 “区域管理”磁贴列出了 Azure Stack Hub 的所有已部署区域。  它默认固定在默认提供商订阅的管理员门户上。 该磁贴除了显示每个区域的活动严重警报和警告警报数目以外， 也是 Azure Stack Hub 运行状况和警报功能的入口点。

![Azure Stack Hub 管理员门户中的“区域管理”磁贴](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack-hub"></a>了解 Azure Stack Hub 中的运行状况

运行状况资源提供程序管理运行状况和警报。 在 Azure Stack Hub 部署和配置期间，Azure Stack Hub 基础结构组件将注册到运行状况资源提供程序。 注册后，可以显示每个组件的运行状况和警报。 Azure Stack Hub 中的“运行状况”是个简单的概念。 如果组件的已注册实例存在警报，该组件的运行状况会反映最不利的活动警报的严重性：警告或严重。

## <a name="alert-severity-definition"></a>警报严重性定义

Azure Stack Hub 引发的警报只有两个严重级别：**警告** 和 **严重**。

- **警告**  
  操作员可以按计划方式处理警告警报。 该警报通常不会影响用户工作负荷。

- **严重**  
  操作员应紧急处理严重警报。 这些警报指示目前影响或将很快影响 Azure Stack Hub 用户的问题。


## <a name="view-and-manage-component-health-state"></a>查看和管理组件运行状况

可以通过管理员门户以及 REST API 和 PowerShell 查看组件的运行状况。

若要在门户中查看运行状况，请在“区域管理”磁贴中单击想要查看的区域。  可以查看基础结构角色和资源提供程序的运行状况。

![基础结构角色列表](media/azure-stack-monitor-health/image2.png)

可以单击资源提供程序或基础结构角色来查看更详细的信息。

> [!WARNING]  
> 如果单击基础结构角色，然后单击角色实例，则会看到“启动”、“重启”或“关机”选项。    对集成系统应用更新时，请勿使用这些操作。 此外，**请勿** 在 Azure Stack 开发工具包 (ASDK) 环境中使用这些选项。 这些选项是针对每个基础结构角色具有多个角色实例的集成系统环境设计的。 在 ASDK 中重启角色实例（特别是 AzS-Xrp01）会导致系统不稳定。 如需故障排除方面的帮助，请在 [Azure Stack Hub 论坛](https://aka.ms/azurestackforum)中提问。
>

## <a name="view-alerts"></a>查看警报

可直接从“区域管理”边栏选项卡查看每个 Azure Stack Hub 区域的活动警报列表。  默认配置中的第一个磁贴是“警报”磁贴，其中显示区域的严重警报和警告警报摘要。  如同此边栏选项卡中的其他磁贴一样，可将“警报”磁贴固定到仪表板，以便快速访问。

![Azure Stack Hub 管理员门户中显示警告的“警报”磁贴](media/azure-stack-monitor-health/image3.png)

 若要查看区域的所有活动警报的列表，请选择“警报”磁贴的顶部。  若要查看警报的筛选列表（“严重”或“警告”），请选择磁贴中的“严重”或“警告”行项。  

“警报”边栏选项卡支持按状态（“活动”或“已关闭”）和严重性（“严重”或“警告”）进行筛选。  默认视图显示所有活动警报。 所有已关闭的警报在七天后将从系统中删除。

>[!Note]
>如果某个警报保持活动状态，但超过一天仍未更新，可运行 [Test-AzureStack](azure-stack-diagnostic-test.md)，如果未报告问题，则可关闭该警报。

![在 Azure Stack Hub 管理员门户的“筛选器”窗格中按严重或警告状态进行筛选](media/azure-stack-monitor-health/alert-view.png)

“视图 API”操作显示用于生成列表视图的 REST API。  借助此操作可以快速熟悉可用于查询警报的 REST API 语法。 可在自动化中使用此 API，或者将它与现有的数据中心监视、报告和票证解决方案相集成。

可以单击特定的警报来查看警报详细信息。 警报详细信息显示与警报关联的所有字段，并可让用户快速导航到受影响的组件和警报源。 例如，如果某个基础结构角色实例脱机或不可访问，则会发生以下警报。  

![Azure Stack Hub 管理员门户中的“警报详细信息”边栏选项卡](media/azure-stack-monitor-health/alert-detail.png)

## <a name="alert-remediation"></a>警报修正

### <a name="automated-remediation"></a>自动修正

某些警报支持 **修复** 选项，如上图所示。 选中以后，“修复”操作会执行特定于警报的步骤来尝试解决问题。 选中以后，“修复”操作的状态会以门户通知的形式提供。

![正在进行的“修复警报”操作](media/azure-stack-monitor-health/repair-in-progress.png)

“修复”操作会在同一门户通知边栏选项卡中报告成功完成了操作或无法完成操作。  如果某项“修复”操作因出现警报而失败，则可在警报详细信息中重新运行“修复”操作。 如果“修复”操作成功完成，**请勿** 重新运行“修复”操作。 基础结构角色实例重新联机后，会自动关闭此警报。

![“修复”操作成功完成](media/azure-stack-monitor-health/repair-completed.png)

### <a name="manual-remediation"></a>手动修正

如果不支持 " **修复** " 选项，请确保遵循警报中提供的一组完整的修正说明。 例如，内部证书过期修正步骤将指导你完成秘密旋转过程：

![证书过期修正](media/azure-stack-monitor-health/certificate-expiration.png)

### <a name="alert-closure"></a>警报关闭

当问题解决时，许多（但不是每个警报）将自动关闭。 如果 Azure Stack 集线器解决该问题，则 "提供修复操作" 按钮的警报将自动关闭。 对于所有其他警报，请在执行补救步骤之后选择“关闭警报”。 如果问题仍然存在，Azure Stack 集线器会生成新的警报。 如果解决了问题，警报将保持关闭，无需采取其他步骤。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack Hub 中管理更新](azure-stack-updates.md)

[Azure Stack Hub 中的区域管理](azure-stack-region-management.md)
