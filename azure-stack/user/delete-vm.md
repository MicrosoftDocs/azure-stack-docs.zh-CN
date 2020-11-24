---
title: 删除 Azure Stack 集线器上具有依赖关系的 VM
description: 如何删除 Azure Stack Hub 上具有依赖项的 VM（虚拟计算机）
author: mattbriggs
ms.topic: how-to
ms.date: 11/22/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: f9e32351dbc73b42e51c485c8e2eb39d4226ea27
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518205"
---
# <a name="how-to-delete-a-vm-virtual-machine-with-dependencies-on-azure-stack-hub"></a>如何删除 Azure Stack Hub 上具有依赖项的 VM（虚拟计算机）

本文介绍了在 Azure Stack Hub 中删除 VM 及其依赖项的步骤。

如果从 Azure Stack Hub 中删除了 VM，相关的组件依赖项（即数据磁盘、虚拟网络接口和诊断容器）仍会保留在资源组中。 这些项不会随 OS 光盘一起自动删除。

## <a name="delete-a-vm-overview"></a>“删除 VM”概述

创建新 VM 时，通常会创建一个新资源组，并在该资源组中放入所有依赖项。 如果想删除 VM 及其所有依赖项，可以删除整个资源组。 Azure 资源管理器将处理依赖项以成功删除它们。 有时无法通过删除资源组来删除 VM。 例如，VM 可能包含某些资源，这些资源不是 VM 的依赖项，但你希望保留。

## <a name="delete-a-vm-with-dependencies"></a>删除带依赖项的 VM

### <a name="with-the-portal"></a>[使用门户](#tab/portal)

如果无法删除资源组，要么依赖项不在同一资源组中，要么存在其他资源，请按照以下步骤操作：

1. 打开 Azure Stack 用户门户。

2. 选择“虚拟机”。 查找虚拟机，然后选择计算机以打开虚拟机边栏选项卡。  
![删除带依赖项的 VM](./media/delete-vm/azure-stack-hub-delete-vm-portal.png)  

3. 记下包含 VM 和 VM 依赖项的资源组。

4. 选择“网络”并记下网络接口。

5. 选择“磁盘”并记下 OS 磁盘和数据磁盘。

6. 返回到“虚拟机”边栏选项卡，并选择“删除” 。

7. 键入 `yes` 以确认删除，然后选择“删除”。

7. 选择“资源组”，然后选择资源组。

8. 通过手动选择依赖项然后选择“删除”来删除依赖项。
    1. 键入 `yes` 以确认删除，然后选择“删除”。
    2. 等待资源完全删除。
    3. 然后，你可以删除下一个依赖项。

### <a name="with-powershell"></a>[使用 PowerShell](#tab/ps-az)

如果无法删除资源组，要么依赖项不在同一资源组中，要么存在其他资源，请按照以下步骤操作。

连接到 Azure Stack Hub 环境，然后使用 VM 名称和资源组更新以下变量。 有关将 PowerShell 会话到 Azure Stack Hub 的说明，请参阅[以用户身份使用 PowerShell 连接到 Azure Stack Hub](azure-stack-powershell-configure-user.md)。

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzVM -Name $machineName -ResourceGroupName $resGroupName
```

检索依赖项的 VM 信息和名称。 请在同一会话中运行以下 cmdlet：

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

删除启动诊断存储容器。 如果计算机名称短于 9 个字符，则需要在创建 `$diagContainer` 变量时将索引更改为 substring 中的字符串长度。 

请在同一会话中运行以下 cmdlet：

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

删除虚拟网络接口。

```powershell
$machine | Remove-AzNetworkInterface -Force
```

删除操作系统磁盘。

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

删除附加到 VM 的数据磁盘。

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

最后，删除 VM。 该 cmdlet 需要一些时间才能运行。 可以通过查看 PowerShell 中的 VM 对象来审核附加到 VM 的组件。 若要查看对象，只需引用包含 VM 对象的变量。 键入 `$machine`。

若要删除 VM，请在同一会话中运行以下 cmdlet：

```powershell
$machine | Remove-AzVM -Force
```
### <a name="with-powershell"></a>[使用 PowerShell](#tab/ps-azureRM)

如果无法删除资源组，要么依赖项不在同一资源组中，要么存在其他资源，请按照以下步骤操作。

连接到 Azure Stack Hub 环境，然后使用 VM 名称和资源组更新以下变量。 有关将 PowerShell 会话到 Azure Stack Hub 的说明，请参阅[以用户身份使用 PowerShell 连接到 Azure Stack Hub](azure-stack-powershell-configure-user.md)。

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzureRmVM -Name $machineName -ResourceGroupName $resGroupName
```

检索依赖项的 VM 信息和名称。 请在同一会话中运行以下 cmdlet：

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzureRmResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

删除启动诊断存储容器。 如果计算机名称短于 9 个字符，则需要在创建 `$diagContainer` 变量时将索引更改为 substring 中的字符串长度。 

请在同一会话中运行以下 cmdlet：

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzureRmStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

删除虚拟网络接口。

```powershell
$machine | Remove-AzureRmNetworkInterface -Force
```

删除操作系统磁盘。

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

删除附加到 VM 的数据磁盘。

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzureRmStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

最后，删除 VM。 该 cmdlet 需要一些时间才能运行。 可以通过查看 PowerShell 中的 VM 对象来审核附加到 VM 的组件。 若要查看对象，只需引用包含 VM 对象的变量。 键入 `$machine`。

若要删除 VM，请在同一会话中运行以下 cmdlet：

```powershell
$machine | Remove-AzureRmVM -Force
```
---
## <a name="next-steps"></a>后续步骤

[Azure Stack Hub VM 功能](azure-stack-vm-considerations.md)