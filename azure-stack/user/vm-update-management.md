---
title: Azure Stack 中心中的 VM 更新和管理自动化
description: 了解如何使用 Azure 自动化中的用于 VM 的 Azure Monitor、更新管理、更改跟踪和清单解决方案来管理 Azure Stack 中心部署的 Windows 和 Linux Vm。
author: mattbriggs
ms.topic: article
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 838ce4ab909019ce14569a8c5ad5da0b19457a90
ms.sourcegitcommit: f082a8aa7e9cfaeab21b750d58520db58147ff1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679699"
---
# <a name="vm-update-and-management-automation-in-azure-stack-hub"></a>Azure Stack 中心中的 VM 更新和管理自动化
使用以下 Azure 自动化解决方案功能管理使用 Azure Stack 中心部署的 Windows 和 Linux 虚拟机（Vm）：

- **[更新管理](https://docs.microsoft.com/azure/automation/automation-update-management)**：使用更新管理解决方案，你可以快速评估所有代理计算机上可用更新的状态，并管理为 Windows 和 Linux vm 安装所需更新的过程。

- **[更改跟踪](https://docs.microsoft.com/azure/automation/automation-change-tracking)**：受监视服务器上已安装软件、windows 服务、windows 注册表和文件以及 Linux 守护程序的更改会发送到云中的 Azure Monitor 服务进行处理。 逻辑应用于接收的数据，云服务则记录数据。 通过使用更改跟踪仪表板上的信息，可以轻松查看对服务器基础结构所做的更改。

- **[清单](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**。 Azure Stack 中心 VM 的库存跟踪提供一个基于浏览器的用户界面，用于设置和配置清单收集。

- **[用于 VM 的 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)**：用于 VM 的 Azure Monitor 按比例监视 Azure，Azure Stack 中心 vm 和虚拟机规模集。 它分析 Windows 和 Linux Vm 的性能和运行状况，还监视其进程以及其他资源和外部进程的依赖项。

> [!IMPORTANT]
> 这些解决方案与用于管理 Azure Vm 的解决方案相同。 使用相同的工具，通过相同的方式管理 Azure 和 Azure Stack 中心 Vm。 将更新管理、更改跟踪、清单和用于 VM 的 Azure Monitor 解决方案与 Azure Stack 中心结合使用时，Azure Stack 中心 Vm 也会与 Azure Vm 定价相同。

## <a name="prerequisites"></a>先决条件
使用这些功能更新和管理 Azure Stack 中心 Vm 之前，必须先满足一些先决条件。 其中包括 Azure 门户中必须执行的步骤，还包括 Azure Stack 中心管理门户。

### <a name="in-the-azure-portal"></a>在 Azure 门户中
若要将用于 VM 的 Azure Monitor、清点、更改跟踪和更新管理 Azure 自动化功能用于 Azure Stack 中心 Vm，首先需要在 Azure 中启用这些解决方案。

> [!TIP]
> 如果已为 Azure Vm 启用了这些功能，则可以使用预先存在的 LogAnalytics 工作区凭据。 如果已有要使用的 LogAnalytics WorkspaceID 和 Primary Key，请跳到[下一节](./vm-update-management.md#in-the-azure-stack-hub-administrator-portal)。 否则，请继续阅读本部分以创建新的 LogAnalytics 工作区和自动化帐户。

启用这些解决方案的第一步是在 Azure 订阅中[创建 LogAnalytics 工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)。 Log Analytics 工作区是一种独特的 Azure Monitor 日志环境，其中包含自身的数据存储库、数据源和解决方案。 创建工作区后，请记下 "WorkspaceID" 和 "密钥"。 若要查看此信息，请参阅 "工作区" 边栏选项卡，单击 "**高级设置**"，并查看 "**工作区 ID** " 和 "**主密钥**" 值。 

接下来，必须[创建一个自动化帐户](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)。 自动化帐户是 Azure 自动化资源的容器。 它提供一种方法来分离环境或进一步组织自动化工作流和资源。 创建自动化帐户后，需要启用库存、更改跟踪和更新管理功能。 若要启用每项功能，请执行以下步骤：

1. 在 Azure 门户中，请参阅要使用的自动化帐户。

2. 选择要启用的解决方案（**清单**、**更改跟踪**或**更新管理**）。

3. 使用 "**选择工作区 ...** " 下拉列表选择要使用的 Log Analytics 工作区。

4. 验证所有剩余的信息是否正确，然后单击 "**启用**" 以启用该解决方案。

5. 重复步骤2-4 以启用所有这三种解决方案。 

   [![](media//vm-update-management/1-sm.PNG "Enable Azure Automation account features")](media//vm-update-management/1-lg.PNG)

### <a name="enable-azure-monitor-for-vms"></a>启用用于 VM 的 Azure Monitor

用于 VM 的 Azure Monitor 按比例监视 Azure Vm 和虚拟机规模集。 它分析 Windows 和 Linux Vm 的性能和运行状况，还监视其进程以及其他资源和外部进程的依赖项。

作为一种解决方案，用于 VM 的 Azure Monitor 包括对监视本地或其他云提供程序中的 Vm 的性能和应用依赖项的支持。 三个主要功能提供深入的见解：

1. 运行 Windows 和 Linux 的 Azure Vm 的逻辑组件，这些组件按照预配置的运行状况条件进行度量，并在满足计算条件时向你发出警报。

2. 预定义的趋势性能图表，显示来宾 VM 操作系统中的核心性能指标。

3. 依赖关系映射，用于显示来自不同资源组和订阅的 VM 的互连组件。

创建 Log Analytics 工作区后，在要在 Linux 和 Windows Vm 上收集的工作区中启用性能计数器。 然后，在工作区中安装并启用 ServiceMap 和 InfrastructureInsights 解决方案。 [部署用于 VM 的 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview)指南中介绍了此过程。

### <a name="in-the-azure-stack-hub-administrator-portal"></a>在 Azure Stack 中心管理员门户
在 Azure 门户中启用 Azure 自动化解决方案之后，接下来需要以云管理员身份登录到 Azure Stack 中心管理员门户，并在 Azure Stack 中心市场下载适用于 Linux 扩展的**Azure Monitor、更新和配置管理**以及**Azure Monitor、更新和配置管理**。

   ![Azure Monitor、更新和配置管理扩展应用商店项](media//vm-update-management/2.PNG) 

若要启用用于 VM 的 Azure Monitor Map 解决方案并深入了解网络依赖项，请下载**Azure Monitor Dependency Agent**：

   ![Azure Monitor Dependency Agent](media//vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-hub-vms"></a>为 Azure Stack 中心 Vm 启用更新管理
按照以下步骤为 Azure Stack 中心 Vm 启用更新管理。

1. 登录到 Azure Stack Hub 用户门户。

2. 在 Azure Stack 集线器用户门户中，前往要为其启用这些解决方案的 Vm 的 "扩展" 边栏选项卡，单击 " **+ 添加**"，选择 " **Azure 更新和配置管理**" 扩展，然后单击 "**创建**"：

   [![](media//vm-update-management/3-sm.PNG "VM extension blade")](media//vm-update-management/3-lg.PNG)

3. 提供以前创建的 WorkspaceID 和主密钥，将代理链接到 LogAnalytics 工作区。 然后单击 **"确定"** 以部署扩展。

   [![](media//vm-update-management/4-sm.PNG "Providing the WorkspaceID and Key")](media//vm-update-management/4-lg.PNG) 

4. 如[更新管理文档](https://docs.microsoft.com/azure/automation/automation-update-management)中所述，需要为要管理的每个 VM 启用更新管理解决方案。 若要为向工作区报告的所有 Vm 启用解决方案，请选择 "**更新管理**"，单击 "**管理计算机**"，然后选择 "**在所有可用的和未来的计算机上启用**" 选项。

   [![](media//vm-update-management/5-sm.PNG "Enable Update Management solution on all machines")](media//vm-update-management/5-lg.PNG) 

   > [!TIP]
   > 重复此步骤，为向工作区报告的 Azure Stack 中心 Vm 启用每个解决方案。 
  
启用 Azure 更新和配置管理扩展后，每个托管 VM 每天执行一次扫描。 每隔15分钟就会调用一次 API，查询上次更新时间以确定状态是否已更改。 如果状态已更改，则启动符合性扫描。

扫描 Vm 后，它们将显示在更新管理解决方案的 Azure 自动化帐户中： 

   [![](media//vm-update-management/6-sm.PNG "Azure Automation account in Update Management")](media//vm-update-management/6-lg.PNG) 

> [!IMPORTANT]
> 可能需要 30 分钟到 6 小时，仪表板才会显示受托管计算机提供的已更新数据。

Azure Stack 中心 Vm 现在可以与 Azure Vm 一起包含在计划的更新部署中。

##  <a name="create-an-update-deployment-schedule"></a>创建更新部署计划

若要创建更新部署计划，必须使用 PowerShell cmdlet 或 Azure REST API 传递计算机。 使用以下 PowerShell 示例来安排你的计算机。 可以将**[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationschedule)** cmdlet 与参数一起使用 `ForUpdateConfiguration` 来创建一个计划。 然后，使用**[AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsoftwareupdateconfiguration)** cmdlet 并将 Azure Stack 集线器计算机传递到 `NonAzureComputer` 参数。 若要运行此脚本，需要使用 global [Azure PowerShell Az 模块](https://docs.microsoft.com/powershell/azure/)。

以下示例介绍如何执行此操作：

```Powershell  
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack-hub"></a>启用 Azure Stack 中心运行的用于 VM 的 Azure Monitor
VM 已安装**Azure Monitor、更新和配置管理**，并且安装了**Azure Monitor Dependency Agent**扩展后，它将开始报告[用于 VM 的 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)解决方案中的数据。 

> [!TIP]
> **Azure Monitor Dependency Agent**扩展不需要任何参数。 用于 VM 的 Azure Monitor 映射依赖项代理本身不传输任何数据，它不需要对防火墙或端口做出任何更改。 映射数据始终由 Log Analytics 代理直接传输到 Azure Monitor 服务；如果 IT 安全策略不允许网络中的计算机连接到 Internet，则通过 [OMS 网关](https://docs.microsoft.com/azure/azure-monitor/platform/gateway)传输这些数据。

用于 VM 的 Azure Monitor 包含一组性能图表，这些图表针对多个关键性能指标（Kpi），以帮助你确定 VM 的执行情况。 图表显示一段时间内的资源使用情况，以便您能够识别瓶颈和异常情况。 还可以切换到列出每台计算机的透视图，根据所选的度量值来查看资源使用情况。 尽管处理性能时要考虑多个元素，但用于 VM 的 Azure Monitor 会监视与处理器、内存、网络适配器和磁盘使用相关的关键操作系统性能指标。 性能图表是对运行状况监视功能的补充，有助于公开指出可能发生系统组件故障的问题。 用于 VM 的 Azure Monitor 还支持容量规划和优化和优化，以实现效率。

   ![Azure Monitor Vm 性能选项卡](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

可以通过两种方式在用于 VM 的 Azure Monitor 中查看在 Azure Stack 集线器中运行的 Windows 和 Linux Vm 上发现的应用组件。 第一个是从 VM 直接进行的，第二个是跨 vm 组 Azure Monitor。
[使用用于 VM 的 Azure Monitor Map 了解应用程序组件](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps)一文有助于了解这两种观点之间的经验以及如何使用地图功能。

   ![Azure Monitor Vm 映射 "选项卡](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)

如果[用于 VM 的 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)未显示任何性能数据，则必须在[LogAnalytics 工作区](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters)高级设置中为 Windows 和 Linux 启用性能数据收集。

## <a name="enable-update-management-using-a-resource-manager-template"></a>使用资源管理器模板启用更新管理
如果有大量 Azure Stack 中心 Vm，则可以使用[此 Azure 资源管理器模板](https://aka.ms/aa6zdzy)在 vm 上更轻松地部署解决方案。 该模板将 Microsoft Monitoring Agent 扩展部署到现有 Azure Stack 中心 VM，并将其添加到现有的 Azure LogAnalytics 工作区。
 
## <a name="next-steps"></a>后续步骤
[优化 SQL Server VM 性能](azure-stack-sql-server-vm-considerations.md)
