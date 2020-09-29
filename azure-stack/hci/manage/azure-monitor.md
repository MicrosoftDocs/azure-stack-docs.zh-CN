---
title: 使用 Azure Monitor 监视 Azure Stack HCI
description: 使用来自 Windows 管理中心的 Azure Monitor 监视服务器和配置警报。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: f721b16d6742cde5e27fae8b81d8d256c7defa2a
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866801"
---
# <a name="monitor-azure-stack-hci-with-azure-monitor"></a>使用 Azure Monitor 监视 Azure Stack HCI

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

[Azure Monitor](/azure/azure-monitor/overview) 从各种资源（包括在本地和云中的 Windows 服务器和虚拟机 (VM)）收集、分析和执行遥测。 虽然 Azure Monitor 从 Azure VM 和其他 Azure 资源中提取数据，但本文将重点介绍 Azure Monitor 如何与在 Azure Stack HCI 上运行的本地服务器和 VM（尤其是与 Windows 管理中心）协同工作。

## <a name="how-does-azure-monitor-work"></a>Azure Monitor 的工作原理是怎样的？
:::image type="content" source="media/monitor/azure-monitor-diagram.png" alt-text="Azure Monitor 工作原理示意图" border="false":::
从本地 Windows 服务器生成的数据收集在 Azure Monitor 中的 Log Analytics 工作区中。 在工作区中，你可以启用各种监视解决方案逻辑集，为特定方案提供见解。 例如，Azure 更新管理、Azure 安全中心和用于 VM 的 Azure Monitor 都是可以在工作区内启用的监视解决方案。

当你在 Log Analytics 工作区中启用监视解决方案时，向该工作区报告的所有服务器都将开始收集与该解决方案相关的数据，以便该解决方案可以为工作区中的所有服务器生成见解。

若要在本地服务器上收集诊断数据并将其推送到 Log Analytics 工作区，Azure Monitor 需要安装 Microsoft Monitoring Agent (MMA)。 某些监视解决方案还需要辅助代理。 例如，用于 VM 的 Azure Monitor 还依赖于 ServiceMap 代理来获得此解决方案提供的其他功能。

某些解决方案（例如 Azure 更新管理）还依赖于 Azure 自动化来集中管理 Azure 和非 Azure 环境中的资源。 例如，Azure 更新管理使用 Azure 自动化在 Azure 门户中对环境中的计算机上的更新安装集中地进行安排和协调。

## <a name="what-data-does-azure-monitor-collect"></a>Azure Monitor 收集哪些数据？

Azure Monitor 收集的所有数据属于以下两种基本类型之一：指标和日志。

