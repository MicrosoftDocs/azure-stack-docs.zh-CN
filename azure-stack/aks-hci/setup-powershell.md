---
title: 使用 Windows PowerShell 在 Azure Stack HCI 上设置 Azure Kubernetes Service 主机的快速入门
description: 了解如何使用 Windows PowerShell 在 Azure Stack HCI 上设置 Azure Kubernetes 服务主机
author: jessicaguan
ms.topic: quickstart
ms.date: 09/23/2020
ms.author: jeguan
ms.openlocfilehash: b4b128c5d51d7f916e0936102224283dd77a971d
ms.sourcegitcommit: 849be7ebd02a1e54e8d0ec59736c9917c67e309e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2020
ms.locfileid: "91134655"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>快速入门：使用 PowerShell 在 Azure Stack HCI 上设置 Azure Kubernetes Service 主机

> 适用于： Azure Stack HCI

在本快速入门中，你将了解如何使用 PowerShell 在 Azure Stack HCI 上设置 Azure Kubernetes 服务主机。 若要改为使用 Windows 管理中心，请参阅 [使用 Windows 管理中心设置](setup.md)。

## <a name="before-you-begin"></a>开始之前

在开始之前，请确保已 Azure Stack HCI 群集或单节点 Azure Stack HCI 的2-4 节点。 **建议 Azure Stack HCI 群集使用2-4 节点。** 如果没有，请按照 [此处](./system-requirements.md)的说明操作。

