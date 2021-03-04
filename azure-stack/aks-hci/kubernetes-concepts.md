---
title: 概念 - Azure Stack HCI 上的 Azure Kubernetes 服务 (AKS) 的 Kubernetes 基础知识
description: 了解 Kubernetes 的基本群集和工作负载组件以及它们与 Azure Stack HCI 上的 Azure Kubernetes 服务中各个功能的关系
author: daschott
ms.author: daschott
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 93c0103cc13fdffbee7ba0d16aea9c33474565ba
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116761"
---
# <a name="clusters-and-workloads-for-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上的 Azure Kubernetes 服务的群集和工作负荷

> 适用于 Azure Stack HCI 上的 AKS、Windows Server 2019 Datacenter 上的 AKS 运行时

Azure Stack HCI 上的 Azure Kubernetes 服务是由 Azure Stack HCI 提供支持的企业级 Kubernetes 容器平台。 它包括 Microsoft 支持的核心 Kubernetes、附加产品、专门构建的 Windows 容器主机和 Microsoft 支持的 Linux 容器主机，其目标是提供简单的部署和生命周期管理体验 **** 。

本文介绍了 Kubernetes 核心基础结构组件，例如控制平面、节点和节点池  。 还介绍了 Pod、部署和集等工作负荷资源，以及如何将资源分组到命名空间   。

## <a name="kubernetes-cluster-architecture"></a>Kubernetes 群集体系结构
Kubernetes 是 Azure Stack HCI 上的 Azure Kubernetes 服务的核心组件。 Azure Stack HCI 上的 Azure Kubernetes 服务使用一组预定义配置有效地部署 Kubernetes 群集，并考虑可伸缩性。
 
部署操作会创建多个 Linux 或 Windows 虚拟机，并将这些虚拟机一起加入以创建 Kubernetes 群集。 
 
部署的系统已准备好接收标准 Kubernetes 工作负载、缩放这些工作负载，甚至是根据需要纵向扩展和缩减虚拟机数量和群集数量。

在 Azure Stack HCI 上，Azure Kubernetes 服务群集划分为两个主要组件：

- 管理群集提供用于部署和管理一个或多个目标群集的核心业务流程机制和接口。
- 目标群集（也称为工作负载群集）是应用程序工作负载运行并由管理群集进行管理的位置。

![说明 Azure Stack HCI 上的 Azure Kubernetes 服务的技术体系结构](.\media\concepts\architecture.png)

## <a name="management-cluster"></a>管理群集
创建 Azure Stack HCI 上的 Azure Kubernetes 服务群集时，会自动创建和配置管理群集。 此管理群集负责预配和管理工作负载在其中运行的目标群集。 管理群集包括以下核心 Kubernetes 组件：
- API 服务器 - API 服务器是公开基础 Kubernetes API 的方式。 此组件为管理工具（如 Windows Admin Center、PowerShell 模块或 `kubectl`）提供交互。
- 负载均衡器 - 负载均衡器是单个专用 Linux VM，具有用于管理群集 API 服务器的负载均衡规则。

### <a name="windows-admin-center"></a>Windows Admin Center
Windows Admin Center 为 Kubernetes 操作员提供直观的 UI，以管理 Azure Stack HCI 上的 Azure Kubernetes 服务群集的生命周期。

### <a name="powershell-module"></a>PowerShell 模块
PowerShell 模块是下载、配置和部署 Azure Stack HCI 上的 Azure Kubernetes 服务的一种简单方法。 PowerShell 模块还支持部署和配置其他目标群集，以及重新配置现有目标群集。

## <a name="target-cluster"></a>目标群集
目标（工作负载）群集是 Kubernetes 的高度可用部署，使用 Linux VM 运行 Kubernetes 控制平面组件以及 Linux 工作器节点。 基于 Windows Server Core 的 VM 用于建立 Windows 工作器节点。 一个管理群集可以管理一个或多个目标群集。

### <a name="worker-nodes"></a>辅助角色节点
要运行应用程序和支持服务，需要 Kubernetes 节点。 Azure Stack HCI 上的 Azure Kubernetes 服务目标群集具有一个或多个工作器节点，这是运行 Kubernetes 节点组件的虚拟机 (VM)，并托管组成应用程序工作负载的 Pod 和服务。

### <a name="load-balancer"></a>负载均衡器
负载均衡器是运行 Linux 和 HAProxy + KeepAlive 的虚拟机，用于为管理群集部署的目标群集提供负载均衡服务。

