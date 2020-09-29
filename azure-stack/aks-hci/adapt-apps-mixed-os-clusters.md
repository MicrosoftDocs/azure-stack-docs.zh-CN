---
title: 调整应用程序以在混合 OS Kubernetes 群集中使用
description: 如何在 Azure Kubernetes Service 上使用节点选择器或 taints 和 tolerations，以确保在正确的工作节点操作系统上计划 Azure Stack HCI 上运行的混合 OS Kubernetes 群集中的应用程序
author: abha
ms.topic: how-to
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: e70ac456929b5c8402e49c969ca8b9c87a703311
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948837"
---
# <a name="adapt-apps-for-mixed-os-kubernetes-clusters-using-node-selectors-or-taints-and-tolerations"></a>使用节点选择器或 taints 和 tolerations 为混合 OS Kubernetes 群集改编应用

通过 Azure Stack HCI 上的 Azure Kubernetes 服务，你可以同时运行 Linux 和 Windows 节点的 Kubernetes 群集，但需要对应用进行少量编辑，以便在这些混合 OS 群集中使用。 本操作方法指南介绍如何确保使用节点选择器或 taints 和 tolerations 在正确的主机操作系统上计划应用程序。

本操作方法指南假定你基本了解 Kubernetes 的概念。 有关详细信息，请参阅 [AZURE STACK HCI 上的 Azure Kubernetes 服务的 Kubernetes 核心概念](kubernetes-concepts.md)。

## <a name="node-selector"></a>节点选择器

*节点选择器* 是 POD 规范 YAML 中的一个简单字段，该字段将 pod 限制为仅计划到与操作系统匹配的正常节点。 在 pod 规范 YAML 中，指定  `nodeSelector`   Windows 或 Linux，如以下示例中所示。 

```yaml
kubernetes.io/os = Windows
```
或者，

```yaml
kubernetes.io/os = Linux
```

有关 nodeSelectors 的详细信息，请访问 [节点选择器](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)。 

## <a name="taints-and-tolerations"></a>Taints 和 tolerations

*Taints* 和 *tolerations* 一起工作，以确保不会无意中在节点上计划 pod。 节点可以是 "感染"，以不接受通过 pod 规范 YAML 中的 "toleration" 显式容忍其破坏的 pod。

Azure Stack HCI 上的 Azure Kubernetes 服务中的 Windows OS 节点可以感染具有以下键值对。 用户不应使用不同的帐户。

```yaml
node.kubernetes.io/os=Windowss:NoSchedule
```
运行 `kubectl get` 并标识要破坏的 Windows 辅助角色节点。

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

使用破坏 Windows server 辅助角色节点 `kubectl taint node` 。

```PowerShell
kubectl taint node my-aks-hci-cluster-md-md-1-5h4bl node.kubernetes.io/os=Windows:NoSchedule
kubectl taint node my-aks-hci-cluster-md-md-1-5xlwz node.kubernetes.io/os=Windows:NoSchedule
```

为 pod 规范 YAML 中的 pod 指定 toleration。 以下 toleration "可匹配" 破坏上的 kubectl 破坏行创建的，因此，具有 toleration 的 pod 将能够计划到我的 aks-或 my 5h4bl---------：

```yaml
tolerations:
- key: node.kubernetes.io/os
  operator: Equal
  value: Windows
  effect: NoSchedule
```
有关 taints 和 tolerations 的详细信息，请访问 [taints 和 tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)。 

## <a name="next-steps"></a>后续步骤

本操作方法指南介绍了如何使用 kubectl 将节点选择器或 taints 和 tolerations 添加到 Kubernetes 群集。 接下来可以：
- [在 Kubernetes 群集上部署 Linux 应用程序](./deploy-linux-application.md)。
- [在 Kubernetes 群集上部署 Windows Server 应用程序](./deploy-windows-application.md)。
