---
title: 在 Azure Stack HCI 群集上的 AKS 中使用永久卷
description: 在 Windows 容器中使用持久存储并为组托管服务帐户准备好 Windows 节点
author: v-susbo
ms.topic: how-to
ms.date: 11/19/2020
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: af18b885999ce4eaa387f7268a77ab3f1bb51d9f
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563553"
---
# <a name="use-persistent-volume-in-an-aks-on-azure-stack-hci-cluster"></a>在 Azure Stack HCI 群集上的 AKS 中使用永久卷

> 适用于 Azure Stack HCI 上的 AKS、Windows Server 2019 Datacenter 上的 AKS 运行时

永久性卷表示已经过预配可以用于 Kubernetes Pod 的存储块。 持久卷可由一个或多个 Pod 使用，旨在用于长期存储。 它还独立于 Pod 或节点生命周期。 虽然你可以为 Windows 和 Linux 节点预配 PVC，但在此部分中，你将了解如何创建永久卷，以及如何在 Windows 应用程序中使用此卷。 有关详细信息，请参阅 [Kubernetes 中的永久性卷](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)。

## <a name="before-you-begin"></a>准备阶段

以下是开始使用需要满足的条件：

* 具有至少一个 Windows 工作器节点的 Kubernetes 群集。
* 用于访问 Kubernetes 群集的 kubeconfig 文件。

## <a name="create-a-persistent-volume-claim"></a>创建永久性卷声明

持久卷声明用于基于存储类自动预配存储。  若要创建卷声明，请首先创建名为 `pvc-akshci-csi.yaml` 的文件，并在以下 YAML 定义中进行复制。 该声明请求大小为 10 GB、具有 ReadWriteOnce **  访问权限的磁盘。 default **  存储类指定为存储类 (vhdx)。  

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: pvc-akshci-csi
spec:
 accessModes:
 - ReadWriteOnce
 resources:
  requests:
   storage: 10Gi
```
通过在 Azure Stack HCI 群集中一台服务器上的管理 PowerShell 会话中运行以下命令来创建卷（使用 [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession) 等方法或远程桌面连接到服务器）： 

```
kubectl create -f pvc-akshci-csi.yaml 
```
以下输出会显示已成功创建持久卷声明：

**输出：**
```
persistentvolumeclaim/pvc-akshci-csi created
```

## <a name="use-persistent-volume"></a>使用持久卷

若要使用持久卷，请创建名为 winwebserver.yaml 的文件，并在以下 YAML 定义中进行复制。  随后创建可访问持久卷声明和 vhdx 的 Pod。 

在下面的 yaml 定义中，mountPath 是用于在容器中装载卷的路径。 成功创建 Pod 之后，你会看到在 C:\\ 中创建了子目录 mnt，并在 mnt 内创建了子目录 akshciscsi   。


```yaml
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  labels: 
    app: win-webserver 
  name: win-webserver 
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: win-webserver 
  template: 
    metadata: 
      labels: 
        app: win-webserver 
      name: win-webserver 
    spec: 
     containers: 
      - name: windowswebserver 
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019 
        ports:  
          - containerPort: 80    
        volumeMounts: 
            - name: akshciscsi 
              mountPath: "/mnt/akshciscsi" 
     volumes: 
        - name: akshciscsi 
          persistentVolumeClaim: 
            claimName:  pvc-akshci-csi 
     nodeSelector: 
      kubernetes.io/os: windows 
```

若要使用以上 yaml 定义创建 Pod，请运行：
```
kubectl create -f winwebserver.yaml 
```

若要确保 Pod 正在运行，请运行以下命令。 等待几分钟，直到 Pod 处于正在运行状态，因为拉取映像会花费时间。 
```
kubectl get pods -o wide 
```
Pod 运行后，便可通过运行以下命令来查看 Pod 状态： 
```
kubectl.exe describe pod %podName% 
```

若要验证是否已在 Pod 中装载了卷，请运行以下命令：
```
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>删除持久卷声明

删除持久卷声明之前，必须通过运行以下内容来删除应用部署：
```
kubectl delete deployments win-webserver
```

随后可以通过运行以下内容来删除持久卷声明：
```
kubectl delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="next-steps"></a>后续步骤
- [在 Kubernetes 群集上部署 Windows 应用程序](./deploy-windows-application.md)。
- [将 Kubernetes 群集连接到 Azure Arc For Kubernetes](./connect-to-arc.md)。
