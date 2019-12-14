---
title: 在 Azure Stack 中创建 VM 磁盘存储 |Microsoft Docs
description: 在 Azure Stack 中创建虚拟机的磁盘。
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
ms.date: 12/03/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 49b89a23b33607eac217e2393a489fac6ce59934
ms.sourcegitcommit: e509ae283c6c3c6a83143dbdc63d0b5556daf54a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2019
ms.locfileid: "75031819"
---
# <a name="create-vm-disk-storage-in-azure-stack"></a>在 Azure Stack 中创建虚拟机磁盘存储

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍如何使用 Azure Stack 门户或 PowerShell 创建虚拟机（VM）磁盘存储。

## <a name="overview"></a>概述

从1808版开始，Azure Stack 支持在虚拟机中使用托管磁盘和非托管磁盘，同时同时作为操作系统（OS）和数据磁盘。 在版本1808之前，只支持非托管磁盘。

[托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)通过管理与 VM 磁盘关联的存储帐户简化了 Azure IaaS vm 的磁盘管理。 你只需指定所需的磁盘大小，Azure Stack 将为你创建和管理磁盘。

非托管磁盘需要创建存储帐户来存储磁盘。 你创建的磁盘称为 VM 磁盘，存储在存储帐户的容器中。

## <a name="best-practice-guidelines"></a>最佳做法准则

建议为 VM 使用托管磁盘，以便更轻松地进行管理和容量平衡。 不需要在使用托管磁盘之前准备好存储帐户和容器。 创建多个托管磁盘时，这些磁盘将分配到多个卷中，这有助于平衡卷的容量。  

对于非托管磁盘，若要提高性能和降低总体成本，建议将每个非托管磁盘放置在单独的容器中。 虽然您可以将 OS 磁盘和数据磁盘放置在同一个容器中，但最好的做法是，一个容器应该包含 OS 磁盘或数据磁盘，但不能同时包含两者。

如果向 VM 添加一个或多个数据磁盘，请使用其他容器作为存储这些磁盘的位置。 其他 Vm 的 OS 磁盘还应位于其自身的容器中。

创建 Vm 时，可以为每个新虚拟机重复使用同一存储帐户。 仅创建的容器应是唯一的。

## <a name="adding-new-disks"></a>添加新磁盘

下表总结了如何使用门户和 PowerShell 来添加磁盘：

