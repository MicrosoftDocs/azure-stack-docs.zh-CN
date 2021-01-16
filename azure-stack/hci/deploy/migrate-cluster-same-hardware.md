---
title: 迁移到同一硬件上的 Azure Stack HCI
description: 了解如何在同一硬件上迁移群集以 Azure Stack HCI
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: d1e033ed2b8bbae2968be8125c7d0dccb3e9f3cf
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254630"
---
# <a name="migrate-to-azure-stack-hci-on-same-hardware"></a>迁移到同一硬件上的 Azure Stack HCI

> 适用于 Azure Stack HCI，版本 20H2;Windows Server 2019，Windows Server 2016，Windows Server 2012 R2，Windows Server 2008 R2

本主题介绍如何使用现有服务器硬件将 Windows Server 2016 或 Windows Server 2019 群集迁移到 Azure Stack HCI。 此过程将安装新的 Azure Stack HCI 操作系统，并保留现有群集设置和存储，并导入 Vm。

下图描述了如何使用相同的服务器硬件来就地迁移 Windows Server 群集。 关闭群集后，会安装 Azure Stack HCI，重新附加存储，并将 Vm 导入，并使其高度可用 (HA) 。

:::image type="content" source="media/migrate/migrate-cluster-same-hardware.png" alt-text="将群集迁移到同一硬件上的 Azure Stack HCI" lightbox="media/migrate/migrate-cluster-same-hardware.png":::

若要将 Vm 迁移到新的 Azure Stack HCI 硬件，请参阅 [在新硬件上迁移到 AZURE STACK HCI](migrate-cluster-new-hardware.md)。

> [!NOTE]
> 本文未介绍迁移延伸的群集。

## <a name="before-you-begin"></a>准备阶段

在开始迁移之前，需要考虑几个要求和事项：

- 必须以管理员身份运行所有 Windows PowerShell 命令。

- 对于 Azure Stack HCI，你必须拥有具有管理员权限的域凭据。

- 备份源群集上的所有 Vm。 完成所有应用程序和数据的崩溃一致性备份，以及所有数据库的应用程序一致性备份。  若要备份到 Azure，请参阅 [使用 Azure 备份](../manage/use-azure-backup.md)。

- 收集所有群集节点和群集命名、网络配置、群集共享卷 (CSV) 复原和容量以及仲裁见证的清单和配置。

- 关闭群集 Vm、脱机 Csv、脱机存储池和群集服务。
- 禁用群集名称对象 (CNO)  (以后再次使用该对象) 和：
    - 检查 CNO 是否具有 (OU) 的 "创建对象" 权限。
    - 检查是否已在 OU 上设置 "阻止继承" 策略
    - 为此 OU 上的 Azure Stack HCI 设置所需的策略

## <a name="vm-version-support-and-update"></a>VM 版本支持和更新

下表列出了支持的 Windows Server 操作系统版本及其在同一硬件上进行就地迁移的 VM 版本。

无论 VM 可能运行于哪个操作系统版本，迁移到 Azure Stack HCI 所支持的最低 VM 版本都是版本5.0。 因此，在迁移之前，必须将 Windows Server 2016 或 Windows Server 2019 群集上版本2.0、3.0 或4.0 上运行的任何 Vm 更新为版本5.0。

|OS 版本|VM 版本|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4.0|
|Windows Server 2012 R2|5.0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

对于 Windows Server 2008 SP1、Windows Server 2008 R2-SP1 和 Windows 2012 群集上的 Vm，不支持直接迁移到 Azure Stack HCI。 在这些情况下，您有两种选择：

- 首先将这些 Vm 迁移到 Windows Server 2012 R2、Windows Server 2016 或 Windows Server 2019，更新 VM 版本，然后开始迁移过程。

- 使用 Robocopy 将所有 VM Vhd 复制到 Azure Stack HCI。 然后创建新的 Vm，并将复制的 Vhd 附加到 Azure Stack HCI 中各自的 Vm。 这会绕过这些较旧 Vm 的 VM 版本限制。

## <a name="updating-the-vm-version"></a>正在更新 VM 版本

使用以下命令显示单个服务器上的所有 VM 版本：

```powershell
Get-VM * | Format-Table Name,Version
```

若要在 Windows Server 群集上的所有节点中显示所有 VM 版本：

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

