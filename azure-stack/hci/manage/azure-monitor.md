---
title: 通过 Azure Monitor 监视 Azure Stack HCI
description: 监视服务器并通过 Windows 管理中心的 Azure Monitor 来配置警报。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 04/03/2020
ms.openlocfilehash: 9dcb6050b4980b476d15552c92ff5445c72bbc55
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80750690"
---
# <a name="monitor-azure-stack-hci-with-azure-monitor"></a>通过 Azure Monitor 监视 Azure Stack HCI

> 适用于：Windows Server Standard 2012 R2

[Azure Monitor](/azure/azure-monitor/overview)从多个资源（包括在本地和云中的 Windows 服务器和虚拟机（vm））收集、分析和处理遥测数据。 尽管 Azure Monitor 从 Azure Vm 和其他 Azure 资源拉取数据，但本文重点介绍 Azure Monitor 如何与在 Azure Stack HCI 上运行的本地服务器和 Vm （特别是 Windows 管理中心）结合使用。

## <a name="how-does-azure-monitor-work"></a>Azure Monitor 是如何工作的？
:::image type="content" source="media/monitor/azure-monitor-diagram.png" alt-text="Azure Monitor 工作原理示意图" border="false":::
从本地 Windows Server 生成的数据在 Azure Monitor 的 Log Analytics 工作区中收集。 在工作区中，你可以启用各种监视解决方案，这是为特定方案提供见解的逻辑集。 例如，Azure 更新管理、Azure 安全中心和用于 VM 的 Azure Monitor 都是可在工作区中启用的所有监视解决方案。

在 Log Analytics 工作区中启用监视解决方案时，所有向该工作区报告的服务器都将开始收集与该解决方案相关的数据，以便解决方案可以为工作区中的所有服务器生成见解。

若要在本地服务器上收集遥测数据并将其推送到 Log Analytics 工作区，Azure Monitor 要求安装 Microsoft Monitoring Agent （MMA）。 某些监视解决方案还需要辅助代理。 例如，用于 VM 的 Azure Monitor 还依赖于 ServiceMap 代理获取此解决方案提供的其他功能。

某些解决方案（如 Azure 更新管理）还依赖于 Azure 自动化，使你能够跨 Azure 和非 Azure 环境集中管理资源。 例如，Azure 更新管理使用 Azure 自动化在环境中从 Azure 门户集中计划和安排更新的安装。

## <a name="what-data-does-azure-monitor-collect"></a>Azure Monitor 收集哪些数据？

Azure Monitor 收集的所有数据都适合以下两种基本类型之一：度量值和日志。

