---
title: 在 Azure Stack 中使用 PowerShell 创建 Windows Server 虚拟机 |Microsoft Docs
description: 在 Azure Stack 中使用 PowerShell 创建 Windows Server 虚拟机。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 04b2f2a5e4e9caa8e8eacc47b44c60a6884a6837
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986035"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-powershell-in-azure-stack"></a>快速入门：在 Azure Stack 中使用 PowerShell 创建 Windows Server 虚拟机

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以通过使用 Azure Stack PowerShell 创建 Windows Server 2016 虚拟机 (VM)。 按照在本文中创建和使用 VM 的步骤。 本文还提供了执行以下操作的步骤：

* 连接到远程客户端的 VM。
* 安装 IIS Web 服务器并查看默认主页。
* 清理资源。

> [!NOTE]
>  可以通过 Azure Stack 开发工具包或基于 Windows 的外部客户端（如果已通过 VPN 建立连接）运行本文中所述的步骤。

## <a name="prerequisites-for-windows-server-vm"></a>Windows Server VM 的先决条件

* 确保 Azure Stack 操作员已将“Windows Server 2016”映像添加到 Azure Stack 市场。

* Azure Stack 需要使用特定版本的 Azure PowerShell 来创建和管理资源。 如果未针对 Azure Stack 配置 PowerShell，请遵循[安装](../operator/azure-stack-powershell-install.md) PowerShell 的步骤。

* 设置 Azure Stack PowerShell 后，将需要连接到 Azure Stack 环境。 有关说明，请参阅[以用户身份使用 PowerShell 连接到 Azure Stack](azure-stack-powershell-configure-user.md)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure Stack 资源的逻辑容器。 在开发工具包或 Azure Stack 集成系统中，运行以下代码块创建资源组。 本文档中为所有变量都分配了值，你可以使用这些值或分配新值。

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>创建存储资源

创建存储帐户和存储容器用于存储 Windows Server 2016 映像。

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

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```

## <a name="create-networking-resources"></a>创建网络资源

创建虚拟网络、子网和公共 IP 地址。 这些资源用来与虚拟机建立网络连接。

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>创建网络安全组和网络安全组规则

网络安全组使用入站和出站规则保护虚拟机。 让我们创建端口 3389 的入站规则以允许传入的远程桌面连接，并创建端口 80 的入站规则以允许传入的 Web 流量。

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
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
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
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
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>为虚拟机创建网卡

网卡将虚拟机连接到子网、网络安全组和公共 IP 地址。

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

创建虚拟机配置。 此配置包括部署虚拟机时使用的设置。 例如：凭据、大小和虚拟机映像。

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

若要远程连接到在上一步骤中创建的虚拟机，需要使用其公共 IP 地址。 运行以下命令，获取虚拟机的公共 IP 地址：

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

使用以下命令创建与虚拟机的远程桌面会话。 将 IP 地址替换为虚拟机的 publicIPAddress。 出现提示时，请输入创建虚拟机时使用的用户名和密码。

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>通过 PowerShell 安装 IIS

现已登录到 Azure VM，可以使用单行 PowerShell 安装 IIS，并启用本地防火墙规则以允许 Web 流量。 打开 PowerShell 提示符并运行以下命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

IIS 已安装，并且已打开 VM 上的端口 80，可以使用所选的 Web 浏览器查看默认的 IIS 欢迎页。 请使用前面部分中记录的 *publicIpAddress* 来访问默认页面。

![IIS 默认站点](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-virtual-machine"></a>删除虚拟机

不再有需要时，可使用以下命令删除包含虚拟机及其相关资源的资源组：

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

在本快速入门，我们部署一个简单的 Windows 虚拟机。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
