---
title: 使用 Windows PowerShell 创建 Azure Stack HCI 群集
description: 了解如何使用 Windows PowerShell 为 Azure Stack HCI 创建群集
author: v-dasis
ms.topic: how-to
ms.date: 01/22/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: f45a77b43178b38d659d9e51b1abf2cbaeae87f8
ms.sourcegitcommit: ea4bb7bf0ba1bd642c769013a0280f24e71550bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98717975"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-powershell"></a>使用 Windows PowerShell 创建 Azure Stack HCI 群集

> 适用于：Azure Stack HCI，版本 v20H2

本文介绍如何使用 Windows PowerShell 创建使用存储空间直通的 Azure Stack HCI 超融合群集。 如果希望使用 Windows Admin Center 中的群集创建向导来创建群集，请参阅使用 [Windows Admin Center 创建群集](create-cluster.md)。

有两种可选的群集类型：

- 包含至少两个服务器节点的标准群集，节点全部位于单个站点中。
- 包含至少四个跨两个站点的服务器节点的延伸群集，每个站点有两个节点。

在本文中，我们将创建一个名为 Cluster1 的示例群集，该群集由四个名为 Server1、Server2、Server3 和服务器4的服务器节点组成。

对于延伸群集方案，我们将使用 ClusterS1 作为名称，并使用跨站点 Site1 和 Site2 的四个相同服务器节点。

有关延伸群集的更多详细信息，请参阅[延伸群集概述](../concepts/stretched-clusters.md)。

如果你对 Azure Stack HCI 的测试感兴趣，但有限制或没有备用硬件，请查看 [AZURE STACK HCI 评估指南](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md)，其中介绍了如何使用嵌套虚拟化（在 Azure 中）或本地单一物理系统上的 Azure Stack HCI。

## <a name="before-you-begin"></a>在开始之前

在开始之前，请确保：

- 已阅读 [Azure Stack HCI 系统需求](../concepts/system-requirements.md)。
- 在群集中的每个服务器上安装 Azure Stack HCI OS。 请参阅[部署 Azure Stack HCI 操作系统](operating-system.md)。
- 拥有一个帐户，该帐户是每个服务器上的本地管理员组的成员。
- 具有在 Active Directory 中创建对象的权限。

## <a name="using-windows-powershell"></a>使用 Windows PowerShell

可以在主机服务器上的 RDP 会话中本地运行 PowerShell，也可以从管理计算机远程运行 PowerShell。 本文会介绍远程选项。

从某一管理计算机运行 PowerShell 时，请将 `-Name` 或 `-Cluster` 参数与所管理的服务器或群集的名称配合使用。 此外，在将 `-ComputerName` 参数用于服务器节点时，可能需要指定完全限定的域名 (FQDN)。

还将需要用于 Hyper-V 和故障转移群集的远程服务器管理工​​具 (RSAT) cmdlet 和 PowerShell 模块。 如果管理计算机上的 PowerShell 会话中尚未提供这些项，则可以使用以下命令添加它们：`Add-WindowsFeature RSAT-Clustering-PowerShell`。

## <a name="step-1-provision-the-servers"></a>步骤 1：预配服务器

首先，我们将连接到每个服务器，将它们加入域（管理计算机所在的域），并安装所需的角色和功能。

### <a name="step-11-connect-to-the-servers"></a>步骤 1.1：连接到服务器

若要连接到服务器，首先必须具有网络连接，加入到同一个域或完全受信任的域中，并具有对服务器的本地管理权限。

打开 PowerShell，使用完全限定的域名或要连接到的服务器的 IP 地址。 在每个服务器上运行以下命令后，系统会提示输入密码。 

在此示例中，我们假定服务器已命名为 Server1、Server2、Server3 和服务器4：

   ```powershell
   Enter-PSSession -ComputerName "Server1" -Credential "Server1\Administrator"
   ```

下面是执行相同操作的另一个示例：

   ```powershell
   $myServer1 = "Server1"
   $user = "$myServer1\Administrator"

   Enter-PSSession -ComputerName $myServer1 -Credential $user
   ```

