---
title: 在 Azure Stack Hub 中创建 VM 磁盘存储
description: 在 Azure Stack Hub 中为虚拟机创建磁盘。
author: sethmanheim
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 757b978012fc1b17362343309d57c0df09862a98
ms.sourcegitcommit: 98f62c33469ba963ba266bd88e206e9144258ea3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "82032835"
---
# <a name="create-vm-disk-storage-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建 VM 磁盘存储

本文介绍如何使用 Azure Stack Hub 门户或 PowerShell 创建虚拟机 (VM) 磁盘存储。

## <a name="overview"></a>概述

从版本 1808 开始，Azure Stack Hub 支持在 VM 上使用托管磁盘和非托管磁盘，作为操作系统 (OS) 磁盘和数据磁盘。 在版本 1808 之前，仅支持非托管磁盘。

[托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)通过管理与 VM 磁盘关联的存储帐户简化了 Azure IaaS VM 的磁盘管理。 只需指定所需的磁盘大小，Azure Stack Hub 即可为你创建和管理磁盘。

非托管磁盘要求创建存储帐户用于存储磁盘。 你创建的磁盘称为 VM 磁盘并且存储在存储帐户中的容器中。

## <a name="best-practice-guidelines"></a>最佳做法准则

建议为 Vm 使用托管磁盘，以便更轻松地进行管理和容量平衡。 不需要在使用托管磁盘之前准备好存储帐户和容器。 创建多个托管磁盘时，会将这些磁盘分配到多个卷中，这有助于平衡卷的容量。  

对于非托管磁盘，为了改进性能并降低总体成本，建议将每个非托管磁盘置于单独的容器中。 虽然可以将 OS 磁盘和数据磁盘放置在同一个容器中，但最佳做法是让一个容器容纳一个 OS 磁盘或一个数据磁盘，而不是同时容纳这二者。

如果向 VM 添加一个或多个数据磁盘，请使用更多的容器作为存储这些磁盘的位置。 附加 VM 的 OS 磁盘也应当位于其自己的容器中。

创建 VM 时，可将同一存储帐户重复用于每个新虚拟机。 只有创建的容器才应是唯一的。

## <a name="adding-new-disks"></a>添加新磁盘

下表概述了如何使用门户以及 PowerShell 添加磁盘：

