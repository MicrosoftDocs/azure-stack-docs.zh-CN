---
title: Azure Stack 中托管磁盘和托管映像的差异与注意事项 | Microsoft Docs
description: 了解 Azure Stack 中托管磁盘和托管映像的差异与注意事项。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 9ea7c3de75be447870d0506cebbbbe8af0f2ffe9
ms.sourcegitcommit: 1c4eda123857d714109e38bb853eb1ce49af5f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67648095"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack 托管磁盘：差异与注意事项

本文汇总了 [Azure Stack 托管磁盘](azure-stack-manage-vm-disks.md)与 [Azure 托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)之间的已知差异。 有关 Azure Stack 与 Azure 之间的大致差异的详细信息，请参阅[重要注意事项](azure-stack-considerations.md)一文。

托管磁盘通过管理与 VM 磁盘关联的[存储帐户](../operator/azure-stack-manage-storage-accounts.md)简化了 IaaS VM 的磁盘管理。

> [!NOTE]  
> Azure Stack 上的托管的磁盘是可从 1808年更新开始。 从 1811年更新开始，它是默认情况下启用时使用 Azure Stack 门户创建虚拟机时。
  
## <a name="cheat-sheet-managed-disk-differences"></a>速查表：托管磁盘的差异

| Feature | Azure（公有云） | Azure Stack |
| --- | --- | --- |
|静态数据加密 |Azure 存储服务加密 (SSE)、Azure 磁盘加密 (ADE)     |BitLocker 128 位 AES 加密      |
|Image          | 管理自定义映像 |支持|
|备份选项 | Azure 备份服务 |尚不支持 |
|灾难恢复选项 | Azure Site Recovery |尚不支持|
|磁盘类型     |高级 SSD、标准 SSD 和标准 HDD |高级 SSD、标准 HDD |
|高级磁盘  |完全支持 |可部署，但无性能限制或保证  |
|高级磁盘 IOPS  |取决于磁盘大小  |每个磁盘 2300 IOPS |
|高级磁盘吞吐量 |取决于磁盘大小 |每个磁盘 145 MB/秒 |
|磁盘大小  |Azure 高级磁盘：P4 (32 GiB) 到 P80 (32 TiB)<br>Azure 标准 SSD 磁盘：E10 (128 GiB) 到 E80 (32 TiB)<br>Azure 标准 HDD 磁盘：S4 (32 GiB) 到 S80 (32 TiB) |M4：32 GiB<br>M6：64 GiB<br>M10：128 GiB<br>M15：256 GiB<br>M20：512 GiB<br>M30：1024 GiB |
|磁盘快照复制|支持附加到正在运行的 VM 的快照 Azure 托管磁盘|快照 Azure 托管磁盘附加到正在运行 VM 尚不支持 |
|磁盘性能分析 |支持的聚合指标和每磁盘指标 |尚不支持 |
|迁移      |提供从现有非托管 Azure 资源管理器 VM 迁移的工具，而无需重新创建 VM  |尚不支持 |

> [!NOTE]  
> Azure Stack 中的托管磁盘 IOPs 和吞吐量是一个上限数字而非预配的数字，这可能会受在 Azure Stack 中运行的硬件和工作负荷影响。

## <a name="metrics"></a>度量值

存储指标也有一些差异：

- 使用 Azure Stack，存储指标中的事务数据不区分内部或外部网络带宽。
- 存储指标中的 Azure Stack 事务数据不包含虚拟机对所装载磁盘的访问。

## <a name="api-versions"></a>API 版本

Azure Stack 托管磁盘支持以下 API 版本：

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>转换为托管磁盘

> [!NOTE]  
> Azure PowerShell cmdlet **Convertto-azurermvmmanageddisk**不能用于将非托管的磁盘转换为 Azure Stack 中的托管磁盘。 Azure Stack 尚不支持此 cmdlet。

可以使用以下脚本将当前预配的 VM 从非托管磁盘转换为托管磁盘。 将占位符替换成自己的值：

```powershell
$SubscriptionId = "SubId"

# The name of your resource group where your VM to be converted exists.
$ResourceGroupName ="MyResourceGroup"

# The name of the managed disk to be created.
$DiskName = "mngddisk"

# The size of the disks in GB. It should be greater than the VHD file size.
$DiskSize = "50"

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$VhdUri = "https://rgmgddisks347.blob.local.azurestack.external/vhds/unmngdvm20181109013817.vhd"

# The storage type for the managed disk: PremiumLRS or StandardLRS.
$AccountType = "StandardLRS"

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM configuration (network config, VM name, location).
$Location = "local"
$VirtualMachineName = "unmngdvm"
$VirtualMachineSize = "Standard_D1"
$PIpName = "unmngdvm-ip"
$VirtualNetworkName = "unmngdrg-vnet"
$NicName = "unmngdvm"

# Set the context to the subscription ID in which the managed disk will be created.
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

# Delete old VM, but keep the OS disk.
Remove-AzureRmVm -Name $VirtualMachineName -ResourceGroupName $ResourceGroupName

# Create the managed disk configuration.
$DiskConfig = New-AzureRmDiskConfig -AccountType $AccountType -Location $Location -DiskSizeGB $DiskSize -SourceUri $VhdUri -CreateOption Import

# Create managed disk.
New-AzureRmDisk -DiskName $DiskName -Disk $DiskConfig -ResourceGroupName $resourceGroupName
$Disk = Get-AzureRmDisk -DiskName $DiskName -ResourceGroupName $ResourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize $VirtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to "-Windows" if the OS disk has the Windows OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $Disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM.
$PublicIp = Get-AzureRmPublicIpAddress -Name $PIpName -ResourceGroupName $ResourceGroupName

# Get the virtual network where the virtual machine will be hosted.
$VNet = Get-AzureRmVirtualNetwork -Name $VirtualNetworkName -ResourceGroupName $ResourceGroupName

# Create NIC in the first subnet of the virtual network.
$Nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Nic.Id

# Create the virtual machine with managed disk.
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $ResourceGroupName -Location $Location
```

