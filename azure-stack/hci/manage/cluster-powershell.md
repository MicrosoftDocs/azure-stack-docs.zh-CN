---
title: 使用 PowerShell 管理 Azure Stack HCI 群集
description: 了解如何使用 PowerShell 在 Azure Stack HCI 上管理群集
author: v-dasis
ms.topic: article
ms.date: 05/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 922a6188c1529d3461288ac28ff542db6fa9a527
ms.sourcegitcommit: f2203f070c17e19ba1a41d681662bac3ab50ed12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83855681"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>使用 PowerShell 管理 Azure Stack HCI 群集

> 适用于 Windows Server 2019

Windows PowerShell 可用于管理资源并在 Azure Stack HCI 群集上配置功能。

通常，你可以从运行 Windows 10 的远程计算机而不是群集中的主机服务器管理群集。 此远程计算机称为管理计算机。

> [!NOTE]
> 在管理计算机上运行 PowerShell 命令时，请将 `-Name` 或参数包含在所管理的群集的名称中 `-Cluster` 。 此外，使用服务器节点的参数时，你将需要指定完全限定的域名（FQDN） `-ComputerName` 。

有关使用 PowerShell 管理群集的完整参考文档，请参阅[failovercluster-cmdinterface 该参考](https://docs.microsoft.com/powershell/module/failoverclusters/?view=win10-ps)。

## <a name="using-windows-powershell"></a>使用 Windows PowerShell

Windows PowerShell 用于执行本文中的所有任务。 建议你将应用程序固定到任务栏以方便使用。

如果以下 cmdlet 在 PowerShell 会话中不可用，则可能需要 `Failover Cluster` 使用以下 PowerShell cmd：为 Windows PowerShell 功能添加模块 `Add-WindowsFeature RSAT-Clustering-PowerShell` 。

> [!NOTE]
> 从 windows 10 年10月2018更新开始，从 Windows 10 开始，RSAT 包含为一组 "按需功能"。 只需中转到 "**设置" > 应用 "> 应用" & 功能 > 可选功能，> RSAT：故障转移群集工具添加功能**，然后选择 "**安装**"。 若要查看安装进度，请单击 "后退" 按钮以查看 "管理可选功能" 页上的状态。 安装的功能将在 Windows 10 版本升级期间保持。

## <a name="view-cluster-settings-and-resources"></a>查看群集设置和资源

获取名为 Cluster1 的群集的相关信息：

```powershell
Get-Cluster -Name Cluster1
```
获取 Cluster1 中的一个或多个节点或服务器的相关信息：

```powershell
Get-ClusterNode -Cluster Cluster1
```

若要查看群集节点上安装了哪些 Windows 功能，请使用 `Get-WindowsFeature` cmdlet。 例如：

```powershell
Get-WindowsFeature -ComputerName Server1
```

查看网络适配器及其属性，如名称、IPv4 地址和 VLAN ID：

```powershell
Get-NetAdapter -CimSession Server1 | Where Status -Eq "Up" | Sort InterfaceAlias | Format-Table Name, InterfaceDescription, Status, LinkSpeed, VLANID, MacAddress
```

若要查看 Hyper-v 虚拟交换机，以及如何对物理网络适配器进行分组：

```powershell
Get-VMSwitch -ComputerName Server1
```

若要查看主机虚拟网络适配器，请执行以下操作：

```powershell
Get-VMNetworkAdapter -ComputerName Server1
```

若要查看是否启用了存储空间直通：

```powershell
Get-CimSession -ComputerName Server1 | Get-ClusterStorageSpacesDirect
```

## <a name="start-or-stop-a-cluster"></a>启动或停止群集

使用 `Start-Cluster` 和 `Stop-Cluster` cmdlet 为群集添加或删除服务器节点。 有关更多示例和用法信息，请参阅[启动群集](https://docs.microsoft.com/powershell/module/failoverclusters/start-cluster?view=win10-ps)和[停止群集](https://docs.microsoft.com/powershell/module/failoverclusters/stop-cluster?view=win10-ps)参考文档。

在尚未启动群集的所有服务器节点上启动群集服务：

```powershell
Start-Cluster -Name Cluster1
```

此示例将停止名为 Cluster1 的群集中所有节点上的群集服务，这将停止群集中配置的所有服务和应用程序：

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>添加或删除服务器

使用 `Add-ClusterNode` 和 `Remove-ClusterNode` cmdlet 为群集添加或删除服务器节点。 有关更多示例和用法信息，请参阅[start-clusternode](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode?view=win10-ps)和[start-clusternode](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode?view=win10-ps)参考文档。

此示例将名为节点4的服务器添加到名为 Cluster1 的群集。 请确保服务器已开机并连接到群集网络。

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

此示例从 cluster Cluster1 中删除名为节点4的节点：

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="set-quorum-options"></a>设置仲裁选项

使用 `Set-ClusterQuorum` cmdlet 设置群集的仲裁选项。 有关更多示例和用法信息，请参阅[set-clusterquorum](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps)参考文档。

此示例将仲裁配置更改为在群集 Cluster1 上使用云见证：

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

此示例使用文件共享见证的 fileserver\fsw 中的磁盘资源，将群集 Cluster1 上的仲裁配置更改为节点和文件共享多数 \\ 。

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>启用存储空间直通

使用 `Enable-ClusterStorageSpacesDirect` cmdlet 启用群集上的存储空间直通。 有关更多示例和用法信息，请参阅[ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps)参考文档。

此示例在 Server1 上启用存储空间直通：

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>配置 Hyper-v 主机

使用 `Set-VMHost` cmdlet 来配置各种 hyper-v 主机设置，如 VHD 和 VM 路径、实时迁移、存储迁移、身份验证、NUMA 跨越及其他设置。 有关更多示例和用法信息，请参阅[ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/hyper-v/Set-VMHost?view=win10-ps)参考文档。

此示例为主机服务器 Server1 上的虚拟硬盘和 Vm 指定新的默认位置：

```powershell
Set-VMHost -ComputerName Server1 -VirtualHardDiskPath "C:\Hyper-V\Virtual Hard Disks" -VirtualMachinePath "C:\Hyper-V\Configuration Files"
```

此示例将主机服务器 Server1 配置为允许同时进行10次实时迁移和存储迁移：

```powershell
Set-VMHost -ComputerName Server1 -MaximumVirtualMachineMigrations 10 -MaximumStorageMigrations 10
```

此示例将主机服务器 Server1 配置为使用 Kerberos 对传入的实时迁移进行身份验证：

```powershell
Set-VMHost -ComputerName Server1 -VirtualMachineMigrationAuthenticationType Kerberos
```

## <a name="validate-a-cluster"></a>验证群集

使用 `Test-Cluster` cmdlet 可在群集上运行验证测试。 有关更多示例和用法信息，请参阅[测试群集](https://docs.microsoft.com/powershell/module/failoverclusters/test-cluster?view=win10-ps)参考文档。

此示例将在名为 Cluster1 的群集上运行所有适用的群集验证测试：

```powershell
Test-Cluster -Cluster Cluster1
```

此示例列出了群集验证中所有测试和类别的名称。 用*Ignore*或*Include*参数指定这些测试名称以运行特定测试：


```powershell
Test-Cluster -Cluster Cluster1 -List
Category                                DisplayName                             Description
--------                                -----------                             -----------
Cluster Configuration                   List Cluster Core Groups                List information about the available...
Cluster Configuration                   List Cluster Network Information        List cluster-specific network settin...
Cluster Configuration                   List Cluster Resources                  List the resources that are configur...
Cluster Configuration                   List Cluster Volumes                    List information for the volumes in ...
Cluster Configuration                   List Clustered Roles                    List information about clustered roles.
Cluster Configuration                   Validate Quorum Configuration           Validate that the current quorum con...
Cluster Configuration                   Validate Resource Status                Validate that cluster resources are ...
Cluster Configuration                   Validate Service Principal Name         Validate that a Service Principal Na...
Cluster Configuration                   Validate Volume Consistency             If any volumes are flagged as incons...
Hyper-V Configuration                   List Information About Servers Runni... List Hyper-V related information on ...
Hyper-V Configuration                   Validate Compatibility of Virtual Fi... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Memory Resource Poo... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Network Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Processor Resource ... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Role Installed         Validate that all the nodes have the...
Hyper-V Configuration                   Validate Hyper-V Storage Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Matching Processor Manufact... Validate that all specified nodes sh...
Hyper-V Configuration                   List Hyper-V Virtual Machine Informa... List Hyper-V virtual machine informa...
Hyper-V Configuration                   Validate Hyper-V Integration Service... Validate that the Hyper-V integratio...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Net... Validate that all virtual machines o...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Sto... Validate that all virtual machines o...
Inventory                               List Fibre Channel Host Bus Adapters    List Fibre Channel host bus adapters...
Inventory                               List iSCSI Host Bus Adapters            List iSCSI host bus adapters on each...
Inventory                               List SAS Host Bus Adapters              List Serial Attached SCSI (SAS) host...
Inventory                               List BIOS Information                   List BIOS information from each node...
Inventory                               List Environment Variables              List environment variables set on ea...
Inventory                               List Memory Information                 List memory information for each node.
Inventory                               List Operating System Information       List information about the operating...
Inventory                               List Plug and Play Devices              List Plug and Play devices on each n...
Inventory                               List Running Processes                  List the running processes on each n...
Inventory                               List Services Information               List information about the services ...
Inventory                               List Software Updates                   List software updates that have been...
Inventory                               List System Drivers                     List the system drivers on each node.
Inventory                               List System Information                 List system information such as comp...
Inventory                               List Unsigned Drivers                   List the unsigned drivers on each node.
Network                                 List Network Binding Order              List the order in which networks are...
Network                                 Validate Cluster Network Configuration  Validate the cluster networks that w...
Network                                 Validate IP Configuration               Validate that IP addresses are uniqu...
Network                                 Validate Multiple Subnet Properties     For clusters using multiple subnets,...
Network                                 Validate Network Communication          Validate that servers can communicat...
Network                                 Validate Windows Firewall Configuration Validate that the Windows Firewall i...
Storage                                 List Disks                              List all disks visible to one or mor...
Storage                                 List Potential Cluster Disks            List disks that will be validated fo...
Storage                                 Validate CSV Network Bindings           Validate that network bindings requi...
Storage                                 Validate CSV Settings                   Validate that settings and configura...
Storage                                 Validate Disk Access Latency            Validate acceptable latency for disk...
Storage                                 Validate Disk Arbitration               Validate that a node that owns a dis...
Storage                                 Validate Disk Failover                  Validate that a disk can fail over s...
Storage                                 Validate File System                    Validate that the file system on dis...
Storage                                 Validate Microsoft MPIO-based disks     Validate that disks that use Microso...
Storage                                 Validate Multiple Arbitration           Validate that in a multiple-node arb...
Storage                                 Validate SCSI device Vital Product D... Validate uniqueness of inquiry data ...
Storage                                 Validate SCSI-3 Persistent Reservation  Validate that storage supports the S...
Storage                                 Validate Simultaneous Failover          Validate that disks can fail over si...
System Configuration                    Validate Active Directory Configuration Validate that all the nodes have the...
System Configuration                    Validate All Drivers Signed             Validate that tested servers contain...
System Configuration                    Validate Cluster Service and Driver ... Validate startup settings used by se...
System Configuration                    Validate Memory Dump Settings           Validate that none of the nodes curr...
System Configuration                    Validate Operating System Edition       Validate that all tested servers are...
System Configuration                    Validate Operating System Installati... Validate that the operating systems ...
System Configuration                    Validate Operating System Version       Validate that the operating systems ...
System Configuration                    Validate Required Services              Validate that services required for ...
System Configuration                    Validate Same Processor Architecture    Validate that all servers run as 64-...
System Configuration                    Validate Service Pack Levels            Validate that all servers with same ...
System Configuration                    Validate Software Update Levels         Validate that all tested servers hav...
System Configuration                    Validate System Drive Variable          Validate that all nodes have the sam...
```

## <a name="remove-cluster-and-resources"></a>删除群集和资源

使用 `Remove-ClusterResource` cmdlet 删除群集上的一个或全部资源。 有关更多示例和用法信息，请参阅[get-clusterresource](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps)参考文档。

> [!NOTE]
> 需要暂时启用凭据安全服务提供程序（CredSSP）身份验证才能删除群集。 有关详细信息，请参阅[enable-wsmancredssp](https://docs.microsoft.com/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7)。

以下示例在群集 Cluster1 上按名称删除群集资源：

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

此示例使用 cmdlet 完全删除群集 Cluster1 `Remove-Cluster` ：

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>后续步骤

了解如何使用 Windows 管理中心来管理群集。 请参阅[使用 Windows 管理中心管理 AZURE STACK HCI 上的群集](cluster.md)。