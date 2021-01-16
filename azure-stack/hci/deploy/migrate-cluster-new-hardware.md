---
title: 迁移到新硬件上的 Azure Stack HCI
description: 了解如何迁移到新硬件上的 Azure Stack HCI
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7a0c0ca7a99b3554b74cc80911acbee92793aa52
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254817"
---
# <a name="migrate-to-azure-stack-hci-on-new-hardware"></a>迁移到新硬件上的 Azure Stack HCI

> 适用于 Azure Stack HCI，版本 20H2;Windows Server 2019，Windows Server 2016，Windows Server 2012 R2，Windows Server 2008 R2

本主题介绍如何使用 Windows PowerShell 和 Robocopy 将 Windows Server 2012 R2、Windows Server 2016 或 Windows Server 2019 上的虚拟机 (VM) 文件迁移到新的 Azure Stack HCI 服务器硬件。 Robocopy 是一种用于将文件从一台服务器复制到另一台服务器的强大方法。 如果断开连接并继续从其最后已知状态运行，则会恢复。 Robocopy 还支持通过服务器消息块 (SMB) 进行多线程文件复制。 有关详细信息，请参阅 [Robocopy](/windows-server/administration/windows-commands/robocopy)。

> [!NOTE]
> 不支持从 Windows Server 到 Azure Stack HCI 的 hyper-v 实时迁移和 Hyper-v 副本。