1. [度量](/azure/azure-monitor/platform/data-platform#metrics)值是在特定时间点描述系统某些方面的数值。 指标是轻型数据，可以支持近实时方案。 你将在 Azure 门户的 "**概述**" 页中看到 Azure Monitor 收集的数据。

    :::image type="content" source="media/monitor/metrics.png" alt-text="指标资源管理器中指标引入的图像" border="false":::

2. [日志](/azure/azure-monitor/platform/data-platform#logs)包含组织到记录中的不同类型的数据，这些数据具有不同的属性集。 与性能数据一样，事件和跟踪等遥测数据也作为日志存储，因此，可将它们合并以进行分析。 可以使用[查询](/azure/azure-monitor/log-query/log-query-overview)来分析 Azure Monitor 收集的日志数据，这些查询可以快速检索、合并和分析所收集的数据。 可以使用 Azure 门户中的 [Log Analytics](/azure/azure-monitor/log-query/portals) 创建和测试查询，然后可以直接使用这些工具分析数据，或者保存查询以便与[可视化效果](/azure/azure-monitor/visualizations)或[警报规则](/azure/azure-monitor/platform/alerts-overview)配合使用。

    :::image type="content" source="media/monitor/logs.png" alt-text="log analytics 中的日志引入的映像" border="false":::

## <a name="how-does-windows-admin-center-enable-you-to-use-azure-monitor"></a>Windows 管理中心如何允许你使用 Azure Monitor？

在 Windows 管理中心内，你可以启用三个监视解决方案：

- [群集的 Azure Monitor](#onboard-your-cluster-using-windows-admin-center)
- [Azure 更新管理](/windows-server/manage/windows-admin-center/azure/azure-update-management)（在**更新**工具中）
- 用于 VM 的 Azure Monitor （在服务器设置中），即虚拟机见解

你可以开始使用这些工具中的 Azure Monitor。 如果以前从未使用过 Azure Monitor，Windows 管理中心会自动预配 Log Analytics 工作区（和 Azure 自动化帐户，如果需要），然后在目标服务器上安装并配置 MMA。 然后，它会将相应的解决方案安装到工作区中。

例如，如果你首先中转到**更新**工具来设置 Azure 更新管理，Windows 管理中心将：

1. 在计算机上安装 MMA。
2. 创建 Log Analytics 工作区和 Azure 自动化帐户（因为在这种情况下，需要使用 Azure 自动化帐户）。
3. 在新创建的工作区中安装更新管理解决方案。

如果要在同一台服务器上的 Windows 管理中心中添加另一个监视解决方案，Windows 管理中心只需将该解决方案安装到该服务器所连接到的现有工作区中。 Windows 管理中心将另外安装其他任何所需的代理。

如果连接到其他服务器，但已设置 Log Analytics 工作区（通过 Windows 管理中心或在 Azure 门户中手动），则还可以在服务器上安装 MMA，并将其连接到现有工作区。 将服务器连接到工作区时，它会自动开始收集数据并报告到该工作区中安装的解决方案。

## <a name="azure-monitor-for-virtual-machines-aka-virtual-machine-insights"></a>虚拟机的 Azure Monitor （也称为 虚拟机见解）

当你在 "**服务器设置**" 中设置用于 VM 的 Azure Monitor 时，Windows 管理中心会启用用于 VM 的 Azure Monitor 解决方案，也称为虚拟机 insights。 此解决方案允许你监视服务器运行状况和事件、创建电子邮件警报、在整个环境中获取服务器性能的合并视图，以及直观显示连接到给定服务器的应用程序、系统和服务。

> [!NOTE]
> 不管名称如何，虚拟机见解都适用于物理服务器和虚拟机。

通过 Azure Monitor 的可用数据/月/客户额度，可以轻松地在一台或两台服务器上试用，无需担心。 继续阅读以了解载入服务器 Azure Monitor 的其他好处，如在环境中跨服务器获取系统性能的综合视图。

## <a name="onboard-your-cluster-using-windows-admin-center"></a>使用 Windows 管理中心加入群集

将群集加入到 Azure Monitor 的最简单方法是使用 Windows 管理中心中的自动工作流，该工作流配置运行状况服务和 Log Analytics，然后安装 MMA。

:::image type="content" source="media/monitor/onboarding.gif" alt-text="要 Azure Monitor 的载入群集的图像":::

在服务器连接的 "概述" 页中，单击 "新建" 按钮 "**管理警报**"，或 " **> 监视和警报**" 中转到 "服务器设置"。 在此页中，通过单击 "**设置**" 并完成安装窗格，使服务器 Azure Monitor。 管理中心负责预配 Azure Log Analytics 工作区、安装所需的代理，并确保配置虚拟机 insights 解决方案。 完成后，服务器会将性能计数器数据发送到 Azure Monitor，使你能够从 Azure 门户中查看和创建基于此服务器的电子邮件警报。

## <a name="onboard-your-cluster-manually-using-powershell"></a>使用 PowerShell 手动载入群集

如果你更愿意手动载入群集，请执行以下步骤。

### <a name="configure-health-service"></a>配置运行状况服务

需要做的第一件事是配置群集。 您可能知道，[运行状况服务](/windows-server/failover-clustering/health-service-overview)改进了运行存储空间直通的群集的日常监视和操作经验。

如上所述，Azure Monitor 从群集中运行的每个节点收集日志。 因此，我们必须将运行状况服务配置为写入事件通道，这恰好是：

```
Event Channel: Microsoft-Windows-Health/Operational
Event ID: 8465
```

若要配置运行状况服务，请运行：

```PowerShell
get-storagesubsystem clus* | Set-StorageHealthSetting -Name "Platform.ETW.MasTypes" -Value "Microsoft.Health.EntityType.Subsystem,Microsoft.Health.EntityType.Server,Microsoft.Health.EntityType.PhysicalDisk,Microsoft.Health.EntityType.StoragePool,Microsoft.Health.EntityType.Volume,Microsoft.Health.EntityType.Cluster"
```

运行上述 cmdlet 会通知运行状况设置开始将事件写入到*Microsoft Windows 运行状况/操作*事件通道。

### <a name="configure-log-analytics"></a>配置 Log Analytics

在群集上设置正确的日志记录后，下一步就是正确配置 Log Analytics。

为了进行概述， [Azure Log Analytics](/azure/azure-monitor/platform/agent-windows)可以将数据从数据中心或其他云环境中的物理或虚拟 Windows 计算机直接收集到单个存储库中，以便进行详细的分析和关联。

若要了解支持的配置，请查看[支持的 Windows 操作系统](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems)和[网络防火墙配置](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

#### <a name="login-in-to-azure-portal"></a>登录到 Azure 门户

登录到 Azure 门户[https://portal.azure.com](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

#### <a name="create-a-workspace"></a>创建工作区

有关下面列出的步骤的更多详细信息，请参阅[Azure Monitor 文档](/azure/azure-monitor/learn/quick-collect-windows-computer)。

1. 在 Azure 门户中，单击 "**所有服务**"。 在资源列表中，键入“Log Analytics”****。 开始键入时，会根据输入筛选该列表。 选择**Log Analytics**。

    :::image type="content" source="media/monitor/azure-portal-01.png" alt-text="Azure 门户":::

2. 单击“创建”****，然后为以下各项选择选项：

   * 为新的**Log Analytics 工作区**提供名称，例如*DefaultLAWorkspace*。 
   * 如果选择的默认值不合适，请从下拉列表中进行选择，以选择要链接到的**订阅**。
   * 对于“资源组”****，选择包含一个或多个 Azure 虚拟机的现有资源组。

    :::image type="content" source="media/monitor/create-loganalytics-workspace-02.png" alt-text="创建 Log Analytics 资源边栏选项卡":::

3. 在“Log Analytics 工作区”窗格上提供所需信息后，单击“确定”********。  

在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度****。

#### <a name="obtain-workspace-id-and-key"></a>获取工作区 ID 和密钥
安装 MMA for Windows 之前，需要 Log Analytics 工作区的工作区 ID 和密钥。  安装向导需要使用此信息来正确配备代理，并确保它能与 Log Analytics 成功通信。  

1. 在 Azure 门户中，单击左上角的“所有服务”****。 在资源列表中，键入“Log Analytics”****。 开始键入时，会根据输入筛选该列表。 选择**Log Analytics**。
2. 在 Log Analytics 工作区列表中，选择之前创建的 DefaultLAWorkspace**。
3. 选择 "**高级设置**"。
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Log Analytics 高级设置":::
4. 选择“已连接的源”，然后选择“Windows 服务器”********。
5. “工作区 ID”和“主密钥”右侧的值********。 将这两种情况都暂时保存到您最喜爱的编辑器中。

### <a name="installing-the-mma-on-windows"></a>在 Windows 上安装 MMA
以下步骤安装并配置 Microsoft Monitoring Agent。

> [!IMPORTANT]
> 请确保在群集中的每个服务器上安装此代理，并指示你希望在 Windows 启动时运行代理。

1. 在“Windows 服务器”页上，选择“下载 Windows 代理”，根据 Windows 操作系统的处理器体系结构下载相应的版本。********
2. 运行安装程序在计算机上安装该代理。
3. 在“欢迎”**** 页上，单击“下一步”****。
4. 在 "**许可条款**" 页上，阅读许可协议，然后单击 "**我同意**"。
5. 在 "**目标文件夹**" 页上，更改或保留默认安装文件夹，然后单击 "**下一步**"。
6. 在“代理安装选项”页上，选择将代理连接到 Azure Log Analytics，单击“下一步”。********
7. 在 " **Azure Log Analytics** " 页上，粘贴前面复制的**工作区 ID**和**工作区密钥（主密钥）** 。 如果计算机需要通过代理服务器来与 Log Analytics 通信，请单击“高级”并提供代理服务器的 URL 和端口号。**** 如果代理服务器要求身份验证，请键入用于在代理服务器上进行身份验证的用户名和密码，并单击“下一步”。****  
8. 提供所需的配置设置后，单击“下一步”。****
    :::image type="content" source="media/monitor/log-analytics-mma-setup-laworkspace.png" alt-text="粘贴工作区 ID 和主要密钥":::
9. 在 "**准备安装**" 页上，查看你的选择，然后单击 "**安装**"。
10. 在“配置已成功完成”页上，单击“完成”。********

完成后，**Microsoft Monitoring Agent** 将显示在“**控制面板**”中。 可以检查配置，并验证代理是否已连接到 Log Analytics。 如果已连接，代理会在“Azure Log Analytics”选项卡上显示一条消息，指出：“Microsoft Monitoring Agent 已成功连接到 Microsoft Log Analytics 服务”。********

:::image type="content" source="media/monitor/log-analytics-mma-laworkspace-status.png" alt-text="MMA 与 Log Analytics 的连接状态":::

若要了解支持的配置，请查看[支持的 Windows 操作系统](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems)和[网络防火墙配置](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements)。

## <a name="setting-up-alerts-using-windows-admin-center"></a>使用 Windows 管理中心设置警报

将服务器连接到 Azure Monitor 后，可以使用 "**设置 > 监视和警报**" 页中的智能超链接导航到 Azure 门户。 在 Windows 管理中心，你可以轻松地配置将应用于 Log Analytics 工作区中所有服务器的默认警报。 Windows 管理中心会自动启用要收集的性能计数器，因此你可以通过自定义许多预定义的查询之一或编写你自己的查询来[创建新的警报](/azure/azure-monitor/platform/alerts-log)。

:::image type="content" source="media/monitor/setup1.gif" alt-text="配置警报屏幕截图":::

这些是可以选择的警报和默认条件：

| 警报名称                | 默认条件                                  |
|---------------------------|----------------------------------------------------|
| CPU 使用率           | 10分钟超过85%                            |
| 磁盘容量利用率 | 10分钟超过85%                            |
| 内存利用率        | 可用内存小于 100 MB，10分钟   |
| 检测信号                 | 5分钟内少于2节拍                   |
| 系统严重错误     | 群集系统事件日志中的任何严重警报 |
| 运行状况服务警报      | 群集上的任何运行状况服务错误            |

在 Windows 管理中心中配置警报后，你可以在 Azure 中的 Log Analytics 工作区中查看警报。

:::image type="content" source="media/monitor/setup2.gif" alt-text="查看警报屏幕截图":::

### <a name="collecting-event-and-performance-data"></a>收集事件和性能数据

Log Analytics 可从 Windows 事件日志以及指定用于长期分析的性能计数器中收集事件，并在检测到特定条件时采取措施。 首先，请按照下列步骤操作，配置 Windows 事件日志以及几个常见性能计数器中收集事件。  

1. 在 Azure 门户中，单击左下角的“更多服务”****。 在资源列表中，键入“Log Analytics”****。 开始键入时，会根据输入筛选该列表。 选择**Log Analytics**。
2. 选择 "**高级设置**"。
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Log Analytics 高级设置":::
3. 选择“数据”****，然后选择“Windows 事件日志”****。  
4. 在此处，通过在下面的名称中键入来添加运行状况服务事件通道，并单击加号**+**。  
   ```
   Event Channel: Microsoft-Windows-Health/Operational
   ```
5. 在表中，选中严重性“错误”**** 和“警告”****。   
6. 单击页面顶部的“保存”来保存配置。****
7. 选择“Windows 性能计数器”****，在 Windows 计算机上启用性能计数器收集。 
8. 首次为新的 Log Analytics 工作区配置 Windows 性能计数器时，可以选择快速创建几个通用的计数器。 将这些计数器在一个复选框中依次列出。
    :::image type="content" source="media/monitor/windows-perfcounters-default.png" alt-text="[选定的默认 Windows 性能计数器":::
    单击“添加所选性能计数器”****。  随即会添加它们，并且通过 10 秒收集示例间隔进行预设。  
9. 单击页面顶部的“保存”来保存配置。****

## <a name="create-queries-and-alerts-based-on-log-data"></a>基于日志数据创建查询和警报

如果已完成此工作，则应将日志和性能计数器发送到 Log Analytics。 下一步是创建定期自动运行日志搜索的警报规则。 如果日志搜索的结果符合特定条件，则会触发警报，发送电子邮件或文本通知。 我们来探讨下面的。

### <a name="create-a-query"></a>创建查询

首先打开**日志搜索**门户。

1. 在 Azure 门户中，单击 "**所有服务**"。 在资源列表中，键入“监视器”****。 开始键入时，会根据输入筛选该列表。 选择 "**监视器**"。
2. 在 "**监视**" 导航菜单上，选择 " **Log Analytics** "，然后选择一个工作区。

用于检索某些要使用的数据的最快方法是使用一个简单查询，它可返回表中的所有记录。 在搜索框中键入以下查询，然后单击 "搜索" 按钮。  

```
Event
```

数据会返回到默认列表视图中，并可看到返回的总记录条数。

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-01.png" alt-text="简单查询屏幕快照":::

屏幕左侧是“筛选器”窗格，可用于向查询添加筛选而无需直接修改查询。  该记录类型显示有多个记录属性，可选择一个或多个属性值来缩小搜索结果范围。

选中 " **EVENTLEVELNAME** " 下的 "**错误**" 旁的复选框，或键入以下项以将结果限制为 "错误事件"。

```
Event | where (EventLevelName == "Error")
```

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-02.png" alt-text="筛选屏幕快照":::

对所关注的事件进行适当的查询后，请将其保存为下一步。

### <a name="create-alerts"></a>创建警报
现在，让我们看一看创建警报的示例。

1. 在 Azure 门户中，单击 "**所有服务**"。 在资源列表中，键入“Log Analytics”****。 开始键入时，会根据输入筛选该列表。 选择**Log Analytics**。
2. 在左窗格中选择“警报”，然后单击页面顶部的“新建警报规则”，以便创建新的警报。********
    :::image type="content" source="media/monitor/alert-rule-02.png" alt-text="创建新的警报规则屏幕快照":::
3. 对于第一步，在 "**创建警报**" 部分下，将选择 "Log Analytics" 工作区作为资源，因为这是一个基于日志的警报信号。  如果有多个，则通过从下拉列表中选择特定**订阅**来筛选结果，其中包含之前创建 Log Analytics 工作区。  从下拉列表中选择“Log Analytics”，对“资源类型”进行筛选。********  最后，选择**资源** **DefaultLAWorkspace**，然后单击“完成”。****
    :::image type="content" source="media/monitor/alert-rule-03.png" alt-text="创建新的警报规则步骤1屏幕截图":::
4. 在 "**警报条件**" 部分下，单击 "**添加条件**" 以选择已保存的查询，然后指定警报规则遵循的逻辑。
5. 使用以下信息配置警报：  
   a. 从 "**基于**" 下拉列表中，选择 "**指标度量值**"。  指标度量将为查询中其值超出指定阈值的每个对象创建一个警报。  
   b. 对于 "**条件**"，选择 "**大于**" 并指定阈值。  
   c. 然后定义触发警报的时间。 例如，你可以选择 "**连续**"，并从下拉列表中选择 "**大于**" 值 "3"。  
   d. 在 "基于的评估" 下，将 "**周期**" 值修改为**30**分钟，将 "**频率**" 修改为5。 此规则将每五分钟运行一次，返回从当前时间算起过去 30 分钟内创建的记录。  将时间段设置为更宽的时间窗口可以解决数据延迟的可能性，并确保查询返回数据以避免警报永远不会触发的漏报。  
6. 单击“完成”，完成警报规则。****
    :::image type="content" source="media/monitor/alert-signal-logic-02.png" alt-text="配置警报信号屏幕截图":::
7. 现在，在第二个步骤中，在 "**警报规则名称**" 字段中提供警报的名称，如**所有错误事件的警报**。  指定“说明”，详细描述该警报的具体信息，并从提供的选项中选择“关键(严重性 0)”作为“严重性”值。************
8. 若要在创建后立即激活警报规则，请接受“创建后启用规则”选项的默认值。****
9. 第三步也是最后一步，指定“操作组”****，确保每次触发警报时都执行相同的操作，而且这些操作可以用于定义的每项规则。 使用以下信息配置新操作组：  
   a. 选择“新建操作组”，此时会显示“添加操作组”窗格。********  
   b. 对于“操作组名称”****，请指定一个长名称，例如“IT 操作 - 通知”，以及一个“短名称”，例如“itops-n”。************  
   c. 验证“订阅”和“资源组”的默认值是否正确********。 如果否，请从下拉列表中选择正确的值。
   d. 在“操作”部分指定操作的名称，例如“发送电子邮件”，然后在“操作类型”下的下拉列表中选择“电子邮件/短信/推送/语音”。************ “电子邮件/短信/推送/语音”属性窗格会在右侧打开，其中包含更多的信息。****  
   e. 在**电子邮件/SMS/推送/语音**窗格上，选择并设置你的首选项。 例如，启用**电子邮件**，并提供有效的电子邮件 SMTP 地址以将消息传递给。  
   f. 单击 **“确定”** 以保存你的更改。<br><br> 

    :::image type="content" source="media/monitor/action-group-properties-01.png" alt-text="创建新的操作组屏幕快照":::

10. 单击“确定”****，完成操作组。 
11. 单击“创建警报规则”，完成警报规则。**** 该警报会立即开始运行。
    :::image type="content" source="media/monitor/alert-rule-01.png" alt-text="完成创建新的警报规则屏幕截图":::

### <a name="example-alert"></a>示例警报

作为参考，这是一个示例警报在 Azure 中的样子。

:::image type="content" source="media/monitor/alert.gif" alt-text="Azure 警报屏幕截图":::

下面是 Azure Monitor 将发送的电子邮件的示例：

:::image type="content" source="media/monitor/warning.png" alt-text="警报电子邮件示例屏幕截图":::

## <a name="create-custom-kusto-queries-in-log-analytics"></a>在 Log Analytics 中创建自定义 Kusto 查询

你还可以使用 Kusto 查询语言在 Azure Monitor 中[编写自定义日志查询](/azure/azure-monitor/log-query/get-started-queries)，以从一个或多个虚拟机收集数据。

## <a name="get-a-consolidated-view-across-multiple-servers"></a>跨多个服务器获取合并视图

如果将多个服务器集成到 Azure Monitor 中的单个 Log Analytics 工作区，则可以从 Azure Monitor 内的[虚拟机 insights](/azure/azure-monitor/insights/vminsights-overview)解决方案获取所有这些服务器的合并视图。 （请注意，仅 Azure Monitor 的虚拟机见解的 "性能" 和 "映射" 选项卡可用于本地服务器– "运行状况" 选项卡仅适用于 Azure Vm。）若要在 Azure 门户中查看此信息，请转到 " **Azure Monitor" > 虚拟机**"，并导航到"**性能**"或"**映射**"选项卡。

## <a name="visualize-connected-services"></a>可视化连接的服务

当 Windows 管理中心中心将服务器加入到 Azure Monitor 中的虚拟机 insights 解决方案中时，它还会亮起称为[服务映射](/azure/azure-monitor/insights/service-map)的一种功能。 此功能自动发现应用程序组件并映射服务之间的通信，以便您可以轻松地直观显示服务器之间的连接以及 Azure 门户的详细信息。 若要找到此信息，请转到 "Azure 门户"，选择 " **Azure Monitor" > 虚拟机**"，在" 见解 "下，导航到"**映射**"选项卡。

> [!NOTE]
> 目前，有六个公共区域提供用于 Azure Monitor 的虚拟机见解的可视化。  有关最新信息，请查看[用于 VM 的 Azure Monitor 文档](/azure/azure-monitor/insights/vminsights-onboard#log-analytics)。 您必须将 Log Analytics 工作区部署在一个受支持的区域中，以获得上述虚拟机 insights 解决方案提供的其他优势。

## <a name="disabling-monitoring"></a>禁用监视

若要从 Log Analytics 工作区完全断开服务器的连接，请卸载 MMA。 这意味着，此服务器将不再向工作区发送数据，并且在该工作区中安装的所有解决方案将不再从该服务器收集和处理数据。 但是，这不会影响工作区本身;向该工作区报告的所有资源将继续进行。 若要卸载 WAC 中的 MMA 代理，请 **& 功能**中转到应用，找到**Microsoft Monitoring Agent**，并单击 "**卸载**"。

如果要关闭工作区中的特定解决方案，需要[从 Azure 门户中删除该监视解决方案](/azure/azure-monitor/insights/solutions#remove-a-management-solution)。 删除监视解决方案意味着将不再为向该工作区报告的_任何_服务器生成该解决方案创建的 insights。 例如，如果卸载用于 VM 的 Azure Monitor 解决方案，将不再从任何连接到工作区的计算机看到有关 VM 或服务器性能的见解。

## <a name="next-steps"></a>后续步骤

相关主题的另请参阅：

- [了解有关 Azure 与 Windows 管理中心的集成的详细信息](/windows-server/manage/windows-admin-center/azure/)
- [使用 Azure Monitor 发送运行状况服务错误的电子邮件](/windows-server/storage/storage-spaces/configure-azure-monitor)
