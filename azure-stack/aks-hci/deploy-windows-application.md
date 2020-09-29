---
title: 教程-在 Azure Stack HCI 上的 AKS 中部署 Windows 应用程序
description: 本教程介绍如何使用 Azure 容器注册表中存储的自定义映像将 Windows 应用程序部署到群集。
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: f6bc0132dd7ce3ee9972b0aaff6d0718cab86843
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948787"
---
# <a name="tutorial-deploy-windows-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>教程：在 Azure Stack HCI 上的 Azure Kubernetes 服务中部署 Windows 应用程序

在本教程中，将 Windows Server 容器中的 ASP.NET 示例应用程序部署到 Kubernetes 群集。 然后，你将了解如何测试和缩放应用程序。 本教程假定你基本了解 Kubernetes 的概念。 有关详细信息，请参阅 [AZURE STACK HCI 上的 Azure Kubernetes 服务的 Kubernetes 核心概念](kubernetes-concepts.md)。

## <a name="before-you-begin"></a>开始之前

验证是否已满足以下要求：

* Azure Stack HCI 群集上的 Azure Kubernetes 服务，其中至少有一个启动并运行的 Windows 工作节点。 
* 用于访问群集的 kubeconfig 文件。
* Azure Stack HCI PowerShell 模块上安装 Azure Kubernetes 服务。
* 在 PowerShell 管理窗口中运行此文档中的命令。
* 确保在适当的容器主机上承载特定于 OS 的工作负荷。 如果你有混合的 Linux 和 Windows 辅助角色节点 Kubernetes 群集，则可以使用节点选择器或 taints 和 tolerations。 有关详细信息，请参阅 [使用节点选择器和 taints 和 tolerations](adapt-apps-mixed-os-clusters.md)。

## <a name="deploy-the-application"></a>部署应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本文中，清单用于创建在 Windows Server 容器中运行 ASP.NET 示例应用程序所需的所有对象。 此清单包括用于 ASP.NET 示例应用程序的 Kubernetes 部署，以及用于从 Internet 访问应用程序的外部 Kubernetes 服务。

ASP.NET 示例应用程序作为 .NET Framework 示例的一部分提供并在 Windows Server 容器中运行。 Azure Stack HCI 上的 Azure Kubernetes 服务需要基于 *Windows server 2019*映像的 windows server 容器。 

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

使用命令部署应用程序 `kubectl apply` 并指定 YAML 清单的名称：

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

若要监视进度，请使用 `kubectl get service` 带有参数的命令 `--watch` 。

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

如果尝试加载页时接收到连接超时，请使用命令验证示例应用程序是否已准备就绪 `kubectl get pods --watch` 。 有时，在 windows 容器启动之前，外部 IP 地址可用。

## <a name="scale-application-pods"></a>缩放应用程序箱

我们已经创建了应用程序前端的单个副本。 若要查看群集中 pod 的数量和状态，请使用命令，如下所示 `kubectl get` ：

```console
kubectl get pods -n default
```

若要更改 *示例* 部署中的 pod 数，请使用 `kubectl scale` 命令。 下面的示例将前端箱数增加到 *3*：

```console
kubectl scale --replicas=3 deployment/sample
```

`kubectl get pods`再次运行以验证是否已创建了其他 pod。 一分钟左右之后，其他 Pod 会在群集中提供：

```console
kubectl get pods -n default
```

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 来监视群集和应用程序](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)。
* [使用持久性存储并在 Windows 容器中配置 gMSA 支持](persistent-storage-windows-nodes.md)。
