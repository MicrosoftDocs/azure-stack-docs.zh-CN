---
title: 使用 Azure Stack 集线器中的特权终结点
description: 了解如何使用 Azure Stack 集线器中的特权终结点（PEP）作为操作员。
author: mattbriggs
ms.topic: article
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 1/8/2020
ms.openlocfilehash: 335afce7423872156c84c655d9c94ed59a9592d2
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512433"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>使用 Azure Stack 集线器中的特权终结点

作为 Azure Stack 中心操作员，你应该使用管理员门户、PowerShell 或 Azure 资源管理器 Api 来完成大多数日常管理任务。 但是，对于某些不太常见的操作，需要使用*特权终结点*（PEP）。 PEP 是一个预配置的远程 PowerShell 控制台，它为你提供了足够的功能来帮助你执行所需的任务。 此终结点使用[POWERSHELL JEA （只是足够的管理）](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview)只公开一组受限的 cmdlet。 若要访问 PEP 并调用受限制的一组 cmdlet，请使用低特权帐户。 不需要管理员帐户。 为了进一步提高安全性，不允许使用脚本。

你可以使用 PEP 来执行以下任务：

- 低级别任务，如[收集诊断日志](azure-stack-get-azurestacklog.md)。
- 许多集成系统的部署后数据中心集成任务，如部署后添加域名系统（DNS）转发器、设置 Microsoft Graph 集成、Active Directory 联合身份验证服务（AD FS）集成、证书旋转等。
- 与支持部门合作，以获取对集成系统的深层故障排除的临时、高级别访问权限。

PEP 记录你在 PowerShell 会话中执行的每个操作（及其相应的输出）。 这将提供操作的完整透明度和完整审核。 你可以保留这些日志文件以供将来审核之用。

> [!NOTE]
> 在 Azure Stack 开发工具包（ASDK）中，你可以直接从开发工具包主机上的 PowerShell 会话运行 PEP 中提供的某些命令。 但是，你可能想要使用 PEP （如日志收集）来测试某些操作，因为这是在集成系统环境中用于执行某些操作的唯一方法。

## <a name="access-the-privileged-endpoint"></a>访问特权终结点

你可以通过远程 PowerShell 会话在承载 PEP 的虚拟机（VM）上访问 PEP。 在 ASDK 中，此 VM 名为**AzS-ERCS01**。 如果使用的是集成系统，则会有三个 PEP 实例，每个实例在不同主机上的 VM 中*运行（ERCS01*、ERCS02 或*ERCS03* *），以*实现复原。

在开始针对集成系统的此过程之前，请确保可以通过 IP 地址或 DNS 访问 PEP。 初始部署 Azure Stack 集线器后，只能通过 IP 地址访问 PEP，因为尚未设置 DNS 集成。 你的 OEM 硬件供应商将为你提供一个名为**AzureStackStampDeploymentInfo**的 JSON 文件，其中包含 PEP IP 地址。

你还可以在 Azure Stack 中心管理员门户中找到该 IP 地址。 打开门户，例如 `https://adminportal.local.azurestack.external`。 选择 "**区域管理** > **属性**"。

在运行特权终结点时，你将需要将当前区域性设置设置为 `en-US`，否则 cmdlet （如 Test-azurestack 或 Get-azurestacklog）将无法按预期工作。

> [!NOTE]
> 出于安全原因，我们要求仅从在硬件生命周期主机上运行的强化 VM 或从专用的安全计算机（例如[特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)）连接到 PEP。 不能修改硬件生命周期主机的原始配置（包括安装新软件），也不能将其用于连接到 PEP。

1. 建立信任关系。

      - 在集成系统上，从提升的 Windows PowerShell 会话运行以下命令，将 PEP 添加为硬件生命周期主机或特权访问工作站上运行的强化 VM 上的受信任主机。

      ```powershell  
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```

      - 如果运行的是 ASDK，请登录到开发工具包主机。

