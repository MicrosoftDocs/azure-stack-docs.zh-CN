---
title: 在 Azure Stack Hub 上使用用于容器的 Azure Monitor
description: 了解如何在 Azure Stack Hub 上使用用于容器的 Azure Monitor。
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 9/2/2020
ms.openlocfilehash: 5b175c4d2c5f8512841dca5e984c05c13ad2951d
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873650"
---
# <a name="use-azure-monitor-for-containers-on-azure-stack-hub"></a>在 Azure Stack Hub 上使用用于容器的 Azure Monitor

可以使用用于容器的 [Azure Monitor](/azure/azure-monitor/) 来监视在 Azure Stack Hub 中通过 AKS 引擎部署的 Kubernetes 群集中的容器。 

> [!IMPORTANT]
> Azure Stack Hub 上用于容器的 Azure Monitor 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以通过指标 API 从 Kubernetes 中可用的控制器、节点和容器中收集内存和处理器指标，从而使用 Azure Monitor 查看容器性能。 此外，该服务还会收集容器日志。 可以使用这些日志诊断 Azure 的本地群集中的问题。 从 Kubernetes 群集设置监视后，将自动收集这些指标和日志。 适用于 Linux 的 Azure Monitor Log Analytics 代理的容器化版本会收集日志。 Azure Monitor 将指标和日志存储在 Azure 订阅的可供访问的 Log Analytics 工作区中。

可通过两种方式在群集上启用 Azure Monitor。 这两种方式都需要在 Azure 中设置 Azure Monitor Log Analytics 工作区。

## <a name="prerequisites"></a>先决条件

这两种方法都需要符合 [Azure Monitor - 容器](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers)中列出的[先决条件](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers#pre-requisites)。

## <a name="method-one"></a>方法一

还可以使用 [Helm](https://helm.sh/) 图表在群集中安装监视代理。 请按照 [Azure Monitor - 容器](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers)一文中的说明操作。

## <a name="method-two"></a>方法二

你可以在 AKS 引擎群集规范 json 文件中指定一个加载项。 该文件也称为“API 模型”。 此加载项中提供用于存储监视信息的 Azure Log Analytics 工作区的 WorkspaceGUID 和 WorkspaceKey 的 base64 编码版本。

在此示例中可以找到 Azure Stack Hub 群集支持的 API 定义：[kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json)。 具体而言，请在 **kubernetesConfig** 中查找 **addons** 属性：

```JSON  
 "orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Guid in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)  
- 请阅读[用于容器的 Azure Monitor 概述](/azure/azure-monitor/insights/container-insights-overview)
