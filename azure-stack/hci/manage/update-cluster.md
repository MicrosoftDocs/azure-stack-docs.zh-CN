---
title: 更新 Azure Stack HCI 群集
description: 如何使用 Windows Admin Center 和 PowerShell 将操作系统和固件更新应用到 Azure Stack HCI。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 001cf81721423aad770093c0fe5cf92ec6b66af8
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010815"
---
# <a name="update-azure-stack-hci-clusters"></a>更新 Azure Stack HCI 群集

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

更新 Azure Stack HCI 群集时，目标是通过一次仅更新群集中的一个服务器来保持可用性。 许多操作系统更新需要使服务器脱机，例如需要重新启动或更新软件（如网络堆栈）。 建议使用群集感知更新 (CAU)，这项功能使你可以在保持应用程序运行的同时，轻松地在群集中的每个服务器上安装更新。 群集感知更新可在安装更新并重启服务器（如有必要）时自动使服务器进入和退出维护模式。 群集感知更新是 Windows Admin Center 使用的默认更新方法，还可以使用 PowerShell 来启动。

本主题重点介绍操作系统更新和软件更新。 如果需要使服务器脱机以对硬件执行维护，请参阅[使服务器脱机以进行维护](maintain-servers.md)。

## <a name="update-a-cluster-using-windows-admin-center"></a>使用 Windows Admin Center 更新群集

Windows Admin Center 利用简单的用户界面，简化了更新群集和应用操作系统和解决方案更新的过程。 如果你已从 Microsoft 硬件合作伙伴处购买了集成系统，则通过安装合适的合作伙伴更新扩展插件，可直接从 Windows Admin Center 轻松获取最新的驱动程序、固件和其他更新。 如果你的硬件不是作为集成系统购买的，则可能需要按照硬件供应商的建议单独更新固件和驱动程序。

Windows 管理中心将检查群集是否已正确配置为 Cluster-Aware 更新运行，如果需要，将询问你是否希望 Windows 管理中心为你配置 CAU，包括安装 CAU 群集角色和启用所需的防火墙规则。

1. 在连接到群集时，如果一个或多个服务器已准备好安装更新，Windows Admin Center 仪表板会向你发出提醒，并提供立即更新的链接。 或者，你可以从左侧的“工具”菜单中选择“更新” 。
1. 若要在 Windows Admin Center 中使用群集感知更新工具，必须启用凭据安全服务提供程序 (CredSSP) 并提供显式凭据。 当系统询问是否启用 CredSSP 时，单击“是”。
1. 指定用户名和密码，单击“继续”。
1. 将显示所有可用的更新；单击“检查可用更新”刷新列表。
1. 选择要安装的更新，然后单击“应用所有更新”。 这将在群集中的每个服务器上安装更新。 如果需要重新启动，会先将群集角色（如虚拟机）转移到另一个服务器，以防止出现中断。
1. 若要提高安全性，请在安装完更新后立即禁用 CredSSP：
    - 在 Windows Admin Center 中的“所有连接”下，选择群集中的第一个服务器，然后选择“连接” 。
    - 在“概述”页上，选择“禁用 CredSSP”，然后在“禁用 CredSSP”弹出窗口中，选择“是”   。

## <a name="update-a-cluster-using-powershell"></a>使用 PowerShell 更新群集

在使用“群集感知更新”功能更新群集之前，需要先安装“故障转移群集工具”，它是“远程服务器管理工具 (RSAT)”的一部分，并包含群集感知更新软件 。 若要更新现有群集，则可能已经安装了这些工具。

若要测试故障转移群集是否已正确设置为使用“群集感知更新”功能应用软件更新，请运行“Test-CauSetup”PowerShell cmdlet，该 cmdlet 将对故障转移群集和网络环境执行最佳实践分析程序 (BPA) 扫描，并发送相关警告或错误消息：

```PowerShell
Test-CauSetup -ClusterName Cluster1
```