> [!TIP]
> 从管理电脑运行 PowerShell 命令时，可能会收到类似“WinRM 无法处理该请求”的错误。 若要解决此问题，请使用 PowerShell 将每个服务器添加到管理计算机上的“受信任的主机”列表。 此列表支持通配符，如 `Server*`。
>
> `Set-Item WSMAN:\Localhost\Client\TrustedHosts -Value Server1 -Force`
>  
> 若要查看受信任的主机列表，请键入 `Get-Item WSMAN:\Localhost\Client\TrustedHosts`。  
>
> 若要清空该列表，请键入 `Clear-Item WSMAN:\Localhost\Client\TrustedHost`。  

### <a name="step-12-join-the-domain-and-add-domain-accounts"></a>步骤 1.2：加入域并添加域帐户

到目前为止，你已使用本地管理员帐户 `<ServerName>\Administrator` 连接到每个服务器节点。

若要继续，需要将服务器加入域，并使用每个服务器上本地管理员组中的域帐户。

使用 `Enter-PSSession` cmdlet 连接到每个服务器并运行以下 cmdlet，并替换服务器名称、域名和域凭据：

```powershell  
Add-Computer -NewName "Server1" -DomainName "contoso.com" -Credential "Contoso\User" -Restart -Force  
```

如果你的管理员帐户不是域管理员组的成员，则请将你的管理员帐户添加到每个服务器上的本地管理员组中，或者更好的是，添加为管理员使用的组。 可使用以下命令执行此操作：

```powershell
Add-LocalGroupMember -Group "Administrators" -Member "king@contoso.local"
```

### <a name="step-13-install-roles-and-features"></a>步骤 1.3：安装角色和功能

下一步是在群集的每个服务器上安装所需的 Windows 角色和功能。 下面是要安装的角色：

- BitLocker
- 数据中心桥接（适用于 RoCEv2 网络适配器）
- 故障转移群集
- 文件服务器
- FS-Data-Deduplication 模块
- Hyper-V
- RSAT-AD-PowerShell 模块
- 延伸群集的存储副本 () 

对于每个服务器，请使用以下命令：

```powershell
Install-WindowsFeature -ComputerName "Server1" -Name "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-AD-Powershell", "RSAT-Clustering-PowerShell", "Storage-Replica" -IncludeAllSubFeature -IncludeManagementTools
```

若要同时在群集中的所有服务器上运行该命令，请使用以下脚本，修改开头的变量列表以适应你的环境。

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"
$FeatureList = "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-AD-Powershell", "RSAT-Clustering-PowerShell", "Storage-Replica"

# This part runs the Install-WindowsFeature cmdlet on all servers in $ServerList, passing the list of features in $FeatureList.
Invoke-Command ($ServerList) {
    Install-WindowsFeature -Name $Using:Featurelist -IncludeAllSubFeature -IncludeManagementTools
}
```
接下来，重启所有服务器：

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Restart-Computer -ComputerName $ServerList -WSManAuthentication Kerberos
```

## <a name="step-2-configure-networking"></a>步骤 2：配置网络

此步骤将配置环境中的各种网络元素。

### <a name="disable-unused-networks"></a>禁用未使用的网络

必须禁用所有断开连接的，或不用于管理、存储或工作负载流量的网络（如 VM）。 下面介绍如何识别未使用的网络：

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-NetAdapter -CimSession $ServerList | Where-Object Status -eq Disconnected
```
下面介绍如何禁用它们：

```powershell
Get-NetAdapter -CimSession $ServerList | Where-Object Status -eq Disconnected | Disable-NetAdapter -Confirm:$False
```

### <a name="create-virtual-switches"></a>创建虚拟交换机

群集中的每个服务器节点都需要一个虚拟交换机。 在以下示例中，使用已连接的网络适配器（状态为“已启动”）创建了具有 SR-IOV 功能的虚拟交换机。 启用 S​​R-IOV 可能也很有用，因为启用了 RDMA 的 vmNIC（VM 的 vNIC）需要 SR-IOV。

所有网络适配器都必须相同才能将 NIC 组合在一起。

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
```

