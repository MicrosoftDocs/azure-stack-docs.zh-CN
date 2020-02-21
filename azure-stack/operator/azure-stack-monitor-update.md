---
title: 使用特权终结点监视 Azure Stack 集线器中的更新
description: 了解如何使用特权终结点监视 Azure Stack 集线器集成系统的更新状态。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 170bb21a8facd66fc5f581729a19f436fbd16fc1
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77507998"
---
# <a name="monitor-updates-in-azure-stack-hub-using-the-privileged-endpoint"></a>使用特权终结点监视 Azure Stack 集线器中的更新

可以使用[特权终结点](azure-stack-privileged-endpoint.md)监视 Azure Stack 中心更新运行的进度。 如果 Azure Stack 集线器门户不可用，则还可以使用特权终结点从上一个成功的步骤中恢复失败的更新运行。 使用 Azure Stack 集线器门户是管理 Azure Stack 中心更新的推荐方法。

适用于 Azure Stack 集线器集成系统的1710更新中包含了以下用于更新管理的新 PowerShell cmdlet。

| Cmdlet  | 说明  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | 返回当前正在运行的、已完成的或失败的更新的状态。 提供更新操作的高级状态和描述当前步骤和相应状态的 XML 文档。 |
| `Resume-AzureStackUpdate` | 恢复失败的更新。 在某些情况下，你可能需要在继续更新之前完成缓解步骤。         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>验证 cmdlet 是否可用
由于 cmdlet 是 Azure Stack 集线器1710更新包中的新 cmdlet，因此，1710更新过程需要在监视功能可用之前获得特定的时间点。 通常，如果管理员门户中的状态表明1710更新在 "**重新启动存储主机**" 步骤上，则可以使用 cmdlet。 具体而言，cmdlet 将在**步骤：运行步骤 2.6-Update PrivilegedEndpoint 白名单**中进行。

你还可以通过从特权终结点查询命令列表来确定是否可以编程方式使用 cmdlet。 若要执行此查询，请从硬件生命周期主机或特权访问工作站运行以下命令。 此外，请确保特权终结点是受信任的主机。 有关详细信息，请参阅[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的步骤1。

1. 在 Azure Stack 中心环境中的任何 ERCS 虚拟机（Vm）上创建 PowerShell 会话（*prefix*-ERCS01、 *prefix*-ERCS02 或*prefix*-ERCS03）。 将*Prefix*替换为特定于您的环境的 VM 前缀字符串。

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   当系统提示输入凭据时，请使用 &lt;*Azure Stack 中心域*&gt;\cloudadmin 帐户，或者使用 CloudAdmins 组的成员帐户。 对于 CloudAdmin 帐户，输入在安装期间为 AzureStackAdmin 域管理员帐户提供的相同密码。

2. 获取特权终结点中可用命令的完整列表。

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. 确定是否更新了特权终结点。

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. 列出特定于 Test-azurestack UpdateManagement 模块的命令。

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   例如：
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>使用更新管理 cmdlet

> [!NOTE]
> 从硬件生命周期主机或特权访问工作站运行以下命令。 此外，请确保特权终结点是受信任的主机。 有关详细信息，请参阅[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的步骤1。

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>连接到特权终结点并分配会话变量

运行以下命令，在 Azure Stack 中心环境中的任何 ERCS*vm （ERCS01* *、ERCS02*或*prefix*-ERCS03）上创建 PowerShell 会话，并分配会话变量。

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 当系统提示输入凭据时，请使用 &lt;*Azure Stack 中心域*&gt;\cloudadmin 帐户，或者使用 CloudAdmins 组的成员帐户。 对于 CloudAdmin 帐户，输入在安装期间为 AzureStackAdmin 域管理员帐户提供的相同密码。

### <a name="get-high-level-status-of-the-current-update-run"></a>获取当前更新运行的高级状态

若要获取当前更新运行的高级状态，请运行以下命令：

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

可能的值包括：

- 正在运行
- 已完成
- 失败 
- 已取消

你可以重复运行这些命令来查看最新状态。 无需重新建立连接即可检查。

### <a name="get-the-full-update-run-status-with-details"></a>获取详细信息的完整更新运行状态

您可以以 XML 字符串的形式获取完整的更新运行摘要。 你可以将字符串写入文件以进行检查，或将其转换为 XML 文档，然后使用 PowerShell 对其进行分析。 以下命令分析 XML 以获取当前正在运行的步骤的分层列表：

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

在以下示例中，顶级步骤（云更新）有一个子计划，用于更新和重新启动存储主机。 它显示 "重新启动存储主机" 计划正在更新其中一个主机上的 Blob 存储服务。

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="resume-a-failed-update-operation"></a>恢复失败的更新操作

如果更新失败，你可以从中断的位置继续更新运行。

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>故障排除

特权终结点在 Azure Stack 中心环境中的所有 ERCS Vm 上都可用。 由于未与高度可用的终结点建立连接，因此可能会出现偶然中断、警告或错误消息。 这些消息可能指示会话已断开连接或与 ECE 服务通信时出错。 此行为是预期的行为。 你可以在几分钟后重试该操作，或在另一个 ERCS Vm 上创建新的特权终结点会话。

## <a name="next-steps"></a>后续步骤

- [管理 Azure Stack 集线器中的更新](azure-stack-updates.md)