若要将所有 Vm 更新到所有 Windows Server 节点上的最新版本：

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="updating-the-servers-and-cluster"></a>更新服务器和群集

迁移包括在 Windows Server 部署上运行 Azure Stack HCI 设置，以便在 Vm 和存储中保持完整的干净操作系统安装。 这将用 Azure Stack HCI 替换当前操作系统。 有关详细信息，请参阅 [部署 AZURE STACK HCI 操作系统](operating-system.md)。 然后，创建新的 Azure Stack HCI 群集，重新连接存储并导入 Vm。

1. 关闭现有群集 Vm、脱机 Csv、脱机存储池和群集服务。

1. 中转到下载 Azure Stack HCI bits 的位置，然后在每个 Windows Server 节点上运行 Azure Stack HCI 设置。

1. 在安装过程中，选择 **"自定义：仅 (高级) 安装 AZURE STACK HCI 的较新版本**。 为每个服务器重复此步骤。

1. 创建新的 Azure Stack HCI 群集。 你可以使用 Windows 管理中心或 Windows PowerShell 来执行此操作，如下所述。  

> [!IMPORTANT]
> Hyper-v 虚拟交换机 (`VMSwitch`) 名称必须与群集配置清单中捕获的名称相同。 在导入 Vm 之前，请确保 Azure Stack HCI 群集上使用的虚拟交换机名称与原始源虚拟交换机名称匹配。

> [!NOTE]
> 必须先将 Azure Stack HCI 群集注册到 Azure，然后才能在该群集上创建新的 Vm。 有关详细信息，请参阅 [向 Azure 注册](register-with-azure.md)。

### <a name="using-windows-admin-center"></a>使用 Windows Admin Center

如果使用 Windows 管理中心创建 Azure Stack HCI 群集，则创建群集向导会自动在每个服务器节点上安装所有必需的角色和功能。

有关如何创建群集的详细信息，请参阅 [使用 Windows 管理中心创建 AZURE STACK HCI 群集](create-cluster.md)。

> [!IMPORTANT]
> 跳过 "创建群集" 向导中的步骤 **4.1 清理驱动器** 。 否则，会删除现有的 Vm 和存储。

1. 启动创建群集向导。 转到 **步骤4：存储**：

1. 跳过步骤 **4.1 清洗驱动器**。 请不要进行此选择。

1. 跳出向导。

1. 打开 PowerShell，运行以下 cmdlet 以创建新 `Storagesubsystem Object` ID、重新发现所有存储机箱并分配 SES 驱动器编号：

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

    如果从 Windows Server 2016 迁移，此方法还会创建一个新的 `ClusterperformanceHistory` ReFS 卷，并将其分配给 SDDC 群集资源组。

    如果从 Windows Server 2019 迁移，此方法还会添加现有 `ClusterperformanceHistory` ReFS 卷，并将其分配给 SDDC 群集资源组。

1. 返回到向导。 在步骤 **4.2 "验证驱动器**" 中，验证是否列出了所有驱动器，但不显示警告或错误。

1. 完成向导。

### <a name="using-windows-powershell"></a>使用 Windows PowerShell

如果使用 PowerShell 创建 Azure Stack HCI 群集，则必须使用以下 cmdlet 在每个 Azure Stack HCI 群集节点上安装以下角色和功能：

```powershell
Install-WindowsFeature -Name Hyper-V, Failover-Clustering, FS-Data-Deduplication, Bitlocker, Data-Center-Bridging, RSAT-AD-PowerShell -IncludeAllSubFeature -IncludeManagementTools -Verbose
```

有关如何使用 PowerShell 创建群集的详细信息，请参阅 [使用 Windows PowerShell 创建 AZURE STACK HCI 群集](create-cluster-powershell.md)。

> [!NOTE]
> 为以前禁用的群集名称对象重复使用相同的名称。

1. 运行以下 cmdlet 以创建群集：

    ```powershell
    New-cluster –name "clustername" –node Server01,Server02 –staticaddress xx.xx.xx.xx –nostorage
    ```

1. 运行以下 cmdlet 以创建新 `Storagesubsystem Object` ID、重新发现所有存储机箱并分配 SES 驱动器号码：

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

1. 如果从 Windows Server 2016 迁移，此方法还会创建一个新的 `ClusterperformanceHistory` ReFS 卷，并将其分配给 SDDC 群集资源组。

    > [!NOTE]
    > 如果存储池显示少数磁盘错误 (可在群集管理器中查看) ，请重新运行 `Enable-ClusterS2D -verbose` cmdlet。