| 方法 | 选项
|-|-|
|用户门户| - 向现有 VM 添加新的数据磁盘。 新磁盘由 Azure Stack Hub 创建。 </br> </br> -向之前创建的 VM 添加现有磁盘（.vhd）文件。 若要执行此操作，必须准备 .vhd，然后将该文件上传到 Azure Stack Hub。 |
|[PowerShell](#use-powershell-to-add-multiple-disks-to-a-vm) | - 使用 OS 磁盘创建新的 VM，同时向该 VM 添加一个或多个数据磁盘。 |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>使用门户向 VM 添加磁盘

默认情况下，使用门户为大多数市场项创建 VM 时，只会创建 OS 磁盘。

创建 VM 后，可以使用门户执行以下操作：

* 创建新的数据磁盘并将其附加到 VM。
* 上传现有的数据磁盘并将其附加到 VM。

添加的每个非托管磁盘都应当放置在单独的容器中。

> [!NOTE]  
> 由 Azure 创建并管理的磁盘称为[托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)。

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>使用门户创建并附加新的数据磁盘

1. 在门户中，依次选择“所有服务”、“虚拟机”。  
   ![示例：VM 仪表板](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. 选择前面创建的 VM。
   ![示例：在仪表板中选择 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. 对于 VM，依次选择“磁盘”、“添加数据磁盘”。  
   ![示例：将新磁盘附加到 VM](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. 对于“数据磁盘”：
   * 输入 **LUN**。 LUN 必须是有效的编号。
   * 选择“创建磁盘”。 
   ![示例：将新磁盘附加到 VM](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. 在“创建托管磁盘”边栏选项卡中： 
   * 输入磁盘的**名称**。
   * 选择现有的**资源组**，或创建一个新的资源组。
   * 选择“位置”。  默认情况下，位置设置为 OS 磁盘所在的同一容器。
   * 选择“帐户类型”。 
      ![示例：将新磁盘附加到 VM](media/azure-stack-manage-vm-disks/create-manage-disk.png)

    > [!NOTE]  
    > 高级磁盘（SSD）和标准磁盘（HDD）由 Azure Stack 集线器中的同一存储基础结构支持。 它们提供相同的性能。

   * 选择“源类型”。****

     从其他磁盘的快照、存储帐户的 Blob 中创建磁盘，或创建空磁盘。

      **快照**：选择快照（如果可用）。 该快照必须在 VM 的订阅和位置中可用。

      **存储 blob**：
     * 添加包含磁盘映像的存储 Blob 的 URI。  
     * 选择“浏览”打开“存储帐户”边栏选项卡。**** 有关说明，请参阅[从存储帐户添加数据磁盘](#add-a-data-disk-from-a-storage-account)。
     * 选择映像的 OS 类型： **Windows**、 **Linux**或**None （数据磁盘）**。

   * 选择“大小(GiB)”。****

     磁盘成本根据磁盘大小的增加而增加。

   * 选择“创建”。  Azure Stack 中心创建并验证托管磁盘。

6. Azure Stack 中心创建磁盘并将其附加到 VM 后，新磁盘将在 "**数据磁盘**" 下的 "VM 磁盘设置" 中列出。

   ![示例：查看磁盘](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>从存储帐户添加数据磁盘

有关在 Azure Stack 集线器中使用存储帐户的详细信息，请参阅[Azure Stack 中心存储简介](azure-stack-storage-overview.md)。

1. 选择要使用的**存储帐户**。
2. 选择要在其中放置数据磁盘的**容器**。 在“容器”边栏选项卡中，可根据需要创建新的容器。**** 然后，可以将新磁盘的位置更改为其自己的容器。 为每个磁盘使用单独的容器时，数据磁盘的位置是分散的，这样可以改进性能。
3. 选择“选择”以保存所做的选择。****

    ![示例：选择容器](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>将现有数据磁盘附加到 VM

1. [准备一个 .vhd 文件](/azure/virtual-machines/windows/classic/createupload-vhd)，用作 VM 的数据磁盘。 将该 .vhd 文件上传到与要附加 .vhd 文件的 VM 一起使用的存储帐户。

    - 计划使用一个容器来保存 .vhd 文件，该容器不同于保存 OS 磁盘的容器。  
    - 在将任何 VHD 上传到 Azure 之前，应按照[准备要上传到 azure 的 WINDOWS VHD 或 VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
    - 开始迁移到[托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)之前，请先查看[规划迁移到托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/on-prem-to-azure#plan-for-the-migration-to-managed-disks)。

    ![示例：上传 VHD 文件](media/azure-stack-manage-vm-disks/upload-vhd.png)

2. 上传 .vhd 文件以后，即可将该 VHD 附加到 VM。 在左侧菜单中，选择 "**虚拟机**"。  
 ![示例：在仪表板中选择 VM](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. 从列表中选择 VM。

    ![示例：在仪表板中选择 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. 在 VM 页上，依次选择“磁盘”、“附加现有”。********

    ![示例：附加现有磁盘](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. 在“附加现有磁盘”页面中，选择“VHD 文件”。******** 此时会打开“存储帐户”页。****

    ![示例：选择 VHD 文件](media/azure-stack-manage-vm-disks/select-vhd.png)

6. 在“存储帐户”**** 下选择要使用的帐户，然后选择一个容器，用于容纳以前上传的 .vhd 文件。 选择该 .vhd 文件，然后选择“选择”以保存所做的选择。****

    ![示例：选择容器](media/azure-stack-manage-vm-disks/select-container2.png)

7. 在“附加现有磁盘”下，选择的文件列在“VHD 文件”下。******** 更新磁盘的“主机缓存”设置，然后选择“确定”以保存 VM 的新磁盘配置。********

    ![示例：附加 VHD 文件](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. Azure Stack 中心创建磁盘并将其附加到 VM 后，新磁盘将在 "**数据磁盘**" 下的 vm 的磁盘设置中列出。

    ![示例：完成磁盘附加操作](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-disks-to-a-vm"></a>使用 PowerShell 将多个磁盘添加到 VM

可以使用 PowerShell 来预配某个 VM，然后添加新的数据磁盘，或者附加预先存在的托管磁盘或 .vhd 文件作为数据磁盘。

**Add-AzureRmVMDataDisk** cmdlet 可以向 VM 添加数据磁盘。 可以在创建 VM 时添加数据磁盘，也可以向现有的 VM 添加数据磁盘。 对于非托管磁盘，请指定 **VhdUri** 参数，将磁盘分配到不同的容器。

### <a name="add-data-disks-to-a-new-vm"></a>将数据磁盘添加到**新** VM

以下示例使用 PowerShell 命令创建包含三个数据磁盘的 VM。 由于使用托管磁盘或非托管磁盘时存在细微差别，因此为此命令提供了多个部件。

#### <a name="create-virtual-machine-configuration-and-network-resources"></a>创建虚拟机配置和网络资源

以下脚本创建 VM 对象，然后将它存储在 `$VirtualMachine` 变量中。 这些命令为 VM 指定名称和大小，然后创建 VM 的网络资源（虚拟网络、子网、虚拟网络适配器、NSG 和公共 IP 地址）：

```powershell
# Create new virtual machine configuration
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"

# Set variables
$rgName = "myResourceGroup"
$location = "local"

# Create a subnet configuration
$subnetName = "mySubNet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

# Create a vnet configuration
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
                                  -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

# Create a public IP
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
                                  -AllocationMethod Dynamic

# Create a network security group configuration
$nsgName = "myNsg"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
                                                -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
                                                -SourceAddressPrefix Internet -SourcePortRange * `
                                                -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
                                       -Name $nsgName -SecurityRules $rdpRule

# Create a NIC configuration
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
                                   -Location $location -SubnetId $vnet.Subnets[0].Id `
                                   -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

```

#### <a name="add-managed-disks"></a>添加托管磁盘

下面的三个命令将托管数据磁盘添加到 `$VirtualMachine` 中存储的虚拟机。 每个命令都会指定磁盘的名称和其他属性：

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -CreateOption Empty
```

以下命令将 OS 磁盘作为托管磁盘添加到 `$VirtualMachine` 中存储的虚拟机。

```powershell
# Set OS Disk
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName  `
                                      -CreateOption FromImage -Windows
```

#### <a name="add-unmanaged-disks"></a>添加非托管磁盘

接下来的三个命令将三个非托管数据磁盘的路径分配给 `$DataDiskVhdUri01`、`$DataDiskVhdUri02` 和 `$DataDiskVhdUri03` 变量。 在 URL 中定义另一路径名称，使磁盘分布到不同的容器：

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

下面的三个命令将数据磁盘添加到 `$VirtualMachine` 中存储的虚拟机。 每个命令都会指定磁盘的名称和其他属性。 每个磁盘的 URI 分别存储在 `$DataDiskVhdUri01`、`$DataDiskVhdUri02` 和 `$DataDiskVhdUri03` 中：

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

以下命令将非托管 OS 磁盘添加到 `$VirtualMachine` 中存储的虚拟机。

```powershell
# Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
                                      -CreateOption FromImage -Windows
```

#### <a name="create-new-virtual-machine"></a>创建新的虚拟机

使用以下 PowerShell 命令设置 OS 映像，将网络配置添加到 VM，然后启动新的 VM：

```powershell
#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine -ProvisionVMAgent | `
                  Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
                  -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```

### <a name="add-data-disks-to-an-existing-vm"></a>向现有 VM 添加新的数据磁盘

以下示例使用 PowerShell 命令将三个数据磁盘添加到现有 VM：

#### <a name="get-virtual-machine"></a>获取虚拟机

 第一个命令通过 **Get-AzureRmVM** cmdlet 获取名为 **VirtualMachine** 的 VM。 此命令将 VM 存储在 `$VirtualMachine` 变量中：

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

#### <a name="add-managed-disk"></a>添加托管磁盘

接下来的三个命令将托管数据磁盘添加到 `$VirtualMachine` 变量中存储的 VM。 每个命令都会指定磁盘的名称和其他属性：

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" -Lun 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" -Lun 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" -Lun 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="add-unmanaged-disk"></a>添加非托管磁盘

接下来的三个命令将三个数据磁盘的路径分配给 `$DataDiskVhdUri01`、`$DataDiskVhdUri02` 和 `$DataDiskVhdUri03` 变量。 VHD URI 中的不同路径名称表示用于放置磁盘的不同容器。

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

接下来的三个命令将数据磁盘添加到 `$VirtualMachine` 变量中存储的 VM。 每个命令都会指定磁盘的名称、位置和其他属性。 每个磁盘的 URI 分别存储在 `$DataDiskVhdUri01`、`$DataDiskVhdUri02` 和 `$DataDiskVhdUri03` 中：

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="update-virtual-machine-state"></a>更新虚拟机状态

以下命令更新 `-ResourceGroupName` 的 `$VirtualMachine` 中存储的 VM 的状态：

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure Stack 中心 Vm 的详细信息，请参阅[Azure Stack 集线器中虚拟机的注意事项](azure-stack-vm-considerations.md)。
