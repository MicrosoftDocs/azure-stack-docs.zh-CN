---
title: 在 Azure Stack Hub 中使用 PowerShell 创建 Windows Server VM
description: 在 Azure Stack Hub 中使用 PowerShell 创建 Windows Server VM。
author: mattbriggs
ms.topic: quickstart
ms.date: 02/18/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 02/18/2021
ms.custom: conteperfq4
ms.openlocfilehash: 7df01aeab1ef8df1e6ea8593d4f25c741f6afc24
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840790"
---
# <a name="quickstart-create-a-windows-server-vm-by-using-powershell-in-azure-stack-hub"></a>快速入门：在 Azure Stack Hub 中使用 PowerShell 创建 Windows Server VM

可以使用 Azure Stack Hub PowerShell 创建 Windows Server 2016 虚拟机 (VM)。 请按照本文中的步骤创建和使用 VM。 本文还提供了执行以下操作的步骤：

* 通过远程客户端连接到 VM。
* 安装 IIS Web 服务器并查看默认主页。
* 清理资源。

> [!NOTE]
>  可以通过 Azure Stack 开发工具包或基于 Windows 的外部客户端（如果已通过 VPN 建立连接）运行本文中所述的步骤。

## <a name="prerequisites-for-windows-server-vm"></a>Windows Server VM 的先决条件

* 确保 Azure Stack Hub 操作员已将“Windows Server 2016”  映像添加到 Azure Stack Hub 市场。

* Azure Stack Hub 需要使用特定版本的 Azure PowerShell 来创建和管理资源。 如果未针对 Azure Stack Hub 配置 PowerShell，请遵循[安装](../operator/powershell-install-az-module.md) PowerShell 的步骤。

* 设置 Azure Stack Hub PowerShell 后，将需要连接到 Azure Stack Hub 环境。 有关说明，请参阅[以用户身份使用 PowerShell 连接到 Azure Stack Hub](azure-stack-powershell-configure-user.md)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure Stack Hub 资源的逻辑容器。 在开发工具包或 Azure Stack Hub 集成系统中，运行以下代码块创建资源组。 

> [!NOTE]
> 代码示例中为所有变量都分配了值。 但是，如果愿意，也可以分配新值。

### <a name="az-modules"></a>[Az 模块](#tab/az1)

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm1)

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRMResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```
---



## <a name="create-storage-resources"></a>创建存储资源

创建存储帐户以存储启动诊断的输出。

### <a name="az-modules"></a>[Az 模块](#tab/az2)

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm2)

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRMCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```
---



## <a name="create-networking-resources"></a>创建网络资源

创建虚拟网络、子网和公共 IP 地址。 这些资源用来与 VM 建立网络连接。

### <a name="az-modules"></a>[Az 模块](#tab/az3)

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm3)

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRMVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRMVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRMPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```
---



### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>创建网络安全组和网络安全组规则

网络安全组使用入站和出站规则来保护 VM。 让我们创建端口 3389 的入站规则以允许传入的远程桌面连接，并创建端口 80 的入站规则以允许传入的 Web 流量。

### <a name="az-modules"></a>[Az 模块](#tab/az4)

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm4)

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRMNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRMNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRMNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```
---



### <a name="create-a-network-card-for-the-vm"></a>为 VM 创建网卡

网卡将 VM 连接到子网、网络安全组和公共 IP 地址。

### <a name="az-modules"></a>[Az 模块](#tab/az5)

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm5)

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRMNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```
---



## <a name="create-a-vm"></a>创建 VM

创建 VM 配置。 此配置包括部署 VM 时使用的设置。 例如：凭据、大小和 VM 映像。

### <a name="az-modules"></a>[Az 模块](#tab/az6)

```powershell
# Define a credential object to store the username and password for the VM
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential -ProvisionVMAgent

$VirtualMachine = Set-AzVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a VM.
$VirtualMachine = Set-AzVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzVMBootDiagnostic -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzVMNetworkInterface -Id $nic.Id


# Create the VM.
New-AzVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm6)

```powershell
# Define a credential object to store the username and password for the VM
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRMVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRMVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential -ProvisionVMAgent

$VirtualMachine = Set-AzureRMVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a VM.
$VirtualMachine = Set-AzureRMVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRMVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRMVMNetworkInterface -Id $nic.Id


# Create the VM.
New-AzureRMVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```
---



## <a name="connect-to-the-vm"></a>连接到 VM

若要远程连接到在上一步骤中创建的 VM，需要使用其公共 IP 地址。 运行以下命令，获取 VM 的公共 IP 地址：

### <a name="az-modules"></a>[Az 模块](#tab/az7)


```powershell
Get-AzPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm7)


```powershell
Get-AzureRMPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```
---


使用以下命令创建与 VM 的远程桌面会话。 将 IP 地址替换为你的 VM 的 *publicIPAddress*。 出现提示时，请输入创建 VM 时使用的用户名和密码。


```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>通过 PowerShell 安装 IIS

登录到 Azure VM 后，可以使用单行 PowerShell 安装 IIS，并启用本地防火墙规则以允许 Web 流量。 打开 PowerShell 提示符并运行以下命令：


```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

IIS 已安装，并且已打开 VM 上的端口 80，可以使用任何浏览器查看默认的 IIS 欢迎页。 请使用前面部分中记录的 *publicIpAddress* 来访问默认页面。

![IIS 默认站点](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-vm"></a>删除 VM

不再有需要时，可使用以下命令删除包含 VM 及其相关资源的资源组：

### <a name="az-modules"></a>[Az 模块](#tab/az8)

```powershell
Remove-AzResourceGroup `
  -Name $ResourceGroupName
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm8)
 ```powershell
Remove-AzureRMResourceGroup `
  -Name $ResourceGroupName
```
---



## <a name="next-steps"></a>后续步骤

在本快速入门中，我们部署了一个简单的 Windows VM。 若要详细了解 Azure Stack Hub VM，请继续阅读 [Azure Stack Hub VM 功能](azure-stack-vm-considerations.md)。
