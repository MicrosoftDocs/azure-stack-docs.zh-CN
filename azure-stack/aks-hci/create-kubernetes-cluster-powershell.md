---
title: 使用 Windows PowerShell 在 Azure Stack HCI 上创建 Kubernetes 群集的快速入门
description: 了解如何使用 Windows PowerShell 在 Azure Stack HCI 上创建 Kubernetes 群集
author: jessicaguan
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: jeguan
ms.openlocfilehash: 38d65798d2f30377f3160484c9a618730cda80ea
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557134"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>快速入门：使用 Windows PowerShell 在 Azure Stack HCI 上创建 Kubernetes 群集

> 适用于 Azure Stack HCI 上的 AKS、Windows Server 2019 Datacenter 上的 AKS 运行时

本快速入门介绍如何使用 Windows PowerShell 在 Azure Stack HCI 上创建 Kubernetes 群集。 然后，了解如何缩放 Kubernetes 群集并升级群集的 Kubernetes 版本。 若要改为使用 Windows Admin Center，请参阅[使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务](setup.md)。

## <a name="before-you-begin"></a>在开始之前

 - 请确保已设置 Azure Stack Kubernetes 主机。 如果没有，请参阅 [快速入门：使用 PowerShell 在 AZURE STACK HCI 上设置 Azure Kubernetes Service 主机](./setup-powershell.md)。
 - 请确保已安装最新的 PowerShell 模块 Aks-Hci。 如果不是，请参阅 [下载并安装 AksHci PowerShell 模块](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module)。

## <a name="step-1-create-a-kubernetes-cluster"></a>步骤 1：创建 Kubernetes 群集

安装 Azure Kubernetes 服务主机之后，便准备好部署 Kubernetes 群集。

以管理员身份打开 PowerShell 并运行以下命令。

   ```powershell
   New-AksHciCluster -clusterName <String>
                    [-kubernetesVersion <String>]
                    [-controlPlaneNodeCount <int>]
                    [-linuxNodeCount <int>]
                    [-windowsNodeCount <int>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-linuxNodeVmSize <VmSize>]
                    [-windowsNodeVmSize <VmSize>]
   ```

### <a name="example"></a>示例

   ```powershell
   New-AksHciCluster -clusterName mynewcluster -kubernetesVersion v1.18.8 -controlPlaneNodeCount 1 -linuxNodeCount 1 -windowsNodeCount 0 
   ```

### <a name="required-parameters"></a>必需的参数

`-clusterName`

Kubernetes 群集的字母数字名称。

### <a name="optional-parameters"></a>可选参数

`-kubernetesVersion`

要部署的 Kubernetes 的版本。 默认值为1.18.8。 若要获取可用版本的列表，请运行 `Get-AksHciKubernetesVersion`。

`-controlPlaneNodeCount`

控制平面中的节点数。 默认值为 1。

`-linuxNodeCount`

Kubernetes 群集中的 Linux 节点数。 默认值为 1。

`-windowsNodeCount`

Kubernetes 群集中的 Windows 节点数。 默认为 0。 如果运行的是 Kubernetes v 1.18.8，则只能部署 Windows 节点。

`-controlPlaneVmSize`

控制平面 VM 的大小。 默认值为 Standard_A2_v2。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

`-loadBalancerVmSize`

负载均衡器 VM 的大小。 默认值为 Standard_A2_V2。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

`-linuxNodeVmSize`

Linux 节点 VM 的大小。 默认值为 Standard_K8S3_v1。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

`-windowsNodeVmSize`

Windows 节点 VM 的大小。 默认值为 Standard_K8S3_v1。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

### <a name="check-your-deployed-clusters"></a>检查部署的群集

若要获取已部署 Azure Kubernetes 服务主机和 Kubernetes 群集的列表，请运行以下命令。

```powershell
Get-AksHciCluster
```

## <a name="step-2-scale-a-kubernetes-cluster"></a>步骤 2：缩放 Kubernetes 群集

如果需要纵向扩展或缩减群集，可以更改控制平面节点、Linux 工作器节点或 Windows 工作器节点的数量。

