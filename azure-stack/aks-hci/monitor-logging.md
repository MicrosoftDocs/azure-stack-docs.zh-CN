---
title: 监视 Azure Stack HCI 群集上的 Azure Kubernetes 服务
description: 监视和查看 Azure Stack HCI 群集上的 Azure Kubernetes 服务的日志记录数据
author: v-susbo
ms.topic: how-to
ms.date: 01/26/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: 7bed9a767728525ae1e40ce4f0e607cc6f034055
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116795"
---
# <a name="monitor-aks-on-azure-stack-hci-clusters"></a>监视 Azure Stack HCI 群集上的 AKS

Azure Stack HCI 上有两种类型的监视和日志记录解决方案可用于 AKS。 下面是两个解决方案的比较： 

| 解决方案  | Azure 连接  | 支持和服务  | 成本 | 部署 |
| ------- |  ------------  | ---------  | --------------  | ---------------- |
| Azure Monitor | 需要使用用于 Kubernetes 的 Azure Arc 将 Azure Stack HCI 群集上的 AKS 连接到 Azure | 完全支持并由 Microsoft 提供服务 | 需要注册 Azure Monitor 服务 |  使用 Azure Arc [监视群集](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-overview) |
| 本地监视和日志记录 | 不需要 Azure 连接 | Microsoft (支持的开放源代码软件不支持协议或 Sla) 、社区和/或外部供应商  | 依赖于供应商 | 客户驱动的，请参阅 [使用本地监视监视群集](#use-on-premises-monitoring) |

若要在 Azure Stack HCI 群集上使用 AKS Azure Monitor，请参阅 [Azure Monitor 概述](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-overview)。 

## <a name="use-on-premises-monitoring"></a>使用本地监视

在生产环境中运行应用时，监视群集上的控制平面节点和工作负荷的运行状况、性能和资源使用情况非常重要。 若要了解如何设置本地监视解决方案，请参阅 [安装 Prometheus 和 Grafana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring)。 此监视解决方案包含以下两个工具： 

- **Prometheus** 是一个监视和警报工具包，可用于监视容器化工作负荷。 Prometheus 适用于不同类型的收集器和代理，用于收集指标并将其存储在可查询和查看报表的数据库中。 

- **Grafana** 是一种用于在 Grafana 仪表板上查看、查询和可视化度量值的工具。 它已预先配置为使用 Prometheus 作为数据源。 

### <a name="install-monitoring-tools"></a>安装监视工具

若要在环境中快速设置简单的监视解决方案，请运行 [Setup-Monitoring.ps1](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#easy-steps-to-setup-monitoring-to-use-local-port-forward-to-access-grafana) PowerShell 脚本。 该脚本包括快速启动和运行监视解决方案所需的所有配置步骤。 

如果要将入口控制器包含在 Grafana 中，请按照 [使用入口控制器访问 Grafana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#detailed-steps-to-setup-monitoring-to-use-ingress-controller-to-access-grafana)的步骤进行操作。 入口控制器是为 Kubernetes services (TLS) 终止提供反向代理、可配置的流量路由和传输层安全性的软件。

### <a name="on-premises-logging"></a>本地日志记录

日志记录对于疑难解答和诊断至关重要。 Azure Stack HCI 上的 AKS 中的日志记录解决方案基于 Elasticsearch、熟知位和 Kibana (EFK) 。 这些组件都部署为容器： 

- 熟知位是日志处理程序和转发器，它从不同的源收集数据和日志，然后在 Elasticsearch 中对其进行格式设置、统一保存并存储。 
- Elasticsearch 是一个分布式搜索和分析引擎，可以集中存储日志以实现快速搜索和数据分析。  
- Kibana 在 web 仪表板中提供交互式可视化效果。 利用此工具，你可以查看和查询存储在 Elasticsearch 中的日志，并通过图形和仪表板将其可视化。

若要设置本地日志记录解决方案，请参阅以下步骤 [设置日志记录以访问 Kibana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#easy-steps-to-setup-logging-to-use-local-port-forward-to-access-kibana)。 本文包含跨群集收集、聚合和查询容器日志所需的所有组件。 

有关高级配置步骤，请参阅 [Windows 日志记录](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#detailed-steps-to-setup-logging)。

## <a name="next-steps"></a>后续步骤

- [在 Kubernetes 群集上部署 Linux 应用程序](./deploy-linux-application.md)。
- [在 Kubernetes 群集上部署 Windows Server 应用程序](./deploy-windows-application.md)。
- [Kubernetes 核心概念](kubernetes-concepts.md)。
