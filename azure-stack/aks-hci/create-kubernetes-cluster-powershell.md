---
title: 使用 Windows PowerShell 在 Azure Stack HCI 上创建 Kubernetes 群集的快速入门
description: 了解如何使用 Windows PowerShell 在 Azure Stack HCI 上创建 Kubernetes 群集
author: jessicaguan
ms.topic: quickstart
ms.date: 2/10/2021
ms.author: jeguan
ms.openlocfilehash: 42d9318b40dc19e77858641364a246209275e5cd
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839838"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>快速入门：使用 Windows PowerShell 在 Azure Stack HCI 上创建 Kubernetes 群集

> 适用于 Azure Stack HCI 上的 AKS、Windows Server 2019 Datacenter 上的 AKS 运行时

本快速入门介绍如何使用 Windows PowerShell 在 Azure Stack HCI 上创建 Azure Kubernetes 群集。 然后，了解如何缩放 Kubernetes 群集并升级群集的 Kubernetes 版本。 若要改为使用 Windows Admin Center，请参阅[使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务](setup.md)。

## <a name="before-you-begin"></a>在开始之前

 - 请确保已设置 Azure Stack Kubernetes 主机。 如果没有，请参阅 [快速入门：使用 PowerShell 在 AZURE STACK HCI 上设置 Azure Kubernetes Service 主机](./setup-powershell.md)。
 - 请确保已安装最新的 PowerShell 模块 Aks-Hci。 如果不这样做，请参阅 [下载并安装 AksHci PowerShell 模块](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module)。

## <a name="step-1-create-a-kubernetes-cluster"></a>步骤 1：创建 Kubernetes 群集

安装 Azure Kubernetes 服务主机之后，便准备好部署 Kubernetes 群集。

以管理员身份打开 PowerShell 并运行以下 [AksHciCluster](./new-akshcicluster) 命令。

```powershell
New-AksHciCluster -name mycluster
```

### <a name="check-your-deployed-clusters"></a>检查部署的群集

若要获取已部署 Azure Kubernetes 服务主机和 Kubernetes 群集的列表，请运行以下命令。

```powershell
Get-AksHciCluster
```

## <a name="step-2-scale-a-kubernetes-cluster"></a>步骤 2：缩放 Kubernetes 群集

如果需要向上或向下缩放群集，可以使用 [AksHciClusterNodeCount](./set-akshciclusternodecount) 命令更改控制平面节点、Linux 工作节点或 Windows 辅助角色节点的数量。

若要缩放控制平面节点，请运行以下命令。

```powershell
Set-AksHciClusterNodeCount –name mycluster -controlPlaneNodeCount 3
```

若要缩放工作器节点，请运行以下命令。

```powershell
Set-AksHciClusterNodeCount –name mycluster -linuxNodeCount 3 -windowsNodeCount 1
```

控制平面节点和工作器节点必须单独缩放。

## <a name="step-3-upgrade-kubernetes-version"></a>步骤 3：升级 Kubernetes 版本

若要查看 Kubernetes 群集是否有任何可用的升级，请运行以下命令。

```powershell
Get-AksHciClusterUpgrades -name mycluster
```

若要更新到最新的 Kubernetes 版本，请运行以下命令。

```powershell
Update-AksHciCluster -name mycluster -kubernetesVersion <k8s version>
```

如果不指定 Kubernetes 版本就运行此命令，则会将群集升级到最新版本。 如果要升级到不是最新版本的其他版本，请使用带有参数的 [AksHciCluster](./update-akshcicluster) `-kubernetesVersion` 和所需的版本作为值 (即，v 1.18.8) 。

## <a name="step-4-access-your-clusters-using-kubectl"></a>步骤 4：使用 kubectl 访问群集

若要使用 kubectl 访问 Kubernetes 群集，请运行 [AksHciCredential](./get-akshcicredential) 命令。 这将使用指定群集的 kubeconfig 文件作为 kubectl 的默认 kubeconfig 文件。

```powershell
Get-AksHciCredential -name mycluster
```

## <a name="delete-a-kubernetes-cluster"></a>删除 Kubernetes 群集

如果需要删除 Kubernetes 群集，请运行以下命令。

```powershell
Remove-AksHciCluster -name mycluster
```

## <a name="get-logs"></a>获取日志

若要从你的所有 pod 获取日志，请运行 [AksHciLogs](./get-akshcilogs) 命令。 此命令将 `akshcilogs.zip` 在工作目录中创建一个名为的输出文件夹。 `akshcilogs.zip`运行以下命令后，该文件夹的完整路径将为输出。

```powershell
Get-AksHciLogs
```

本快速入门介绍了如何使用 PowerShell 创建、缩放和升级群集的 Kubernetes 版本。

## <a name="next-steps"></a>后续步骤

- [将群集连接到用于 Kubernetes 的 Azure Arc](./connect-to-arc.md)。
- [在 Kubernetes 群集上部署 Linux 应用程序](./deploy-linux-application.md)。
- [在 Kubernetes 群集上部署 Windows 应用程序](./deploy-windows-application.md)。