对于每个目标群集，Azure Stack HCI 上的 Azure Kubernetes 服务都会至少添加一个负载均衡器虚拟机 (LB VM)。 除此之外，还可以在目标群集上创建其他负载均衡器以实现 API 服务器的高可用性。 在目标群集上创建的 `LoadBalancer` 类型的任何 Kubernetes 服务都会最终在 LB VM 中创建负载均衡规则。

### <a name="add-on-components"></a>附加组件
可以在任何给定的群集中部署多个可选的附加组件，最值得注意的是： Azure Arc、Prometheus、Grafana 或 Kubernetes 仪表板。

## <a name="kubernetes-components"></a>Kubernetes 组件
此部分介绍可在 Azure Stack HCI 上的 Azure Kubernetes 服务目标群集上部署的核心 Kubernetes 工作负载组件（如 Pod、部署和集），以及如何将资源分组为命名空间。

### <a name="pods"></a>Pod

Kubernetes 使用 Pod 来运行应用程序的实例。 Pod 表示应用程序的单个实例。 Pod 通常与容器存在 1 对 1 映射，但高级方案中一个 Pod 可能包含多个容器。 在同一个节点上共同计划这些多容器 Pod，并允许容器共享相关资源。

创建 Pod 时，可定义资源请求以请求一定数量的 CPU 或内存资源。 Kubernetes 计划程序尝试计划在具有可用资源的节点上运行 Pod，以满足请求。 此外可以指定最大资源限制，防止给定的 Pod 从基础节点消耗过多计算资源。 最佳做法是包括所有 Pod 的资源限制，以帮助 Kubernetes 计划程序了解所需和允许的资源。

有关详细信息，请参阅 [Kubernetes Pod][kubernetes-pods] 和 [Kubernetes Pod 生命周期][kubernetes-pod-lifecycle]。

Pod 是逻辑资源，但容器是应用程序工作负荷的运行位置。 Pod 通常是短暂的可支配资源，单独计划的 Pod 会错过 Kubernetes 提供的一些高可用性和冗余功能。 相反，Pod 由 Kubernetes 控制器（例如 Deployment 控制器）进行部署和管理。

### <a name="deployments-and-yaml-manifests"></a>部署和 YAML 清单

部署表示由 Kubernetes Deployment 控制器管理的一个或多个相同 Pod。 部署定义要创建的副本 (Pod) 数量，Kubernetes 计划程序确保如果 Pod 或节点出现故障，则在正常节点上安排其他 Pod。

可以更新部署以更改 Pod 的配置、使用的容器映像或附加存储。 Deployment 控制器耗尽并终止给定数量的副本，从新部署定义创建副本，并继续该过程，直至部署中的所有副本都已更新。

大多数无状态应用程序应使用部署模型，而不是计划单个 Pod。 Kubernetes 可以监视部署的运行状况和状态，以确保在群集中运行所需数量的副本。 只计划单个 Pod 时，如果 Pod 出现故障则不会重启；如果当前节点出现故障，则不会在正常节点上重新计划。

如果应用程序需要一定数量的实例才能做出管理决策，你不希望更新进程来中断该功能。 Pod 中断预算可用于定义在更新或节点升级期间部署中可以删除的副本数。 例如，如果部署中有五 (5) 个副本，则可以定义 4 个 Pod 中断，以便一次只允许删除/重新计划一个副本 。 与 Pod 资源限制一样，最佳做法是在需要始终存在最少数量副本的应用程序上定义 Pod 中断预算。

通常使用 `kubectl create` 或 `kubectl apply` 来创建和管理部署。 为创建部署，可使用 YAML（YAML 不标记语言）格式定义清单文件。 以下示例创建 NGINX Web 服务器的基本部署。 部署指定要创建的三 (3) 个副本，并要求在容器上打开端口 80 。 还为 CPU 和内存定义了资源请求和限制。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

通过在 YAML 清单中包含负载均衡器等服务，还可以创建更复杂的应用程序。

有关详细信息，请参阅 [Kubernetes 部署][kubernetes-deployments]

##### <a name="mixed-os-deployments"></a>混合 OS 部署

如果 Azure Stack HCI 上的 Azure Kubernetes 服务给定目标群集由 Linux 和 Windows 工作器节点组成，则需要将工作负载安排到可支持预配工作负载的 OS 上。 Kubernetes 提供了两种机制来确保工作负载进入具有目标操作系统的节点：

