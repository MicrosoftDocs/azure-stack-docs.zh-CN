---
title: 使用特权终结点监视 Azure Stack Hub 中的更新
description: 了解如何使用特权终结点监视 Azure Stack Hub 集成系统中的更新状态。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 943f391d709f772ec3ed5aa0c99bd738f5de679a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "78368118"
---
# <a name="monitor-updates-in-azure-stack-hub-using-the-privileged-endpoint"></a>使用特权终结点监视 Azure Stack Hub 中的更新

可以使用[特权终结点](azure-stack-privileged-endpoint.md)来监视 Azure Stack Hub 更新运行的进度。 也可以使用特权终结点在 Azure Stack Hub 门户不可用时，从最后一个成功的步骤恢复失败的更新运行。 使用 Azure Stack Hub 门户是管理 Azure Stack Hub 中的更新的建议方法。

Azure Stack Hub 集成系统 1710 更新版中包含以下用于更新管理的新 PowerShell cmdlet。

| Cmdlet  | 说明  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | 返回当前正在运行的、已完成的或失败的更新状态。 提供更新操作的高级状态以及描述当前步骤和相应状态的 XML 文档。 |
| `Resume-AzureStackUpdate` | 从更新失败的位置恢复更新。 在某些情况下，可能需要先完成风险缓解步骤，然后才能恢复更新。         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>验证 cmdlet 是否可用
由于 cmdlet 是适用于 Azure Stack Hub 1710 更新包中的新功能，因此 1710 更新过程需要运行到特定的步骤，才能使用监视功能。 一般而言，如果管理员门户中的状态指示 1710 更新正在执行“重启存储主机”步骤，则可以使用 cmdlet。  具体而言，cmdlet 更新在执行“步骤: 正在运行步骤 2.6 - 更新 PrivilegedEndpoint 允许列表”期间发生。 

也可以通过从特权终结点查询命令列表，来确定是否可以编程方式使用 cmdlet。 若要执行此查询，请从硬件生命周期主机或特权访问工作站运行以下命令。 此外，请确保特权终结点是受信任的主机。 有关详细信息，请参阅[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的步骤 1。

1. 在 Azure Stack Hub 环境中的任何 ERCS 虚拟机 (VM)（*Prefix*-ERCS01、*Prefix*-ERCS02 或 *Prefix*-ERCS03）上创建 PowerShell 会话。 将 Prefix  替换为环境特定的 VM 前缀字符串。

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   系统提示输入凭据时，请使用 &lt;*Azure Stack Hub 域*&gt;\cloudadmin 帐户或属于 CloudAdmins 组成员的帐户。 对于 CloudAdmin 帐户，请输入安装 AzureStackAdmin 域管理员帐户期间提供的相同密码。

2. 获取特权终结点中可用的完整命令列表。

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. 确定特权终结点是否已更新。

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. 列出 Microsoft.AzureStack.UpdateManagement 模块特定的命令。

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
> 从硬件生命周期主机或特权访问工作站运行以下命令。 此外，请确保特权终结点是受信任的主机。 有关详细信息，请参阅[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的步骤 1。

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>连接到特权终结点并分配会话变量

运行以下命令，在 Azure Stack Hub 环境中的任何 ERCS VM（*Prefix*-ERCS01、*Prefix*-ERCS02 或 *Prefix*-ERCS03）上创建 PowerShell 会话，并分配会话变量。

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 系统提示输入凭据时，请使用 &lt;*Azure Stack Hub 域*&gt;\cloudadmin 帐户或属于 CloudAdmins 组成员的帐户。 对于 CloudAdmin 帐户，请输入安装 AzureStackAdmin 域管理员帐户期间提供的相同密码。

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

可以重复运行这些命令来查看最新状态。 无需重新建立连接即可再次检查状态。

### <a name="get-the-full-update-run-status-with-details"></a>获取完整更新运行状态和详细信息

可以获取 XML 字符串形式的完整更新运行摘要。 可将此字符串写入文件供检查，或将其转换为 XML 文档，然后使用 PowerShell 进行分析。 以下命令分析 XML，以获取当前正在运行的步骤的分层列表：

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

在以下示例中，顶级步骤（云更新）包含一个更新并重启存储主机的子计划。 其中显示“重启存储主机”计划正在某台主机上更新 Blob 存储服务。

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

如果更新失败，可以从更新中断的位置恢复更新运行。

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>故障排除

特权终结点适用于 Azure Stack Hub 环境中的所有 ERCS VM。 由于未与高度可用的终结点建立连接，因此可能会遇到偶发性中断、警告或错误消息。 这些消息可能指示会话已断开，或者与 ECE 服务通信时出错。 此行为是预期的行为。 可以在几分钟后重试此操作，或者在其他某个 ERCS VM 上新建特权终结点会话。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack Hub 中管理更新](azure-stack-updates.md)


