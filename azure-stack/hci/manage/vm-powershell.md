---
title: 使用 Windows PowerShell 在 Azure Stack HCI 上管理 Vm
description: 如何使用 Windows PowerShell 在 Azure Stack HCI 上管理虚拟机
author: v-dasis
ms.topic: article
ms.date: 05/20/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 471dc57505832ef58424a74d08f1534a66ea285e
ms.sourcegitcommit: d69eacbf48c06309b00d17c82ebe0ce2bc6552df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83794192"
---
# <a name="manage-vms-on-azure-stack-hci-using-windows-powershell"></a>使用 Windows PowerShell 在 Azure Stack HCI 上管理 Vm

> 适用于 Windows Server 2019

Windows PowerShell 可用于在 Azure Stack HCI 上创建和管理虚拟机（Vm）。

通常，你可以从运行 Windows 10 的远程计算机而不是群集中的主机服务器管理 Vm。 此远程计算机称为管理计算机。

> [!NOTE]
> 在管理计算机上运行 PowerShell 命令时，请将-ComputerName 参数包含在所管理的主机服务器的名称中。 允许 NetBIOS 名称、IP 地址和完全限定的域名。

有关使用 PowerShell 管理 Vm 的完整参考文档，请参阅[hyper-v 参考](https://docs.microsoft.com/powershell/module/hyper-v/?view=win10-ps)。

## <a name="get-a-list-of-vms"></a>获取 Vm 列表

以下示例返回 Server1 上所有 Vm 的列表。

```powershell
Get-VM -ComputerName Server1
```

以下示例通过使用命令添加筛选器，返回服务器上所有正在运行的 Vm 的列表 `Where-Object` 。 有关详细信息，请参阅[使用对象](<https://docs.microsoft.com/previous-versions/windows/it-pro/windows-powershell-1.0/ee177028(v=technet.10)>)文档。

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Running"
```

下一个示例将返回服务器上所有关机 Vm 的列表。

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Off"
```

## <a name="start-and-stop-a-vm"></a>启动和停止 VM

使用 `Start-VM` 和 `Stop-VM` 命令启动或停止 VM。 有关详细信息，请参阅[启动 vm](https://docs.microsoft.com/powershell/module/hyper-v/start-vm?view=win10-ps)和[停止 vm](https://docs.microsoft.com/powershell/module/hyper-v/stop-vm?view=win10-ps)参考文档。

以下示例演示如何启动名为 VM1 的 VM：

```powershell
Start-VM -Name VM1 -ComputerName Server1
```

以下示例演示如何关闭名为 TestVM 的 VM：

```powershell
Stop-VM -Name VM1 -ComputerName Server1
```

## <a name="move-a-vm"></a>移动 VM

`Move-VM`Cmdlet 可将 VM 移到其他服务器。 有关详细信息，请参阅[移动-VM](https://docs.microsoft.com/powershell/module/hyper-v/move-vm?view=win10-ps)参考文档。

 下面的示例演示如何在 VM 存储在 Server1 上的 SMB 共享上时，将 VM 移动到 Server2：

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2
 ```

下面的示例演示如何将 VM 从 Server1 移到 Server2，并将与 VM 关联的所有文件移到远程计算机上的 D:\ VM_name：

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2 -IncludeStorage -DestinationStoragePath D:\VM_name
```

## <a name="import-or-export-a-vm"></a>导入或导出 VM

`Import-VM`和 `Export-VM` cmdlet 将导入和导出 VM。 下面显示了一些示例。 有关详细信息，请参阅[导入 vm](https://docs.microsoft.com/powershell/module/hyper-v/import-vm?view=win10-ps)和[导出 vm](https://docs.microsoft.com/powershell/module/hyper-v/export-vm?view=win10-ps)参考文档。

下面的示例演示如何从配置文件导入 VM。 VM 已就地注册，因此不会复制其文件：

```powershell
Import-VM -ComputerName Server1 -Name VM1 -Path 'C:\<vm export path>\2B91FEB3-F1E0-4FFF-B8BE-29CED892A95A.vmcx'
```

下面的示例将 VM 导出到 D 驱动器的根目录：

```powershell
Export-VM -ComputerName Server1 -Name VM1 -Path D:\
```

## <a name="rename-a-vm"></a>重命名 VM

`Rename-VM`Cmdlet 用于重命名虚拟机。 有关详细信息，请参阅[重命名 VM](https://docs.microsoft.com/powershell/module/hyper-v/rename-vm?view=win10-ps)参考文档。

下面的示例将 VM1 重命名为 VM2，并显示重命名的虚拟机：

```powershell
Rename-VM -ComputerName Server1 -Name VM1 -NewName VM2
```

## <a name="create-a-vm-checkpoint"></a>创建 VM 检查点

`Checkpoint-VM`Cmdlet 用于创建 VM 的检查点。 有关详细信息，请参阅[检查点-VM](https://docs.microsoft.com/powershell/module/hyper-v/checkpoint-vm?view=win10-ps)参考文档。

下面的示例为名为 Test 的 VM 创建名为 BeforeInstallingUpdates 的检查点。

```powershell
Checkpoint-VM -ComputerName Server1 -Name VM1 -SnapshotName BeforeInstallingUpdates
```

## <a name="create-a-vhd-for-a-vm"></a>为 VM 创建 VHD

`New-VHD`Cmdlet 用于为 VM 创建新的 VHD。 有关如何使用它的详细信息，请参阅[新的 VHD](https://docs.microsoft.com/powershell/module/hyper-v/new-vhd?view=win10-ps)参考文档。

以下示例创建一个采用 VHDX 格式的动态虚拟硬盘，大小为 10 GB。 由于未指定类型，因此文件扩展名确定使用动态的格式和默认类型。

```powershell
New-VHD -ComputerName Server1 -Name VM1 -Path c:\Base.vhdx -SizeBytes 10GB
```

## <a name="add-a-network-adapter-to-a-vm"></a>将网络适配器添加到 VM

`Add-VMNetworkAdapter`Cmdlet 用于将虚拟网络适配器添加到 VM。 下面显示了一些示例。 有关如何使用它的详细信息，请参阅[VMNetworkAdapter](https://docs.microsoft.com/powershell/module/hyper-v/add-vmnetworkadapter?view=win10-ps)参考文档。

下面的示例将名为 Redmond NIC1 的虚拟网络适配器添加到名为 VM1 的虚拟机：

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -Name "Redmond NIC1"
```

此示例将虚拟网络适配器添加到名为 VM1 的虚拟机，并将其连接到名为 Network 的虚拟交换机：

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -SwitchName Network
```

## <a name="create-a-virtual-switch-for-a-vm"></a>为 VM 创建虚拟交换机

`New-VMSwitch`Cmdlet 用于虚拟机主机上的新虚拟交换机。 有关如何使用它的详细信息，请参阅[新的 VMSwitch](https://docs.microsoft.com/powershell/module/hyper-v/new-vmswitch?view=win10-ps)参考文档。

以下示例创建名为 "QoS 交换机" 的新交换机，该交换机绑定到名为有线以太网连接3的网络适配器，并支持基于权重的最小带宽。

```powershell
New-VMSwitch "QoS Switch" -NetAdapterName "Wired Ethernet Connection 3" -MinimumBandwidthMode Weight
```

## <a name="set-memory-for-a-vm"></a>为 VM 设置内存

`Set-VMMemory`Cmdlet 用于配置虚拟机的内存。 有关如何使用它的详细信息，请参阅[set-vmmemory](https://docs.microsoft.com/powershell/module/hyper-v/set-vmmemory?view=win10-ps)参考文档。

以下示例在名为 VM1 的 VM 上启用动态内存，设置其最小值、启动和最大内存、内存优先级及其缓冲区。

```powershell
Set-VMMemory -ComputerName Server1 -Name VM1 -DynamicMemoryEnabled $true -MinimumBytes 64MB -StartupBytes 256MB -MaximumBytes 2GB -Priority 80 -Buffer 25
```

## <a name="set-virtual-processors-for-a-vm"></a>设置 VM 的虚拟处理器

`Set-VMProcessor`Cmdlet 用于配置 VM 的虚拟处理器。 有关如何使用它的详细信息，请参阅[VMProcessor](https://docs.microsoft.com/powershell/module/hyper-v/set-vmprocessor?view=win10-ps
)参考文档。

以下示例配置名为 VM1 的 VM，该 VM 包含两个虚拟处理器，保留为10%，最大限制为75%，相对权重为200。

```powershell
Set-VMProcessor -ComputerName Server1 -Name VM1 -Count 2 -Reserve 10 -Maximum 75 -RelativeWeight 200
```

## <a name="create-a-vm"></a>创建 VM  

`New-VM`Cmdlet 用于创建新 VM。 有关详细用法，请参阅[新的-VM](https://docs.microsoft.com/powershell/module/hyper-v/new-vm?view=win10-ps)参考文档。

下面是使用现有虚拟硬盘创建新 VM 时可以指定的设置，其中：

- **-Name**是为你要创建的虚拟机提供的名称。

- **-MemoryStartupBytes**是虚拟机在启动时可用的内存量。

- **-BootDevice**是虚拟机启动时启动的设备。
 通常，这是一个虚拟硬盘（VHD）、一个用于基于 DVD 的启动的 .iso 文件或用于网络启动的网络适配器（网络适配器）。

- **-VHDPath**是要使用的虚拟机磁盘的路径。

- **-Path**是用于存储虚拟机配置文件的路径。

- **-代**为虚拟机生成。 为 VHD 使用第1代，为 VHDX 使用第2代。

- **-Switch**是你希望虚拟机用于连接到其他虚拟机或网络的虚拟交换机的名称。 使用[获取-VMSwitch](https://docs.microsoft.com/powershell/module/hyper-v/get-vmswitch?view=win10-ps)获取虚拟交换机的名称。 例如：  

用于创建名为 VM1 的 VM 的完整命令如下所示：

 ```powershell
 New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes <Memory> -BootDevice <BootDevice> -VHDPath <VHDPath> -Path <Path> -Generation <Generation> -Switch <SwitchName>
 ```

下一个示例将创建 2 GB 内存的第2代虚拟机。 它从当前目录中的 VMs\Win10.vhdx 文件夹启动，并使用名为 ExternalSwitch 的虚拟交换机。 虚拟机配置文件存储在 VMData 文件夹中。  

``` powershell
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -VHDPath .\VMs\Win10.vhdx -Path .\VMData -Generation 2 -Switch ExternalSwitch
```

以下参数用于指定虚拟硬盘。

若要使用新的虚拟硬盘创建虚拟机，请将上述示例中的 **-VHDPath**参数替换为 **-NewVHDPath** ，并添加 **-NewVHDSizeBytes**参数，如下所示：  

``` powershell  
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -NewVHDPath .\VMs\Win10.vhdx -Path .\VMData -NewVHDSizeBytes 20GB -Generation 2 -Switch ExternalSwitch  
 ```  

若要使用启动到操作系统映像包的新虚拟磁盘创建虚拟机，请参阅在[Windows 10 上创建适用于 hyper-v 的虚拟机演练](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_create_vm)中的 PowerShell 示例。  

## <a name="next-steps"></a>后续步骤  

你还可以使用 Windows 管理中心创建和管理 Vm。 有关详细信息，请参阅[Windows 管理中心](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)。