---
title: 使用 Windows PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机的快速入门
description: 了解如何使用 Windows PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机
author: jessicaguan
ms.topic: quickstart
ms.date: 02/12/2021
ms.author: jeguan
ms.openlocfilehash: f14f3550e7a6d51ffd51a521bc6f155186b4c0c0
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839719"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>快速入门：使用 PowerShell 设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机

> 适用于： Azure Stack HCI、Windows Server 2019 Datacenter

在本快速入门中，你将了解如何使用 PowerShell 设置 Azure Kubernetes 服务主机。 若要改为使用 Windows Admin Center，请参阅[使用 Windows Admin Center 进行设置](setup.md)。

## <a name="before-you-begin"></a>准备阶段

请确保具有以下各项之一：
 - 2-4 节点 Azure Stack HCI 群集
 - Windows Server 2019 Datacenter 故障转移群集
 - 单节点 Windows Server 2019 Datacenter 
 
在开始之前，请确保已满足[系统要求](.\system-requirements.md)页上的所有先决条件。 
建议使用 2-4 节点的 Azure Stack HCI 群集。 如果没有上述任何一种，请按照 [AZURE STACK HCI 注册页](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)上的说明进行操作。    

> [!IMPORTANT]
> 删除 Azure Stack HCI 上的 Azure Kubernetes 服务时，请参阅 [在 AZURE STACK hci 上删除 Azure Kubernetes 服务](#remove-azure-kubernetes-service-on-azure-stack-hci) ，并仔细按照说明进行操作。 

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>步骤 1：下载并安装 AksHci PowerShell 模块

请从 [Azure Stack HCI 上的 Azure Kubernetes 服务注册页](https://aka.ms/AKS-HCI-Evaluate)下载 `AKS-HCI-Public-Preview-Feb-2021`。 zip 文件 `AksHci.Powershell.zip` 包含 PowerShell 模块。

关闭 PowerShell 窗口。 删除位于路径中的 AksHci、AksHci、Day2.ps1、Kva、Moc 和 MSK8sDownloadAgent 的任何现有目录 `%systemdrive%\program files\windowspowershell\modules` 。 完成此操作后，便可提取新 zip 文件的内容。 请确保在正确的位置 (`%systemdrive%\program files\windowspowershell\modules`) 提取 zip 文件。

   ```powershell
   Import-Module AksHci
   ```

**关闭所有 powershell 窗口** ，然后重新打开新的管理会话，以检查是否安装了最新版本的 powershell 模块。
  
   ```powershell
   Get-Command -Module AksHci
   ```

 **输出：**
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Initialize-AksHciNode                              0.2.19     akshci
Function        Get-AksHciCluster                                  0.2.19     akshci
Function        Get-AksHciClusterUpgrades                          0.2.19     akshci
Function        Get-AksHciConfig                                   0.2.19     akshci
Function        Get-AksHciCredential                               0.2.19     akshci
Function        Get-AksHciEventLog                                 0.2.19     akshci
Function        Get-AksHciKubernetesVersion                        0.2.19     akshci
Function        Get-AksHciLogs                                     0.2.19     akshci
Function        Get-AksHciUpdates                                  0.2.19     akshci
Function        Get-AksHciVersion                                  0.2.19     akshci
Function        Get-AksHciVmSize                                   0.2.19     akshci
Function        Install-AksHci                                     0.2.19     akshci
Function        Install-AksHciAdAuth                               0.2.19     akshci
Function        Install-AksHciArcOnboarding                        0.2.19     akshci
Function        New-AksHciCluster                                  0.2.19     akshci
Function        New-AksHciNetworkSetting                           0.2.19     akshci
Function        Remove-AksHciCluster                               0.2.19     akshci
Function        Restart-AksHci                                     0.2.19     akshci
Function        Set-AksHciClusterNodeCount                         0.2.19     akshci
Function        Set-AksHciConfig                                   0.2.19     akshci
Function        Uninstall-AksHci                                   0.2.19     akshci
Function        Uninstall-AksHciAdAuth                             0.2.19     akshci
Function        Uninstall-AksHciArcOnboarding                      0.2.19     akshci
Function        Update-AksHci                                      0.2.19     akshci
Function        Update-AksHciCluster                               0.2.19     akshci
```

运行上述命令后，关闭所有 PowerShell 窗口，然后重新打开管理会话，以运行以下步骤中的命令。

### <a name="update-your-clusters-to-the-latest-aks-hci-version"></a>将群集更新到最新的 AKS-HCI 版本

如果你已有部署并想要更新它，请执行此步骤。 如果没有现有部署，请跳过此步骤，并继续执行步骤2。 或者，如果你有一个部署，但想要运行下一个 AKSHCI 版本的全新安装，请运行， `Uninstall-AksHci` 然后继续执行步骤2。

若要更新到 Azure Stack HCI 上的 Azure Kubernetes 服务的最新版本，请运行 [akshci](./update-akshci.md) 命令。 此命令更新 Azure Kubernetes 服务主机和本地 Microsoft 运行的云平台。 此命令不会升级任何现有工作负荷群集中的 Kubernetes 和 Windows 节点操作系统版本。 更新 AKS 主机后创建的新工作负载群集将不同于其 Windows 节点 OS 版本和 Kubernetes 版本中的现有工作负荷群集。

```powershell
Update-AksHci
```

建议在更新管理群集后立即更新工作负荷群集，以防止在 Kubernetes 群集中运行 Windows 节点不受支持的 Windows Server 操作系统版本。 如果工作负载群集处于不受支持的版本中，则它们仍受支持，但你将无法创建新节点。 若要更新工作负荷群集，请访问 [更新工作负荷群集](create-kubernetes-cluster-powershell.md)。

## <a name="step-2-prepare-your-machines-for-deployment"></a>步骤 2：准备要部署的计算机

在每个物理节点上运行检查，以查看所有要求是否都已得到满足，以便安装 Azure Stack HCI 上的 Azure Kubernetes 服务。
以管理员身份打开 PowerShell 并运行以下命令。

```powershell
Initialize-AksHciNode
```

检查完以后，你会看到以绿色文本显示的“已完成”。

## <a name="step-3-create-a-virtual-network"></a>步骤3：创建虚拟网络

若要获取可用外部交换机的名称，请运行以下命令：

```powershell
Get-VMSwitch
```

必须使用外部开关。

示例输出：
```output
Name SwitchType NetAdapterInterfaceDescription
---- ---------- ------------------------------
extSwitch External Mellanox ConnectX-3 Pro Ethernet Adapter
```

若要为你的部署中要使用的节点创建虚拟网络，请使用 [akshcinetworksetting](.\new-akshcinetworksetting.md) PowerShell 命令创建环境变量。 稍后将使用它来配置使用静态 IP 的部署。 如果要配置 AKS 部署和 DHCP，请访问 [akshcinetworksetting](.\new-akshcinetworksetting.md) 以获取示例。

```powershell
#static IP
$vnet = New-AksHciNetworkSetting -vnetName "extSwitch" -k8sNodeIpPoolStart "172.16.10.0" -k8sNodeIpPoolEnd "172.16.10.255" -vipPoolStart "172.16.255.0" -vipPoolEnd
"172.16.255.254" -ipAddressPrefix "172.16.0.0/16" -gateway "172.16.0.1" -dnsServers "172.16.0.1"
```

> [!NOTE]
> 需要为你的环境自定义此示例命令中给定的值。

## <a name="step-4-configure-your-deployment"></a>步骤4：配置部署

使用 [akshciconfig](./set-akshciconfig.md) 命令设置 Azure Kubernetes 服务主机的配置设置。 **如果要在2-4 节点 Azure Stack HCI 群集或 Windows Server 2019 Datacenter 故障转移群集，则必须指定 `imageDir` 和 `cloudConfigLocation` 参数。** 如果要重置配置详细信息，请用新参数再次运行该命令。

使用以下命令配置部署。

```powershell
Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16" 
```

> [!NOTE]
> `-cloudservicecidr`此示例命令中给定的值将需要为你的环境自定义。

使用 `-enableDiagnosticData` 参数同意将所需的诊断数据发送给 Microsoft。 如果未使用此参数，则在运行时，系统将提示您启用诊断数据 `Set-AksHciConfig` 。 `Install-AksHci` 如果你不接受许可提示，则会失败 `Set-AksHciConfig` 。

## <a name="step-5-start-a-new-deployment"></a>步骤5：启动新部署

配置部署后，必须启动它。 这会在 Azure Stack HCI 代理/服务和 Azure Kubernetes 服务主机上安装 Azure Kubernetes 服务。

若要开始部署，请运行以下命令。

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>验证已部署的 Azure Kubernetes 服务主机

若要确保 Azure Kubernetes 服务主机已部署，请运行 [akshcicluster](./get-akshcicluster.md) 命令。 部署 Kubernetes 群集后，还可以使用相同的命令获取这些群集。

```powershell
Get-AksHciCluster
```

**输出：**
```

Name            : clustergroup-management
Version         : v1.18.10
Control Planes  : 1
Linux Workers   : 0
Windows Workers : 0
Phase           : provisioned
Ready           : True
```

## <a name="step-6-access-your-clusters-using-kubectl"></a>步骤6：使用 kubectl 访问群集

若要使用访问 Azure Kubernetes 服务主机 `kubectl` ，请运行 [akshcicredential](./get-akshcicredential.md) 命令。 这将使用指定群集的 _kubeconfig_ 文件作为的默认 _kubeconfig_ 文件 `kubectl` 。 部署后，还可以使用此命令访问其他 Kubernetes 群集。

```powershell
Get-AksHciCredential -name clustergroup-management
```

## <a name="get-logs"></a>获取日志

若要从你的所有 pod 获取日志，请运行 [akshcilogs](./get-akshcilogs) 命令。 此命令将在路径中创建一个名为的输出文件夹 `akshcilogs` `c:\%workingdirectory%\%AKS HCI release number%\%filename%` (例如 `c:\AksHci\0.9.6.0\akshcilogs.zip`) 。

```powershell
Get-AksHciLogs
```

## <a name="update-to-the-latest-version-of-azure-kubernetes-service-on-azure-stack-hci"></a>在 Azure Stack HCI 上更新到最新版本的 Azure Kubernetes 服务

若要更新到 Azure Stack HCI 上的 Azure Kubernetes 服务的最新版本，请运行 [akshci](./update-akshci.md) 命令。 仅当安装了 Oct 版本或更高版本时，更新命令才起作用。 对于早于10月版本的版本，它将不起作用。 此更新命令将更新 Azure Kubernetes 服务主机和本地 Microsoft 运行的云平台。 此命令不会升级任何现有的工作负荷群集。 更新 AKS 主机后创建的新工作负载群集将不同于其 Windows 节点 OS 版本和 Kubernetes 版本中的现有工作负荷群集。

```powershell
Update-AksHci
```
   
建议在更新管理群集后立即更新工作负荷群集，以防止在 Kubernetes 群集中运行 Windows 节点不受支持的 Windows Server 操作系统版本。 如果工作负载群集处于不受支持的版本中，则它们仍受支持，但你将无法创建新节点。 若要更新工作负荷群集，请访问 [更新工作负荷群集](create-kubernetes-cluster-powershell.md)。

## <a name="restart-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上重新启动 Azure Kubernetes 服务

如果在 Azure Stack HCI 上重新启动 Azure Kubernetes 服务，则将删除所有 Kubernetes 群集（如果有）以及 Azure Kubernetes 服务主机。 它还会从节点中卸载 Azure Stack HCI 代理和服务上的 Azure Kubernetes 服务。 然后，它会重复原始安装过程步骤，直到重新创建主机。 你通过 `Set-AksHciConfig` 配置的 Azure Stack HCI 上的 Azure Kubernetes 服务配置和下载的 VHDX 映像将保留。

若要在具有相同配置设置的 Azure Stack HCI 上重新启动 Azure Kubernetes 服务，请运行以下命令。

```powershell
Restart-AksHci
```

## <a name="reset-configuration-settings-and-reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>在 Azure Stack HCI 上重置配置设置并重新安装 Azure Kubernetes 服务

若要在具有不同配置设置的 Azure Stack HCI 上重新安装 Azure Kubernetes Service，请首先运行以下命令。

```powershell
Uninstall-AksHci
```

运行上述命令后，可以通过以下命令更改配置设置。 参数将保持不变，如步骤3中所述。 

```powershell
Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16" 
```

将配置更改为所需的设置后，运行以下命令，在 Azure Stack HCI 上重新安装 Azure Stack Kubernetes。

```powershell
Install-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>删除 Azure Stack HCI 上的 Azure Kubernetes 服务


若要删除 Azure Stack HCI 上的 Azure Kubernetes 服务，请运行以下命令。 此命令将删除旧配置， `Set-AksHciConfig` 重新安装时将需要再次运行。

```powershell
Uninstall-AksHci
```

如果要保留旧配置，请运行以下命令。

```powershell
Uninstall-AksHci -SkipConfigCleanup
```

## <a name="next-steps"></a>后续步骤

- [为应用程序创建 Kubernetes 群集](create-kubernetes-cluster-powershell.md)
