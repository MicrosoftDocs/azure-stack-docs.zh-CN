---
title: 调整应用程序以在混合 OS Kubernetes 群集中使用
description: 如何在 Azure Kubernetes 服务上使用节点选择器或排斥和容许，以确保将在 Azure Stack HCI 上运行的混合 OS Kubernetes 群集中的应用程序安排在正确工作器节点操作系统上
author: abha
ms.topic: how-to
ms.date: 10/20/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 04b103fee921cf8bdab82a4004c6c80afd54d687
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253938"
---
# <a name="adapt-apps-for-mixed-os-kubernetes-clusters-using-node-selectors-or-taints-and-tolerations"></a>使用节点选择器或排斥和容许为混合 OS Kubernetes 群集调整应用

Azure Stack HCI 上的 Azure Kubernetes 服务使你可以运行同时具有 Linux 和 Windows 节点的 Kubernetes 群集，但需要对应用进行少量编辑，以便在这些混合 OS 群集中使用。 本操作指南介绍如何确保使用节点选择器或排斥和容许将应用程序安排在正确的主机操作系统上。

本操作指南假定你对 Kubernetes 概念有基本的了解。 有关详细信息，请参阅 [Azure Stack HCI 上的 Azure Kubernetes 服务的 Kubernetes 核心概念](kubernetes-concepts.md)。

## <a name="node-selector"></a>节点选择器

节点选择器是 Pod 规范 YAML 中的一个简单字段，可将 Pod 约束为仅安排到与操作系统匹配的正常节点。 在 Pod 规范 YAML 中，指定 `nodeSelector` - Windows 或 Linux，如以下示例中所示。 

```yaml
kubernetes.io/os = Windows
```
或者，

```yaml
kubernetes.io/os = Linux
```

有关节点选择器的详细信息，请访问[节点选择器](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)。 

## <a name="taints-and-tolerations"></a>排斥和容许

排斥和容许 一起工作，以确保不会无意中将 Pod 安排到节点。 节点可以“进行排斥”，以便不接受不通过 Pod 规范 YAML 中的“容许”显式容许其排斥的 Pod。

Azure Stack HCI 上的 Azure Kubernetes 服务中的 Windows OS 节点可以使用以下键值对进行进行排斥。 用户不应使用不同的内容。

```yaml
node.kubernetes.io/os=Windows:NoSchedule
```
运行 `kubectl get` 并标识要排斥的 Windows 工作器节点。

```PowerShell
kubectl get nodes --all-namespaces -o=custom-columns=NAME:.metadata.name,OS:.status.nodeInfo.operatingSystem
```
输出：
```output
NAME                                     OS
my-aks-hci-cluster-control-plane-krx7j   linux
my-aks-hci-cluster-md-md-1-5h4bl         windows
my-aks-hci-cluster-md-md-1-5xlwz         windows
```

使用 `kubectl taint node` 排斥 Windows Server 工作器节点。

```PowerShell
kubectl taint node my-aks-hci-cluster-md-md-1-5h4bl node.kubernetes.io/os=Windows:NoSchedule
kubectl taint node my-aks-hci-cluster-md-md-1-5xlwz node.kubernetes.io/os=Windows:NoSchedule
```

为 Pod 规范 YAML 中的 Pod 指定容许。 以下容许与以上 kubectl 排斥行创建的排斥“匹配”，因此，具有该容许的 Pod 将能够安排到 my-aks-hci-cluster-md-md-1-5h4bl 或 my-aks-hci-cluster-md-md-1-5xlwz 上：

```yaml
tolerations:
- key: node.kubernetes.io/os
  operator: Equal
  value: Windows
  effect: NoSchedule
```
有关排斥和容许的详细信息，请参阅[排斥和容许](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)。 

## <a name="next-steps"></a>后续步骤

本操作指南介绍了如何使用 kubectl 将节点选择器或排斥和容许添加到 Kubernetes 群集。 接下来可以：
- [在 Kubernetes 群集上部署 Linux 应用程序](./deploy-linux-application.md)。
- [在 Kubernetes 群集上部署 Windows Server 应用程序](./deploy-windows-application.md)。