1. 使用群集管理器，启用除卷之外的每个 CSV `ClusterperformanceHistory` ，这是 refs 卷 (确保这不是 REFS CSV) 。

1. 如果是从 Windows Server 2019 迁移，请重新运行 `Enable-ClusterS2D -verbose` cmdlet。 这会将 `ClusterperformanceHistory` ReFS 卷与 SDDC 群集资源组关联。

1. 通过运行以下内容来确定当前存储池名称和版本：

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. 现在，确定新的存储池名称和版本：

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. 创建仲裁见证。 有关如何操作的详细信息，请参阅 [设置分类见证](./witness.md)。

1. 使用以下各项验证存储修复作业是否已完成：

    ```powershell
    Get-StorageJob
    ```

    > [!NOTE]
    >这可能需要相当长的时间，具体取决于升级过程中运行的 Vm 的数量。

1. 验证所有磁盘是否正常：

    ```powershell
    Get-VirtualDisk
    ```

1. 确定用于显示和的群集节点版本 `ClusterFunctionalLevel` `ClusterUpgradeVersion` 。 运行以下内容以获取：

    ```powershell
    Get-ClusterNodeSupportedVersion
    ```

    > [!NOTE]
    > `ClusterFunctionalLevel` 将自动设置为 `10` ，并且不需要更新，因为操作系统和群集创建新。

1. 更新存储池，如下所示：

    ```powershell
    Get-StoragePool | Update-StoragePool
    ```

## <a name="refs-volumes"></a>ReFS 卷

如果从 Windows Server 2016 迁移，则支持复原文件系统 (ReFS) 卷，但此类卷不会从 Azure Stack HCI 的以下性能增强功能中受益：

- 镜像加速奇偶校验
- 映射绕过日志

这些增强功能需要使用 cmdlet 创建新的 ReFS 卷 `New-Volume` 。

> [!NOTE]
> 对于 Windows Server 2016 镜像加速奇偶校验卷，ReFS 压缩功能不可用，因此重新连接这些卷是正常的，但与在 Azure Stack HCI 群集上创建新的映射卷相比，其性能较低。

## <a name="import-the-vms"></a>导入 Vm

最佳做法是为每个群集节点至少创建一个群集共享卷 (CSV) ，以便为每个 CSV 所有者启用虚拟机平衡，从而提高 VM 工作负荷的复原能力、性能和规模。 默认情况下，此平衡每五分钟自动发生一次，并需要在源群集节点和目标群集节点之间使用 Robocopy 来确保源和目标 CSV 所有者匹配以提供最理想的传输路径和速度。

在 Azure Stack HCI 群集上执行以下步骤以导入 Vm，使其高度可用，并启动这些 Vm：

1. 运行以下 cmdlet 以显示所有 CSV 所有者节点：

    ```powershell
    Get-ClusterSharedVolume
    ```

1. 对于每个服务器节点，请参阅 `C:\Clusterstorage\Volume` 并设置所有 vm 的路径（例如） `C:\Clusterstorage\volume01` 。

1. 在每个 CSV 所有者节点上运行以下 cmdlet，以在导入 VM 之前显示每个卷的所有 VM .VMCX 文件的路径。 修改路径，使其与你的环境匹配：

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse
    ```

1. 为每个服务器节点运行以下 cmdlet，以导入和注册所有 Vm，并使其在每个 CSV 所有者节点上具有高可用性。 这可确保虚拟机的分配实现最佳处理器和内存分配：

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole
    ```

1. 在每个节点上启动每个目标 VM：

    ```powershell
    Start-VM -Name
    ```

1. 登录并验证所有 Vm 是否正在运行，以及你的所有应用和数据是否都存在：

    ```powershell
    Get-VM -ComputerName Server01 | Where-Object {$_.State -eq 'Running'}
    ```

1. 最后，将 Vm 更新到最新的 Azure Stack HCI 版本，以利用所有改进：

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

## <a name="next-steps"></a>后续步骤

- 迁移后验证群集。 请参阅 [验证 AZURE STACK HCI 群集](validate.md)。
- 若要将 Windows Server Vm 迁移到新的 Azure Stack HCI 硬件，请参阅 [迁移到新硬件上 AZURE STACK HCI](migrate-cluster-new-hardware.md)。