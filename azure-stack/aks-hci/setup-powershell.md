---
title: 使用 Windows PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机的快速入门
description: 了解如何使用 Windows PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机
author: jessicaguan
ms.topic: quickstart
ms.date: 09/23/2020
ms.author: jeguan
ms.openlocfilehash: 089488e246bdb7c12bbd0808ef2e92a4c83b0fce
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253952"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>快速入门：使用 PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机

> 适用于：Azure Stack HCI

在本快速入门中，你将了解如何使用 PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机。 若要改为使用 Windows 管理中心，请参阅 [使用 Windows 管理中心设置](setup.md)。

## <a name="before-you-begin"></a>准备阶段

在开始之前，请确保你已有一个 2-4 节点的 Azure Stack HCI 群集或单节点 Azure Stack HCI。 **建议使用 2-4 节点的 Azure Stack HCI 群集。** 否则，请按照 [AZURE STACK HCI 注册页](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)上的说明进行操作。

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>步骤1：下载并安装 AksHci PowerShell 模块

`AKS-HCI-Public=Preview-Oct-2020`从[Azure Stack HCI 注册页上的 Azure Kubernetes 服务](https://aka.ms/AKS-HCI-Evaluate)下载。 Zip 文件 `AksHci.Powershell.zip` 包含 PowerShell 模块。

如果以前在使用 PowerShell 或 Windows 管理中心 Azure Stack HCI 上安装了 Azure Kubernetes 服务，请在继续操作之前运行以下命令。

   ```powershell
   Uninstall-AksHci
   ```

**关闭所有 PowerShell 窗口。** 删除位于路径中的 AksHci、AksHci、Day2.ps1 和 MSK8sDownloadAgent 的任何现有目录 `%systemdrive%\program files\windowspowershell\modules` 。 完成此操作后，你可以提取新的 zip 文件的内容。 请确保 () 中将 zip 文件提取到正确的位置 `%systemdrive%\program files\windowspowershell\modules` 。

   ```powershell
   Import-Module AksHci
   ```

运行上述命令后，关闭所有 PowerShell 窗口，然后重新打开管理会话，以运行以下步骤中的命令。

## <a name="step-2-prepare-your-machines-for-deployment"></a>步骤2：准备要部署的计算机 () 

在每个物理节点上运行检查，查看是否满足所有要求，以便在 Azure Stack HCI 上安装 Azure Kubernetes 服务。

以管理员身份打开 PowerShell 并运行以下命令。

   ```powershell
   Initialize-AksHciNode
   ```

检查完以后，你会看到以绿色文本显示的“已完成”。

## <a name="step-3-configure-your-deployment"></a>步骤3：配置部署

设置 Azure Kubernetes 服务主机的配置设置。 **对于 2-4 节点的 Azure Stack HCI 群集，必须在 `-deploymentType`、`wssdImageDir` 和 `cloudConfigLocation` 参数中指定 `MultiNode`。** 对于 1 节点的 Azure Stack HCI 群集，所有参数都是可选的，并设置为其默认值。 但是，为了获得最佳性能，**建议使用 2-4 节点的 Azure Stack HCI 群集部署。**

使用以下命令配置部署。

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
                    [-vlanID]
                    [-cloudServiceCidr]
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

部署类型。 接受的值：SingleNode、MultiNode。 默认值为 SingleNode。

`-wssdImageDir`

Azure Stack HCI 上的 Azure Kubernetes 服务将在其中存储 VHD 映像的目录的路径。 对于单节点部署，默认值为 `%systemdrive%\wssdimagestore`。 对于多节点部署，必须指定此参数。 路径必须指向共享的存储路径（例如 `C:\ClusterStorage\Volume2\ImageStore` ）或 SMB 共享（例如 `\\FileShare\ImageStore`）。

`-cloudConfigLocation`

云代理将存储其配置的位置。 对于单节点部署，默认值为 `%systemdrive%\wssdimagestore`。 此位置可以与上面的 `-wssdImageDir` 路径相同。 对于多节点部署，必须指定此参数。

`-nodeConfigLocation`

节点代理将存储其配置的位置。 这必须是一个本地路径。

`-vnetName`

要将虚拟机与之连接的虚拟交换机的名称。 默认为“外部”名称。 如果该交换机不存在，则会创建它。  

`-controlPlaneVmSize`

要为控制平面创建的 VM 的大小。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

`-loadBalancerVmSize`

要为负载均衡器 VM 创建的 VM 的大小。 若要获取可用 VM 大小的列表，请运行 `Get-AksHciVmSize`。

`-sshPublicKey`

SSH 公钥文件的路径。 使用此公钥，你将能够登录到 Azure Stack HCI 部署上的 Azure Kubernetes 服务创建的任何 VM。 如果未提供任何密钥，我们将在 `%systemdrive%\Users\<username>\.ssh\id_rsa.pub` 下查找一个密钥。 如果文件不存在，将生成并使用上述位置的 SSH 密钥对。  

`-vipPoolStartIp`

使用 VIP 池进行部署时，此参数将指定池的网络开始 IP。 默认为无。

`-vipPoolEndIp`

使用 VIP 池进行部署时，此参数将指定池的网络结束 IP。 默认为无。

`-macPoolStart`

这用于指定你希望用于 Azure Kubernetes 服务主机 VM 的 MAC 池的 MAC 地址开头。 MAC 地址的语法要求第一个字节的最小有效位应始终为 0，第一个字节应始终是偶数（即 00、02、04、06...）。典型的 MAC 地址可能如下所示：02:1E:2B:78:00:00。 默认为无。

`-macPoolEnd`

这用于指定你希望用于 Azure Kubernetes 服务主机 VM 的 MAC 池的 MAC 地址结尾。 MAC 地址的语法要求第一个字节的最小有效位应始终为 0，第一个字节应始终是偶数（即 00、02、04、06...）。作为 `-macPoolEnd` 传递的地址的第一个字节应与作为 `-macPoolStart` 传递的地址的第一个字节相同。 默认为无。

`-vlandID`

这可以用来指定网络 VLAN ID。 Azure Kubernetes 服务主机和 Kubernetes 群集 VM 网络适配器将用提供的 VLAN ID 进行标记。 默认为无。

`cloudServiceCidr`

这可用于提供要分配给 MOC CloudAgent 服务的静态 IP/网络前缀。 应使用 CIDR 格式提供此值。  (示例： 192.168.1.2/16) 。 默认为无。

`-wssdDir`

这是用于存储小文件的模块的工作目录。 默认为 `%PROGRAMFILES%\AksHci` ，大多数部署不应进行更改。  

`-akshciVersion`

要部署的 Azure Stack HCI 上的 Azure Kubernetes 服务的版本。 默认值为最新版本。

`-vnetType`

要连接或创建的虚拟交换机的类型。 此值默认为“外部”交换机类型。

`-nodeAgentPort`

节点代理应侦听的 TCP/IP 端口号。 默认为 45000。  

`-nodeAgentAuthorizerPort`

节点代理应当将它用于其授权端口的 TCP/IP 端口号。 默认为 45001。  

`-clusterRoleName`

这指定在群集中将云代理创建为一般服务时要使用的名称。 这默认设置为采用前缀 ca- 和一个 guid 后缀的唯一名称（例如：“ca-9e6eb299-bc0b-4f00-9fd7-942843820c26”）

`-skipHostLimitChecks`

在允许部署继续进行之前，请求脚本跳过它用于确认内存和磁盘空间是否可用的所有检查。

`-insecure`

在不安全模式（无 TLS 安全连接）下部署 Azure Stack HCI 上的 Azure Kubernetes 服务组件，例如云代理和节点代理。  建议不要在生产环境中使用不安全模式。

`-skipUpdates`

如果要跳过任何可用的更新，请使用此标志。

`-forceDnsReplication`

在某些系统上，DNS 复制最多可能需要一小时。 这会导致部署速度缓慢。 如果遇到此问题，你会看到 Install-AksHci 停滞在循环中。 若要解决此问题，请尝试使用此标志。 `-forceDnsReplication` 标志不是有保证的修补方法。 如果该标志背后的逻辑失败，则会隐藏该错误，并且命令将继续执行，如同未提供该标志一样。

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>重置 Azure Stack HCI 上的 Azure Kubernetes 服务的配置

若要重置 Azure Stack HCI 上的 Azure Kubernetes 服务的配置，请运行以下命令。 单独运行此命令会将配置重置为默认值。

```powershell
Set-AksHciConfig
```

## <a name="step-4-start-a-new-deployment"></a>步骤4：启动新部署

配置你的部署后，你必须启动部署。 这会在 Azure Stack HCI 代理/服务和 Azure Kubernetes 服务主机上安装 Azure Kubernetes 服务。

若要开始部署，请运行以下命令。

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>验证已部署的 Azure Kubernetes 服务主机

若要确保 Azure Kubernetes 服务主机已部署，请运行以下命令。 部署 Kubernetes 群集后，还可以使用相同的命令获取这些群集。

```powershell
Get-AksHciCluster
```

## <a name="step-5-access-your-clusters-using-kubectl"></a>步骤5：使用 kubectl 访问群集

若要使用 kubectl 访问 Azure Kubernetes 服务主机或 Kubernetes 群集，请运行以下命令。 这将使用指定群集的 kubeconfig 文件作为 kubectl 的默认 kubeconfig 文件。

```powershell
Get-AksHciCredential -clusterName
                     [-outputLocation]
```

### <a name="required-parameters"></a>必需参数

`clusterName`

群集的名称。

### <a name="optional-parameters"></a>可选参数

`outputLocation`

要下载 kubeconfig 的位置。 默认值为 `%USERPROFILE%\.kube`。

## <a name="get-logs"></a>获取日志

若要从所有 Pod 获取日志，请运行以下命令。 此命令会在路径 `C:\wssd\akshcilogs` 中创建名为 `akshcilogs` 的输出压缩文件夹。

```powershell
Get-AksHciLogs
```

## <a name="reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>重新安装 Azure Stack HCI 上的 Azure Kubernetes 服务

重新安装 Azure Stack HCI 上的 Azure Kubernetes 服务会删除所有 Kubernetes 群集（如果有）以及 Azure Kubernetes 服务主机。 它还会从节点中卸载 Azure Stack HCI 代理和服务上的 Azure Kubernetes 服务。 然后，它会重复原始安装过程步骤，直到重新创建主机。 你通过 `Set-AksHciConfig` 配置的 Azure Stack HCI 上的 Azure Kubernetes 服务配置和下载的 VHDX 映像将保留。

若要重新安装 Azure Stack HCI 上的 Azure Kubernetes 服务，请运行以下命令。

```powershell
Restart-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>删除 Azure Stack HCI 上的 Azure Kubernetes 服务

若要删除 Azure Stack HCI 上的 Azure Kubernetes 服务，请运行以下命令。

```powershell
Uninstall-AksHci
```

## <a name="next-steps"></a>后续步骤

- [为应用程序创建 Kubernetes 群集](create-kubernetes-cluster-powershell.md)
