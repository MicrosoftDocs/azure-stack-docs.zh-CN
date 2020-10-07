---
title: 使用 Windows 容器中的使用永久存储
description: 在 Windows 容器中使用持久性存储并为组托管服务帐户准备 Windows 节点
author: abha
ms.topic: how-to
ms.date: 09/21/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 91f7249beb34e5afee808d299df48611a5ce26bb
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778123"
---
# <a name="use-persistent-storage-in-a-windows-container-and-prepare-windows-nodes-for-group-managed-service-accounts"></a>在 Windows 容器中使用持久性存储并为组托管服务帐户准备 Windows 节点

永久性卷表示已经过预配可以用于 Kubernetes Pod 的存储块。 永久性卷可供一个或多个 pod 使用，并适用于长期存储。 它还独立于 pod 或节点生命周期。在本部分中，你将了解如何创建永久性卷，以及如何在 Windows 应用程序中使用此卷。

## <a name="before-you-begin"></a>开始之前

下面是你需要入门的内容：

* 至少具有一个 Windows 辅助角色节点的 Kubernetes 群集。
* 用于访问 Kubernetes 群集的 kubeconfig 文件。


## <a name="create-a-persistent-volume-claim"></a>创建永久性卷声明

永久性卷声明用于基于存储类自动预配存储。若要创建卷声明，请先创建一个名为的文件， `pvc-akshci-csi.yaml` 并在以下 YAML 定义中复制。 声明请求的磁盘大小为 10 GB， *ReadWriteOnce*   访问。  *默认*   存储类指定为 (vhdx) 的存储类。  

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
通过在 Azure Stack HCI 群集中的某台服务器上运行以下命令来创建卷 (使用诸如 [Enter](/powershell/module/microsoft.powershell.core/enter-pssession) 或远程桌面等方法连接到服务器) ： 


```PowerShell
kubectl create -f pvc-akshci-csi.yaml 
```
以下输出将显示已成功创建永久卷声明：

**输出：**
```PowerShell
persistentvolumeclaim/pvc-akshci-csi created
```

## <a name="use-persistent-volume"></a>使用永久卷

若要使用永久性卷，请创建一个名为 winwebserver 的文件，并复制以下 YAML 定义。然后，将创建一个可以访问永久性卷声明和 vhdx 的 pod。 

在下面的 yaml 定义中， *mountPath* 是在容器中装载卷的路径。 成功创建 pod 后，会看到在*mnt*中* \\ *创建的子目录 akshciscsi，并在*mnt*中创建了子目录*akshciscsi* 。


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

若要使用上述 yaml 定义创建 pod，请运行：

```PowerShell
Kubectl create -f winwebserver.yaml 
```

若要确保 pod 正在运行，请运行以下命令。 等待几分钟，直到 pod 进入运行状态，因为拉取映像会花费时间。

```PowerShell
kubectl get pods -o wide 
```
Pod 运行后，请运行以下命令查看 pod 状态： 

```PowerShell
kubectl.exe describe pod %podName% 
```

若要验证是否已在 pod 中装入了卷，请运行以下命令：

```PowerShell
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>删除永久性卷声明

删除永久性卷声明之前，必须通过运行以下操作删除应用程序部署：

```PowerShell
kubectl.exe delete deployments win-webserver
```

然后，你可以通过运行以下内容删除永久性卷声明：

```PowerShell
kubectl.exe delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="prepare-windows-nodes-for-group-managed-service-account-support-on-windows-nodes"></a>为 Windows 节点上的组托管服务帐户支持准备 Windows 节点

组托管服务帐户是一种特定类型的 Active Directory 帐户，它提供自动密码管理，简化的服务主体名称 (SPN) 管理，并且能够将管理委派给多个服务器上的其他管理员。 若要为将在 Windows 节点上运行的 pod 和容器配置组托管服务帐户 (gMSA) ，必须先将 Windows 节点加入 Active Directory 域。

若要启用组托管服务帐户支持，Kubernetes 群集名称必须少于4个字符。 这是因为，加入域的服务器名称的最大支持长度为15个字符，辅助节点 Azure Stack HCI Kubernetes 群集命名约定上的 AKS 将向节点名称添加一些预定义的字符。

若要将 Windows 辅助角色节点加入到域中，请通过运行并记录值来登录到 Windows 辅助角色节点 `kubectl get` `EXTERNAL-IP` 。

```PowerShell
kubectl get nodes -o wide
``` 

然后，你可以使用通过 SSH 连接到该节点 `ssh Administrator@ip` 。 

成功登录到 Windows 辅助角色节点后，运行以下 PowerShell 命令，将节点加入到域。 系统将提示你输入 **域管理员帐户** 凭据。 你还可以使用已授予权限将计算机加入到给定域的特权用户凭据。 然后，需要重新启动 Windows 辅助角色节点。

```PowerShell
add-computer --domainame "YourDomainName" -restart
```

将所有 Windows 辅助角色节点加入到域后，请按照 [配置 gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa) 中详细说明的步骤将 Kubernetes gMSA 自定义资源定义和 webhook 应用到 Kubernetes 群集。

有关具有 gMSA 的 Windows 容器的详细信息，请参阅 [windows 容器和 gMSA](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts)。 

## <a name="next-steps"></a>后续步骤
- [在 Kubernetes 群集上部署 Windows 应用程序](./deploy-windows-application.md)。
- [将 Kubernetes 群集连接到 Azure Arc For Kubernetes](./connect-to-arc.md)。
