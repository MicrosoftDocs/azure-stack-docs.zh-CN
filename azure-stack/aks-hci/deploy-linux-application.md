---
title: 教程 - 在 Azure Stack HCI 上的 AKS 中部署 Linux 应用程序
description: 在本教程中，你会使用存储在 Azure 容器注册表中的自定义映像将多容器 Linux 应用程序部署到群集。
author: abha
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: e5f7f96956248dc7cc2c92ae678970b40951ece4
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612295"
---
# <a name="tutorial-deploy-linux-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>教程：在 Azure Stack HCI 上的 Azure Kubernetes 服务中部署 Linux 应用程序

> 适用于 Azure Stack HCI 上的 AKS、Windows Server 2019 Datacenter 上的 AKS 运行时

在本教程中，你会在 Azure Stack HCI 上的 Azure Kubernetes 服务群集中部署包含 web 前端和 Redis 数据库实例的多容器应用程序。 随后会了解如何测试和缩放应用程序。 

本教程假定你对 Kubernetes 概念有基本的了解。 有关详细信息，请参阅 [Azure Stack HCI 上的 Azure Kubernetes 服务的 Kubernetes 核心概念](kubernetes-concepts.md)。

## <a name="before-you-begin"></a>开始之前

验证是否已满足以下要求：

* Azure Stack HCI 上的 Azure Kubernetes 服务群集，其中至少有一个启动并运行的 Linux 工作器节点。 
* 用于访问群集的 kubeconfig 文件。
* 安装了 Azure Stack HCI 上的 Azure Kubernetes 服务 PowerShell 模块。
* 在 PowerShell 管理窗口中运行本文档中的命令。
* 确保特定于操作系统的工作负荷位于适当的容器主机上。 如果具有混合 Linux 和 Windows 工作器节点 Kubernetes 群集，则可以使用节点选择器或是排斥和容许。 有关详细信息，请参阅[使用节点选择器以及排斥和容许](adapt-apps-mixed-os-clusters.md)。

## <a name="deploy-the-application"></a>部署应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本快速入门中，清单用于创建运行 [Azure Vote 应用程序](https://github.com/Azure-Samples/azure-voting-app-redis)所需的所有对象。 此清单包括两个 Kubernetes 部署 - 一个用于 Azure Vote Python 示例应用程序，另一个用于 Redis 实例。 此外，还会创建两个 Kubernetes 服务 - 一个内部服务用于 Redis 实例，一个外部服务用于从 Internet 访问 Azure Vote 应用程序。

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

使用 `kubectl apply` 命令部署应用程序，并指定 YAML 清单的名称：

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

若要监视进度，请将 `kubectl get service` 命令与 `--watch` 参数配合使用。

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

## <a name="scale-application-pods"></a>缩放应用程序 Pod

我们已创建 Azure 投票前端和 Redis 实例的单个副本。 若要查看群集中 Pod 的数目和状态，请使用 `kubectl get` 命令，如下所示：

```console
kubectl get pods -n default
```

以下示例输出显示一个前端 pod 和一个后端 pod：

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1       Running   0          25m
azure-vote-front-84c8bf64fc-cdq86   1/1       Running   0          25m
```

若要更改 azure-vote-front 部署中的 Pod 数，请使用 `kubectl scale` 命令。 下面的示例将前端箱数增加到 *5*：

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

再次运行 `kubectl get pods`，验证是否已创建了其他 Pod。 一分钟左右之后，其他 Pod 会在群集中提供：

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
