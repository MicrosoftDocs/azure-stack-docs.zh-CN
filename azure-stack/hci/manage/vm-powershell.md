---
title: 使用 Windows PowerShell 管理 Vm-Azure Stack HCI
description: 如何使用 Windows PowerShell 管理 Azure Stack HCI 上的虚拟机
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 8d5f654ed5af1fe66c9a28bbfd6bebb09672fa88
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573507"
---
# <a name="manage-vms-on-azure-stack-hci-using-windows-powershell"></a>使用 Windows PowerShell 管理 Azure Stack HCI 上的 VM

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

可以使用 Windows PowerShell 在 Azure Stack HCI 上创建和管理虚拟机 (VM)。

通常，你可以从远程计算机（而不是群集中的主机服务器）管理 VM。 此远程计算机称为管理计算机。

> [!NOTE]
> 从某一管理计算机运行 PowerShell 命令时，请将 -ComputerName 参数与你管理的主机服务器的名称配合使用。 允许使用 NetBIOS 名称、IP 地址和完全限定的域名。

有关使用 PowerShell 管理 VM 的完整参考文档，请参阅 [Hyper-V 参考](/powershell/module/hyper-v/?view=win10-ps)。

## <a name="create-a-vm"></a>创建 VM  

`New-VM` cmdlet 用于创建新的 VM。 有关详细用法，请查看 [New-VM](/powershell/module/hyper-v/new-vm?view=win10-ps) 参考文档。

下面是使用现有虚拟硬盘创建新的 VM 时可以指定的设置，其中：

- **-Name** 是为你要创建的虚拟机提供的名称。

- **-MemoryStartupBytes** 是启动时可供虚拟机使用的内存量。

- **-BootDevice** 是虚拟机在启动时引导到的设备。
 通常情况下，它是虚拟硬盘 (VHD)、.iso 文件（对于基于 DVD 的引导）或网络适配器 (NetworkAdapter)（对于网络引导）。

- **-VHDPath** 是要使用的虚拟机磁盘的路径。

- **-Path** 是存储虚拟机配置文件的路径。

- **-Generation** 是虚拟机代系。 对于 VHD，请使用第 1 代；对于 VHDX，请使用第 2 代。

- **-Switch** 是你希望虚拟机用来连接其他虚拟机或网络的虚拟交换机的名称。 可以使用 [Get-VMSwitch](/powershell/module/hyper-v/get-vmswitch?view=win10-ps) 获取虚拟交换机的名称。 例如：  

用于创建名为 VM1 的 VM 的完整命令如下所示：

 ```powershell
 New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes <Memory> -BootDevice <BootDevice> -VHDPath <VHDPath> -Path <Path> -Generation <Generation> -Switch <SwitchName>
 ```

下一个示例将创建内存为 4GB 的第 2 代虚拟机。 它从当前目录中的 VMs\Win10.vhdx 文件夹引导，并使用名为 ExternalSwitch 的虚拟交换机。 虚拟机配置文件存储在 VMData 文件夹中。  

``` powershell
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -VHDPath .\VMs\Win10.vhdx -Path .\VMData -Generation 2 -Switch ExternalSwitch
```

以下参数用于指定虚拟硬盘。

若要使用新的虚拟硬盘创建虚拟机，请将以上示例中的 **-VHDPath** 参数替换为 **-NewVHDPath** 并添加 **-NewVHDSizeBytes** 参数，如下所示：  

``` powershell  
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -NewVHDPath .\VMs\Win10.vhdx -Path .\VMData -NewVHDSizeBytes 20GB -Generation 2 -Switch ExternalSwitch  
 ```  

若要使用新的虚拟磁盘创建引导到操作系统映像的虚拟机，请参阅[为 Windows 10 上的 Hyper-V 创建虚拟机演练](/virtualization/hyper-v-on-windows/quick-start/create-virtual-machine)中的 PowerShell 示例。  

## <a name="get-a-list-of-vms"></a>获取 VM 列表

以下示例返回 Server1 上所有 VM 的列表。

```powershell
Get-VM -ComputerName Server1
```

以下示例使用 `Where-Object` 命令添加了一个筛选器，返回服务器上所有正在运行的 VM 的列表。 有关详细信息，请参阅[使用 Where-Object](/previous-versions/windows/it-pro/windows-powershell-1.0/ee177028(v=technet.10)) 文档。

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Running"
```

下一个示例将返回服务器上所有已关闭 VM 的列表。

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Off"
```

## <a name="start-and-stop-a-vm"></a>启动和停止 VM

使用 `Start-VM` 和 `Stop-VM` 命令启动或停止 VM。 有关详细信息，请查看 [Start-VM](/powershell/module/hyper-v/start-vm?view=win10-ps) 和 [Stop-VM](/powershell/module/hyper-v/stop-vm?view=win10-ps) 参考文档。

以下示例展示了如何启动名为 VM1 的 VM：

```powershell
Start-VM -Name VM1 -ComputerName Server1
```

以下示例展示了如何关闭名为 TestVM 的 VM：

```powershell
Stop-VM -Name VM1 -ComputerName Server1
```

## <a name="move-a-vm"></a>移动 VM

`Move-VM` cmdlet 可将 VM 移动到其他服务器。 有关详细信息，请查看 [Move-VM](/powershell/module/hyper-v/move-vm?view=win10-ps) 参考文档。

 下面的示例展示了当 VM 存储在 Server1 的 SMB 共享上时，如何将 VM 移动到 Server2：

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2
 ```

下面的示例展示了如何将 VM 从 Server1 移动到 Server2，并将与该 VM 关联的所有文件移动到远程计算机上的 D:\VM_name：

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2 -IncludeStorage -DestinationStoragePath D:\VM_name
```

