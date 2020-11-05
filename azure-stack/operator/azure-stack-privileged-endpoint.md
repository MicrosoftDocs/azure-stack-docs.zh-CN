---
title: 使用 Azure Stack Hub 中的特权终结点
description: 了解如何以操作员身份使用 Azure Stack Hub 中的特权终结点 (PEP)。
author: mattbriggs
ms.topic: article
ms.date: 04/28/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/28/2020
ms.custom: conteperfq4
ms.openlocfilehash: 9009064b2664d09a677ad1b2e21b2a3e5b17b57f
ms.sourcegitcommit: 08aa3b381aec7a6a3df4f9591edd6f08928071d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93363922"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>使用 Azure Stack Hub 中的特权终结点

Azure Stack Hub 操作员应使用管理员门户、PowerShell 或 Azure 资源管理器 API 来完成大多数日常管理任务。 但是，对于非常规操作，需要使用特权终结点 (PEP)。 PEP 是预配置的远程 PowerShell 控制台，可提供恰到好处的功能来帮助执行所需的任务。 该终结点使用 [PowerShell JEA (Just Enough Administration)](/powershell/scripting/learn/remoting/jea/overview)，只公开一组受限的 cmdlet。 若要访问 PEP 并调用一组受限的 cmdlet，可以使用低特权帐户。 无需管理员帐户。 为了提高安全性，不允许使用脚本。

可以使用 PEP 执行以下任务：

- 低级任务，例如[收集诊断日志](azure-stack-get-azurestacklog.md)。
- 针对集成系统的许多部署后数据中心集成任务，例如在部署后添加域名系统 (DNS) 转发器、设置 Microsoft Graph 集成、Active Directory 联合身份验证服务 (AD FS) 集成、证书轮换，等等。
- 与支持人员合作，获取临时性的高级访问权限，以便对集成系统进行深入的故障排除。

PEP 记录你在 PowerShell 会话中执行的每项操作（及其相应的输出）。 这可以提供完全透明度和完整的操作审核。 可以保留这些日志文件供以后审核。

> [!NOTE]
> 在 Azure Stack 开发工具包 (ASDK) 中，可以在开发工具包主机上，直接从 PowerShell 会话运行 PEP 中可用的某些命令。 但是，可能需要使用 PEP 来测试某些操作（例如日志收集），因为这是在集成系统环境中执行某些操作的唯一可行方法。

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

## <a name="access-the-privileged-endpoint"></a>访问特权终结点

可在托管 PEP 的虚拟机 (VM) 上通过远程 PowerShell 会话来访问 PEP。 在 ASDK 中，此 VM 名为 **AzS-ERCS01** 。 如果使用集成系统，则有三个 PEP 实例，每个实例在不同主机上的 VM（Prefix-ERCS01、Prefix-ERCS02 或 Prefix-ERCS03）中运行，以提供复原能力。

在开始针对集成系统执行此过程之前，请确保可以通过 IP 地址或 DNS 访问 PEP。 完成 Azure Stack Hub 的初始部署之后，只能通过 IP 地址来访问 PEP，因为尚未设置 DNS 集成。 OEM 硬件供应商将提供名为 **AzureStackStampDeploymentInfo** 的 JSON 文件，其中包含 PEP IP 地址。

还可以在 Azure Stack Hub 管理员门户中找到 IP 地址。 打开门户，例如 `https://adminportal.local.azurestack.external`。 选择“区域管理” > “属性”。

在运行特权终结点时，需要将当前区域性设置设置为 `en-US`，否则 cmdlet （如 Test-AzureStack 或 Get-AzureStackLog）无法按预期工作。

> [!NOTE]
> 出于安全原因，我们要求只从硬件生命周期主机上运行的强化 VM 或者从专用的安全计算机（例如[特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)）连接到 PEP。 不得修改硬件生命周期主机的原始配置（包括安装新软件），也不得使用该主机来连接 PEP。

1. 建立信任。

      - 在集成系统中，从权限提升的 Windows PowerShell 会话运行以下命令，将 PEP 添加为硬件生命周期主机或特权访问工作站上运行的强化 VM 的受信任主机。

      ```powershell  
    Set-Item WSMan:\localhost\Client\TrustedHosts -Value '<IP Address of Privileged Endpoint>' -Concatenate
      ```

      - 如果运行的是 ASDK，请登录到开发工具包主机。