如果要迁移的 Windows 2012 R2 或更早版本上有 Vm，请参阅 [迁移旧 vm](#migrating-older-vms)。

若要使用相同的硬件迁移到 Azure Stack HCI，请参阅 [在同一硬件上迁移到 AZURE STACK hci](migrate-cluster-same-hardware.md)。

下图显示了一个 Windows Server 源群集和一个 Azure Stack 的 HCI 目标群集作为示例。 也可以在独立服务器上迁移 Vm。

:::image type="content" source="media/migrate/migrate-cluster.png" alt-text="将群集迁移到 Azure Stack HCI" lightbox="media/migrate/migrate-cluster.png":::

根据预期停机时间，在群集之间使用具有双 40 GB RDMA East-West 网络的单个 NIC，并为 32 multithreads 配置 Robocopy，可实现每小时 1.9 TB 的传输速度。

> [!NOTE]
> 本文未介绍迁移延伸群集的 Vm 的情况。

## <a name="before-you-begin"></a>准备阶段

在开始迁移之前，需要考虑几个要求和事项：

- 必须以管理员身份运行所有 Windows PowerShell 命令。

- 对于源和目标群集，你必须拥有具有管理员权限的域凭据，对源和目标组织单位具有完全权限， (OU) 包含两个群集。

- 这两个群集必须位于同一个 Active Directory 林和域中，以便在群集之间进行 Kerberos 身份验证，以便迁移 Vm。

- 这两个群集必须位于 Active Directory OU 中，此 ou 组策略对象 (GPO) 阻止继承在此 OU 上设置。 这可确保没有域级 Gpo 和安全策略会影响迁移。

- 这两个群集都必须连接到同一时间源，以支持群集之间一致的 Kerberos 身份验证。

- 记下源群集上的 Vm 所使用的 Hyper-v 虚拟交换机名称。 导入 Vm 之前，必须为 Azure Stack HCI 目标群集 "虚拟机网络" 使用相同的虚拟交换机名称。

- 删除源 Vm 的所有 ISO 映像文件。 这是使用 **硬件部分** 的 **VM 属性** 中的 hyper-v 管理器完成的。 对于任何虚拟 CD/DVD 驱动器，请选择 " **删除** "。

- 关闭源群集上的所有 Vm。 这是确保在整个迁移过程中保持版本控制和状态所必需的。

- 检查 Azure Stack HCI 是否支持你的 Vm 版本，以根据需要导入和更新 Vm。 请参阅 [VM 版本支持和更新](#vm-version-support-and-update) 部分，了解如何执行此操作。

- 备份源群集上的所有 Vm。 完成所有应用程序和数据的崩溃一致性备份，以及所有数据库的应用程序一致性备份。 若要备份到 Azure，请参阅 [使用 Azure 备份](../manage/use-azure-backup.md)。

- 为源群集 Vm 和域控制器创建检查点，以防必须回滚到之前的状态。 这不适用于物理服务器。

- 请确保源和目标群集存储网络之间的最大巨型帧大小相同，特别是 RDMA 网络适配器及其各自的交换机网络端口，以便提供最有效的端到端传输数据包大小。

- 请记下源群集上的 Hyper-v 虚拟交换机名称。 你需要在目标群集上重复使用它。

- Azure Stack HCI 硬件的容量和配置应至少等于源硬件。

- 最大程度地减少源群集和目标群集之间的网络跃点数或物理距离，以加快文件传输速度。

## <a name="vm-version-support-and-update"></a>VM 版本支持和更新

下表列出了 Windows Server 操作系统版本及其 VM 版本。

无论 VM 可能运行于哪个操作系统版本，直接迁移到 Azure Stack HCI 支持的最低 VM 版本都是版本5.0。 这表示 Windows Server 2012 R2 上 Vm 的默认版本。 因此，在版本2.0、3.0 或4.0 上运行的任何 Vm 都必须在迁移之前更新为版本5.0。

|OS 版本|VM 版本|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4.0|
|Windows Server 2012 R2|5.0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

对于 Windows Server 2012 R2、Windows Server 2016 和 Windows Server 2019 上的 Vm，请先将所有 Vm 更新为源硬件上支持的最新 VM 版本，然后再运行 Robocopy 迁移脚本。 这可确保成功导入 VM 的所有 Vm 至少5.0 版。

对于 Windows Server 2008 SP1、Windows Server 2008 R2-SP1 和 Windows 2012 上的 Vm，VM 版本将低于版本5.0。 这些 Vm 还使用 .xml 文件进行配置，而不是使用 vcmx 文件。 因此，不支持将 VM 直接导入 Azure Stack HCI。 在这些情况下，可以使用两个选项，如 [迁移较旧的 vm](#migrating-older-vms)中所述。

### <a name="updating-the-vm-version"></a>正在更新 VM 版本

以下命令适用于 Windows Server 2012 R2 及更高版本。 使用以下命令显示单个服务器上的所有 VM 版本：

```powershell
Get-VM * | Format-Table Name,Version
```

显示群集上所有服务器中的所有 VM 版本：

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

将所有 Vm 更新到所有服务器上支持的最新版本：

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="rdma-recommendations"></a>RDMA 建议

如果 (RDMA) 使用远程直接内存访问，则 Robocopy 可以利用它在群集之间复制 Vm。 下面是有关使用 RDMA 的一些建议：

- 将两个群集连接到机架 (ToR) 交换机，以便使用源和目标群集之间最快的网络路径。 对于存储网络路径，这通常支持 10GbE/25GbE 或更高的速度，并利用 RDMA。

- 如果源群集和目标群集之间的 RDMA 适配器或标准不同 (ROCE vs iWARP) ，则 Robocopy 将通过最快的可用网络来利用 TCP/IP 上的 SMB。 这通常是 East-West 网络的双 10Gbe/25Gbe 或更快的速度，提供在群集之间复制 VM VHDX 文件的最佳方法。

- 若要确保 Robocopy 可以在群集 (东-西网络) 间利用 RDMA，请配置 RDMA 存储网络，使其在源群集和目标群集之间路由。

## <a name="create-the-new-cluster"></a>创建新群集

在创建 Azure Stack HCI 群集之前，需要在将位于群集中的每台新服务器上安装 Azure Stack HCI OS。 有关如何执行此操作的信息，请参阅 [部署 AZURE STACK HCI 操作系统](operating-system.md)。

使用 Windows 管理中心或 Windows PowerShell 创建新群集。 有关如何执行此操作的详细信息，请参阅 [使用 Windows 管理中心创建 AZURE STACK hci 群集](create-cluster.md) 和 [使用 windows POWERSHELL 创建 Azure Stack hci 群集](create-cluster-powershell.md)。

> [!IMPORTANT]
> Hyper-v 虚拟交换机在 `VMSwitch` 群集之间 () 名称必须相同。 请确保在目标群集上创建的虚拟交换机名称与在所有服务器上的源群集中使用的名称相匹配。 请在导入 Vm 之前验证开关名称是否相同。

> [!NOTE]
> 必须先将 Azure Stack HCI 群集注册到 Azure，然后才能在该群集上创建新的 Vm。 有关详细信息，请参阅 [向 Azure 注册](register-with-azure.md)。

## <a name="run-the-migration-script"></a>运行迁移脚本

下面的 PowerShell 脚本 `Robocopy_Remote_Server_.ps1` 使用 Robocopy 将 VM 文件及其从属目录和元数据从源复制到目标群集。 此脚本已从 TechNet 上的原始脚本修改 [，使用 PowerShell 和 robocopy 从： Robocopy 文件到远程服务器](https://gallery.technet.microsoft.com/scriptcenter/Robocoy-Files-to-Remote-bdfc5154)。

此脚本将所有 VM VHD、VHDX 和 .VMCX 文件复制到目标群集， (CSV) 指定群集共享卷。 一次迁移一个 CSV。

迁移脚本在每个源服务器上本地运行，以利用 RDMA 和快速网络传输的好处。 要执行此操作：

1. 请确保将每个目标群集节点设置为目标 CSV 的 CSV 所有者。

1. 若要确定要复制的所有 VM VHD 和 VHDX 文件的位置，请使用以下 cmdlet。 查看 `C:\vmpaths.txt` 文件以确定 Robocopy 的最顶层源文件路径，步骤4：

    ```powershell  
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

    > [!NOTE]
    > 如果 VHD 和 VHDX 文件位于同一卷上的不同路径，则需要为每个不同的路径运行迁移脚本，以将它们全部复制。

1. 更改以下三个变量，使源群集 VM 路径与目标群集 VM 路径匹配：

    - `$Dest_Server = "Node01"`
    - `$source  = "C:\Clusterstorage\Volume01"`
    - `$dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"`

1. 在每个 Windows Server 源服务器上运行以下脚本：

```powershell
<#
#===========================================================================  
# Script: Robocopy_Remote_Server_.ps1
#===========================================================================  
.DESCRIPTION:
Change the following variables to match your source cluster VM path with the destination cluster VM path. Then run this script on each source Cluster Node CSV owner and make sure the destination cluster node is set to the CSV owner for the destination CSV.

        Change $Dest_Server = "Node01"
        Change $source  = "C:\Clusterstorage\Volume01"
        Change $dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"
#>

$Space       = Write-host ""
$Dest_Server = "Node01"
$source      = "C:\Clusterstorage\Volume01"
$dest        = "\\$Dest_Server\C$\Clusterstorage\Volume01"
$Logfile     = "c:\temp\Robocopy1-$date.txt"
$date        = Get-Date -UFormat "%Y%m%d"
$cmdArgs     = @("$source","$dest",$what,$options)  
$what        = @("/COPYALL")
$options     = @("/E","/MT:32","/R:0","/W:1","/NFL","/NDL","/LOG:$logfile","/xf")
 
## Get Start Time
$startDTM = (Get-Date)
 
$Dest_Server     = "Node01"
$TARGETDIR   = \\$Dest_Server\C$\Clusterstorage\Volume01
$Space
Clear
## Provide Information
Write-host ".....Copying Virtual Machines FROM $Source to $TARGETDIR ....................." -fore Green -back black
Write-Host "........................................." -Fore Green

## Kick off the copy with options defined  
robocopy @cmdArgs
 
## Get End Time
$endDTM = (Get-Date)
 
## Echo Time elapsed
$Time = "Elapsed Time: = $(($endDTM-$startDTM).totalminutes) minutes"  
## Provide time it took
Write-host ""
Write-host " Copy Virtual Machines to $Dest_Server has been completed......" -fore Green -back black
Write-host " Copy Virtual Machines to $Dest_Server took $Time        ......" -fore Cyan
```

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

    > [!NOTE]
    > Windows Server 2012 R2 和更低版本的 Vm 使用 XML 文件而不是 VCMX 文件。 有关详细信息，请参阅 **迁移较旧的 vm** 部分。

1. 为每个服务器节点运行以下 cmdlet，以导入、注册每个 CSV 所有者节点并使这些 Vm 高度可用。 这可确保虚拟机的分配实现最佳处理器和内存分配：

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

1. 将 Vm 更新到最新版本，以便 Azure Stack HCI 充分利用所有改进：

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. 脚本完成后，检查 Robocopy 日志文件中是否列出了列出的任何错误，并验证是否已成功复制所有 Vm。

## <a name="migrating-older-vms"></a>迁移较旧的 Vm

如果你有 Windows Server 2008 SP1、Windows Server 2008 R2-SP1、Windows Server 2012 或 Windows Server 2012 R2 Vm，则本部分适用于你。 可以使用两个选项来处理这些 Vm：

- 首先将这些 Vm 迁移到 Windows Server 2012 R2、Windows Server 2016 或 Windows Server 2019，更新 VM 版本，然后开始迁移过程。

- 使用 Robocopy 将所有 VM Vhd 复制到 Azure Stack HCI。 然后创建新的 Vm，并将复制的 Vhd 附加到 Azure Stack HCI 中的 Vm。 这会绕过这些较旧 Vm 的 VM 版本限制。

Windows Server 2012 R2 和更早版本的 Hyper-v 主机使用 XML 文件格式进行 VM 配置，这不同于用于 Windows Server 2016 和更高版本 Hyper-v 主机的 VCMX 文件格式。 这需要不同的 Robocopy 命令以将这些 Vm 复制到 Azure Stack HCI。

### <a name="option-1-staged-migration"></a>选项1：分阶段迁移

这是一个两阶段的迁移，适用于托管在 Windows Server 2008 SP1、Windows Server 2008 R2-SP 和 Windows Server 2012 上的 Vm。 下面是你使用的过程：

1. 发现要复制的所有 VM VHD 和 VHDX 文件的位置，然后查看该 `vmpaths.txt` 文件以确定 Robocopy 要从其开始的最顶层源文件路径。 使用以下 cmdlet：

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

1. 使用以下示例 Robocopy 命令，首先使用步骤1中确定的最顶层路径将 Vm 复制到 Windows Server 2012 R2：

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. 验证 `VMSwitch` Windows server 2012 r2 群集上使用的虚拟交换机 () 名称是否与 windows 2008 r2 或 Windows server 2008 R2-SP1 源上使用的交换机名称相同。 若要显示群集中所有服务器上使用的交换机名称，请使用以下命令：

     ```powershell
    Get-VMSwitch -CimSession $Servers | Select-Object Name
    ```

    根据需要重命名 Windows Server 20212 R2 上的交换机名称。 若要在群集中的所有服务器之间重命名交换机名称，请使用以下命令：

    ```powershell
    Invoke-Command -ComputerName $Servers -ScriptBlock {rename-VMSwitch -Name $using:vSwitcholdName -NewName $using:vSwitchnewname}
    ```

1. 将 Vm 复制并导入到 Windows Server 2012 R2：

     ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. 在 Windows Server 2012 R2 上，为所有 Vm 将 VM 版本更新为5.0：

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. [运行迁移脚本](#run-the-migration-script) ，将 vm 复制到 Azure Stack HCI。

1. 按照 [导入 vm](#import-the-vms)中的过程操作，将步骤3和步骤4替换为以下内容，以处理 XML 文件并将 vm 导入 Azure Stack HCI：

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. 完成 [导入 vm](#import-the-vms)中的其余步骤。

### <a name="option-2-direct-vhd-copy"></a>选项2：直接 VHD 副本

此方法使用 Robocopy 将 Windows 2008 SP1、Windows 2008 R2-SP1 和 Windows 2012 上承载的 VM Vhd 复制到 Azure Stack HCI。 这会绕过这些较旧 Vm 支持的最低 VM 版本限制。 对于托管在 Windows Server 2008 SP1 和 Windows Server 2008 R2-SP1 中的 Vm，建议选择此选项。

托管在 Windows 2008 SP1 和 Windows 2008 R2-SP1 上的 Vm 仅支持第1代 Vhd 的第1代 Vm。 因此，需要在 Azure Stack HCI 上创建相应的第1代 Vm，以便可以将复制的 Vhd 附加到新 Vm。 请注意，无法将这些 Vhd 升级到第2代 Vhd。

> [!NOTE]
> Windows Server 2012 支持第1代和第2代 Vm。

下面是你使用的过程：

1. 使用示例 Robocopy 将 Vm Vhd 直接复制到 Azure Stack HCI：

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. 创建新的第1代 Vm。 有关如何执行此操作的详细信息，请参阅 [管理 vm](../manage/vm.md)。

1. 将复制的 VHD 文件附加到新的 Vm。 有关详细信息，请参阅 [管理虚拟硬盘 (VHD) ](/windows-server/storage/disk-management/manage-virtual-hard-disks)

作为一个仅供参考，以下 Windows Server 来宾操作系统支持第2代 Vm：

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows 10
- 64位版本的 Windows 8.1 (64) 
- 64位版本的 Windows 8 (64) 
- Linux (请参阅 [支持的 linux 和 FreeBSD vm](/windows-server/virtualization/hyper-v/Supported-Linux-and-FreeBSD-virtual-machines-for-Hyper-V-on-Windows)) 

## <a name="next-steps"></a>后续步骤

- 迁移后验证群集。 请参阅 [验证 AZURE STACK HCI 群集](validate.md)。

- 若要使用相同硬件迁移到 Azure Stack HCI，请参阅 [在同一硬件上迁移到 AZURE STACK hci](migrate-cluster-same-hardware.md)。