## <a name="import-or-export-a-vm"></a>导入或导出 VM

`Import-VM` 和 `Export-VM` cmdlet 可导入和导出 VM。 下面显示了一些示例。 有关详细信息，请查看 [Import-VM](/powershell/module/hyper-v/import-vm?view=win10-ps) 和 [Export-VM](/powershell/module/hyper-v/export-vm?view=win10-ps) 参考文档。

下面的示例展示了如何基于 VM 的配置文件导入 VM。 VM 就地注册，因此不会复制其文件：

```powershell
Import-VM -ComputerName Server1 -Name VM1 -Path 'C:\<vm export path>\2B91FEB3-F1E0-4FFF-B8BE-29CED892A95A.vmcx'
```

下面的示例将 VM 导出到 D 驱动器的根目录：

```powershell
Export-VM -ComputerName Server1 -Name VM1 -Path D:\
```

## <a name="rename-a-vm"></a>重命名 VM

`Rename-VM` cmdlet 用于重命名 VM。 有关详细信息，请查看 [Rename-VM](/powershell/module/hyper-v/rename-vm?view=win10-ps) 参考文档。

下面的示例将 VM1 重命名为 VM2，并显示重命名后的虚拟机：

```powershell
Rename-VM -ComputerName Server1 -Name VM1 -NewName VM2
```

## <a name="create-a-vm-checkpoint"></a>创建 VM 检查点

`Checkpoint-VM` cmdlet 用于为 VM 创建检查点。 有关详细信息，请查看 [Checkpoint-VM](/powershell/module/hyper-v/checkpoint-vm?view=win10-ps) 参考文档。

下面的示例为名为 Test 的 VM 创建名为 BeforeInstallingUpdates 的检查点。

```powershell
Checkpoint-VM -ComputerName Server1 -Name VM1 -SnapshotName BeforeInstallingUpdates
```

## <a name="create-a-vhd-for-a-vm"></a>为 VM 创建 VHD

`New-VHD` cmdlet 用于为 VM 创建新的 VHD。 有关如何使用该 cmdlet 的详细信息，请查看 [New-VHD](/powershell/module/hyper-v/new-vhd?view=win10-ps) 参考文档。

下面的示例采用 VHDX 格式创建一个大小为 10 GB 的动态虚拟硬盘。 由于未指定类型，因此文件扩展名决定了格式，将使用默认类型“动态”。

```powershell
New-VHD -ComputerName Server1 -Name VM1 -Path c:\Base.vhdx -SizeBytes 10GB
```

## <a name="add-a-network-adapter-to-a-vm"></a>向 VM 添加网络适配器

`Add-VMNetworkAdapter` cmdlet 用于向 VM 添加虚拟网络适配器。 下面显示了一些示例。 有关如何使用该 cmdlet 的详细信息，请查看 [Add-VMNetworkAdapter](/powershell/module/hyper-v/add-vmnetworkadapter?view=win10-ps) 参考文档。

下面的示例将名为 Redmond NIC1 的虚拟网络适配器添加到名为 VM1 的虚拟机：

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -Name "Redmond NIC1"
```

此示例将一个虚拟网络适配器添加到名为 VM1 的虚拟机，并将其连接到名为 Network 的虚拟交换机：

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -SwitchName Network
```

## <a name="create-a-virtual-switch-for-a-vm"></a>为 VM 创建虚拟交换机

`New-VMSwitch` cmdlet 用于在 VM 主机上新建虚拟交换机。 有关如何使用该 cmdlet 的详细信息，请查看 [New-VMSwitch](/powershell/module/hyper-v/new-vmswitch?view=win10-ps) 参考文档。

以下示例创建一个名为“QoS switch”的新交换机，该交换机绑定到名为 Wired Ethernet Connection 3 的网络适配器，并支持基于权重的最小带宽。

```powershell
New-VMSwitch "QoS Switch" -NetAdapterName "Wired Ethernet Connection 3" -MinimumBandwidthMode Weight
```

## <a name="set-memory-for-a-vm"></a>为 VM 设置内存

`Set-VMMemory` cmdlet 用于为 VM 配置内存。 有关如何使用该 cmdlet 的详细信息，请查看 [Set-VMMemory](/powershell/module/hyper-v/set-vmmemory?view=win10-ps) 参考文档。

以下示例在名为 VM1 的 VM 上启用动态内存，设置其最小内存、启动内存和最大内存、其内存优先级及其缓冲区。

```powershell
Set-VMMemory -ComputerName Server1 -Name VM1 -DynamicMemoryEnabled $true -MinimumBytes 64MB -StartupBytes 256MB -MaximumBytes 2GB -Priority 80 -Buffer 25
```

## <a name="set-virtual-processors-for-a-vm"></a>为 VM 设置虚拟处理器

`Set-VMProcessor` cmdlet 用于为 VM 配置虚拟处理器。 有关如何使用该 cmdlet 的详细信息，请查看 [Set-VMProcessor](/powershell/module/hyper-v/set-vmprocessor?view=win10-ps) 参考文档。

下面的示例配置名为 VM1 的 VM，该 VM 具有两个虚拟处理器，预留比例为 10%，限制为 75%，相对权重为 200。

```powershell
Set-VMProcessor -ComputerName Server1 -Name VM1 -Count 2 -Reserve 10 -Maximum 75 -RelativeWeight 200
```

## <a name="next-steps"></a>后续步骤  

你还可以使用 Windows Admin Center 创建和管理 VM。 有关详细信息，请参阅 [Windows Admin Center](vm.md)。