## <a name="managed-images"></a>托管映像

Azure Stack 支持托管映像，可让你在通用化 VM（非托管和托管的 VM）上创建托管映像对象，以后只能创建托管磁盘 VM。  托管映像可实现以下两种方案：

- 你有通用化的非托管 VM，后来想要使用托管磁盘。
- 你有通用化的托管 VM，并想要创建多个类似的托管 VM。

### <a name="step-1-generalize-the-vm"></a>步骤 1：一般化 VM

对于 Windows，请遵循[使用 Sysprep 通用化 Windows VM](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) 部分操作。 对于 Linux，请遵循[此处](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm)所述的步骤 1。

> [!NOTE]
> 请务必将 VM 通用化。 基于未正确通用化的映像创建 VM 会导致 **VMProvisioningTimeout** 错误。

### <a name="step-2-create-the-managed-image"></a>步骤 2：创建托管映像

可以使用门户、 PowerShell 或 CLI 创建托管的映像。 按照中的步骤[创建托管的映像](/azure/virtual-machines/windows/capture-image-resource)。

### <a name="step-3-choose-the-use-case"></a>步骤 3：选择用例

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>案例 1：将非托管 VM 迁移到托管磁盘

执行此步骤之前，请务必正确通用化 VM。 通用化之后，不再可以使用此 VM。 基于未正确通用化的映像创建 VM 会导致 **VMProvisioningTimeout** 错误。

按照中的说明[从存储帐户中的 VHD 创建映像](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vhd-in-a-storage-account)从存储帐户中的通用 VHD 创建托管的映像。 以后可以使用此映像创建托管 VM。

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>案例 2：使用 PowerShell 基于托管映像创建托管 VM

创建从现有的映像托管后磁盘使用中的脚本的 VM[从托管磁盘使用 PowerShell 创建映像](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell)，下面的示例脚本从现有的图像对象创建类似的 Linux VM。

Azure Stack PowerShell 模块 1.7.0 或更高版本： 按照中的说明[从托管映像创建 VM](/azure/virtual-machines/windows/create-vm-generalized-managed)。

Azure Stack PowerShell 模块 1.6.0 或更低版本：

```powershell
# Variables for common values
$ResourceGroupName = "MyResourceGroup"
$Location = "local"
$VirtualMachineName = "MyVM"
$ImageRG = "managedlinuxrg"
$ImageName = "simplelinuxvmm-image-2019122"

# Create credential object
$Cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a subnet configuration
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "MySubnet" -AddressPrefix "192.168.1.0/24"

# Create a virtual network
$VNet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyVNet" -AddressPrefix "192.168.0.0/16" -Subnet $SubnetConfig

# Create a public IP address and specify a DNS name
$PIp = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "MyNetworkSecurityGroupRuleRDP"  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyNetworkSecurityGroup" -SecurityRules $NsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$Nic = New-AzureRmNetworkInterface -Name "MyNic" -ResourceGroupName $ResourceGroupName -Location $Location `
  -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PIp.Id -NetworkSecurityGroupId $Nsg.Id

$Image = Get-AzureRmImage -ResourceGroupName $ImageRG -ImageName $ImageName

# Create a virtual machine configuration
$VmConfig = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $VirtualMachineName -Credential $Cred | `
Set-AzureRmVMSourceImage -Id $Image.Id | `
Add-AzureRmVMNetworkInterface -Id $Nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VmConfig
```

也可以使用门户基于托管映像创建 VM。 有关详细信息，请参阅 Azure 托管映像文章：[在 Azure 中创建通用化 VM 的托管映像](/azure/virtual-machines/windows/capture-image-resource)和[从托管映像创建 VM](/azure/virtual-machines/windows/create-vm-generalized-managed)。

## <a name="configuration"></a>配置

应用 1808 或更高版本的更新后，必须先执行以下配置才能使用托管磁盘：

- 如果订阅是在应用 1808 更新之前创建的，请遵循以下步骤来更新订阅。 否则，在此订阅中部署 VM 可能会失败，并出现错误消息“磁盘管理器发生内部错误。”
   1. 在 Azure Stack 用户门户中，转到**订阅**和找到的订阅。 依次单击“资源提供程序”、“Microsoft.Compute”  、“重新注册”  。 
   2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“Azure Stack - 托管磁盘”是否已列出。  
- 如果使用多租户环境，请让云操作员（可以是组织内部或来自服务提供商的操作员）根据[此文](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步骤重新配置每个来宾目录。 否则，在与该来宾目录相关联的订阅中部署 Vm 可能会失败并显示错误消息，**磁盘管理器中的出现内部错误**。

## <a name="next-steps"></a>后续步骤

- [了解 Azure Stack 虚拟机](azure-stack-compute-overview.md)