- 节点选择器是 Pod 规范中的一个简单字段，可将 Pod 约束为仅安排到与操作系统匹配的正常节点。 
- 排斥和容许一起工作，以确保不会无意中将 Pod 安排到节点。 节点可以“进行排斥”，以便不接受不通过 Pod 规范中的“容许”显式容许其排斥的 Pod。

有关详细信息，请参阅[节点选择器][node-selectors]以及[排斥和容许][taints-tolerations]。

### <a name="statefulsets-and-daemonsets"></a>StatefulSet 和 DaemonSet

Deployment 控制器使用 Kubernetes 计划程序在具有可用资源的任何可用节点上运行给定数量的副本。 这种使用部署的方法对于无状态应用程序可能已足够，但对于需要持久命名约定或存储的应用程序则不行。 对于群集内需要在每个节点或选定节点上存在副本的应用程序，Deployment 控制器不会查看副本在节点间的分布情况。

以下两种 Kubernetes 资源可以管理这类应用程序：

- StatefulSet - 维护超出单个 Pod 生命周期的应用程序的状态（如存储）。
- DaemonSet - 确保在 Kubernetes 启动进程早期每个节点上都有正在运行的实例。

### <a name="statefulsets"></a>StatefulSet

现代应用程序开发通常针对无状态应用程序，但 StatefulSet 可用于有状态应用程序（如包含数据库组件的应用程序）。 StatefulSet 是类似于创建和管理一个或多个相同 Pod 的部署。 StatefulSet 中的副本按照正常有序的方法来部署、缩放、升级和终止。 使用 StatefulSet（重新计划副本时），命名约定、网络名称和存储将保持不变。

使用 `kind: StatefulSet` 以 YAML 格式定义应用程序，然后 StatefulSet 控制器处理所需副本的部署和管理。 数据会写入持久存储，即使在删除 StatefulSet 之后，基础存储仍保持不变。

有关详细信息，请参阅 [Kubernetes StatefulSet][kubernetes-statefulsets]。

计划 StatefulSet 中的副本，并在 Azure Stack HCI 上的 Azure Kubernetes 服务群集中的任何可用节点上运行这些副本。 如需确保集中至少有一个 Pod 在节点上运行，则可以改用 DaemonSet。

### <a name="daemonsets"></a>DaemonSet

对于特定的日志集合或监视需求，可能需要在所有或选定的节点上运行给定的 Pod。 DaemonSet 再次用于部署一个或多个相同的 Pod，但 DaemonSet 控制器会确保指定的每个节点都运行 Pod 实例。

在默认的 Kubernetes 计划程序启动之前，DaemonSet 控制器可以在群集启动进程的早期计划节点上的 Pod。 此功能可确保在计划 Deployment 或 StatefulSet 中的传统 Pod 之前启动 DaemonSet 中的 Pod。

与 StatefulSet 一样，系统使用 `kind: DaemonSet` 将 DaemonSet 定义为 YAML 定义的一部分。

有关详细信息，请参阅 [Kubernetes DaemonSet][kubernetes-daemonset]。

### <a name="namespaces"></a>命名空间

Kubernetes 资源（如 Pod 和部署）以逻辑方式分组到命名空间中。 这些分组提供了一种以逻辑方式划分 Azure Stack HCI 上的 Azure Kubernetes 服务目标群集并限制创建、查看或管理资源访问权限的方法。 例如，可以创建命名空间以分隔业务组。 用户只能与分配的命名空间内的资源进行交互。

创建 Azure Stack HCI 上的 Azure Kubernetes 服务群集时，可以使用以下命名空间：

- default - 不提供任何命名空间时，默认情况下在此命名空间中创建 Pod 和部署。 在小型环境中，可以将应用程序直接部署到默认命名空间，而无需创建其他逻辑分隔。 与 Kubernetes API（例如 `kubectl get pods`）交互时，如果未指定命名空间，则使用默认值。
- kube-system - 此命名空间是核心资源的所在位置，例如 DNS 和代理等网络功能或 Kubernetes 仪表板。 通常不会将应用程序部署到此命名空间中。
- kube-public - 通常不使用此命名空间，但可以用于让资源在整个群集中可见，并可供任何用户查看。

有关详细信息，请参阅 [Kubernetes 命名空间][kubernetes-namespaces]。

[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[node-selectors]: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
[taints-tolerations]: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