还需要确保已安装 AksHci PowerShell 模块。 你可以在 [此处](https://aka.ms/AKS-HCI-Evaluate) 找到的下载包将模块包含在 zip 文件中。 请确保 () 中将 zip 文件提取到正确的位置 `%systemdrive%\program files\windowspowershell\modules` ，然后在 PowerShell 管理窗口中运行以下命令。

   ```powershell
   Import-Module AksHci
   ```

运行上述命令后，请关闭所有 PowerShell 窗口，然后重新打开管理会话，以运行以下步骤中的命令。

## <a name="step-1-prepare-your-machines-for-deployment"></a>步骤1：准备要部署的计算机 () 

首先，我们将在每个物理节点上运行检查，以查看是否满足所有要求，以便在 Azure Stack HCI 上安装 Azure Kubernetes 服务。

以管理员身份打开 PowerShell 并运行以下命令。

   ```powershell
   Initialize-AksHciNode
   ```

检查完成后，你将看到 "完成" 显示为绿色文本。

## <a name="step-2-configure-your-deployment"></a>步骤2：配置部署

设置 Azure Kubernetes 服务主机的配置设置。 **对于2-4 节点 Azure Stack HCI 群集，必须 `MultiNode` 在 `-deploymentType` 、 `wssdImageDir` 和参数中指定 `cloudConfigLocation` 。** 对于1节点 Azure Stack HCI 群集，所有参数都是可选的，并设置为其默认值。 但是，为了获得最佳性能， **建议使用2-4 节点 AZURE STACK HCI 群集部署。**

通过以下命令配置部署。

   ```powershell
   Set-AksHciConfig [-deploymentType {SingleNode, MultiNode}]
                    [-wssdImageDir]
                    [-cloudConfigLocation]
                    [-nodeConfigLocation]
                    [-vnetName]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-sshPublicKey]
                    [-vipPoolStartIp]
                    [-vipPoolEndIp]
                    [-macPoolStart]
                    [-macPoolEnd]
                    [-wssdDir]
                    [-akshciVersion]
                    [-vnetType]
                    [-nodeAgentPort]
                    [-nodeAgentAuthorizerPort]
                    [-clusterRoleName]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="optional-parameters"></a>可选参数

`-deploymentType`

部署类型。 接受的值： SingleNode、多节点。 默认值为 SingleNode。

`-wssdImageDir`

Azure Stack HCI 上的 Azure Kubernetes 服务将在其中存储 VHD 映像的目录的路径。 `%systemdrive%\wssdimagestore`对于单节点部署，默认为。 *对于多节点部署，必须指定此参数*。 路径必须指向共享存储路径（如）  `C:\ClusterStorage\Volume2\ImageStore`   或 SMB 共享（例如）  `\\FileShare\ImageStore` 。

`-cloudConfigLocation`

云代理将存储其配置的位置。 `%systemdrive%\wssdimagestore`对于单节点部署，默认为。 位置可以与上述路径相同  `-wssdImageDir` 。对于 *多节点部署，必须指定此参数*。

`-nodeConfigLocation`

节点代理存储其配置的位置。 这必须是本地路径。

`-vnetName`

要将虚拟机连接到的虚拟交换机的名称。 默认为 "外部" 名称。 如果此开关不存在，则将创建它。  

`-controlPlaneVmSize`

要为控制平面创建的 VM 的大小。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize` 。

`-loadBalancerVmSize`

要为负载均衡器 Vm 创建的 VM 的大小。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize` 。

`-sshPublicKey`

SSH 公钥文件的路径。 使用此公钥，你将能够登录到 Azure Stack HCI 部署上的 Azure Kubernetes 服务创建的任何 Vm。 如果未提供任何密钥，我们将在下查找一个密钥  `%systemdrive%\Users\<username>\.ssh\id_rsa.pub` 。 如果文件不存在，将生成并使用上述位置的 SSH 密钥对。  

`-vipPoolStartIp`

使用 VIP 池进行部署时，此参数将指定池的网络启动。 默认为无。

`-vipPoolEndIp`

使用 VIP 池进行部署时，此参数将指定池的网络端。 默认为无。

`-macPoolStart`

这用于指定你希望用于 Azure Kubernetes 服务主机 VM 的 MAC 池的 MAC 地址开始。 MAC 地址的语法要求第一个字节的最小有效位应始终为0，第一个字节应始终为偶数 (例如00、02、04、06 ... ) 。典型的 MAC 地址如下所示：02：1E：2B：78：00：00。 默认为无。

`-macPoolEnd`

用于指定要用于 Azure Kubernetes 服务主机 VM 的 MAC 池的 MAC 地址的结尾。 MAC 地址的语法要求第一个字节的最小有效位应始终为0，第一个字节应始终为偶数 (例如00、02、04、06 ... ) 。作为传递的地址的第一个字节 `-macPoolEnd` 应与作为传递的地址的第一个字节相同 `-macPoolStart` 。 默认为无。

`-wssdDir`

这是一个用于存储小文件的模块的工作目录。 `%PROGRAMFILES%\AksHci`   对于大多数部署，不应更改默认值和。  

`-akshciVersion`

要部署 Azure Stack HCI 上的 Azure Kubernetes 服务版本。 默认值为最新版本。

`-vnetType`

要连接或创建的虚拟交换机的类型。 默认值为 "外部" 交换机类型。

`-nodeAgentPort`

Nodeagents 应侦听的 TCP/IP 端口号。 默认值为45000。  

`-nodeAgentAuthorizerPort`

Nodeagents 应用于其授权端口的 TCP/IP 端口号。 默认值为45001。  

`-clusterRoleName`

此名称指定在群集中创建 cloudagent 作为一般服务时要使用的名称。 此名称默认为具有 ca 前缀的唯一名称和 guid 后缀 (例如： "ca-9e6eb299-bc0b-4f00-9fd7-942843820c26" ) 

`-skipHostLimitChecks`

请求脚本在允许部署继续之前，跳过它用于确认内存和磁盘空间的所有检查是否可用。

`-insecure`

在 Azure Stack HCI 组件（例如 cloudagent 和 nodeagent (s）上部署 Azure Kubernetes 服务，) 在不安全模式下 (无 TLS 安全连接) 。不建议在生产环境中使用不安全模式。

`-skipUpdates`

如果要跳过所有可用的更新，请使用此标志。

`-forceDnsReplication`

在某些系统上，DNS 复制最多可能需要一小时。 这将导致部署速度变慢。 如果遇到此问题，你将看到 AksHci 将停滞在循环中。 若要解决此问题，请尝试使用此标志。 该 `-forceDnsReplication` 标志不是一个保证修补程序。 如果该标志后面的逻辑失败，则将隐藏该错误，并且命令将继续执行，如同未提供该标志。

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Azure Stack HCI 配置重置 Azure Kubernetes 服务

若要重置 Azure Stack HCI 配置上的 Azure Kubernetes 服务，请运行以下命令。 自行运行此命令会将配置重置为默认值。

```powershell
Set-AksHciConfig
```

## <a name="step-3-start-a-new-deployment"></a>步骤3：启动新部署

配置部署后，必须启动部署。 这会在 Azure Stack HCI 代理/服务和 Azure Kubernetes 服务主机上安装 Azure Kubernetes 服务。

若要开始部署，请运行以下命令。

```powershell
Install-AksHci
```

### <a name="check-your-deployed-clusters"></a>检查部署的群集

若要获取已部署的 Azure Kubernetes 服务主机的列表，请运行以下命令。 部署 Kubernetes 群集后，还可以使用相同的命令获取这些群集。

```powershell
Get-AksHciCluster
```

## <a name="step-4-access-your-clusters-using-kubectl"></a>步骤4：使用 kubectl 访问群集

若要使用 kubectl 访问 Azure Kubernetes Service 主机或 Kubernetes 群集，请运行以下命令。 这将使用指定群集的 kubeconfig 文件作为 kubectl 的默认 kubeconfig 文件。

```powershell
Set-AksHciKubeConfig -clusterName
```

## <a name="get-logs"></a>获取日志

若要从你的所有 pod 获取日志，请运行以下命令。 此命令将在路径中创建一个名为的输出文件夹 `akshcilogs` `C:\wssd\akshcilogs` 。

```powershell
Get-AksHciLogs
```

## <a name="reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上重新安装 Azure Kubernetes 服务

在 Azure Stack HCI 上重新安装 Azure Kubernetes 服务将删除所有 Kubernetes 群集（如果有）以及 Azure Kubernetes 服务主机。 它还将从节点上的 Azure Stack HCI 代理和服务上卸载 Azure Kubernetes 服务。 然后，它将返回原始安装过程步骤，直到重新创建主机。 通过配置的 Azure Stack HCI 配置上的 Azure Kubernetes 服务 `Set-AksHciConfig` 和下载的 VHDX 映像会被保留。

若要在 Azure Stack HCI 上重新安装 Azure Kubernetes 服务，请运行以下命令。

```powershell
Restart-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上删除 Azure Kubernetes 服务

若要在 Azure Stack HCI 上删除 Azure Kubernetes 服务，请运行以下命令。

```powershell
Uninstall-AksHci
```

## <a name="next-steps"></a>后续步骤

- [为应用程序创建 Kubernetes 群集](create-kubernetes-cluster-powershell.md)
