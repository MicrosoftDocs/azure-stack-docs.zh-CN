---
title: 使用 Azure Stack 中心的容器 Azure Monitor
description: 了解如何在 Azure Stack 中心使用容器 Azure Monitor。
author: mattbriggs
ms.topic: article
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: ff54cd88ce7ade137a73637f2a89e96138854ff3
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704924"
---
# <a name="use-azure-monitor-for-containers-on-azure-stack-hub"></a>使用 Azure Stack 中心的容器 Azure Monitor

可以使用容器[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)来监视在 Azure Stack Hub 中部署了 Kubernetes 群集的 AKS 引擎中的容器。 

> [!IMPORTANT]
> Azure Stack 中心的容器 Azure Monitor 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以通过指标 API 从 Kubernetes 中提供的控制器、节点和容器中收集内存和处理器指标，从而使用 Azure Monitor 查看容器性能。 此外，服务还会收集容器日志。 可以使用这些日志诊断 Azure 的本地群集中的问题。 设置 Kubernetes 群集的监视后，将自动收集这些指标和日志。 适用于 Linux 的 Azure Monitor Log Analytics 代理的容器化版本收集日志。 Azure Monitor 将指标和日志存储在你的 Azure 订阅可访问的 log analytics 工作区中。

可以通过两种方式在群集上启用 Azure Monitor。 这两种方法都需要在 Azure 中设置 Azure Monitor Log Analytics 工作区。

## <a name="prerequisites"></a>必备条件

这两种方法都需要[Azure Monitor –容器](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers)中列出的[先决条件](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers#pre-requisites)。

## <a name="method-one"></a>方法一

还可以使用[Helm](https://helm.sh/)图在群集中安装监视代理。 按照以下文章[Azure Monitor –容器](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers)中的说明进行操作。

## <a name="method-two"></a>方法2

可以在 AKS 引擎群集规范 json 文件中指定**加载**项。 文件也称为 API 模型。 在此加载项中，提供用于存储监视信息的 Azure Log Analytics 工作区的 base64 编码版本的**WorkspaceGUID**和**WorkspaceKey** 。

在此示例中，可以找到 Azure Stack 中心群集支持的 API 定义： [monitoring_existing_workspace_id_and_key kubernetes](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json)。 具体而言，请在**kubernetesConfig**中查找**加载项**属性：

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

- 了解[Azure Stack 集线器上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)  
- 了解[容器 Azure Monitor 概述](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)