2. 在硬件生命周期主机或特权访问工作站上运行的强化 VM 上，打开 Windows PowerShell 会话。 运行以下命令，在托管 PEP 的 VM 上建立远程会话：
 
  - 在集成系统上：

    ```powershell  
    $cred = Get-Credential

    $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
    Enter-PSSession $pep
    ```
    
    `ComputerName` 参数可以是承载 PEP 的某个 Vm 的 IP 地址或 DNS 名称。

    > [!NOTE]  
    >验证 PEP 凭据时，Azure Stack 中心不会进行远程调用。 它依赖于本地存储的 RSA 公钥来实现此目的。

   - 如果运行的是 ASDK：

     ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
     ```
    
   - 出现提示时，请使用以下凭据：
   
       - **用户名**：指定 CloudAdmin 帐户，采用 **&lt;*Azure Stack 中心域*&gt;\cloudadmin**的格式。 （对于 ASDK，用户名为**azurestack\cloudadmin**。）
  
        - **密码**：输入在安装 AzureStackAdmin 域管理员帐户期间提供的相同密码。

      > [!NOTE]
      > 如果无法连接到 ERCS 终结点，请重试步骤1和步骤2，并将其替换为另一个 ERCS VM IP 地址。

3. 连接后，提示将更改为 **[*IP 地址或 ERCS VM 名称*]： ps >** 或改为 **[AZS-ercs01]： ps >** ，具体取决于环境。 在此处运行 `Get-Command` 以查看可用 cmdlet 的列表。

   其中的许多 cmdlet 仅适用于集成系统环境（如与数据中心集成相关的 cmdlet）。 在 ASDK 中，已验证以下 cmdlet：

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
   - 新-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>使用特权终结点的提示 

如前所述，PEP 是[POWERSHELL JEA](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview)终结点。 在提供强安全层的同时，JEA 终结点减少了某些基本 PowerShell 功能，如脚本编写或 tab 自动补全。 如果尝试任何类型的脚本操作，则操作将失败，并出现错误**ScriptsNotAllowed**。 此失败是预期的行为。

例如，若要获取给定 cmdlet 的参数列表，请运行以下命令：

```powershell
    Get-Command <cmdlet_name> -Syntax
```

或者，可以使用[import-module](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) cmdlet 将所有 PEP cmdlet 导入到本地计算机上的当前会话中。 这样，PEP 的所有 cmdlet 和功能现在都可在本地计算机上使用，并可与 tab 自动补全一起使用，更多的是一般的脚本编写。

若要在本地计算机上导入 PEP 会话，请执行以下步骤：

1. 建立信任关系。

    - 在集成系统上，从提升的 Windows PowerShell 会话运行以下命令，将 PEP 添加为硬件生命周期主机或特权访问工作站上运行的强化 VM 上的受信任主机。

    ```powershell
    winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
    ```

    - 如果运行的是 ASDK，请登录到开发工具包主机。

2. 在硬件生命周期主机或特权访问工作站上运行的强化 VM 上，打开 Windows PowerShell 会话。 运行以下命令，在托管 PEP 的虚拟机上建立远程会话：

    - 在集成系统上：
    
      ```powershell  
        $cred = Get-Credential
      
        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```
    
      `ComputerName` 参数可以是承载 PEP 的某个 Vm 的 IP 地址或 DNS 名称。

    - 如果运行的是 ASDK：
     
        ```powershell  
          $cred = Get-Credential
    
          $session = New-PSSession -ComputerName azs-ercs01 `
             -ConfigurationName PrivilegedEndpoint -Credential $cred
        ```

     出现提示时，请使用以下凭据：

     - **用户名**：指定 CloudAdmin 帐户，采用 **&lt;*Azure Stack 中心域*&gt;\cloudadmin**的格式。 （对于 ASDK，用户名为**azurestack\cloudadmin**。）
     - **密码**：输入在安装 AzureStackAdmin 域管理员帐户期间提供的相同密码。

3. 将 PEP 会话导入到本地计算机：

    ```powershell 
      Import-PSSession $session
    ```

4. 现在，你可以使用 tab 自动补全，并使用 PEP 的所有功能和 cmdlet 在本地 PowerShell 会话中照常编写脚本，而不会降低 Azure Stack 中心的安全状况。 请尽情享受其中的乐趣！

## <a name="close-the-privileged-endpoint-session"></a>关闭特权终结点会话

 如前所述，PEP 记录你在 PowerShell 会话中执行的每个操作（及其相应的输出）。 必须使用 `Close-PrivilegedEndpoint` cmdlet 关闭会话。 此 cmdlet 会正确关闭终结点，并将日志文件传输到外部文件共享以进行保留。

关闭终结点会话：

1. 创建可由 PEP 访问的外部文件共享。 在开发工具包环境中，只需在开发工具包主机上创建文件共享即可。
2. 运行以下 cmdlet：

  ```powershell  
     Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
  ```

   Cmdlet 使用下表中的参数：

   | 参数 | 说明 | 类型 | 必选 |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | 定义为 "fileshareIP\sharefoldername" 的外部文件共享的路径 | String | 是|
   | *凭据* | 用于访问文件共享的凭据 | SecureString |   是 |


将记录日志文件成功传输到文件共享后，它们会自动从 PEP 删除。 

> [!NOTE]
> 如果使用 cmdlet `Exit-PSSession` 或 `Exit`关闭 PEP 会话，或者只是关闭 PowerShell 控制台，则脚本日志不会传输到文件共享。 它们保留在 PEP 中。 下次运行 `Close-PrivilegedEndpoint` 并包含文件共享时，先前会话中的脚本日志也会传输。 不要使用 `Exit-PSSession` 或 `Exit` 来关闭 PEP 会话;改用 `Close-PrivilegedEndpoint`。


## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器诊断工具](azure-stack-diagnostic-log-collection-overview-tzl.md)
