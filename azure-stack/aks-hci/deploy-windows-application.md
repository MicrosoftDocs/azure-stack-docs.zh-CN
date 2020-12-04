---
title: Azure Stack HCI 上的 AKS 中部署 Windows .NET 应用程序
description: 了解如何使用 Azure 容器注册表中存储的自定义映像将 Windows .NET 应用程序部署到群集。
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: e77cda56b3a707cbd117976af9e1871712dbc6cc
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557049"
---
# <a name="deploy-windows-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>在 Azure Stack HCI 上的 Azure Kubernetes 服务中部署 Windows 应用程序

> 适用于 Azure Stack HCI 上的 AKS、Windows Server 2019 Datacenter 上的 AKS 运行时

在本教程中，将 Windows Server 容器中的 ASP.NET 示例应用程序部署到 Kubernetes 群集，并了解如何测试和缩放应用程序。 你还将了解如何将 Windows 节点加入到 Active Directory 域。
本教程假定你对 Kubernetes 概念有基本的了解。 有关详细信息，请参阅 [Azure Stack HCI 上的 Azure Kubernetes 服务的 Kubernetes 核心概念](kubernetes-concepts.md)。

## <a name="before-you-begin"></a>开始之前

验证是否已满足以下要求：

* Azure Stack HCI 上的 Azure Kubernetes 服务群集，其中至少有一个启动并运行的 Windows 工作器节点。 
* 用于访问群集的 kubeconfig 文件。
* 安装了 Azure Stack HCI 上的 Azure Kubernetes 服务 PowerShell 模块。
* 在 PowerShell 管理窗口中运行本文档中的命令。
* 确保在适当的容器主机上承载特定于 OS 的工作负载。 如果具有混合 Linux 和 Windows 工作器节点 Kubernetes 群集，则可以使用节点选择器或是排斥和容许。 有关详细信息，请参阅[使用节点选择器以及排斥和容许](adapt-apps-mixed-os-clusters.md)。

## <a name="deploy-the-application"></a>部署应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本文中，清单用于创建在 Windows Server 容器中运行 ASP.NET 示例应用程序所需的所有对象。 此清单包括用于 ASP.NET 示例应用程序的 Kubernetes 部署，以及用于从 Internet 访问应用程序的外部 Kubernetes 服务。

ASP.NET 示例应用程序作为 .NET Framework 示例的一部分提供并在 Windows Server 容器中运行。 Azure Stack HCI 上的 Azure Kubernetes 服务要求 Windows Server 容器基于 Windows Server 2019 的映像。 

Kubernetes 清单文件还必须定义节点选择器，以指示 AKS 群集在可运行 Windows Server 容器的节点上运行 ASP.NET 示例应用程序的 Pod。

创建名为 `sample.yaml` 的文件，并将其复制到以下 YAML 定义中。 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

使用 `kubectl apply` 命令部署应用程序，并指定 YAML 清单的名称：

```console
kubectl apply -f sample.yaml
```

以下示例输出显示已成功创建部署和服务：

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。 有时，预配服务所需的时间可能不止几分钟。 在这种情况下，最多需要 10 分钟。

若要监视进度，请将 `kubectl get service` 命令与 `--watch` 参数配合使用。

```PowerShell
kubectl get service sample --watch
```

最初，示例服务的 EXTERNAL-IP 显示为“挂起”  。

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

当 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看示例应用的实际效果，请打开 Web 浏览器并转到服务的外部 IP 地址。

![浏览到 ASP.NET 示例应用程序的图像](media/deploy-windows-application/asp-net-sample-app.png)

如果尝试加载页面时收到连接超时，请使用 `kubectl get pods --watch` 命令验证示例应用是否已准备就绪。 有时，外部 IP 地址在 Windows 容器启动之前可用。

## <a name="scale-application-pods"></a>缩放应用程序 Pod

我们已创建了应用程序前端的单个副本。 若要查看群集中 Pod 的数目和状态，请使用 `kubectl get` 命令，如下所示：

```console
kubectl get pods -n default
```

若要更改示例部署中的 Pod 数，请使用 `kubectl scale` 命令。 以下示例将前端 Pod 数增加到 3：

```console
kubectl scale --replicas=3 deployment/sample
```

再次运行 `kubectl get pods`，验证是否已创建了其他 Pod。 一分钟左右之后，其他 Pod 会在群集中提供：

```console
kubectl get pods -n default
```

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 来监视群集和应用程序](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)。
* [在 Kubernetes 群集上使用永久性卷](persistent-volume.md)。