2. 在运行在硬件生命周期主机或特权访问工作站上的强化 VM 中，打开 Windows PowerShell 会话。 运行以下命令，在托管 PEP 的 VM 上建立远程会话：
 
  - 在集成系统上：

    ```powershell  
    $cred = Get-Credential

    $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
    Enter-PSSession $pep
    ```
    
    `ComputerName` 参数可以是托管 PEP 的某个 VM 的 IP 地址或 DNS 名称。

    > [!NOTE]  
    >验证 PEP 凭据时，Azure Stack Hub 不会进行远程调用。 它依赖于本地存储的 RSA 公钥来实现此目的。

   - 如果运行的是 ASDK：

     ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
     ```
    
   - 出现提示时，请使用以下凭据：
   
       - **用户名** ：指定 CloudAdmin 帐户，格式为 **&lt;*Azure Stack Hub 域*&gt;\cloudadmin** 。 （对于 ASDK，用户名为 **azurestack\cloudadmin** 。）
  
        - **密码** ：输入安装 AzureStackAdmin 域管理员帐户期间提供的相同密码。

      > [!NOTE]
      > 如果无法连接到 ERCS 终结点，请使用另一个 ERCS VM IP 地址重试步骤一和步骤二。

3. 在连接后，提示符将更改为 **[ *IP 地址或 ERCS VM 名称* ]:PS>** 或 **[azs-ercs01]:PS>** ，具体取决于环境。 在此处运行 `Get-Command` 可查看可用的 cmdlet 列表。

    可以在 [Azure Stack Hub 特权终结点参考](../reference/pep-2002/index.md)中找到 cmdlet 的参考

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

## <a name="how-to-use-the-privileged-endpoint"></a>如何使用特权终结点 

如前所述，PEP 是一个 [PowerShell JEA](/powershell/scripting/learn/remoting/jea/overview) 终结点。 尽管 JEA 终结点提供强大的安全层，但也缩减了部分 PowerShell 基本功能，例如脚本编写或 Tab 键补全。 尝试执行任何类型的脚本操作时，该操作会失败并出现错误 **ScriptsNotAllowed** 。 此失败是预期行为。

例如，若要获取给定 cmdlet 的参数列表，请运行以下命令：

```powershell
    Get-Command <cmdlet_name> -Syntax
```

或者，可以使用 [Import-PSSession](/powershell/module/microsoft.powershell.utility/import-pssession?view=powershell-5.1) cmdlet 将所有 PEP cmdlet 导入到本地计算机上的当前会话中。 PEP 的 cmdlet 和函数，以及 Tab 键补全和更常用的脚本功能现在都可在本地计算机上使用。 还可以运行 [Get-Help](/powershell/module/microsoft.powershell.core/get-help) 模块来查看 cmdlet 说明。

若要在本地计算机上导入 PEP 会话，请执行以下步骤：

1. 建立信任。

    - 在集成系统中，从权限提升的 Windows PowerShell 会话运行以下命令，将 PEP 添加为硬件生命周期主机或特权访问工作站上运行的强化 VM 的受信任主机。

    ```powershell
    winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
    ```

    - 如果运行的是 ASDK，请登录到开发工具包主机。

2. 在运行在硬件生命周期主机或特权访问工作站上的强化 VM 中，打开 Windows PowerShell 会话。 运行以下命令，在托管 PEP 的虚拟机上建立远程会话：

    - 在集成系统上：
    
      ```powershell  
        $cred = Get-Credential
      
        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```
    
      `ComputerName` 参数可以是托管 PEP 的某个 VM 的 IP 地址或 DNS 名称。

    - 如果运行的是 ASDK：
     
        ```powershell  
          $cred = Get-Credential
    
          $session = New-PSSession -ComputerName azs-ercs01 `
             -ConfigurationName PrivilegedEndpoint -Credential $cred
        ```

     出现提示时，请使用以下凭据：

     - **用户名** ：指定 CloudAdmin 帐户，格式为 **&lt;*Azure Stack Hub 域*&gt;\cloudadmin** 。 （对于 ASDK，用户名为 **azurestack\cloudadmin** 。）
     - **密码** ：输入安装 AzureStackAdmin 域管理员帐户期间提供的相同密码。

3. 将 PEP 会话导入本地计算机：

    ```powershell 
      Import-PSSession $session
    ```

4. 现在，可以在本地 PowerShell 会话中，配合 PEP 的所有函数和 cmdlet 如常使用 Tab 键补全和执行脚本操作，而无需降低 Azure Stack Hub 的安全级别。 请尽情享受其中的乐趣！

## <a name="close-the-privileged-endpoint-session"></a>关闭特权终结点会话

 如前所述，PEP 会记录你在 PowerShell 会话中执行的每项操作（及其相应的输出）。 必须使用 `Close-PrivilegedEndpoint` cmdlet 关闭会话。 此 cmdlet 会正常关闭终结点，并将日志文件传送到外部文件共享进行保留。

关闭终结点会话：