如果需要安装功能、工具或角色，请参阅后续几节。 否则，请直接跳到[使用 PowerShell 检查更新](#check-for-updates-with-powershell)。

### <a name="install-failover-clustering-and-failover-clustering-tools-using-powershell"></a>使用 PowerShell 安装“故障转移群集”和“故障转移群集工具”

若要检查群集或服务器是否已安装“故障转移群集”功能和“故障转移群集工具”，请从管理 PC 发出 **`Get-WindowsFeature`** PowerShell cmdlet（或直接在群集或服务器上运行，并忽略 -ComputerName 参数）：

```PowerShell
Get-WindowsFeature -Name Failover*, RSAT-Clustering* -ComputerName Server1
```

请确保“安装状态”显示为“已安装”，且故障转移群集和 Windows PowerShell 的故障转移群集模块之前都出现了 X：

```
Display Name                                            Name                       Install State
------------                                            ----                       -------------
[X] Failover Clustering                                 Failover-Clustering            Installed
        [X] Failover Clustering Tools                   RSAT-Clustering                Installed
            [X] Failover Cluster Module for Windows ... RSAT-Clustering-Powe...        Installed
            [ ] Failover Cluster Automation Server      RSAT-Clustering-Auto...        Available
            [ ] Failover Cluster Command Interface      RSAT-Clustering-CmdI...        Available
```

如果未安装故障转移群集功能，请使用 **`Install-WindowsFeature`** cmdlet 以及 -IncludeAllSubFeature 和 -IncludeManagementTools 参数，将其安装到群集中的每个服务器上：

```PowerShell
Install-WindowsFeature –Name Failover-Clustering -IncludeAllSubFeature –IncludeManagementTools -ComputerName Server1
```

此命令还将安装 PowerShell 的故障转移群集模块，该模块包括用于管理故障转移群集的 PowerShell cmdlet，以及用于在故障转移群集上安装软件更新的 PowerShell 群集感知更新模块。

如果已安装故障转移群集功能，但尚未安装 Windows PowerShell 的故障转移群集模块，只需使用“Install-WindowsFeature”cmdlet 将其安装到群集中的每个服务器上：

```PowerShell
Install-WindowsFeature –Name RSAT-Clustering-PowerShell -ComputerName Server1
```

### <a name="choose-an-updating-mode"></a>选择更新模式

群集感知更新可以在两种模式下协调完成完整的群集更新操作：  
  
-   自我更新模式：在此模式下，群集感知更新群集角色配置为要更新的故障转移群集上的工作负载，并定义了关联的更新计划。 群集通过使用默认的或自定义的“更新运行配置文件”，在计划的时间进行自我更新。 在更新运行期间，将在当前拥有群集感知更新群集角色的节点上启动“群集感知更新”更新协调器进程，该进程会依次在每个群集节点上执行更新。 若要更新当前群集节点，群集感知更新群集角色将故障转移到另一个群集节点，并且该节点上的一个新更新协调器进程将接管并继续控制更新运行。 在自我更新模式下，群集感知更新功能可以使用完全自动化的端到端更新过程来更新故障转移群集。 管理员也可以在此模式中按需激活更新，或在需要时直接使用远程更新方法。
  
-   远程更新模式：在此模式下，将使用“故障转移群集工具”配置与故障转移群集之间有网络连接但不是故障转移群集成员的远程管理计算机（通常是 Windows 10 PC）。 从该远程管理计算机（称为更新协调器）中，管理员使用默认或自定义更新运行配置文件触发按需更新运行。 远程更新模式对于监视更新运行期间的实时进度以及在服务器核心安装上运行的群集非常有用。  

   > [!NOTE]
   > 从 Windows 10 的 2018 年 10 月版更新开始，RSAT 作为 Windows 10 中的一组“按需功能”提供。 只需转到“设置”>“应用”>“应用和功能”>“可选功能”>“添加功能”>“RSAT:故障转移群集工具”并选择“安装”即可。 若要查看安装进度，请单击“上一步”按钮以查看“管理可选功能”页上的状态。 安装的功能会保留在 Windows 10 升级版中。 若要为 2018 年 10 月更新版本之前的 Windows 10 安装 RSAT，请[下载 RSAT 程序包](https://www.microsoft.com/download/details.aspx?id=45520)。

### <a name="add-cau-cluster-role-to-the-cluster"></a>将 CAU 群集角色添加到群集

自我更新模式需要使用群集感知更新群集角色。 如果使用 Windows Admin Center 执行更新，会自动添加群集角色。

**`Get-CauClusterRole`** cmdlet 显示指定群集上群集感知更新群集角色的配置属性。

```PowerShell
Get-CauClusterRole -ClusterName Cluster1
```

如果尚未在群集上配置角色，你将看到以下错误消息：

```Get-CauClusterRole : The current cluster is not configured with a Cluster-Aware Updating clustered role.```

若要使用 PowerShell 为自我更新模式添加群集感知更新群集角色，请使用 **`Add-CauClusterRole`** cmdlet 并提供合适的 [参数](/powershell/module/clusterawareupdating/add-cauclusterrole#parameters)，如下面的示例所示：

```PowerShell
Add-CauClusterRole -ClusterName Cluster1 -MaxFailedNodes 0 -RequireAllNodesOnline -EnableFirewallRules -VirtualComputerObjectName Cluster1-CAU -Force -CauPluginName Microsoft.WindowsUpdatePlugin -MaxRetriesPerNode 3 -CauPluginArguments @{ 'IncludeRecommendedUpdates' = 'False' } -StartDate "3/2/2020 3:00:00 AM" -DaysOfWeek 4 -WeeksOfMonth @(3) -verbose
```

   > [!NOTE]
   > 上面的命令必须从管理 PC 或域控制器运行。

### <a name="enable-firewall-rules-to-allow-remote-restarts"></a>启用防火墙规则以允许远程重启

需要允许服务器在更新过程中远程重新启动。 如果你正在使用 Windows Admin Center 执行更新，则 Windows 防火墙规则将在每个服务器上自动更新，以允许远程重启。 如果要使用 PowerShell 进行更新，请在 Windows 防火墙中启用“远程关闭”防火墙规则组，或者将 -EnableFirewallRules 参数传递给 cmdlet，如上面的示例所示。

## <a name="check-for-updates-with-powershell"></a>使用 PowerShell 检查更新

你可以使用 **`Invoke-CAUScan`** cmdlet 扫描服务器以查找适用的更新，并获取应用于指定群集中每个服务器的初始更新集的列表：

```PowerShell
Invoke-CauScan -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -Verbose
```

生成列表可能需要几分钟才能完成。 预览列表只包含一组初始更新；它不包括安装初始更新后可能适用的更新。

## <a name="install-updates-with-powershell"></a>使用 PowerShell 安装更新

要扫描服务器以查找适用的更新并在指定群集上执行完全更新运行，请使用 **`Invoke-CAURun`** cmdlet：

```PowerShell
Invoke-CauRun -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -MaxFailedNodes 1 -MaxRetriesPerNode 3 -RequireAllNodesOnline -EnableFirewallRules -Force
```

此命令在名为 Cluster1 的集群上执行扫描和完全更新运行。 此 cmdlet 使用 Microsoft.WindowsUpdatePlugin 插件并要求所有群集节点在运行此 cmdlet 之前处于联机状态。 此外，此 cmdlet 允许每个节点在将节点标记为失败之前重试不超过三次，并且在将整个更新运行标记为失败之前，只允许最多一个节点失败。 它还能使防火墙规则允许服务器远程重启。 由于命令指定了 Force 参数，因此 cmdlet 运行时不显示确认提示。

更新运行过程包括以下内容： 
- 在群集中的每个服务器上扫描并下载适用的更新
- 将当前正在运行的群集角色从每个服务器上转移走
- 在每个服务器上安装更新
- 如果已安装的更新需要重新启动服务器，请执行
- 将群集角色移回原始服务器

更新运行过程还包括确保保持仲裁、查找仅在安装了初始更新集之后才能安装的其他更新，以及保存所采取的操作的报告。

## <a name="check-on-the-status-of-an-updating-run"></a>检查更新运行状态

管理员可以通过运行 **`Get-CauRun`** cmdlet 来获取有关正在进行的更新运行的摘要信息：

```PowerShell
Get-CauRun -ClusterName Cluster1
```

下面是一些示例输出：

```
RunId                   : 834dd11e-584b-41f2-8d22-4c9c0471dbad 
RunStartTime            : 10/13/2019 1:35:39 PM 
CurrentOrchestrator     : NODE1 
NodeStatusNotifications : { 
Node      : NODE1 
Status    : Waiting 
Timestamp : 10/13/2019 1:35:49 PM 
} 
NodeResults             : { 
Node                     : NODE2 
Status                   : Succeeded 
ErrorRecordData          : 
NumberOfSucceededUpdates : 0 
NumberOfFailedUpdates    : 0 
InstallResults           : Microsoft.ClusterAwareUpdating.UpdateInstallResult[] 
}
```

## <a name="perform-a-fast-offline-update-of-all-servers-in-a-cluster"></a>对群集中的所有服务器执行快速的脱机更新

此方法使你可以立即关闭群集中的所有服务器，并同时更新所有服务器。 这样可以节省更新过程中的时间，但是要权衡的是托管资源的停机时间。

如果有重要的安全更新程序需要快速应用，或者需要确保更新在维护时段内完成，则可以使用此方法。 此过程会关闭 Azure Stack HCI 群集，更新服务器，然后再次将其重启。

1. 规划维护时段。
2. 使虚拟磁盘脱机。
3. 停止群集以使存储池脱机。 运行 **Stop-Cluster** cmdlet 或使用 Windows Admin Center 停止群集。
4. 在每个服务器上的 Services.msc 中将群集服务设置为“禁用”。 这会阻止群集服务在更新时启动。
5. 将 Windows Server 累积更新和任何所需的服务堆栈更新应用于所有服务器。 可以同时更新所有服务器，因为群集已关闭，无需等待。
6. 重启服务器，并确保一切正常。
7. 在每个服务器上将群集服务重新设置为“自动”。
8. 启动群集。 运行 Start-Cluster cmdlet 或使用 Windows Admin Center。

   稍等几分钟。  请确保存储池处于正常状态。

9. 使虚拟磁盘恢复联机状态。
10. 通过运行 Get-Volume 和 Get-VirtualDisk cmdlet 来监视虚拟磁盘的状态。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [群集感知更新 (CAU)](/windows-server/failover-clustering/cluster-aware-updating)
- [在存储空间直通中更新驱动器固件](/windows-server/storage/update-firmware)
- [验证 Azure Stack HCI 群集](../deploy/validate.md)