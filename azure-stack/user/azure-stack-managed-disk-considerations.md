---
title: Azure Stack 中心托管磁盘;差异和注意事项
description: 了解在 Azure Stack 集线器中使用托管磁盘和托管映像时的差异和注意事项。
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: c1369ada357e8439e39fed4642996af86925522c
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883450"
---
# <a name="azure-stack-hub-managed-disks-differences-and-considerations"></a>Azure Stack 集线器托管磁盘：差异和注意事项

本文总结了 Azure 中 Azure Stack 中心和[托管磁盘](/azure/virtual-machines/windows/managed-disks-overview)的[*托管磁盘*](azure-stack-manage-vm-disks.md)之间的差异。 若要了解 Azure Stack 中心与 Azure 之间的高级差异，请参阅[关键注意事项](azure-stack-considerations.md)一文。

托管磁盘通过管理与 VM 磁盘关联的[存储帐户](../operator/azure-stack-manage-storage-accounts.md)，简化 IaaS 虚拟机（vm）的磁盘管理。

> [!NOTE]  
> 从1808更新开始提供 Azure Stack 集线器上的托管磁盘。 从1811更新开始，在使用 Azure Stack 集线器门户创建 Vm 时，默认情况下启用此功能。
  
## <a name="cheat-sheet-managed-disk-differences"></a>备忘单：托管磁盘差异

| 功能 | Azure （全局） | Azure Stack Hub |
| --- | --- | --- |
|静态数据加密 |Azure 存储服务加密（SSE），Azure 磁盘加密（ADE）。     |BitLocker 128 位 AES 加密      |
|图像          | 托管自定义映像 |受支持|
|备份选项 | Azure 备份服务 |尚不支持 |
|灾难恢复选项 | Azure 站点恢复 |尚不支持|
|磁盘类型     |高级 SSD、标准 SSD 和标准 HDD。 |高级 SSD，标准 HDD |
|高级磁盘  |完全支持。 |可以预配，但不能限制性能  |
|高级磁盘 IOPs  |取决于磁盘大小。  |每个磁盘 2300 IOPs |
|高级磁盘吞吐量 |取决于磁盘大小。 |每个磁盘 145 MB/秒 |
|磁盘大小  |Azure 高级磁盘： P4 （32 GiB）到 P80 （32 TiB）<br>Azure 标准 SSD 磁盘： E10 （128 GiB）到 E80 （32 TiB）<br>Azure 标准 HDD 磁盘： S4 （32 GiB）到 S80 （32 TiB） |M4： 32 GiB<br>M6： 64 GiB<br>M10： 128 GiB<br>M15： 256 GiB<br>M20： 512 GiB<br>M30： 1023 GiB |
|磁盘快照复制|将 Azure 托管磁盘连接到支持的运行中 VM 的快照。|尚不支持 |
|磁盘性能分析 |支持聚合度量值和每个磁盘指标。 |尚不支持 |
|迁移      |提供从现有的非托管 Azure 资源管理器 Vm 迁移的工具，而无需重新创建 VM。  |尚不支持 |

> [!NOTE]  
> Azure Stack 集线器中的托管磁盘 IOPs 和吞吐量是 cap 数量，而不是预配的数量，可能会受到 Azure Stack 集线器中运行的硬件和工作负载的影响。

## <a name="metrics"></a>指标

与存储指标之间也有一些差异：

- 利用 Azure Stack Hub，存储度量值中的事务数据不会区分内部或外部的网络带宽。
- 存储度量值中的 Azure Stack 中心事务数据不包括虚拟机对已装载磁盘的访问权限。

## <a name="api-versions"></a>API 版本

Azure Stack 中心托管磁盘支持以下 API 版本：

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>转换为托管磁盘

> [!NOTE]  
> 不能使用 Azure PowerShell cmdlet **Convertto-html convertto-azurermvmmanageddisk 进行转换**将非托管磁盘转换为 Azure Stack 集线器中的托管磁盘。 Azure Stack 中心目前不支持此 cmdlet。