1. 创建可供 PEP 访问的外部文件共享。 在开发工具包环境中，只能在开发工具包主机上创建文件共享。
2. 运行以下 cmdlet：

  ```powershell  
     Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
  ```

   该 cmdlet 使用下表中的参数：

   | 参数 | 说明 | 类型 | 必须 |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | 定义为“fileshareIP\sharefoldername”的外部文件共享的路径 | String | 是|
   | *凭据* | 用于访问文件共享的凭据 | SecureString |   是 |


将脚本日志文件成功传送到文件共享后，它们会自动从 PEP 中删除。 

> [!NOTE]
> 如果使用 `Exit-PSSession` 或 `Exit` cmdlet 关闭 PEP 会话或只是关闭 PowerShell 控制台，则脚本日志不会传送到文件共享。 它们会保留在 PEP 中。 下次运行 `Close-PrivilegedEndpoint` 并包含文件共享时，也将传送前面会话中的脚本日志。 不要使用 `Exit-PSSession` 或 `Exit` 关闭 PEP 会话；请改用 `Close-PrivilegedEndpoint`。

## <a name="unlocking-the-privileged-endpoint-for-support-scenarios"></a>为支持方案解锁特权终结点

 在支持方案中，Microsoft 支持工程师可能需要提升特权终结点 PowerShell 会话，以访问 Azure Stack 中心基础结构的内部机制。 此过程有时非正式地称为 "中断玻璃" 或 "解锁 PEP"。 PEP 会话提升过程分为两个步骤：两个用户，两个组织身份验证过程。 解锁过程由 Azure Stack 中心操作员启动，该操作员随时保持对其环境的控制。 操作员访问 PEP 并执行以下 cmdlet：
 
 ```powershell  
      Get-SupportSessionToken
  ```
 Cmdlet 返回支持会话请求标记，这是一个非常长的字母数字字符串。 然后，操作员通过其选择 (（例如聊天、电子邮件) ）将请求令牌传递给 Microsoft 支持工程师。 Microsoft 支持工程师使用请求令牌来生成支持会话授权令牌（如果有效），并将其发送回 Azure Stack 中心操作员。 在同一 PEP PowerShell 会话上，操作员会将授权令牌作为输入传递给此 cmdlet：

 ```powershell  
      unlock-supportsession
      cmdlet Unlock-SupportSession at command pipeline position 1
      Supply values for the following parameters:
      ResponseToken:
  ```

如果授权令牌有效，则通过提供完整的管理员功能并将完全可用于基础结构来提升 PEP PowerShell 会话。 

> [!NOTE]
> 在提升的 PEP 会话中执行的所有操作和 cmdlet 必须在 Microsoft 支持工程师的严格监督下执行。 否则，可能会导致严重的停机时间、数据丢失，并可能需要完全重新部署 Azure Stack 中心环境。

 一旦终止支持会话，就必须使用 **PrivilegedEndpoint** cmdlet （如上述部分所述）关闭提升的 PEP 会话，这一点非常重要。 一个 PEP 会话终止，解除锁定令牌将不再有效，且不能再次再次用于重新锁定 PEP 会话。
提升的 PEP 会话的有效期为8小时，此后，如果未终止，则提升的 PEP 会话将自动锁定为常规 PEP 会话。

## <a name="content-of-the-privileged-endpoint-tokens"></a>特权终结点令牌的内容

 PEP 支持会话请求和授权令牌利用加密来保护访问权限，并确保只有经授权的令牌才能解锁 PEP 会话。 令牌的设计目的是为了保证响应令牌只能由生成请求令牌的 PEP 会话接受。 PEP 令牌不包含任何可以唯一识别 Azure Stack 中心环境或客户的信息。 它们完全是匿名的。 提供每个令牌内容的详细信息。
 
### <a name="support-session-request-token"></a>支持会话请求令牌

 PEP 支持会话请求令牌由三个对象组成：

      - A randomly generated Session ID.
      - A self-signed certificate, generated for the purpose of having a one-time public/private key pair. The certificate does not contain any information on the environment. 
      - A time stamp that indicates the request token expiration.
      
  然后，将用注册了 Azure Stack 中心环境的 Azure 云的公钥来加密请求令牌。
 
 ### <a name="support-session-authorization-response-token"></a>支持会话授权响应令牌

PEP 支持授权响应令牌包含两个对象：

      - The randomly generated session ID extracted from the request token.
      - A time stamp that indicates the response token expiration.
      
 然后，将用请求令牌中包含的自签名证书对响应令牌进行加密。 自签名证书已通过与为其注册 Azure Stack 中心环境的 Azure 云关联的私钥进行解密。


## <a name="next-steps"></a>后续步骤

- [Azure Stack Hub 诊断工具](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)
- [Azure Stack Hub 特权终结点参考](../reference/pep-2002/index.md)
