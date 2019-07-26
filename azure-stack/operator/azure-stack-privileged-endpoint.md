---
title: 使用 Azure Stack 中的特权终结点 | Microsoft Docs
description: 介绍如何使用 Azure Stack 中的特权终结点 (PEP)（面向 Azure Stack 操作员）。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 9d088cb128243b0b178e7a317ba05176a59e83c1
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494064"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>使用 Azure Stack 中的特权终结点

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

作为 Azure Stack 操作员，你应当使用管理员门户、PowerShell 或 Azure 资源管理器 API 执行大多数日常管理任务。 但是，对于非常规操作，需要使用特权终结点 (PEP)。 PEP 是预配置的远程 PowerShell 控制台，可提供恰到好处的功能来帮助执行所需的任务。 该终结点使用 [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview)，只公开一组受限的 cmdlet。 若要访问 PEP 并调用一组受限的 cmdlet，可以使用低特权帐户。 无需管理员帐户。 为了提高安全性，不允许使用脚本。

使用 PEP 可以执行如下所述的任务：

- 执行低级任务，例如[收集诊断日志](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)。
- 针对集成系统执行许多部署后的数据中心集成任务，例如在部署后添加域名系统 (DNS) 转发器、设置 Microsoft Graph 集成、Active Directory 联合身份验证服务 (AD FS) 集成、证书轮换，等等。
- 与支持人员合作，获取临时性的高级访问权限，以便对集成系统进行深入的故障排除。

PEP 记录你在 PowerShell 会话中执行的每项操作（及其相应的输出）。 这可以提供完全透明度和完整的操作审核。 可以保留这些日志文件供以后审核。

> [!NOTE]
> 在 Azure Stack 开发工具包 (ASDK) 中，可以在开发工具包主机上，直接从 PowerShell 会话运行 PEP 中可用的某些命令。 但是，可能需要使用 PEP 来测试某些操作（例如日志收集），因为这是在集成系统环境中执行某些操作的唯一可行方法。

## <a name="access-the-privileged-endpoint"></a>访问特权终结点

可在托管 PEP 的虚拟机上通过远程 PowerShell 会话来访问 PEP。 在 ASDK 中，此虚拟机名为 **AzS-ERCS01**。 如果使用集成系统，则有三个 PEP 实例，每个实例在不同主机上的虚拟机中运行（Prefix-ERCS01、Prefix-ERCS02 或 Prefix-ERCS03），以提供复原能力。 

在开始针对集成系统执行此过程之前，请确保可以通过 IP 地址或 DNS 访问 PEP。 完成 Azure Stack 的初始部署之后，只能通过 IP 地址来访问 PEP，因为尚未设置 DNS 集成。 OEM 硬件供应商将提供名为 **AzureStackStampDeploymentInfo** 的 JSON 文件，其中包含 PEP IP 地址。


> [!NOTE]
> 出于安全原因，我们要求只从硬件生命周期主机顶层运行的强化虚拟机或者从专用的安全计算机（例如[特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)）连接到 PEP。 不得修改硬件生命周期主机的原始配置（包括安装新软件），也不能使用该主机来连接 PEP。

1. 建立信任。

    - 在集成系统中，从权限提升的 Windows PowerShell 会话运行以下命令，将 PEP 添加为硬件生命周期主机或特权访问工作站上运行的强化虚拟机的受信任主机。

      ```powershell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - 如果运行的是 ASDK，请登录到开发工具包主机。

2. 在硬件生命周期主机或特权工作站上运行的强化虚拟机中，打开 Windows PowerShell 会话。 运行以下命令，在托管 PEP 的虚拟机上建立远程会话：
 
   - 在集成系统上：
     ```powershell
       $cred = Get-Credential

       Enter-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     `ComputerName` 参数可以是托管 PEP 的某个虚拟机的 IP 地址或 DNS 名称。 
   - 如果运行的是 ASDK：
     
     ```powershell
       $cred = Get-Credential

       Enter-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     出现提示时，请使用以下凭据：

     - **用户名**：指定 CloudAdmin 帐户，格式为 **&lt;*Azure Stack 域*&gt;\cloudadmin**。 （对于 ASDK，用户名为 **azurestack\cloudadmin**。）
     - **密码**：输入安装 AzureStackAdmin 域管理员帐户期间提供的相同密码。

     > [!NOTE]
     > 如果无法连接到 ERCS 终结点，请使用尚未尝试连接到的 ERCS VM 的 IP 地址重试步骤 1 和 2。

3. 在连接后，提示符将更改为 **[*IP 地址或 ERCS VM 名称*]:PS>** 或 **[azs-ercs01]:PS>** ，具体取决于环境。 在此处运行 `Get-Command` 可查看可用的 cmdlet 列表。

   其中的许多 cmdlet 仅供集成系统环境使用（例如与数据中心集成相关的 cmdlet）。 在 ASDK 中，以下 cmdlet 已经过验证：

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>有关使用特权终结点的提示 

如前所述，PEP 是一个 [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) 终结点。 尽管 JEA 终结点提供强大的安全层，但也缩减了部分 PowerShell 基本功能，例如脚本编写或 Tab 键补全。 尝试执行任何类型的脚本操作时，该操作会失败并出现错误 **ScriptsNotAllowed**。 这是预期行为。

例如，若要获取特定 cmdlet 的参数列表，需运行以下命令：

```powershell
    Get-Command <cmdlet_name> -Syntax