| 方法 | 选项
|-|-|
|用户门户|-将新的数据磁盘添加到现有 VM。 新磁盘由 Azure Stack 创建。 </br> </br> -向之前创建的 VM 添加现有磁盘（.vhd）文件。 为此，必须准备 .vhd，然后将文件上传到 Azure Stack。 |
|[PowerShell](#use-powershell-to-add-multiple-disks-to-a-vm) | -使用 OS 磁盘创建新的 VM，同时将一个或多个数据磁盘添加到该 VM。 |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>使用门户向 VM 添加磁盘

默认情况下，当你使用门户为大多数 marketplace 项目创建 VM 时，只会创建 OS 磁盘。

创建 VM 后，可以使用门户执行以下操作：

* 创建新的数据磁盘并将其附加到 VM。
* 上传现有数据磁盘并将其附加到 VM。

你添加的每个非托管磁盘都应放在单独的容器中。

>[!NOTE]  
>由 Azure 创建和管理的磁盘称为[托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)。

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>使用门户创建并附加新的数据磁盘

1. 在门户中，依次选择 "**所有服务**"、"**虚拟机**"。
   ![示例： VM 仪表板](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. 选择以前创建的 VM。
   ![示例：在仪表板中选择一个 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. 对于 VM，请选择 "**磁盘**"，然后选择 "**添加数据磁盘**"。
   ![示例：将新磁盘附加到 vm](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. 对于数据磁盘：
   * 输入**LUN**。 LUN 必须是有效的数字。
   * 选择 "**创建磁盘**"。
   ![示例：将新磁盘附加到 vm](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. 在 "**创建托管磁盘**" 边栏选项卡中：
   * 输入磁盘的**名称**。
   * 选择现有资源组或创建新**资源组**。
   * 选择**位置**。 默认情况下，该位置设置为包含 OS 磁盘的相同容器。
   * 选择**帐户类型**。
      ![示例：将新磁盘附加到 vm](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **高级·SSD**  
      高级磁盘（SSD）由固态硬盘支持，并提供一致的低延迟性能。 它们在价格与性能之间提供最佳平衡，适用于 i/o 密集型应用程序和生产工作负荷。

      **标准 HDD**  
      标准磁盘（HDD）由磁盘驱动器支持，更适用于不经常访问数据的应用。 区域冗余磁盘是通过区域冗余存储（ZRS）提供支持的，这些存储将数据复制到多个区域，从而确保数据即使在单个区域关闭时也可用。

   * 选择**源类型**。

     从另一个磁盘的快照、存储帐户中的 blob 创建磁盘，或创建空磁盘。

      **快照**：选择快照（如果可用）。 快照必须在 VM 的订阅和位置中可用。

      **存储 blob**：
     * 添加包含磁盘映像的存储 blob 的 URI。  
     * 选择 "**浏览**" 以打开 "存储帐户" 边栏选项卡。 有关说明，请参阅[从存储帐户添加数据磁盘](#add-a-data-disk-from-a-storage-account)。
     * 选择映像的 OS 类型： **Windows**、 **Linux**或**None （数据磁盘）** 。

   * 选择**大小（GiB）** 。

     标准磁盘会根据磁盘大小增加。 根据磁盘大小增加了高级磁盘的成本和性能。 有关详细信息，请参阅[托管磁盘定价](https://go.microsoft.com/fwlink/?linkid=843142)。

   * 选择**创建**。 Azure Stack 创建和验证托管磁盘。

6. Azure Stack 创建磁盘并将其附加到 VM 后，新磁盘将在 "**数据磁盘**" 下的 "VM 磁盘设置" 中列出。

   ![示例：查看磁盘](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>从存储帐户添加数据磁盘

有关在 Azure Stack 中使用存储帐户的详细信息，请参阅[Azure Stack 存储简介](azure-stack-storage-overview.md)。

1. 选择要使用的**存储帐户**。
2. 选择要在其中放置数据磁盘的**容器**。 在 "**容器**" 边栏选项卡中，可以根据需要创建新的容器。 然后，你可以将新磁盘的位置更改为其自己的容器。 为每个磁盘使用单独的容器时，会分散数据磁盘的位置，从而提高性能。
3. 选择 "**选择**" 以保存选择。

    ![示例：选择容器](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>将现有数据磁盘附加到 VM

1. 准备用作 VM 数据磁盘的[.vhd 文件](/azure/virtual-machines/windows/classic/createupload-vhd)。 将该 .vhd 文件上传到你要将 .vhd 文件附加到的 VM 所使用的存储帐户。

    - 计划使用不同的容器来保存 .vhd 文件，而不是保留 OS 磁盘的容器。  
    - 将任何 VHD 上传到 Azure 之前，应按照[准备要上传到 Azure 的 Windows VHD 或 VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 进行操作。
    - 开始迁移到[托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)之前，请先查看[规划迁移到托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/on-prem-to-azure#plan-for-the-migration-to-managed-disks)。
    
    ![示例：上传 VHD 文件](media/azure-stack-manage-vm-disks/upload-vhd.png)



2. 上传 .vhd 文件后，即可将 VHD 附加到 VM。 在左侧菜单中，选择 "**虚拟机**"。  
 ![示例：在仪表板中选择一个 VM](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. 从列表中选择 VM。

    ![示例：在仪表板中选择 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. 在 VM 的页面上，选择 "**磁盘**"，然后选择 "**附加现有**"。

    ![示例：附加现有磁盘](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. 在 "**附加现有磁盘**" 页上，选择 " **VHD 文件**"。 此时将打开 "**存储帐户**" 页。

    ![示例：选择 VHD 文件](media/azure-stack-manage-vm-disks/select-vhd.png)

6. 在 "**存储帐户**" 下，选择要使用的帐户，然后选择保存之前上传的 .vhd 文件的容器。 选择 .vhd 文件，然后选择 "**选择**" 以保存选择。

    ![示例：选择容器](media/azure-stack-manage-vm-disks/select-container2.png)

7. 在 "**附加现有磁盘**" 下，你选择的文件将在 " **VHD 文件**" 下列出。 更新磁盘的 "**主机缓存**" 设置，然后选择 **"确定"** 以保存 VM 的新磁盘配置。

    ![示例：附加 VHD 文件](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. Azure Stack 创建磁盘并将其附加到 VM 后，新磁盘将在 "**数据磁盘**" 下的虚拟机磁盘设置中列出。

    ![示例：完成磁盘附加](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-disks-to-a-vm"></a>使用 PowerShell 将多个磁盘添加到 VM

你可以使用 PowerShell 来预配 VM 并添加新的数据磁盘，或将预先存在的托管磁盘或 .vhd 文件附加为数据磁盘。

**Add-azurermvmdatadisk** cmdlet 将数据磁盘添加到 VM。 你可以在创建 VM 时添加数据磁盘，或者将数据磁盘添加到现有 VM。 对于非托管磁盘，请指定**VhdUri**参数，以将磁盘分发到不同的容器。

### <a name="add-data-disks-to-a-new-vm"></a>将数据磁盘添加到**新**VM

以下示例使用 PowerShell 命令创建包含三个数据磁盘的 VM。 由于使用托管磁盘或非托管磁盘时的细微差别，此命令随几个部分提供。 

#### <a name="create-virtual-machine-configuration-and-network-resources"></a>创建虚拟机配置和网络资源

下面的脚本创建一个 VM 对象，然后将其存储在 `$VirtualMachine` 变量中。 命令将名称和大小分配给 VM，然后创建 VM 的网络资源（虚拟网络、子网、虚拟网络适配器、NSG 和公共 IP 地址）：

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

#### <a name="add-managed-disk"></a>添加托管磁盘
>[!NOTE]  
>它仅用于在此部分中添加托管磁盘。 

以下三个命令将托管数据磁盘添加到 `$VirtualMachine`中存储的虚拟机。 每个命令指定磁盘的名称和其他属性：

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

以下命令将 OS 磁盘作为托管磁盘添加到 `$VirtualMachine`中存储的虚拟机。

```powershell
# Set OS Disk
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName  `
                                      -CreateOption FromImage -Windows
```

#### <a name="add-unmanaged-disk"></a>添加非托管磁盘

>[!NOTE]  
>本节仅用于添加非托管磁盘。 

接下来的三个命令将三个非托管数据磁盘的路径分配到 `$DataDiskVhdUri01`、`$DataDiskVhdUri02`和 `$DataDiskVhdUri03` 变量。 在 URL 中定义其他路径名称，以将磁盘分发到不同的容器：

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

以下三个命令将数据磁盘添加到 `$VirtualMachine`中存储的虚拟机。 每个命令指定磁盘的名称和其他属性。 每个磁盘的 URI 存储在 `$DataDiskVhdUri01`、`$DataDiskVhdUri02`和 `$DataDiskVhdUri03`中：

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

以下命令将非托管 OS 磁盘添加到 `$VirtualMachine`中存储的虚拟机。

```powershell
# Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
                                      -CreateOption FromImage -Windows
```


#### <a name="create-new-virtual-machine"></a>新建虚拟机
使用以下 PowerShell 命令设置 OS 映像，将网络配置添加到 VM，然后启动新的 VM。

```powershell
#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine -ProvisionVMAgent | `
                  Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
                  -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```


### <a name="add-data-disks-to-an-existing-vm"></a>将数据磁盘添加到**现有**VM
以下示例使用 PowerShell 命令将三个数据磁盘添加到现有 VM。

#### <a name="get-virtual-machine"></a>获取虚拟机

 第一个命令获取名为**VirtualMachine**的 VM，方法是使用**get-azurermvm** cmdlet。 该命令将 VM 存储在 `$VirtualMachine` 变量中：

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

#### <a name="add-managed-disk"></a>添加托管磁盘

>[!NOTE]  
>本节仅用于添加托管磁盘。

接下来的三个命令将托管数据磁盘添加到 `$VirtualMachine` 变量中存储的 VM。 每个命令指定磁盘的名称和其他属性：

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

>[!NOTE]  
>本节仅用于添加非托管磁盘。 

接下来的三个命令将三个数据磁盘的路径分配到 `$DataDiskVhdUri01`、`$DataDiskVhdUri02`和 `$DataDiskVhdUri03` 变量。 VHD Uri 中的不同路径名称表示磁盘放置的不同容器：

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

接下来的三个命令将数据磁盘添加到 `$VirtualMachine` 变量中存储的 VM。 每个命令指定磁盘的名称、位置和其他属性。 每个磁盘的 URI 存储在 `$DataDiskVhdUri01`、`$DataDiskVhdUri02`和 `$DataDiskVhdUri03`中：

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

此命令更新存储在 `-ResourceGroupName`中 `$VirtualMachine` 的 VM 状态：

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Stack Vm，请参阅[Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