创建虚拟交换机，方法如下：

```powershell
Invoke-Command -ComputerName $ServerList -ScriptBlock {New-VMSwitch -Name $using:vSwitchName -EnableEmbeddedTeaming $TRUE -EnableIov $true -NetAdapterName (Get-NetAdapter | Where-Object Status -eq Up ).InterfaceAlias}
```

现在，请验证是否已成功创建交换机：

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-VMSwitch -CimSession $ServerList | Select-Object Name, IOVEnabled, IOVS*
Get-VMSwitchTeam -CimSession $ServerList
```

### <a name="assign-virtual-network-adapters"></a>分配虚拟网络适配器

接下来，你将分配用于管理的和其余流量的虚拟网络适配器 (vNIC)，如以下示例中所示。 需要为群集管理配置至少一个网络适配器。

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
Rename-VMNetworkAdapter -ManagementOS -Name $vSwitchName -NewName Management -ComputerName $ServerList
Add-VMNetworkAdapter -ManagementOS -Name SMB01 -SwitchName $vSwitchName -CimSession $ServerList
Add-VMNetworkAdapter -ManagementOS -Name SMB02 -SwitchName $vSwitchName -Cimsession $ServerList
```

并验证是否已成功添加和分配网络适配器：

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-VMNetworkAdapter -CimSession $ServerList -ManagementOS
```

### <a name="configure-ip-addresses-and-vlans"></a>配置 IP 地址和 VLAN

你可以配置一个或两个子网。 如果要防止交换机互连重载，则首选使用两个子网。 例如，SMB 存储流量将保留在专用于一个物理交换机的子网中。

> [!NOTE]
> 配置 IP 地址时，连接可能会中断几分钟，因为在获取新 IP 地址时，可能会连接到其中一个虚拟适配器。

#### <a name="obtain-network-interface-information"></a>获取网络接口信息

在为网络接口卡设置 IP 地址之前，首先需要了解一些信息，例如接口索引 (`ifIndex`)、`Interface Alias` 和 `Address Family`。 为每个服务器节点写下这些内容，以备日后使用。

运行以下内容：

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-NetIPInterface -CimSession $ServerList
```

