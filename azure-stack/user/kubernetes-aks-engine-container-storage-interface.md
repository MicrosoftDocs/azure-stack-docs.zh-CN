---
title: 将容器存储添加到 Azure Stack 集线器中的 Kubernetes
description: 了解如何将容器存储添加到 Azure Stack 集线器中的 Kubernetes。
author: mattbriggs
ms.topic: how-to
ms.date: 3/4/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/4/2021
ms.openlocfilehash: ee176f8e111c6e10831124b78afd99535d2fe7ee
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102233836"
---
# <a name="add-container-storage-to-kubernetes-in-azure-stack-hub"></a>将容器存储添加到 Azure Stack 集线器中的 Kubernetes

> [!IMPORTANT]  
> 此功能目前处于公开预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

作为 Kubernetes 社区工作的一部分 ([Kubernetes 树到 CSI 卷迁移](https://kubernetes.io/blog/2019/12/09/kubernetes-1-17-feature-csi-migration-beta/)) 若要将树中的卷提供程序移至容器存储接口 [CSI](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)，可以在 Azure Stack 中找到以下两个 CSI 驱动程序： Azure 磁盘和 NFS。

|                       | **Azure 磁盘 CSI 驱动程序**                                                                                                    | **NFS CSI 驱动程序**                                                       |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------|
| 项目存储库    | [add-azuredisk-驱动程序](https://github.com/kubernetes-sigs/azuredisk-csi-driver)                                              | [csi-驱动程序-nfs](https://github.com/kubernetes-csi/csi-driver-nfs)       |
| CSI 驱动程序版本    | 1.0.0 版 +                                                                                                                      | v 3.0.0 +                                                                  |
| 访问模式           | ReadWriteOnce                                                                                                                | ReadWriteOnce ReadOnlyMany ReadWriteMany                                 |
| Windows 代理节点    | 支持                                                                                                                      | 不支持                                                              |
| 动态预配  | 支持                                                                                                                      | 支持                                                                  |
| 注意事项        | [Azure 磁盘 CSI 驱动程序限制](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/limitations.md) | 用户将负责设置和维护 NFS 服务器。 |
| 时差支持渠道 | [\#提供程序-azure](https://kubernetes.slack.com/archives/C5HJXTT9Q)                                                          | [\#sig-存储](https://kubernetes.slack.com/archives/C09QZFCE5)         |

目前，已断开连接的环境不支持 CSI 驱动程序。

## <a name="requirements"></a>要求

-   Azure Stack 版本2011及更高版本。
-   AKS 引擎版本 v 0.60.1 及更高版本。
-   Kubernetes 版本1.18 及更高版本。
-   由于 CSI 驱动程序的控制器服务器需要两个副本，因此不建议使用单个节点主池。
-   [Helm 3](https://helm.sh/docs/intro/install/)

## <a name="install-and-uninstall-csi-drivers"></a>安装和卸载 CSI 驱动程序

在本部分中，请按照示例命令部署使用 CSI 驱动程序的 statefulset 应用程序。

## <a name="azure-disk-csi-driver"></a>Azure 磁盘 CSI 驱动程序

### <a name="install-csi-driver"></a>安装 CSI 驱动程序

```bash  
helm repo add azuredisk-csi-driver https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/charts
helm install azuredisk-csi-driver azuredisk-csi-driver/azuredisk-csi-driver --namespace kube-system --set cloud=AzureStackCloud --set controller.runOnMaster=true --version v1.0.0

```
### <a name="deploy-storage-class"></a>部署存储类

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/storageclass-azuredisk-csi-azurestack.yaml
```

### <a name="deploy-example-statefulset-application"></a>部署示例 statefulset 应用程序

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/statefulset.yaml
```

### <a name="validate-volumes-and-applications"></a>验证卷和应用程序

应会看到一系列时间戳在卷中持久保存。

```bash  
kubectl exec statefulset-azuredisk-0 -- tail /mnt/azuredisk/outfile
```

### <a name="delete-example-statefulset-application"></a>删除示例 statefulset 应用程序

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/statefulset.yaml
```

### <a name="delete-storage-class"></a>删除存储类

删除存储类之前，请确保已终止使用存储类的 pod。

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/storageclass-azuredisk-csi-azurestack.yaml
```

### <a name="uninstall-csi-driver"></a>卸载 CSI 驱动程序

```bash  
helm uninstall azuredisk-csi-driver --namespace kube-system
helm repo remove azuredisk-csi-driver
```

## <a name="nfs-csi-driver"></a>NFS CSI 驱动程序

### <a name="install-csi-driver"></a>安装 CSI 驱动程序

```bash  
helm repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts
helm install csi-driver-nfs csi-driver-nfs/csi-driver-nfs --namespace kube-system --set controller.runOnMaster=true --version v3.0.0
```

### <a name="deploy-nfs-server"></a>部署 NFS 服务器

> [!NOTE]  
> NFS 服务器仅用于验证，可正确设置和维护 NFS 服务器以用于生产。

为生产环境正确设置和维护 NFS 服务器。

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/nfs-provisioner/nfs-server.yaml
```

### <a name="deploy-storage-class"></a>部署存储类

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/storageclass-nfs.yaml
```

### <a name="deploy-example-statefulset-application"></a>部署示例 statefulset 应用程序

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/statefulset.yaml
```

### <a name="validate-volumes-and-applications"></a>验证卷和应用程序

应会看到一系列时间戳在卷中持久保存。

```bash  
kubectl exec statefulset-nfs-0 -- tail /mnt/nfs/outfile
```

### <a name="delete-example-statefulset-application"></a>删除示例 statefulset 应用程序

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/nfs-provisioner/nfs-server.yaml
```

### <a name="delete-storage-class"></a>删除存储类

删除存储类之前，请确保已终止使用存储类的 pod。

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/storageclass-nfs.yaml
```
### <a name="delete-example-nfs-server"></a>删除示例 NFS 服务器

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/nfs-provisioner/nfs-server.yaml
```

### <a name="uninstall-csi-driver"></a>卸载 CSI 驱动程序

```bash  
helm uninstall csi-driver-nfs --namespace kube-system
helm repo remove csi-driver-nfs
```
## <a name="azure-disk-csi-driver-limitations-on-azure-stack-hub"></a>Azure Stack 集线器上的 Azure Disk CSI 驱动程序限制

-   Azure 磁盘 IOPS 上限为2300， [Azure Stack 集线器中支持的读取 VM 大小](azure-stack-vm-sizes.md) 。
-   Azure Stack 中心不支持共享磁盘，因此大于 `maxShares` 1 的参数在 StorageClass 中无效。
-   Azure Stack 中心仅支持标准的本地冗余 (Standard_LRS) 和高级本地冗余 (Premium_LRS) 存储帐户类型，因此只有 Standard_LRS 和 Premium_LRS 对中的参数有效 `skuName` `StorageClass` 。
-   Azure Stack 中心不支持增量磁盘快照，因此仅 false 对中的参数有效 `incremental` `VolumeSnapshotClass` 。
-   对于 Windows 代理节点，你将需要安装 Windows CSI Proxy，请参阅 [WINDOWS Csi 代理](https://github.com/kubernetes-csi/csi-proxy)。 若要通过 AKS 引擎 API 模型启用代理，请参阅适用于 [Windows 的 CSI 代理](https://github.com/Azure/aks-engine/blob/master/docs/topics/csi-proxy-windows.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)  