1. [指标](/azure/azure-monitor/platform/data-platform#metrics)是数字值，用于描述系统某些方面在特定时间点的情况。 指标是轻型数据，可以支持近实时方案。 Azure 门户的“概述”页中显示了 Azure Monitor 收集的数据****。

    :::image type="content" source="media/monitor/metrics.png" alt-text="在指标资源管理器中引入指标的图像" border="false":::

2. [日志](/azure/azure-monitor/platform/data-platform#logs)包含不同类型的已经整理成记录的数据，每种类型都有不同的属性集。 与性能数据一样，事件和跟踪等遥测数据也作为日志存储，因此，可将它们合并以进行分析。 可以使用[查询](/azure/azure-monitor/log-query/log-query-overview)来分析 Azure Monitor 收集的日志数据，这些查询可以快速检索、合并和分析所收集的数据。 可以使用 Azure 门户中的 [Log Analytics](/azure/azure-monitor/log-query/portals) 创建和测试查询，然后可以直接使用这些工具分析数据，或者保存查询以便与[可视化效果](/azure/azure-monitor/visualizations)或[警报规则](/azure/azure-monitor/platform/alerts-overview)配合使用。

    :::image type="content" source="media/monitor/logs.png" alt-text="在 Log Analytics 中引入日志的图像" border="false":::

## <a name="how-does-windows-admin-center-enable-you-to-use-azure-monitor"></a>如何通过 Windows 管理中心使用 Azure Monitor？

在 Windows 管理中心，可以启用三个监视解决方案：

- [用于群集的 Azure Monitor](#onboard-your-cluster-using-windows-admin-center)
- [Azure 更新管理](/windows-server/manage/windows-admin-center/azure/azure-update-management)（“更新”工具中）****
- 用于 VM 的 Azure Monitor（在服务器设置中），即虚拟机见解

你可以从这些工具中的任何一个开始使用 Azure Monitor。 如果你以前从未使用过 Azure Monitor，Windows 管理中心将自动提供 Log Analytics 工作区（以及 Azure 自动化帐户，如果需要的话），并在目标服务器上安装和配置 MMA。 然后再将相应的解决方案安装到工作区中。

例如，如果先使用“更新”工具设置 Azure 更新管理，则 Windows Admin Center 将执行以下操作****：

1. 在计算机上安装 MMA。
2. 创建 Log Analytics 工作区和 Azure 自动化帐户（因为本例中必须使用 Azure 自动化帐户）。
3. 在新创建的工作区中安装更新管理解决方案。

如果要在同一服务器上的 Windows 管理中心内添加另一个监视解决方案，Windows 管理中心只需将该解决方案安装到该服务器连接到的现有工作区中。 Windows 管理中心还将安装任何其他必需的代理。

如果连接到其他服务器，但已设置 Log Analytics 工作区（通过 Windows Admin Center 或在 Azure 门户中手动设置），则也可以在服务器上安装 MMA 并将其连接到现有工作区。 将服务器连接到工作区时，它会自动开始收集数据并向该工作区中安装的解决方案报告。

## <a name="azure-monitor-for-virtual-machines-virtual-machine-insights"></a>用于虚拟机的 Azure Monitor（虚拟机见解）

在“服务器设置”中设置用于 VM 的 Azure Monitor 时，Windows 管理中心将启用用于 VM 的 Azure Monitor 解决方案，也称为虚拟机见解****。 使用此解决方案，你可监视服务器运行状况和事件，创建电子邮件警报，获得整个环境中服务器性能的统一视图，以及可视化连接到给定服务器的应用、系统和服务。

> [!NOTE]
> 不管名称如何，虚拟机见解都适用于物理服务器和虚拟机。

使用 Azure Monitor 的免费 5 GB 数据/月/客户额度，你可以轻松地在一两台服务器上试用，而无需担心付费。 继续阅读以了解将服务器载入到 Azure Monitor 中的其他好处，例如获得环境中服务器的系统性能的统一视图。

## <a name="onboard-your-cluster-using-windows-admin-center"></a>使用 Windows 管理中心载入群集

将群集载入到 Azure Monitor 的最简单方法是，使用 Windows 管理中心中的自动工作流配置运行状况服务和 Log Analytics，然后安装 MMA。

:::image type="content" source="media/monitor/onboarding.gif" alt-text="将群集载入到 Azure Monitor 的图像":::

在服务器连接的“概述”页中，单击“管理警报”新按钮，或转到“服务器设置”>“监视和警报”**** ****。 在此页中，单击“设置”并完成设置窗格，将服务器载入到 Azure Monitor****。 管理中心负责配置 Azure Log Analytics 工作区，安装所需的代理，并确保配置虚拟机见解解决方案。 完成后，服务器将向 Azure Monitor 发送性能计数器数据，使你能够从 Azure 门户查看和创建基于此服务器的电子邮件警报。

## <a name="onboard-your-cluster-manually-using-powershell"></a>使用 PowerShell 手动载入群集

如果你想要手动载入群集，请执行以下步骤。

### <a name="configure-health-service"></a>配置运行状况服务

首先要做的就是配置群集。 你可能也知道，[运行状况服务](/windows-server/failover-clustering/health-service-overview)改进了运行存储空间直通的群集的日常监视和操作体验。

如上所述，Azure Monitor 从群集中运行的每个节点收集日志。 因此，我们必须将运行状况服务配置为写入事件通道，这恰好是：

```
Event Channel: Microsoft-Windows-Health/Operational
Event ID: 8465
```

若要配置运行状况服务，请运行：

```PowerShell
get-storagesubsystem clus* | Set-StorageHealthSetting -Name "Platform.ETW.MasTypes" -Value "Microsoft.Health.EntityType.Subsystem,Microsoft.Health.EntityType.Server,Microsoft.Health.EntityType.PhysicalDisk,Microsoft.Health.EntityType.StoragePool,Microsoft.Health.EntityType.Volume,Microsoft.Health.EntityType.Cluster"
```

运行上述 cmdlet 会通知运行状况设置开始将事件写入 Microsoft-Windows-Health/Operational 事件通道**。

### <a name="configure-log-analytics"></a>配置 Log Analytics

在群集上正确设置日志记录之后，下一步就是正确配置 Log Analytics。

为了提供概述，[Azure Log Analytics](/azure/azure-monitor/platform/agent-windows) 可将物理或虚拟 Windows 计算机中的数据从数据中心或其他云环境直接收集到单个存储库中，以便进行详细的分析和关联。

若要了解支持的配置，请查看[支持的 Windows 操作系统](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems)和[网络防火墙配置](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

#### <a name="log-in-to-azure-portal"></a>登录到 Azure 门户

通过 [https://portal.azure.com](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 登录到 Azure 门户。

#### <a name="create-a-workspace"></a>创建工作区

有关下面列出的步骤的详细信息，请参阅 [Azure Monitor 文档](/azure/azure-monitor/learn/quick-collect-windows-computer)。

1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。

    :::image type="content" source="media/monitor/azure-portal-01.png" alt-text="Azure 门户":::

2. 单击“创建”****，然后为以下各项选择选项：

   * 为新的 Log Analytics 工作区**** 提供名称，如 DefaultLAWorkspace**。
   * 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
   * 对于“资源组”，选择包含一个或多个 Azure 虚拟机的现有资源组。

    :::image type="content" source="media/monitor/create-loganalytics-workspace-02.png" alt-text="创建 Log Analytics 资源边栏选项卡":::

3. 在“Log Analytics 工作区”窗格上提供所需信息后，单击“确定”**** ****。

在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度。

#### <a name="obtain-workspace-id-and-key"></a>获取工作区 ID 和密钥
在安装适用于 Windows 的 MMA 前，需要先获得 Log Analytics 工作区的工作区 ID 和秘钥。  安装向导需要使用此信息来正确配备代理，并确保它能与 Log Analytics 成功通信。

1. 在 Azure 门户中，单击左上角的“所有服务”****。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 在 Log Analytics 工作区列表中，选择之前创建的 DefaultLAWorkspace。
3. 选择“高级设置”。
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Log Analytics 高级设置":::
4. 选择“已连接的源”，然后选择“Windows 服务器” 。
5. “工作区 ID”和“主密钥”右侧的值**** ****。 暂时保存两者 - 暂时将两者复制粘贴到你最喜欢的编辑器中。

### <a name="installing-the-mma-on-windows"></a>在 Windows 上安装 MMA
请按照下面的步骤安装和配置 Microsoft Monitoring Agent。

> [!IMPORTANT]
> 请确保在群集中的每台服务器上安装此代理，并指示希望在 Windows 启动时运行该代理。

1. 在“Windows 服务器”页上，选择“下载 Windows 代理”，根据 Windows 操作系统的处理器体系结构下载相应的版本。**** ****
2. 运行安装程序在计算机上安装该代理。
3. 在“欢迎”页面上，单击“下一步”。 
4. 在“许可条款”页面上阅读许可协议，然后单击“我接受” 。
5. 在“目标文件夹”页面上更改或保留默认安装文件夹，然后单击“下一步” 。
6. 在“代理安装选项”页上，选择将代理连接到 Azure Log Analytics，单击“下一步”。 
7. 在 Azure Log Analytics 页上，粘贴先前复制的“工作区 ID”和“工作区密钥(主密钥)”**** **** ****。 如果计算机需要通过代理服务器来与 Log Analytics 通信，请单击“高级”并提供代理服务器的 URL 和端口号。 如果代理服务器要求身份验证，请键入用于在代理服务器上进行身份验证的用户名和密码，并单击“下一步”。
8. 提供所需的配置设置后，单击“下一步”。
    :::image type="content" source="media/monitor/log-analytics-mma-setup-laworkspace.png" alt-text="粘贴工作区 ID 和主键":::
9. 在“准备安装”页上检查所做的选择，并单击“安装”。 
10. 在“配置已成功完成”页上，单击“完成”。 

完成后，**Microsoft Monitoring Agent** 将显示在“**控制面板**”中。 可以检查配置，并验证代理是否已连接到 Log Analytics。 处于已连接状态时，在“Azure Log Analytics”选项卡上，代理会显示一条消息****：Microsoft Monitoring Agent 已成功连接到 Microsoft Log Analytics 服务。****

:::image type="content" source="media/monitor/log-analytics-mma-laworkspace-status.png" alt-text="MMA 与 Log Analytics 的连接状态":::

若要了解支持的配置，请查看[支持的 Windows 操作系统](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems)和[网络防火墙配置](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements)。

## <a name="setting-up-alerts-using-windows-admin-center"></a>使用 Windows 管理中心设置警报

将服务器连接到 Azure Monitor 后，可以使用 " **设置 > 监视和警报** " 页中的智能超链接导航到 Azure 门户。 在 Windows 管理中心中，你可以轻松配置将应用于 Log Analytics 工作区中所有服务器的默认警报。 Windows 管理中心自动启用性能计数器进行收集，因此你可以通过自定义许多预定义查询之一或编写自己的警报来[创建新的警报](/azure/azure-monitor/platform/alerts-log)。

:::image type="content" source="media/monitor/setup1.gif" alt-text="配置警报屏幕截图":::

下面列出了可选择加入的警报及其默认条件：

| 警报名称                | 默认条件                                  |
|---------------------------|----------------------------------------------------|
| CPU 使用率           | 持续 10 分钟超过 85%                            |
| 磁盘容量利用率 | 持续 10 分钟超过 85%                            |
| 内存利用率        | 持续 10 分钟可用内存小于 100 MB   |
| 检测信号                 | 持续 5 分钟少于 2 个信号                   |
| 系统严重错误     | 群集系统事件日志中的任何严重警报 |
| 运行状况服务警报      | 群集上的任何运行状况服务故障            |

在 Windows 管理中心配置警报后，可以在 Azure 的 Log Analytics 工作区中查看警报。

:::image type="content" source="media/monitor/setup2.gif" alt-text="查看警报屏幕截图":::

### <a name="collecting-event-and-performance-data"></a>收集事件和性能数据

Log Analytics 可从 Windows 事件日志以及指定用于长期分析的性能计数器中收集事件，并在检测到特定条件时采取措施。 首先，请按照下列步骤操作，配置 Windows 事件日志以及几个常见性能计数器中收集事件。

1. 在 Azure 门户中，单击左下角的“更多服务”****。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 选择“高级设置”。
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Log Analytics 高级设置":::
3. 选择“数据”，然后选择“Windows 事件日志”。
4. 在此处，通过键入下面的名称并单击加号“+”来添加运行状况服务事件通道****。
   ```
   Event Channel: Microsoft-Windows-Health/Operational
   ```
5. 在表中，选中严重性“错误”和“警告”。
6. 单击页面顶部的“保存”来保存配置。****
7. 选择“Windows 性能计数器”，在 Windows 计算机上启用性能计数器收集。
8. 首次为新的 Log Analytics 工作区配置 Windows 性能计数器时，可以选择快速创建几个通用的计数器。 将这些计数器在一个复选框中依次列出。
    :::image type="content" source="media/monitor/windows-perfcounters-default.png" alt-text="默认选中的 Windows 性能计数器":::
    单击“添加所选性能计数器”****。  随即会添加它们，并且通过 10 秒收集示例间隔进行预设。
9. 单击页面顶部的“保存”来保存配置。****

## <a name="create-queries-and-alerts-based-on-log-data"></a>基于日志数据创建查询和警报

如果已完成此项，群集应向 Log Analytics 发送日志和性能计数器。 下一步是创建警报规则，以定期自动运行日志搜索。 如果日志搜索的结果与特定条件匹配，则会触发警报，向你发送电子邮件或文本通知。 下面我们来探讨这个问题。

### <a name="create-a-query"></a>创建查询

首先打开“日志搜索”门户****。

1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“监视器”****。 开始键入时，会根据输入筛选该列表。 选择“监视器”****。
2. 在“监视器”导航菜单上，选择“Log Analytics”，然后选择一个工作区**** ****。

用于检索某些要使用的数据的最快方法是使用一个简单查询，它可返回表中的所有记录。 在搜索框中键入以下查询，然后单击“搜索”按钮。

```
Event
```

数据会返回到默认列表视图中，并可看到返回的总记录条数。

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-01.png" alt-text="简单查询屏幕截图":::

屏幕左侧是“筛选器”窗格，可用于向查询添加筛选而无需直接修改查询。  该记录类型显示有多个记录属性，可选择一个或多个属性值来缩小搜索结果范围。

选中“EVENTLEVELNAME”下“错误”旁边的复选框，或键入以下内容将结果限制为错误事件**** ****。

```
Event | where (EventLevelName == "Error")
```

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-02.png" alt-text="筛选器屏幕截图":::

在对所关注的事件进行了适当的查询之后，保存这些查询，然后进行下一步。

### <a name="create-alerts"></a>创建警报
现在，让我们看一看创建警报的示例。

1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 在左窗格中选择“警报”，然后单击页面顶部的“新建警报规则”，以便创建新的警报。**** ****
    :::image type="content" source="media/monitor/alert-rule-02.png" alt-text="创建新的警报规则屏幕截图":::
3. 第一步是在“创建警报”部分选择充当资源的 Log Analytics 工作区，因为这是基于日志的警报信号****。  对结果进行筛选，方法是：从下拉列表中选择特定的“订阅”（如果有多个），其中包含此前创建的 Log Analytics 工作区****。  从下拉列表中选择“Log Analytics”，对“资源类型”进行筛选。**** ****  最后，选择**资源** **DefaultLAWorkspace**，然后单击“完成”。****
    :::image type="content" source="media/monitor/alert-rule-03.png" alt-text="创建新的警报规则步骤 1 屏幕截图":::
4. 在“警报条件”部分下，单击“添加条件”，选择保存的查询，然后指定警报规则遵循的逻辑**** ****。
5. 使用以下信息配置警报：a. 从“基于”下拉列表中选择“指标度量”**** ****。  指标度量将为查询中其值超出指定阈值的每个对象创建一个警报。
   b. 对于“条件”，选择“大于”并指定阈值**** ****。
   c. 然后定义触发警报的时间。 例如，可以选择“连续违规”，然后从下拉列表中选择“大于”值 3**** ****。
   d. 在“评估条件”部分下，将“期间”值修改为“30”分钟，频率改为“5”**** **** ****。 此规则将每五分钟运行一次，返回从当前时间算起过去 30 分钟内创建的记录。  将时间段设置为更宽的时间窗口可以解决数据延迟的可能性，并确保查询返回数据以避免警报永远不会触发的漏报。
6. 单击“完成”，完成警报规则。****
    :::image type="content" source="media/monitor/alert-signal-logic-02.png" alt-text="配置警报信号屏幕截图":::
7. 现在转到第二步，在“警报规则名称”字段中提供警报的名称，例如“所有错误事件的警报”**** ****。  指定“说明”，详细描述该警报的具体信息，并从提供的选项中选择“关键(严重性 0)”作为“严重性”值。**** **** ****
8. 若要在创建后立即激活警报规则，请接受“创建后启用规则”选项的默认值。****
9. 第三步也是最后一步，指定“操作组”****，确保每次触发警报时都执行相同的操作，而且这些操作可以用于定义的每项规则。 使用以下信息配置新操作组：a. 选择“新建操作组”，此时会显示“添加操作组”窗格。**** ****
   b. 对于“操作组名称”****，请指定一个长名称，例如“IT 操作 - 通知”，以及一个“短名称”，例如“itops-n”。**** **** ****
   c. 验证“订阅”和“资源组”的默认值是否正确**** ****。 如果否，请从下拉列表中选择正确的值。
   d. 在“操作”部分指定操作的名称，例如“发送电子邮件”，然后在“操作类型”下的下拉列表中选择“电子邮件/短信/推送/语音”。**** **** **** “电子邮件/短信/推送/语音”属性窗格会在右侧打开，其中包含更多的信息。****
   e. 在 **电子邮件/SMS/推送/语音** 窗格上，选择并设置你的首选项。 例如，启用“电子邮件”，并提供有效的可以接收邮件的电子邮件 SMTP 地址****。
   f. **** 单击“确定”以保存你的更改。<br><br>

    :::image type="content" source="media/monitor/action-group-properties-01.png" alt-text="创建新的操作组屏幕截图":::

10. 单击“确定”****，完成操作组。
11. 单击“创建警报规则”，完成警报规则。**** 该警报会立即开始运行。
    :::image type="content" source="media/monitor/alert-rule-01.png" alt-text="完成创建新警报规则的屏幕截图":::

### <a name="example-alert"></a>示例警报

作为参考，下面提供 Azure 中的示例警报。

:::image type="content" source="media/monitor/alert.gif" alt-text="Azure 警报屏幕截图":::

下面是将由 Azure Monitor 发送的电子邮件的示例：

:::image type="content" source="media/monitor/warning.png" alt-text="警报电子邮件示例屏幕截图":::

## <a name="create-custom-kusto-queries-in-log-analytics"></a>在 Log Analytics 中创建自定义 Kusto 查询

还可以使用 Kusto 查询语言在 Azure Monitor 中[编写自定义日志查询](/azure/azure-monitor/log-query/get-started-queries)，以从一个或多个虚拟机收集数据。

## <a name="get-a-consolidated-view-across-multiple-servers"></a>跨多个服务器获取统一视图

如果将多个服务器载入到 Azure Monitor 中的单个 Log Analytics 工作区，则可以从 Azure Monitor 中的[虚拟机见解](/azure/azure-monitor/insights/vminsights-overview)解决方案获得所有这些服务器的统一视图。 （请注意，只有适用于 Azure Monitor 的虚拟机见解的“性能”和“映射”选项卡才能与本地服务器一起使用，运行状况选项卡只能与 Azure VM 一起使用。）若要在 Azure 门户中查看此内容，请转到“Azure Monitor”>“虚拟机”（位于“见解”下），然后导航到“性能”或“映射”选项卡**** **** ****。

## <a name="visualize-connected-services"></a>可视化连接服务

当 Windows 管理中心将服务器载入到 Azure Monitor 中的虚拟机见解解决方案时，它还会启用[服务映射](/azure/azure-monitor/insights/service-map)功能。 此功能会自动发现应用程序组件并映射服务之间的通信，以便你可以从 Azure 门户轻松查看服务器之间的连接，并提供非常详细的信息。 你可以通过转到 Azure 门户并选择“Azure Monitor”>“虚拟机”（位于“见解”下）并导航到“映射”选项卡来找到此功能**** ****。

> [!NOTE]
> 目前在六个公共区域提供了针对 Azure Monitor 的虚拟机见解的可视化。  有关最新信息，请查看[用于 VM 的 Azure Monitor 文档](/azure/azure-monitor/insights/vminsights-onboard#log-analytics)。 必须将 Log Analytics 工作区部署在一个受支持的区域中，才能获得上述虚拟机见解解决方案提供的其他优势。

## <a name="disabling-monitoring"></a>禁用监视

若要完全断开服务器与 Log Analytics 工作区的连接，请卸载 MMA。 这意味着此服务器将不再向工作区发送数据，并且该工作区中安装的所有解决方案将不再从该服务器收集和处理数据。 但是，这不会影响工作区本身；向该工作区报告的所有资源仍将继续运行。 若要卸载 Windows Admin Center 中的 MMA 代理，请连接到服务器，然后转到“已安装的应用”，查找 Microsoft Monitoring Agent，然后选择“删除” 。

如果要关闭工作区内的特定解决方案，则需要[从 Azure 门户中删除监视解决方案](/azure/azure-monitor/insights/solutions#remove-a-management-solution)。 删除监视解决方案意味着不再为向该工作区报告的任何服务器生成由该解决方案创建的见解__。 例如，如果卸载用于 VM 的 Azure Monitor 解决方案，你将不再能够从连接到工作区的任何计算机上看到有关 VM 或服务器性能的信息。

## <a name="next-steps"></a>后续步骤

相关主题另请参阅：

- [了解有关 Azure 与 Windows 管理中心集成的更多信息](/windows-server/manage/windows-admin-center/azure/)
- [使用 Azure Monitor 发送有关运行状况服务故障的电子邮件](/windows-server/storage/storage-spaces/configure-azure-monitor)
