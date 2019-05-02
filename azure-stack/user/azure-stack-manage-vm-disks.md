---
title: 在 Azure Stack 中管理 VM 磁盘 | Microsoft Docs
description: 在 Azure Stack 中为虚拟机创建磁盘。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 360ff9a016dd0c564a15bf00142022bb4f959021
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64303834"
---
# <a name="create-virtual-machine-disk-storage-in-azure-stack"></a>在 Azure Stack 中创建虚拟机磁盘存储

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍如何使用 Azure Stack 门户或 PowerShell 创建虚拟机磁盘存储。

## <a name="overview"></a>概述

从版本 1808 开始，Azure Stack 支持在虚拟机上使用托管磁盘和非托管磁盘，作为操作系统 (OS) 磁盘和数据磁盘。 在版本 1808 之前，仅支持非托管磁盘。 

**[托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)** 通过管理与 VM 磁盘关联的存储帐户简化了 Azure IaaS VM 的磁盘管理。 你只需指定所需的磁盘大小，Azure Stack 将为你创建和管理磁盘。

非托管磁盘要求创建存储帐户用于存储磁盘。 你创建的磁盘称为 VM 磁盘并且存储在存储帐户中的容器中。

### <a name="best-practice-guidelines"></a>最佳做法准则

为了改进性能并降低总体成本，建议将每个 VM 磁盘置于单独的容器中。 容器可以容纳 OS 磁盘或数据磁盘，但不应二者同时容纳。 （但是，没有任何限制阻止你将两种类型的磁盘放置在同一容器中。）

如果向 VM 添加一个或多个数据磁盘，请使用更多的容器作为存储这些磁盘的位置。 附加 VM 的 OS 磁盘也应当位于其自己的容器中。

创建 VM 时，可将同一存储帐户重复用于每个新虚拟机。 只有创建的容器才应是唯一的。

### <a name="adding-new-disks"></a>添加新磁盘

下表概述了如何使用门户以及 PowerShell 添加磁盘。

| 方法 | 选项
|-|-|
|用户门户|- 向现有 VM 添加新的数据磁盘。 新磁盘由 Azure Stack 创建。 </br> </br>- 将现有的磁盘 (.vhd) 文件添加到以前创建的 VM。 若要执行此操作，必须准备 .vhd，然后将该文件上传到 Azure Stack。 |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | - 使用 OS 磁盘创建新的 VM，同时向该 VM 添加一个或多个数据磁盘。 |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>使用门户向 VM 添加磁盘

默认情况下，使用门户为大多数市场项创建 VM 时，只会创建 OS 磁盘。

创建 VM 后，可以使用门户执行以下操作：
* 创建新的数据磁盘并将其附加到 VM。
* 上传现有的数据磁盘并将其附加到 VM。

添加的每个非托管磁盘都应当放置在单独的容器中。

