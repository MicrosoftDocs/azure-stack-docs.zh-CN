---
title: VM 更新和管理 Azure Stack |Microsoft Docs
description: 了解如何使用 Azure Monitor 为 Vm、 更新管理、 更改跟踪和清单解决方案在 Azure 自动化中的管理 Windows 和 Azure Stack 中部署的 Linux Vm。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: cb8258c0f837d0e70ba87a26246f055b0efe5c00
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64301689"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack VM 更新和管理
以下 Azure 自动化解决方案功能可用于管理 Windows 和 Linux Vm 使用 Azure Stack 部署：

- **[更新管理](https://docs.microsoft.com/azure/automation/automation-update-management)**。 使用更新管理解决方案中，可以快速评估所有代理计算机上的可用更新的状态和管理这些 Windows 和 Linux Vm 中安装所需的更新的过程。

- **[更改跟踪](https://docs.microsoft.com/azure/automation/automation-change-tracking)**。 对已安装的软件、 Windows 服务、 Windows 注册表和文件，并受监视服务器上的 Linux 守护程序的更改发送到云中的 Azure Monitor 服务进行处理。 逻辑应用于接收的数据，云服务则记录数据。 通过使用“更改跟踪”仪表板上的信息，可以轻松查看服务器基础结构中所做的更改。

- **[库存](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**。 库存跟踪为 Azure Stack 虚拟机提供了用于设置和配置清单收集的基于浏览器的用户界面。

- **[适用于 Vm 的 azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)**。 适用于 Vm 的 azure 监视器监视 Azure 和 Azure Stack 虚拟机 (VM) 和规模的虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。 

> [!IMPORTANT]
> 这些解决方案都与用于管理 Azure Vm 的相同。 Azure 和 Azure Stack Vm 进行管理相同的方式，从相同的接口，使用相同的工具。 Azure Stack Vm 与 Azure Stack 配合使用更新管理、 更改跟踪、 清单和 Azure 监视虚拟机解决方案时还进行定价与 Azure Vm 相同。

## <a name="prerequisites"></a>必备组件
使用这些功能更新和管理 Azure Stack Vm 之前，必须满足几个先决条件。 在 Azure 门户和 Azure Stack 管理门户，其中包括必须采取的步骤。

### <a name="in-the-azure-portal"></a>在 Azure 门户中
若要使用 Azure Monitor 为 Vm、 清单、 更改跟踪和更新管理 Azure 自动化功能 Azure Stack Vm，首先需要启用 Azure 中的这些解决方案。

> [!TIP]
> 如果已为 Azure Vm 启用这些功能，可以使用预先存在的 LogAnalytics 工作区凭据。 如果已 LogAnalytics 工作区 Id 和你想要使用的主键，跳到[下一步部分](./vm-update-management.md#in-the-azure-stack-administration-portal)。 否则，继续在此部分中创建新的 LogAnalytics 工作区和自动化帐户。

启用这些解决方案的第一步是向[创建 LogAnalytics 工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)在 Azure 订阅中。 Log Analytics 工作区是一个唯一的 Azure Monitor 日志环境具有其自己的数据存储库、 数据源和解决方案。 创建一个工作区后，请注意工作区 Id 和密钥。 若要查看此信息，请转到工作区边栏选项卡，单击**高级设置**，并查看**工作区 ID**并**Primary Key**值。 

接下来，必须[创建自动化帐户](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)。 自动化帐户是 Azure 自动化资源的容器。 它使您能够隔离环境，或者进一步组织您的自动化工作流和资源。 创建自动化帐户后，您需要以启用清单，更改跟踪，并更新管理功能。 若要执行此操作，请执行以下步骤启用每个功能：

1. 在 Azure 门户中，转到你想要使用自动化帐户。

2. 选择要启用的解决方案 (任一**清单**，**更改跟踪**，或**更新管理**)。

3. 使用**选择工作区...** 下拉列表选择要使用的 Log Analytics 工作区。

4. 验证所有剩余信息是否正确，然后依次**启用**以启用此解决方案。

5. 重复步骤 2-4，以启用所有三种解决方案。 

   [![](media/vm-update-management/1-sm.PNG "启用自动化帐户功能")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>启用用于 VM 的 Azure Monitor

用于 VM 的 Azure Monitor 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。

作为一种解决方案，用于 VM 的 Azure Monitor 支持监视本地或其他云提供程序中托管的 VM 的性能和应用程序依赖关系。 三个主要功能提供深入的见解：

1. 运行 Windows 和 Linux 的 Azure Vm 的逻辑组件：根据预配置的运行状况条件进行衡量，并在满足评估条件时提醒你。 

2. 预定义趋势性能图表：显示来宾 VM 操作系统的核心性能指标。

3. 依赖项映射：显示来自各种资源组和订阅的 VM 的互连组件。

创建 Log Analytics 工作区后，将需要在 Linux 和 Windows Vm 上的集合的工作区中启用性能计数器，以及安装和启用工作区中的 ServiceMap 和 InfrastructureInsights 解决方案。 中介绍该过程[部署 Vm 的 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#deploy-azure-monitor-for-vms)指南。

### <a name="in-the-azure-stack-administration-portal"></a>在 Azure Stack 管理门户
启用后在 Azure 门户中的 Azure 自动化解决方案，接下来需要登录到云管理员在 Azure Stack 管理门户并下载**Azure Monitor、 更新和配置管理**和**Azure Monitor、 更新和适用于 Linux 的配置管理**扩展 Azure Stack marketplace 项。 

   ![Azure 监视器、 更新和配置管理扩展 marketplace 项](media/vm-update-management/2.PNG) 

若要启用的 Vm 映射解决方案 Azure Monitor 和深入了解网络依赖项，你将需要下载**Azure 监视器依赖关系代理**:

   ![Azure 监视器依赖关系代理](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>为 Azure Stack 虚拟机启用更新管理
按照以下步骤来为 Azure Stack Vm 启用更新管理。

1. 登录到 Azure Stack 用户门户。

2. 在 Azure Stack 用户门户，转到你想要启用这些解决方案，请单击虚拟机的扩展边栏选项卡 **+ 添加**，选择**Azure 更新和配置管理**扩展，然后单击**创建**:

   [![](media/vm-update-management/3-sm.PNG "VM 扩展边栏选项卡")](media/vm-update-management/3-lg.PNG#lightbox)

3. 提供将先前创建的工作区 Id 和主密钥，若要链接的代理具有 LogAnalytics 工作区，然后单击**确定**将扩展部署。

   [![](media/vm-update-management/4-sm.PNG "提供工作区 Id 和密钥")](media/vm-update-management/4-lg.PNG#lightbox) 

4. 如中所述[自动化更新管理文档](https://docs.microsoft.com/azure/automation/automation-update-management)，您需要为你想要管理每个 VM 启用更新管理解决方案。 若要启用向工作区报告的所有 Vm 解决方案，选择**更新管理**，单击**管理机**，然后选择**上所有可用的和未来计算机都启用**选项。

   [![](media/vm-update-management/5-sm.PNG "提供工作区 Id 和密钥")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > 重复此步骤，为 Azure Stack Vm 该报表到工作区中启用每个解决方案。 
  
Azure 更新和配置管理扩展已启用后，扫描是每天执行两次为每个托管的 VM。 查询上次更新时间，以确定是否已更改状态每隔 15 分钟，将调用 API。 如果状态已更改，则会启动符合性扫描。

虚拟机进行扫描后，它们将显示在更新管理解决方案中的 Azure 自动化帐户中： 

   [![](media/vm-update-management/6-sm.PNG "提供工作区 Id 和密钥")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> 可能需要 30 分钟到 6 小时，仪表板才会显示受托管计算机提供的已更新数据。

Azure Stack Vm 现在可以包含在计划的更新部署以及 Azure Vm。

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>在 Azure Stack 上运行的虚拟机中启用 Azure 监视器
一旦 VM 拥有**Azure Monitor、 更新和配置管理**并**Azure 监视器依赖关系代理**安装的扩展，它将开始报告中的数据[Azure Monitor适用于 Vm](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)解决方案。 

> [!TIP]
> **Azure 监视器依赖关系代理**扩展不需要任何参数。 用于 VM 的 Azure Monitor 映射依赖项代理本身不传输任何数据，它不需要对防火墙或端口做出任何更改。 映射数据始终由 Log Analytics 代理直接传输到 Azure Monitor 服务；如果 IT 安全策略不允许网络中的计算机连接到 Internet，则通过 [OMS 网关](https://docs.microsoft.com/azure/azure-monitor/platform/gateway)传输这些数据。

用于 VM 的 Azure Monitor 包含一组针对几项关键性能指标 (KPI) 的性能图表，帮助你确定虚拟机的性能状况。 这些图表显示一段时间内的资源利用率，可让你识别瓶颈和异常，或切换到列出每个计算机的透视图，以根据所选指标查看资源利用率。 虽然有大量元素时处理性能，请注意，与处理器、 内存、 网络适配器和磁盘利用率相关的 Vm 监视关键的操作系统性能指标的 Azure 监视器。 “性能”对运行状况监视功能做了补充，可帮助揭示可能指示系统组件发生了故障的问题，支持调整和优化以提高效率，或支持容量规划。

   ![Azure 监视性能选项卡](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

在 Azure Stack 中运行的 Windows 和 Linux 虚拟机上查看发现的应用程序组件可以观察到通过 Azure Monitor 的两种方式对于 Vm，请从虚拟机直接或跨 vm 从 Azure Monitor 的组。
[Vm （预览版） 将地图以了解应用程序组件的使用 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps)文章将帮助你了解两个角度以及如何使用地图功能的体验。

   ![Azure 监视性能选项卡](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>启用更新管理使用资源管理器模板
如果有大量的 Azure Stack Vm，则可以使用[此 Azure 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)来更轻松地部署虚拟机上的解决方案。 该模板将 Microsoft Monitoring Agent 扩展部署到现有的 Azure Stack VM，并将其添加到现有 Azure LogAnalytics 工作区。
 
## <a name="next-steps"></a>后续步骤
[优化 SQL Server 虚拟机性能](azure-stack-sql-server-vm-considerations.md)