若要缩放控制平面节点，请运行以下命令。

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -controlPlaneNodeCount <int>
```

若要缩放工作器节点，请运行以下命令。

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

控制平面节点和工作器节点必须单独缩放。

### <a name="example"></a>示例

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -controlPlaneNodeCount 3
```

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -linuxNodeCount 2 -windowsNodeCount 2 
```

## <a name="step-3-upgrade-kubernetes-version"></a>步骤 3：升级 Kubernetes 版本

若要查看可用 Kubernetes 版本的列表，请运行以下命令。

```powershell
Get-AksHciKubernetesVersion
```

若要更新到下一个 Kubernetes 版本，请运行以下命令。

```powershell
Update-AksHciCluster -clusterName <String>
                     [-patch]
```
每个 Kubernetes 版本都有主要版本、次版本和修补程序版本。 例如，在 v 1.18.6 中，1表示主版本，18表示次版本，6表示修补程序版本。 随着时间的推移，AKS 会支持一个主要版本、三个次要版本和每个次要版本2个修补程序，共6个受支持的版本。 但对于此预览版，我们支持总共4个 release-v 1.16.10、v 1.16.15、1.17.11、1.18.8。 

当在 `patch` 运行时添加参数时 `Update-AksHciCluster` ，该命令会升级到下一个修补程序版本 (如果有任何) 用于次版本。 如果在没有参数的情况下运行该命令 `patch` ，则默认升级体验将是下一个次要版本。 为了使此过程更容易，下表包含所有可能的更新体验：

| 当前版本           | 无修补程序的 Kubernetes 更新版本         | Kubernetes 更新版本与修补程序
| ---------------------------- | ------------ | -------------------------------- |
| v 1.16.10           |     v 1.17.11      | v 1.16.15
| v 1.16.15            | v 1.17.11 | 就地附加升级
| v 1.17.11           |  v 1.18。8          | 就地附加升级
| v 1.18。8             | 就地附加升级   | 就地附加升级

就地附加升级会更新 AKS 管理的所有 Kubernetes 外接程序，如。 此升级不会更改节点的 OS 版本。 它也不会更改 Kubernetes 版本。

### <a name="example---upgrade-kubernetes-version-to-the-next-minor-version"></a>示例-将 Kubernetes 版本升级到下一个次要版本

```powershell
Update-AksHciCluster -clusterName mynewcluster
```

### <a name="example---upgrade-kubernetes-version-to-the-next-patch-version"></a>示例-将 Kubernetes 版本升级到下一个修补程序版本

```powershell
Update-AksHciCluster -clusterName mynewcluster -patch
```


## <a name="step-4-access-your-clusters-using-kubectl"></a>步骤 4：使用 kubectl 访问群集

若要使用 kubectl 访问 Kubernetes 群集，请运行以下命令。 这将使用指定群集的 kubeconfig 文件作为 kubectl 的默认 kubeconfig 文件。

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>示例

```powershell
Get-AksHciCredential -clusterName mynewcluster
```

### <a name="required-parameters"></a>必需参数

`clusterName`

群集的名称。

### <a name="optional-parameters"></a>可选参数

`outputLocation`

要下载 kubeconfig 的目标位置。 默认值为 `%USERPROFILE%\.kube`。

## <a name="delete-a-kubernetes-cluster"></a>删除 Kubernetes 群集

如果需要删除 Kubernetes 群集，请运行以下命令。

```powershell
Remove-AksHciCluster -clusterName
```

### <a name="example"></a>示例

```powershell
Remove-AksHciCluster -clusterName mynewcluster
```

## <a name="get-logs"></a>获取日志

若要从所有 Pod 获取日志，请运行以下命令。 此命令将在路径 `C:\wssd\akshcilogs` 中创建名为 `akshcilogs` 的输出压缩文件夹。

```powershell
Get-AksHciLogs
```

本快速入门介绍了如何使用 PowerShell 创建、缩放和升级群集的 Kubernetes 版本。

## <a name="next-steps"></a>后续步骤

- [将群集连接到用于 Kubernetes 的 Azure Arc](./connect-to-arc.md)。
- [在 Kubernetes 群集上部署 Linux 应用程序](./deploy-linux-application.md)。
- [在 Kubernetes 群集上部署 Windows 应用程序](./deploy-windows-application.md)。