#### <a name="configure-one-subnet"></a>配置一个子网

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
$StorNet="172.16.1."
$StorVLAN=1
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $ServerList){
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    $IP++
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN -Access -ManagementOS -CimSession $ServerList
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $ServerList
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $ServerList
```

#### <a name="configure-two-subnets"></a>配置两个子网

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
$StorNet1="172.16.1."
$StorNet2="172.16.2."
$StorVLAN1=1
$StorVLAN2=2
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $ServerList){
    New-NetIPAddress -IPAddress ($StorNet1+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    New-NetIPAddress -IPAddress ($StorNet2+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN1 -Access -ManagementOS -CimSession $ServerList
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN2 -Access -ManagementOS -CimSession $ServerList
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="verify-vlan-ids-and-subnets"></a>验证 VLAN ID 和子网

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
#verify ip config
Get-NetIPAddress -CimSession $ServerList -InterfaceAlias vEthernet* -AddressFamily IPv4 | Sort-Object -Property PSComputername | ft pscomputername,interfacealias,ipaddress -AutoSize -GroupBy PSComputerName

#Verify that the VlanID is set
Get-VMNetworkAdapterVlan -ManagementOS -CimSession $ServerList | Sort-Object -Property Computername | Format-Table ComputerName,AccessVlanID,ParentAdapter -AutoSize -GroupBy ComputerName
```

## <a name="step-3-prep-for-cluster-setup"></a>步骤 3：准备群集设置

接下来，请验证服务器是否已准备好进行群集。 首先，作为健全性检查，请考虑运行以下命令以确保服务器尚未属于群集：

使用 `Get-ClusterNode` 显示所有节点：

```powershell
Get-ClusterNode
```

使用 `Get-ClusterResource` 显示所有群集节点：

```powershell
Get-ClusterResource
```

使用 `Get-ClusterNetwork` 显示所有群集网络：

```powershell
Get-ClusterNetwork
```

### <a name="step-31-prepare-drives"></a>步骤 3.1：准备驱动器

启用存储空间直通之前，请确保驱动器为空。 运行以下脚本以删除所有旧分区或其他数据。

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"

Invoke-Command ($ServerList) {
    Update-StorageProviderCache
    Get-StoragePool | ? IsPrimordial -eq $false | Set-StoragePool -IsReadOnly:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Remove-StoragePool -Confirm:$false -ErrorAction SilentlyContinue
    Get-PhysicalDisk | Reset-PhysicalDisk -ErrorAction SilentlyContinue
    Get-Disk | ? Number -ne $null | ? IsBoot -ne $true | ? IsSystem -ne $true | ? PartitionStyle -ne RAW | % {
        $_ | Set-Disk -isoffline:$false
        $_ | Set-Disk -isreadonly:$false
        $_ | Clear-Disk -RemoveData -RemoveOEM -Confirm:$false
        $_ | Set-Disk -isreadonly:$true
        $_ | Set-Disk -isoffline:$true
    }
    Get-Disk | Where Number -Ne $Null | Where IsBoot -Ne $True | Where IsSystem -Ne $True | Where PartitionStyle -Eq RAW | Group -NoElement -Property FriendlyName
} | Sort -Property PsComputerName, Count
```

### <a name="step-32-test-cluster-configuration"></a>步骤 3.2：测试群集配置

在此步骤中，你需确保服务器节点已正确配置以创建群集。 `Test-Cluster` cmdlet 用于运行测试，以验证你的配置是否适合用作超融合群集。 下面的示例使用 `-Include` 参数，并指定了特定类别的测试。 这可确保验证中包含正确的测试。

```powershell
Test-Cluster –Node $ServerList –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
```

## <a name="step-4-create-the-cluster"></a>步骤 4：创建群集

现在，你可以使用在前面的步骤中已验证的服务器节点来创建群集。

创建群集时，你将收到一条显示 `"There were issues while creating the clustered role that may prevent it from starting. For more information, view the report file below."` 的警告，你可以放心地忽略此警告。 这是因为没有可供稍后创建的群集见证使用的磁盘。 

> [!NOTE]
> 如果服务器使用的是静态 IP 地址，请通过添加以下参数并指定 IP 地址来修改以下命令以反映静态 IP 地址：`–StaticAddress <X.X.X.X>;`。

```powershell
$ClusterName="cluster1" New-Cluster -Name $ClusterName –Node $ServerList –nostorage
```

恭喜，你的群集现已成功创建。

创建群集后，群集名称可能需要一些时间才能通过 DNS 跨域复制，尤其是在新添加到 Active Directory 的情况下。 虽然群集可能显示在 Windows Admin Center 中，但可能尚不能连接到它。

确保所有群集资源处于联机状态的一个好的检查：

```powershell
Get-Cluster -Name $ClusterName | Get-ClusterResource
```

如果一段时间后解析群集未成功，则在大多数情况下，你可以使用其中一个群集服务器的名称代替群集名称进行连接。

## <a name="step-5-set-up-sites-stretched-cluster"></a>步骤 5：设置站点（延伸群集）

此任务仅适用于在两个站点之间创建延伸群集的情况。 

> [!NOTE]
> 如果你预先设置了 Active Directory 站点和服务，则无需手动创建站点，如下所述。

### <a name="step-51-create-sites"></a>步骤 5.1：创建站点

在下面的 cmdlet 中，“FaultDomain”只是站点的另一个名称。 本示例使用“ClusterS1”作为延伸群集的名称。

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site1"
```

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site2"
```

使用 `Get-ClusterFaultDomain` cmdlet 验证是否已为群集创建了两个站点。

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-52-assign-server-nodes"></a>步骤 5.2：分配服务器节点

接下来，我们将四个服务器节点分配给其各自的站点。 在下面的示例中，Server1 和 Server2 分配给 Site1，而 Server3 和 Server4 分配到 Site2。

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server1", "Server2" -Parent "Site1"
```

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server3", "Server4" -Parent "Site2"
```

使用 `Get-ClusterFaultDomain` cmdlet 验证节点是否已在正确的站点中。

```powershell
Get-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-53-set-a-preferred-site"></a>步骤 5.3：设置首选站点

你还可以定义全局首选站点，这意味着指定的资源和组必须在首选站点上运行。  可使用以下命令在站点级别定义此设置：  

```powershell
(Get-Cluster).PreferredSite = "Site1"
```

为延伸群集指定首选站点具有以下优势：

- **冷启动** - 冷启动期间，虚拟机将放置在首选站点中

- **仲裁投票**
  - 使用动态仲裁时，将首先从被动（复制）站点减少权重，以确保在所有其他条件都相同的情况下，首选站点仍然存在。 此外，在发生诸如非对称网络连接失败一类的事件后，在重组期间，将首先从被动站点中删除服务器节点。

  - 在两个站点的仲裁拆分期间，如果无法联系群集见证，则会自动选择首选站点获胜。 然后，被动站点中的服务器节点会退出群集成员资格。 这使群集可以承受同时产生的 50% 的投票损失。

还可以在群集角色或组级别配置首选站点。 在这种情况下，可以为每个虚拟机组配置不同的首选站点。 这会使站点处于活动状态，且使其对于特定虚拟机而言是首选站点。

## <a name="step-6-enable-storage-spaces-direct"></a>步骤 6：启用存储空间直通

创建群集后，请使用 `Enable-ClusterStorageSpacesDirect` cmdlet，该 cmdlet 将启用存储空间直通并自动执行以下操作：

- **创建存储池：** 为群集创建存储池，其名称类似于“Cluster1 存储池”。

- **创建群集性能历史记录磁盘：** 在存储池中创建群集性能历史记录虚拟磁盘。

- **创建数据和日志卷：** 在存储池中创建数据卷和日志卷。

- **配置存储空间直通缓存：** 如果存在多个可用于存储空间直通的介质（驱动器）类型，则它将启用最快的介质作为缓存设备（在大多数情况下为读取和写入）。

- **创建层：** 创建两个层作为默认层。 其中一个称为“容量”，另一个称为“性能”。 cmdlet 通过组合设备类型和复原能力来分析设备并配置每个层。

对于延伸群集，`Enable-ClusterStorageSpacesDirect` cmdlet 还将执行以下操作：

- 检查是否已设置站点
- 确定哪些节点位于哪些站点中
- 确定每个节点具有的可用存储
- 检查是否在每个节点上均安装了存储副本功能
- 为每个站点创建一个存储池，并使用站点名称标识它
- 创建每个存储池中的数据和日志卷 - 每个站点一个

以下命令可启用存储空间直通。 你还可以指定存储池的易记名称，如下所示：

```powershell
Enable-ClusterStorageSpacesDirect -PoolFriendlyName "$ClusterName Storage Pool" -CimSession $ClusterName
```

若要查看存储池，请使用：

```powershell
Get-StoragePool -CimSession $session
```

恭喜，现已创建了一个群集。

## <a name="after-you-create-the-cluster"></a>创建群集后

创建完成后，仍需要完成一些重要任务：

- 设置群集见证。 请参阅[设置群集见证](witness.md)。
- 创建卷。 请参阅[创建卷](../manage/create-volumes.md)。
- 对于延伸群集，请使用存储副本创建卷并设置复制。 请参阅[为延伸群集创建卷和设置复制](../manage/create-stretched-volumes.md)。

## <a name="next-steps"></a>后续步骤

- 将群集注册到 Azure。 请参阅[管理 Azure 注册](../manage/manage-azure-registration.md)。
- 对群集进行最终验证。 请参阅[验证 Azure Stack HCI 群集](validate.md)