```

或者，可以使用 [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) cmdlet 将所有 PEP cmdlet 导入到本地计算机上的当前会话中。 这样，PEP 的所有 cmdlet 和函数，以及 Tab 键补全和更常用的脚本功能现在都可在本地计算机上使用。 

若要在本地计算机上导入 PEP 会话，请执行以下步骤：

1. 建立信任。

    在集成系统中，从权限提升的 Windows PowerShell 会话运行以下命令，将 PEP 添加为硬件生命周期主机或特权访问工作站上运行的强化虚拟机的受信任主机。

      ```powershell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```
    - 如果运行的是 ASDK，请登录到开发工具包主机。

2. 在硬件生命周期主机或特权工作站上运行的强化虚拟机中，打开 Windows PowerShell 会话。 运行以下命令，在托管 PEP 的虚拟机上建立远程会话：
 
   - 在集成系统上：
     ```powershell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ```
     `ComputerName` 参数可以是托管 PEP 的某个虚拟机的 IP 地址或 DNS 名称。 
   - 如果运行的是 ASDK：
     
     ```powershell
      $cred = Get-Credential

      $session = New-PSSession -ComputerName azs-ercs01 `
         -ConfigurationName PrivilegedEndpoint -Credential $cred
     ``` 
     出现提示时，请使用以下凭据：

     - **用户名**：指定 CloudAdmin 帐户，格式为 **&lt;*Azure Stack 域*&gt;\cloudadmin**。 （对于 ASDK，用户名为 **azurestack\cloudadmin**。）
     - **密码**：输入安装 AzureStackAdmin 域管理员帐户期间提供的相同密码。

3. 将 PEP 会话导入本地计算机
     ```powershell 
        Import-PSSession $session
   ```
4. 现在，可以在本地 PowerShell 会话中，配合 PEP 的所有函数和 cmdlet 如常使用 Tab 键补全和执行脚本操作，而无需降低 Azure Stack 的安全级别。 请尽情享受其中的乐趣！


## <a name="close-the-privileged-endpoint-session"></a>关闭特权终结点会话

 如前所述，PEP 会记录你在 PowerShell 会话中执行的每项操作（及其相应的输出）。 必须使用 `Close-PrivilegedEndpoint` cmdlet 关闭会话。 此 cmdlet 会正常关闭终结点，并将日志文件传送到外部文件共享进行保留。

关闭终结点会话：

1. 创建 PEP 可访问的外部文件共享。 在开发工具包环境中，只能在开发工具包主机上创建文件共享。
2. 运行以下 cmdlet： 
     ```powershell
     Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
     ```
   它使用下表中的参数。

   | 参数 | 描述 | 类型 | 必填 |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | 定义为 "fileshareIP\sharefoldername" 的外部文件共享的路径 | String | 是|
   | *凭据* | 用于访问文件共享的凭据 | SecureString |   是 |


将脚本日志文件成功传送到文件共享后，它们会自动从 PEP 中删除。 

> [!NOTE]
> 如果使用 `Exit-PSSession` 或 `Exit` cmdlet 关闭 PEP 会话或只是关闭 PowerShell 控制台，则脚本日志不会传送到文件共享。 它们会保留在 PEP 中。 下次运行 `Close-PrivilegedEndpoint` 并包含文件共享时，也将传送前面会话中的脚本日志。 不要使用 `Exit-PSSession` 或 `Exit` 关闭 PEP 会话；请改用 `Close-PrivilegedEndpoint`。


## <a name="next-steps"></a>后续步骤

[Azure Stack 诊断工具](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)
