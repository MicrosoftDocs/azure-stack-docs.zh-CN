---
title: 在 Windows 容器中使用持久存储
description: 在 Windows 容器中使用持久存储并为组托管服务帐户准备好 Windows 节点
author: abhilashaagarwala
ms.topic: how-to
ms.date: 09/21/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 19b934e4bdec9e0ab6f4e7808dfea6e6fb648245
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94784846"
---
# <a name="use-persistent-storage-in-a-windows-container-and-prepare-windows-nodes-for-group-managed-service-accounts"></a>在 Windows 容器中使用持久存储并为组托管服务帐户准备好 Windows 节点

永久性卷表示已经过预配可以用于 Kubernetes Pod 的存储块。 持久卷可由一个或多个 Pod 使用，旨在用于长期存储。 它还独立于 Pod 或节点生命周期。    在此部分中，你将了解如何创建持久卷，以及如何在 Windows 应用程序中使用此卷。

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


```PowerShell
kubectl create -f pvc-akshci-csi.yaml 
```
以下输出会显示已成功创建持久卷声明：

**输出：**
```PowerShell
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

```PowerShell
Kubectl create -f winwebserver.yaml 
```

若要确保 Pod 正在运行，请运行以下命令。 等待几分钟，直到 Pod 处于正在运行状态，因为拉取映像会花费时间。

```PowerShell
kubectl get pods -o wide 
```
Pod 运行后，便可通过运行以下命令来查看 Pod 状态： 

```PowerShell
kubectl.exe describe pod %podName% 
```

若要验证是否已在 Pod 中装载了卷，请运行以下命令：

```PowerShell
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>删除持久卷声明

删除持久卷声明之前，必须通过运行以下内容来删除应用部署：

```PowerShell
kubectl.exe delete deployments win-webserver
```

随后可以通过运行以下内容来删除持久卷声明：

```PowerShell
kubectl.exe delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="prepare-windows-nodes-for-group-managed-service-account-support-on-windows-nodes"></a>为 Windows 节点上的组托管服务帐户支持准备好 Windows 节点

组托管服务帐户是一种特定类型的 Active Directory 帐户，可提供自动密码管理、简化的服务主体名称 (SPN) 管理以及在多台服务器间将管理委托给其他管理员的功能。 若要为在 Windows 节点上运行的 Pod 和容器配置组托管服务帐户 (gMSA)，必须首先将 Windows 节点加入 Active Directory 域。

若要启用组托管服务帐户支持，Kubernetes 群集名称必须少于 4 个字符。 这是因为，加入域的服务器名称所支持的最大长度为 15 个字符，而适用于工作器节点的 Azure Stack HCI Kubernetes 群集上的 AKS 命名约定会向节点名称添加一些预定义的字符。

若要将 Windows 工作器节点加入域，请通过运行 `kubectl get` 并记下 `EXTERNAL-IP` 值，来登录 Windows 工作器节点。

```PowerShell
kubectl get nodes -o wide
``` 

随后可以使用 `ssh Administrator@ip` 通过 SSH 登录节点。 

成功登录 Windows 工作器节点之后，运行以下 PowerShell 命令以将节点加入域。 系统会提示输入域管理员帐户凭据。 还可以使用已授权将计算机加入给定域的提升的用户凭据。 随后需要重新启动 Windows 工作器节点。

```PowerShell
add-computer --domainame "YourDomainName" -restart
```

将所有 Windows 工作器节点加入域后，请按照[配置 gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa) 中详细介绍的步骤在 Kubernetes 群集上应用 Kubernetes gMSA 自定义资源定义和 Webhook。

有关具有 gMSA 的 Windows 容器的详细信息，请参阅 [Windows 容器和 gMSA](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts)。 

## <a name="next-steps"></a>后续步骤
- [在 Kubernetes 群集上部署 Windows 应用程序](./deploy-windows-application.md)。
- [将 Kubernetes 群集连接到 Azure Arc For Kubernetes](./connect-to-arc.md)。
