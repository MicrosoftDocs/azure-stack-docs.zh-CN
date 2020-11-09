---
title: Azure Stack Hub 中的 VM 更新和管理自动化
description: 在 Azure 自动化中使用用于 VM 的 Azure Monitor、更新管理、更改跟踪和库存等解决方案来管理 Azure Stack Hub 中部署的 Windows 和 Linux VM。
author: mattbriggs
ms.topic: article
ms.date: 10/08/2020
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 10/08/2020
ms.openlocfilehash: 7b3c69b26ef1fee21e652c70f0ca9a9ddc156460
ms.sourcegitcommit: ce864e1d86ad05a03fe896721dea8f0cce92085f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2020
ms.locfileid: "94383660"
---
# <a name="vm-update-and-management-automation-in-azure-stack-hub"></a>Azure Stack Hub 中的 VM 更新和管理自动化
使用以下 Azure 自动化解决方案功能来管理使用 Azure Stack Hub 部署的 Windows 和 Linux 虚拟机 (VM)：

- **[更新管理](/azure/automation/update-management/overview)** ：借助更新管理解决方案，可以快速评估所有代理计算机上可用更新的状态，并管理为 Windows 和 Linux VM 安装所需更新的过程。

- **[更改跟踪](/azure/automation/automation-change-tracking)** ：受监视服务器上已安装软件、windows 服务、windows 注册表和文件以及 Linux 守护程序的更改会发送到云中的 Azure Monitor 服务进行处理。 逻辑应用于接收的数据，云服务则记录数据。 通过使用更改跟踪仪表板上的信息，可以轻松查看对服务器基础结构所做的更改。

- **[清单](/azure/automation/automation-vm-inventory)** 。 Azure Stack 中心 VM 的库存跟踪提供一个基于浏览器的用户界面，用于设置和配置清单收集。

- **[用于 VM 的 Azure Monitor](/azure/azure-monitor/insights/vminsights-overview)** ：用于 VM 的 Azure Monitor 可以大规模监视 Azure 与 Azure Stack Hub VM 和虚拟机规模集。 它分析 Windows VM 和 Linux VM 的性能和运行状况，还监视它们的进程及其对其他资源和外部进程的依赖关系。

> [!IMPORTANT]
> 这些解决方案与用于管理 Azure VM 的解决方案相同。 使用相同的工具，从相同的界面，通过相同的方式管理 Azure 和 Azure Stack Hub VM。 将更新管理、更改跟踪、清单和用于 VM 的 Azure Monitor 解决方案与 Azure Stack 中心结合使用时，Azure Stack 中心 Vm 也会与 Azure Vm 定价相同。

## <a name="prerequisites"></a>先决条件
使用这些功能更新和管理 Azure Stack Hub VM 之前，必须先满足一些先决条件。 其中包括 Azure 门户以及 Azure Stack Hub 管理门户中必须执行的步骤。

### <a name="in-the-azure-portal"></a>在 Azure 门户中
若要将用于 VM 的 Azure Monitor、清点、更改跟踪和更新管理 Azure 自动化功能用于 Azure Stack 中心 Vm，首先需要在 Azure 中启用这些解决方案。