>[!NOTE]  
>由 Azure 创建并管理的磁盘称为[托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>使用门户创建并附加新的数据磁盘

1. 在门户中，选择“所有服务” > “虚拟机”。    
   示例：![VM 仪表板](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. 选择以前创建的虚拟机。   
   ![示例：在仪表板中选择 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. 对于虚拟机，请选择“磁盘” > “添加数据磁盘”。       
   示例：![将新磁盘附加到 VM](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4. 对于“数据磁盘”：
   -  输入 **LUN**。 LUN 必须是有效的编号。
   -  选择“创建磁盘”。
   示例：![将新磁盘附加到 VM](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. 在“创建托管磁盘”边栏选项卡中：
   - 输入磁盘的**名称**。
   - 选择现有的资源组，或创建新的**资源组**。
   - 选择“位置”。 默认情况下，位置设置为 OS 磁盘所在的同一容器。
   - 选择“帐户类型”。 
      示例：![将新磁盘附加到 VM](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **高级·SSD**  
      高级磁盘 (SSD) 基于固态硬盘，提供一致的低延迟性能。 高级磁盘可在价格与性能之间实现最佳平衡，非常适合用于 I/O 密集型应用程序和生产工作负荷。
       
      **标准 HDD**  
      标准磁盘 (HDD) 基于磁驱动器，适用于不经常访问数据的应用程序。 区域冗余磁盘基于区域冗余存储 (ZRS)，此存储可跨多个区域复制数据，即使单个区域出现故障，也仍可供使用。 

   - 选择“源类型”。

     从其他磁盘的快照、存储帐户的 Blob 中创建磁盘，或创建空磁盘。

      **快照**  
      选择一个快照（如果有）。 该快照必须在 VM 的订阅和位置中可用。

      **存储 Blob**  
     - 添加包含磁盘映像的存储 Blob 的 URI。  
     - 选择“浏览”打开“存储帐户”边栏选项卡。 有关说明，请参阅[从存储帐户添加数据磁盘](#add-a-data-disk-from-a-storage-account)。
     - 选择映像的 OS 类型：“Windows”、“Linux”或“无(数据磁盘)”。

       **无(空磁盘)**

   - 选择“大小(GiB)”。

     标准磁盘的费用随着磁盘大小的增大而提高。 高级磁盘的费用和性能随着磁盘大小的增大而提高。 有关详细信息，请参阅[托管磁盘定价](https://go.microsoft.com/fwlink/?linkid=843142)。

   - 选择“创建”。 Azure Stack 将创建并验证托管磁盘。

5. 在 Azure Stack 创建磁盘并将磁盘附加到虚拟机之后，新磁盘列在“数据磁盘”下的虚拟机磁盘设置中。   

   ![示例：查看磁盘](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>从存储帐户添加数据磁盘

有关如何在 Azure Stack 中使用存储帐户的详细信息，请参阅 [Azure Stack 存储简介](azure-stack-storage-overview.md)。

1. 选择要使用的**存储帐户**。
2. 选择要在其中放置数据磁盘的**容器**。 在“容器”边栏选项卡中，可根据需要创建新的容器。 然后，可以将新磁盘的位置更改为其自己的容器。 为每个磁盘使用单独的容器时，数据磁盘的位置是分散的，这样可以改进性能。
3. 选择“选择”以保存所做的选择。

    ![示例：选择容器](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>将现有数据磁盘附加到 VM

1.  [准备一个 .vhd 文件](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd)，用作 VM 的数据磁盘。 将该 .vhd 文件上传到一个存储帐户，该帐户可以与要向其附加 .vhd 文件的 VM 配合使用。

    计划使用一个容器来保存 .vhd 文件，该容器不同于保存 OS 磁盘的容器。   
    ![示例：上传 VHD 文件](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  上传 .vhd 文件以后，即可将该 VHD 附加到 VM。 在左侧菜单中选择“虚拟机”。  
 示例：![在仪表板中选择 VM](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  从列表中选择虚拟机。

    ![示例：在仪表板中选择 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  在该虚拟机的页面上，选择“磁盘” > “附加现有磁盘”。   

    ![示例：附加现有磁盘](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  在“附加现有磁盘”页面中，选择“VHD 文件”。 此时会打开“存储帐户”页。    

    ![示例：选择 VHD 文件](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  在“存储帐户”下选择要使用的帐户，然后选择一个容器，用于容纳以前上传的 .vhd 文件。 选择该 .vhd 文件，然后选择“选择”以保存所做的选择。    

    ![示例：选择容器](media/azure-stack-manage-vm-disks/select-container2.png)

7.  在“附加现有磁盘”下，选择的文件列在“VHD 文件”下。 更新磁盘的“主机缓存”设置，然后选择“确定”以保存 VM 的新磁盘配置。    

    ![示例：附加 VHD 文件](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  在 Azure Stack 创建磁盘并将磁盘附加到虚拟机之后，新磁盘列在“数据磁盘”下的虚拟机磁盘设置中。   

    ![示例：完成磁盘附加操作](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>使用 PowerShell 将多个非托管磁盘添加到 VM

可以使用 PowerShell 来预配某个 VM，然后添加新的数据磁盘，或者附加预先存在的 **.vhd** 文件作为数据磁盘。

**Add-AzureRmVMDataDisk** cmdlet 可以向虚拟机添加数据磁盘。 可以在创建虚拟机时添加数据磁盘，也可以向现有的虚拟机添加数据磁盘。 指定 **VhdUri** 参数，使磁盘分布到不同的容器。

### <a name="add-data-disks-to-a-new-virtual-machine"></a>向新的虚拟机添加数据磁盘
以下示例使用 PowerShell 命令来创建具有三个数据磁盘的 VM，每个磁盘放置在不同的容器中。

第一个命令创建虚拟机对象，然后将其存储在 *$VirtualMachine* 变量中。 此命令用于向虚拟机分配名称和大小。

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                    -VMSize "Standard_A2"
```

后续的三个命令将三个数据磁盘的路径分配给 *$DataDiskVhdUri01*、*$DataDiskVhdUri02* 和 *$DataDiskVhdUri03* 变量。 在 URL 中定义另一路径名称，使磁盘分布到不同的容器。

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

最后三个命令将数据磁盘添加到 *$VirtualMachine* 中存储的虚拟机。 每个命令都会指定磁盘的名称、位置和其他属性。 每个磁盘的 URI 存储在 *$DataDiskVhdUri01*、*$DataDiskVhdUri02* 和 *$DataDiskVhdUri03* 中。

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

使用以下 PowerShell 命令将 OS 磁盘和网络配置添加到 VM，然后启动新 VM。

```powershell
#set variables
$rgName = "myResourceGroup"
$location = "local"
#Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
    -CreateOption FromImage -Windows

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
-Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```

### <a name="add-data-disks-to-an-existing-virtual-machine"></a>将数据磁盘添加到现有的虚拟机

以下示例使用 PowerShell 命令将三个数据磁盘添加到现有的 VM。
第一个命令通过 **Get-AzureRmVM** cmdlet 获取名为 VirtualMachine 的虚拟机。 该命令在 *$VirtualMachine* 变量中存储虚拟机。

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```
后续的三个命令将三个数据磁盘的路径分配给 $DataDiskVhdUri01、$DataDiskVhdUri02 和 $DataDiskVhdUri03 变量。  vhduri 中的不同路径名称表示用于放置磁盘的不同容器。
```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```
```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```
```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```


后续三个命令将数据磁盘添加到 *$VirtualMachine* 变量中存储的虚拟机。 每个命令都会指定磁盘的名称、位置和其他属性。 每个磁盘的 URI 存储在 *$DataDiskVhdUri01*、*$DataDiskVhdUri02* 和 *$DataDiskVhdUri03* 中。

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

最后一个命令更新虚拟机的状态，该虚拟机存储在 -*ResourceGroupName* 的 *$VirtualMachine* 中。

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>后续步骤

有关 Azure Stack 虚拟机的详细信息，请参阅 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
