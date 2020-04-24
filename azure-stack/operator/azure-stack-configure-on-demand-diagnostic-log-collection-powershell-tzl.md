---
title: 使用特权终结点将 Azure Stack 集线器诊断日志发送到 Azure （PEP）
description: 了解如何使用特权终结点（PEP）将 Azure Stack 集线器诊断日志发送到 Azure。
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: ca6240cb4f1e54c5e5fbfda79c46697909612063
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520294"
---
# <a name="send-azure-stack-hub-diagnostic-logs-to-azure-using-the-privileged-endpoint-pep"></a>使用特权终结点将 Azure Stack 集线器诊断日志发送到 Azure （PEP）

使用此方法时，无需提供任何目标存储位置。 如果未提供**fromDate**和**toDate**参数，则它默认为最后4个小时，从执行时间开始。 

你可以选择指定**FilterByRole**或**FilterByResourceProvider**参数，以仅包括特定角色或值-添加 RP 日志。 

下面是一个示例脚本，可以使用 PEP 运行该脚本来收集集成系统上的日志。 


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP address. 
 
$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force 
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password) 
 
$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred 
 
$fromDate = (Get-Date).AddHours(-8) # Optional. 
$toDate = (Get-Date).AddHours(-2) # Optional. Provide the time that includes the period for your issue 
 
Invoke-Command -Session $session {Send-AzureStackDiagnosticLog -FromDate $using:fromDate -ToDate $using:toDate} 
 
if ($session) { 
    Remove-PSSession -Session $session 
} 
```

## <a name="parameter-considerations"></a>参数注意事项 

* 可以使用 **FromDate** 和 **ToDate** 参数来收集特定时间段的日志。 如果未指定这些参数，则默认收集过去四小时的日志。

* 使用 **FilterByNode** 参数按计算机名筛选日志。 例如：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* 使用 **FilterByLogType** 参数按类型筛选日志。 可以选择按文件、共享或 WindowsEvent 进行筛选。 例如：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* 使用**FilterByResourceProvider**参数发送值添加资源提供程序（RPs）的诊断日志。 一般语法为：
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```
 
  为 IoT 中心发送诊断日志： 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  发送事件中心的诊断日志：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  为 Azure Stack Edge 发送诊断日志：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* 使用**FilterByRole**参数从 VirtualMachines 和 BareMetal 角色发送诊断日志：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* 若要从 VirtualMachines 和 BareMetal 角色发送诊断日志，并在过去8小时内对日志文件进行日期筛选：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* 若要从 VirtualMachines 和 BareMetal 角色发送诊断日志，并将日志文件的日期筛选用于8个小时前和2小时前的时间段：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```


## <a name="next-steps"></a>后续步骤

[使用特权终结点（PEP）发送 Azure Stack 集线器诊断日志](azure-stack-get-azurestacklog.md)
