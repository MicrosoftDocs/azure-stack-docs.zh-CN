---
title: 教程-在 Azure Stack HCI 上的 AKS 中部署 Linux 应用程序
description: 本教程介绍如何使用 Azure 容器注册表中存储的自定义映像将多容器 Linux 应用程序部署到群集。
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 6fd907a44cdaad5f5dfe7ccb3a29f5fc6a0152b6
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948789"
---
# <a name="tutorial-deploy-linux-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>教程：在 Azure Stack HCI 上的 Azure Kubernetes 服务中部署 Linux 应用程序

在本教程中，将在 Azure Stack HCI 群集上的 Azure Kubernetes 服务中部署包含 web 前端和 Redis 数据库实例的多容器应用程序。 然后，你将了解如何测试和缩放应用程序。 

本教程假定你基本了解 Kubernetes 的概念。 有关详细信息，请参阅 [AZURE STACK HCI 上的 Azure Kubernetes 服务的 Kubernetes 核心概念](kubernetes-concepts.md)。

## <a name="before-you-begin"></a>开始之前

验证是否已满足以下要求：

* Azure Stack HCI 群集上的 Azure Kubernetes 服务，其中至少有一个启动并运行的 Linux 工作节点。 
* 用于访问群集的 kubeconfig 文件。
* Azure Stack HCI PowerShell 模块上安装 Azure Kubernetes 服务。
* 在 PowerShell 管理窗口中运行此文档中的命令。
* 确保在适当的容器主机上承载特定于 OS 的工作负荷。 如果你有混合的 Linux 和 Windows 辅助角色节点 Kubernetes 群集，则可以使用节点选择器或 taints 和 tolerations。 有关详细信息，请参阅 [使用节点选择器和 taints 和 tolerations](adapt-apps-mixed-os-clusters.md)。

## <a name="deploy-the-application"></a>部署应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本快速入门中，清单用于创建运行 [Azure 投票应用程序](https://github.com/Azure-Samples/azure-voting-app-redis)所需的所有对象。 此清单包括两个 Kubernetes 部署 - 一个用于 Azure Vote Python 示例应用程序，另一个用于 Redis 实例。 同时还会创建两个 Kubernetes 服务-Redis 实例的内部服务，以及用于从 internet 访问 Azure 投票应用程序的外部服务。

创建名为 `azure-vote.yaml` 的文件，并将其复制到以下 YAML 定义中。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

使用命令部署应用程序 `kubectl apply` 并指定 YAML 清单的名称：

```PowerShell
kubectl apply -f azure-vote.yaml
```

以下示例输出显示已成功创建了部署和服务：

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。

若要监视进度，请使用 `kubectl get service` 带有参数的命令 `--watch` 。

```PowerShell
kubectl get service azure-vote-front --watch
```

最初，*azure-vote-front* 服务的 *EXTERNAL-IP* 显示为 *pending*。

```output
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27      <pending>     80:30572/TCP   22m
```

当 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   24m
```

若要查看 Azure Vote 应用的实际效果，请打开 Web 浏览器并转到服务的外部 IP 地址。

![Azure 上的 Kubernetes 群集映像](media/deploy-linux-application/azure-vote.png)

## <a name="scale-application-pods"></a>缩放应用程序箱

我们已创建 Azure 投票前端和 Redis 实例的单个副本。 若要查看群集中 pod 的数量和状态，请使用命令，如下所示 `kubectl get` ：

```console
kubectl get pods -n default
```

以下示例输出显示一个前端 Pod 和一个后端 Pod：

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1       Running   0          25m
azure-vote-front-84c8bf64fc-cdq86   1/1       Running   0          25m
```

若要更改 *azure 投票前* 部署中的 pod 数，请使用 `kubectl scale` 命令。 以下示例将前端 Pod 数增加到 *5*：

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

`kubectl get pods`再次运行以验证是否已创建了其他 pod。 一分钟左右之后，其他 Pod 会在群集中提供：

```console
kubectl get pods -n default

Name                                READY   STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1     Running   0          31m
azure-vote-front-84c8bf64fc-cdq86   1/1     Running   0          31m
azure-vote-front-84c8bf64fc-56h64   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-djkp8   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-jmmvs   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-znc6z   1/1     Running   0          80s
```

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 来监视群集和应用程序](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)。