> [!TIP]
> 如果已为 Azure VM 启用了这些功能，则可以使用预先存在的 LogAnalytics 工作区凭据。 如果你已有要使用的 LogAnalytics 工作区 ID 和主密钥，请跳到[下一节](./vm-update-management.md#in-the-azure-stack-hub-administrator-portal)。 否则，请继续阅读本部分以创建新的 LogAnalytics 工作区和自动化帐户。

启用这些解决方案的第一步是在 Azure 订阅中[创建 LogAnalytics 工作区](/azure/log-analytics/log-analytics-quick-create-workspace)。 Log Analytics 工作区是独特的 Azure Monitor 日志环境，其包含自身的数据存储库、数据源和解决方案。 创建工作区后，请记下工作区 ID 和密钥。 若要查看此信息，请转到工作区边栏选项卡，单击“高级设置”，然后查看“工作区 ID”和“主密钥”值  。 

接下来必须[创建自动化帐户](/azure/automation/automation-create-standalone-account)。 自动化帐户是 Azure 自动化资源的容器。 它提供一种方法来分离环境或进一步组织自动化工作流和资源。 创建自动化帐户后，需要启用库存、更改跟踪和更新管理功能。 若要启用该功能，请执行以下步骤：

1. 在 Azure 门户中，转到要使用的自动化帐户。

2. 选择要启用 ( **库存** 、 **更改跟踪** 或 **更新管理** ) 的解决方案。

3. 使用“选择工作区...”下拉列表，选择要使用的 Log Analytics 工作区。

4. 验证所有剩余的信息是否正确，然后单击“启用”以启用该解决方案。

5. 重复步骤 2-4 以启用所有这三种解决方案。 

   [![“诊断和解决问题”窗口显示了两个列表，其中突出显示了三个选项。“库存”处于选中状态。还有一个“Log Analytics 工作区”下拉列表和一个“启用”按钮。](media//vm-update-management/1-sm.PNG "启用 Azure 自动化帐户功能")](media//vm-update-management/1-lg.PNG)

### <a name="enable-azure-monitor-for-vms"></a>启用用于 VM 的 Azure Monitor

用于 VM 的 Azure Monitor 可以大规模监视 Azure VM 和虚拟机规模集。 它分析 Windows VM 和 Linux VM 的性能和运行状况，还监视它们的进程及其对其他资源和外部进程的依赖关系。

作为一种解决方案，用于 VM 的 Azure Monitor 支持监视本地或其他云提供程序中托管的 VM 的性能和应用依赖关系。 三个主要功能提供深入的见解：

1. Azure VM 的逻辑组件，将根据预先配置的运行状况条件对运行 Windows 和 Linux 的 Azure VM 进行度量，并在满足评估的条件时发出警报。

2. 预定义的趋势性能图表，显示来宾 VM 操作系统中的核心性能指标。

3. 依赖关系映射，显示来自各种资源组和订阅的 VM 的互连组件。

创建 Log Analytics 工作区后，在该工作区中启用性能计数器，用于在 Linux 和 Windows VM 上收集数据。 然后在工作区中安装并启用 ServiceMap 和 InfrastructureInsights 解决方案。 [部署用于 VM 的 Azure Monitor](/azure/azure-monitor/insights/vminsights-enable-overview) 指南中介绍了此过程。

### <a name="in-the-azure-stack-hub-administrator-portal"></a>在 Azure Stack Hub 管理员门户
在 Azure 门户中启用 Azure 自动化解决方案之后，接下来需要以云管理员身份登录到 Azure Stack Hub 管理员门户，然后在 Azure Stack Hub 市场下载 Azure Monitor 更新和配置管理，以及适用于 Linux 的 Azure Monitor 更新和配置管理扩展 。

   ![“主页 > 市场管理 > 从 Azure 添加 > Azure Monitor、更新和配置管理”对话框介绍了该扩展，并提供了“下载”按钮。](media//vm-update-management/2.PNG) 

若要启用用于 VM 的 Azure Monitor 映射解决方案并深入了解网络依赖关系，请下载 Azure Monitor Dependency Agent：

   ![“主页 > 市场管理 > 从 Azure 添加 > Azure Monitor Dependency Agent”对话框介绍了该扩展，并提供了“下载”按钮。](media//vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-hub-vms"></a>为 Azure Stack Hub VM 启用更新管理
按照以下步骤为 Azure Stack Hub VM 启用更新管理。

1. 登录到 Azure Stack Hub 用户门户。

2. 在 Azure Stack Hub 用户门户中，转到要为其启用这些解决方案的 VM 的“扩展”边栏选项卡，单击“+ 添加”，选择“Azure 更新和配置管理”扩展，然后单击“创建”  ：

    ![“Azure 更新和配置管理”对话框提供了解释信息、用于添加该扩展的“创建”按钮（已突出显示），以及指向详细信息的链接。](media//vm-update-management/3-sm.PNG "VM 扩展边栏选项卡")

3. 提供之前创建的工作区 ID 和主密钥，将代理链接到 LogAnalytics 工作区。 然后单击“确定”部署扩展。

   [![“安装扩展”对话框中包含用于提供 Azure WorkspaceID 和 WorkspaceKey 的文本框。](media//vm-update-management/4-sm.PNG "提供工作区 ID 和密钥")](media//vm-update-management/4-lg.PNG) 

4. 如[更新管理文档](/azure/automation/update-management/overview)中所述，需要为要管理的每个 VM 启用更新管理解决方案。 若要为向工作区报告的所有 VM 启用解决方案，请选择“更新管理”，单击“管理计算机”，然后选择“在所有可用的和未来的计算机上启用”选项  。

   [![“管理计算机 - 更新管理”对话框显示了未启用更新管理的计算机。提供了三个启用选项，并且选中并突出显示了“在所有可用的和未来的计算机上启用”。有一个“启用”按钮。](media//vm-update-management/5-sm.PNG "在所有计算机上启用更新管理解决方案")](media//vm-update-management/5-lg.PNG) 

   > [!TIP]
   > 重复此步骤，为向工作区报告的 Azure Stack Hub VM 启用每个解决方案。 
  
启用 Azure 更新和配置管理扩展后，每天对每个托管 VM 执行两次扫描。 每隔 15 分钟就会调用一次 API 来查询上次更新时间，以确定状态是否已更改。 如果状态已更改，则会启动符合性扫描。

扫描 VM 后，它们将显示在更新管理解决方案的 Azure 自动化帐户中： 

   [![列出了已经过扫描的计算机。对于每一台，都提供符合性状态、平台、操作系统，以及缺少的关键更新的计数。总计数据中显示了需要关注的计算机的数量、缺少的更新的数量，等等。](media//vm-update-management/6-sm.PNG "更新管理中的 Azure 自动化帐户")](media//vm-update-management/6-lg.PNG) 

> [!IMPORTANT]
> 可能需要 30 分钟到 6 小时，仪表板才会显示受托管计算机提供的已更新数据。

Azure Stack Hub VM 现在可以与 Azure VM 一起包含在计划的更新部署中。

##  <a name="create-an-update-deployment-schedule"></a>创建更新部署计划

若要创建更新部署计划，必须使用 PowerShell cmdlet 或 Azure REST API 来传递计算机。 使用以下 PowerShell 示例来安排计算机。 可以将 [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) cmdlet 与 `ForUpdateConfiguration` 参数配合使用创建一个计划。 然后，使用 [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/az.automation/new-azautomationsoftwareupdateconfiguration) cmdlet 并将 Azure Stack Hub 计算机传递到 `NonAzureComputer` 参数。 若要运行此脚本，需要使用全局 [Azure PowerShell Az 模块](/powershell/azure/)。

以下示例介绍如何执行此操作：

```Powershell  
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack-hub"></a>启用在 Azure Stack Hub 上运行的用于 VM 的 Azure Monitor
当 VM 具有“Azure Monitor 更新和配置管理”，并安装“Azure Monitor Dependency Agent”扩展后，它将开始在[用于 VM 的 Azure Monitor](/azure/azure-monitor/insights/vminsights-overview) 解决方案中报告数据 。 

> [!TIP]
> Azure Monitor Dependency Agent 扩展不需要任何参数。 用于 VM 的 Azure Monitor 映射依赖项代理本身不传输任何数据，它不需要对防火墙或端口做出任何更改。 映射数据始终由 Log Analytics 代理直接传输到 Azure Monitor 服务；如果 IT 安全策略不允许网络中的计算机连接到 Internet，则通过 [OMS 网关](/azure/azure-monitor/platform/gateway)传输这些数据。

用于 VM 的 Azure Monitor 包含一组针对几项关键性能指标 (KPI) 的性能图表，帮助你确定 VM 的性能状况。 图表显示一段时间内的资源使用情况，以便你能够识别瓶颈和异常情况。 还可以切换到列出每台计算机的透视图，根据所选的指标来查看资源使用情况。 处理性能时需要考虑大量的要素，而用于 VM 的 Azure Monitor 会监视与处理器、内存、网络适配器和磁盘使用情况相关的操作系统关键性能指标。 性能图表是对运行状况监视功能的补充，有助于揭示指出可能发生系统组件故障的问题。 用于 VM 的 Azure Monitor 还支持容量规划、微调和优化以实现效率。

   ![Azure Monitor VM 性能选项卡](/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

可以通过两种方式使用用于 VM 的 Azure Monitor 查看在 Azure Stack Hub 中运行的 Windows 和 Linux VM 上发现的应用组件。 第一种方式是直接从 VM 查看，第二种方式是从 Azure Monitor 跨 VM 组查看。
通过[借助用于 VM 的 Azure Monitor 映射了解应用组件](/azure/azure-monitor/insights/vminsights-maps)一文，可以了解这两个角度的不同体验，以及如何使用映射功能。

   ![Azure Monitor VM 映射选项卡](/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)

如果[用于 VM 的 Azure Monitor](/azure/azure-monitor/insights/vminsights-overview) 未显示任何性能数据，则必须在 [LogAnalytics 工作区](/azure/azure-monitor/platform/data-sources-performance-counters)“高级设置”中启用 Windows 和 Linux 性能数据收集。

## <a name="enable-update-management-using-a-resource-manager-template"></a>使用资源管理器模板启用更新管理
如果有大量 Azure Stack Hub VM，则可以使用[此 Azure 资源管理器模板](https://aka.ms/aa6zdzy)更轻松地在 VM 上部署解决方案。 该模板将 Microsoft Monitoring Agent 扩展部署到现有的 Azure Stack Hub VM，并将其添加到现有的 Azure LogAnalytics 工作区。
 
## <a name="next-steps"></a>后续步骤
[优化 SQL Server VM 性能](azure-stack-sql-server-vm-considerations.md)