你可以使用以下脚本将当前预配的 VM 从非托管磁盘转换为托管磁盘。 将占位符替换为你自己的值：

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

# The Azure Stack Hub location where the managed disk will be located.
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

Azure Stack 中心支持*托管映像*，这使你能够在通用化 VM （非托管和托管）上创建托管映像对象，这种对象只能创建托管磁盘 vm。 托管映像实现了以下两种方案：

- 已通用化的非托管 Vm，并想要使用托管磁盘。
- 你有一个通用托管 VM，并且想要创建多个类似的托管 Vm。

### <a name="step-1-generalize-the-vm"></a>步骤1：通用化 VM

对于 Windows，请遵循[使用 Sysprep 通用化 WINDOWS VM](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)部分。 对于 Linux，请执行[此处](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm)的步骤1。

> [!NOTE]
> 请确保通用化虚拟机。 从不正确通用化的映像创建 VM 可能会产生**VMProvisioningTimeout**错误。

### <a name="step-2-create-the-managed-image"></a>步骤2：创建托管映像

可以使用门户、PowerShell 或 CLI 创建托管映像。 按照[创建托管映像](/azure/virtual-machines/windows/capture-image-resource)中的步骤操作。

### <a name="step-3-choose-the-use-case"></a>步骤3：选择用例

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>情况1：将非托管 Vm 迁移到托管磁盘

执行此步骤之前，请确保正确通用化 VM。 通用化后，将无法再使用此 VM。 从不正确通用化的映像创建 VM 将导致**VMProvisioningTimeout**错误。

按照[使用存储帐户从 VM 创建映像](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vm-that-uses-a-storage-account)中的说明，从存储帐户中的通用 VHD 创建托管映像。 以后可以使用此映像创建托管 Vm。

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>案例2：使用 Powershell 从托管映像创建托管 VM

使用[PowerShell 从托管磁盘创建映像](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell)中的现有托管磁盘 VM 创建映像后，请使用以下示例脚本通过现有的映像对象创建类似的 Linux VM。

Azure Stack 中心 PowerShell 模块1.7.0 或更高版本：按照[从托管映像创建 VM](/azure/virtual-machines/windows/create-vm-generalized-managed)中的说明进行操作。

Azure Stack 中心 PowerShell 模块1.6.0 或更早版本：

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

你还可以使用门户从托管映像创建 VM。 有关详细信息，请参阅 Azure 托管映像文章[在 azure 中创建通用 VM 的托管映像](/azure/virtual-machines/windows/capture-image-resource)和[从托管映像创建 VM](/azure/virtual-machines/windows/create-vm-generalized-managed)。

## <a name="configuration"></a>配置

应用1808更新或更高版本后，必须在使用托管磁盘之前进行以下配置更改：

- 如果订阅是在1808更新之前创建的，请执行以下步骤来更新订阅。 否则，在此订阅中部署 Vm 可能会失败，并出现错误消息 "磁盘管理器中出现内部错误"。
   1. 在 Azure Stack Hub 用户门户中，请参阅 "**订阅**"，并查找订阅。 依次单击“资源提供程序”、“Microsoft.Compute”、“重新注册”。
   2. 在同一订阅下，中转到 "**访问控制（IAM）** "，并验证是否列出了**Azure Stack 集线器托管磁盘**。
- 如果你使用多租户环境，请根据[此文](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-hub-with-the-guest-directory)中的步骤，要求你的云运营商（可能在你自己的组织中，或从服务提供商处）重新配置每个来宾目录。 否则，在与该来宾目录关联的订阅中部署 Vm 可能会失败，并出现错误消息 "磁盘管理器中出现内部错误"。

## <a name="next-steps"></a>后续步骤

- 了解[Azure Stack 中心虚拟机](azure-stack-compute-overview.md)。
