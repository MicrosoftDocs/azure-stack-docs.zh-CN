---
title: 轮换机密
titleSuffix: Azure Stack
description: 了解如何在 Azure Stack 中轮换机密。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.reviewer: ppacent
ms.author: mabrigg
ms.lastreviewed: 09/30/2019
monikerRange: '>=azs-1802'
ms.openlocfilehash: d00cbc5eaacd80ba67b339e11562dc516fcd0991
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465393"
---
# <a name="rotate-secrets-in-azure-stack"></a>在 Azure Stack 中轮换机密

*这些说明仅适用于 Azure Stack 集成系统版本1803及更高版本。请勿在1802之前的版本 Azure Stack 上尝试机密旋转*

机密可帮助你维护 Azure Stack 基础结构资源与服务之间的安全通信。

## <a name="rotate-secrets-overview"></a>旋转机密概述

1. 准备将用于机密轮换的证书。
2. 查看 Azure Stack[公钥基础结构证书要求](https://docs.microsoft.com/azure-stack/operator/azure-stack-pki-certs)。
3. [使用特权终结点](azure-stack-privileged-endpoint.md)并运行**test-azurestack**以确认一切正常。  
4. 查看[机密旋转的预先步骤](#pre-steps-for-secret-rotation)。
5. [验证 Azure Stack 的 PKI 证书](https://docs.microsoft.com/azure-stack/operator/azure-stack-validate-pki-certs)。 请确保密码中没有特殊字符，如 `*` 或 `)`。
6. 请确保 PFX 加密为**TripleDES**。 如果遇到问题，请参阅[解决 AZURE STACK PKI 证书的常见问题](https://docs.microsoft.com/azure-stack/operator/azure-stack-remediate-certs#pfx-encryption)。
7. 准备文件夹结构。  可以在 "[轮换外部机密](https://docs.microsoft.com/azure-stack/operator/azure-stack-rotate-secrets#rotating-external-secrets)" 部分中找到一个示例。
8. [开始秘密旋转](#use-powershell-to-rotate-secrets)。

## <a name="rotate-secrets"></a>轮换机密

Azure Stack 使用各种机密来维持 Azure Stack 基础结构资源与服务之间的安全通信。

- **内部机密**

    由 Azure Stack 基础结构使用的所有证书、密码、安全字符串和密钥，无需 Azure Stack 操作员的介入。

- **外部机密**

    对外服务的基础结构服务证书，由 Azure Stack 操作员提供。 外部机密包括以下服务的证书：

    - 管理员门户
    - 公共门户
    - 管理员 Azure 资源管理器
    - 全局 Azure 资源管理器
    - 管理员 Key Vault
    - 密钥保管库
    - 管理扩展主机
    - ACS（包括 Blob、表和队列存储）
    - ADFS
    - 图形
    
    \*仅当环境的标识提供者是 Active Directory 联合身份验证服务 (AD FS) 时才适用。

> [!Note]
> 所有其他安全密钥和字符串（包括 BMC 和交换机密码以及用户和管理员帐户密码）仍由管理员手动更新。

> [!Important]
> 从 Azure Stack 1811 版开始，内部证书和外部证书的机密轮换已分开。

为了保持 Azure Stack 基础结构的完整性，操作员需要能够定期定期旋转其基础结构的机密，其频率与组织的安全要求一致。

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>使用新证书颁发机构的外部证书轮换机密

在以下上下文中，Azure Stack 支持使用新证书颁发机构 (CA) 颁发的外部证书进行机密轮换：

|已安装的证书 CA|要轮换到的 CA|支持|支持 Azure Stack 版本|
|-----|-----|-----|-----|
|从自签名|到企业|支持|1903 和更高版本|
|从自签名|到自签名|不支持||
|从自签名|到公共<sup>*</sup>|支持|1803 和更高版本|
|从企业|到企业|。 从 1803-1903：只要客户使用与在部署时使用的相同的企业 CA，就可以支持|1803 和更高版本|
|从企业|到自签名|不支持||
|从企业|到公共<sup>*</sup>|支持|1803 和更高版本|
|从公共<sup>*</sup>|到企业|支持|1903 和更高版本|
|从公共<sup>*</sup>|到自签名|不支持||
|从公共<sup>*</sup>|到公共<sup>*</sup>|支持|1803 和更高版本|

<sup>*</sup>指示公共证书颁发机构属于 Windows 受信任根计划。 可以在[Microsoft 受信任的根证书程序：参与者（截至2017年6月27日）](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)中找到完整列表。

## <a name="fixing-alerts"></a>修复警报

当机密处于有效期30天内时，管理员门户中会生成以下警报：

- 挂起的服务帐户密码过期
- 挂起的内部证书过期
- 外部证书到期挂起

使用以下说明运行机密轮换将修复这些警报。

> [!Note]
> 在 1811 之前版本的 Azure Stack 环境中，可能会看到内部证书挂起或机密过期的警报。 这些警报并不正确，应将其忽略，且不运行内部机密轮换。 不准确的内部机密过期警报是在1811中解决的已知问题。 内部机密不会过期，除非环境处于活动状态两年内。

## <a name="pre-steps-for-secret-rotation"></a>机密轮换前的步骤

   > [!IMPORTANT]
   > 如果已在 Azure Stack 环境中执行过机密轮换，在再次执行机密轮换之前，必须先将系统更新到 1811 或更高版本。 必须通过[特权终结点](azure-stack-privileged-endpoint.md)执行机密旋转，并要求 Azure Stack 操作员凭据。 如果你的环境 Azure Stack 操作员不知道是否在你的环境上运行了机密旋转，请在再次执行密钥轮换之前更新为1811。

1. 强烈建议将 Azure Stack 实例更新到版本1811。

    > [!Note]
    > 对于1811之前的版本，无需轮换机密即可添加扩展主机证书。 应该遵照[准备 Azure Stack 的扩展主机](azure-stack-extension-host-prepare.md)一文中的说明添加扩展主机证书。

2. 在 Azure Stack 机密轮换期间，操作员可能会注意到警报打开并自动关闭。  此行为是预期行为，可以忽略警报。  操作员可以运行 **Test-AzureStack** 来验证这些警报的有效性。  对于使用 System Center Operations Manager 监视 Azure Stack 系统的操作人员来说，将系统置于维护模式将阻止这些警报到达其 ITSM 系统，但如果 Azure Stack 系统无法访问，则将继续发出警报。

3. 在执行任何维护操作之前通知用户。 将普通的维护时间段尽量安排在非营业时间。 维护操作可能会同时影响用户工作负荷和门户操作。

    > [!Note]
    > 后续步骤仅适用于轮换 Azure Stack 外部机密。

4. 运行 **[test-azurestack](azure-stack-diagnostic-test.md)** ，并确认所有测试输出都处于正常状态，然后再旋转机密。
5. 准备新的替换外部证书集。 新集与 [Azure Stack PKI 证书要求](azure-stack-pki-certs.md)中所述的证书规范匹配。 可以使用[生成 PKI 证书](azure-stack-get-pki-certs.md)中概述的步骤，生成用于购买或创建新证书的证书签名请求 (CSR)，然后使用[准备 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤来准备这些证书，以在 Azure Stack 环境中使用。 请务必使用[验证 PKI 证书](azure-stack-validate-pki-certs.md)中概述的步骤来验证准备的证书。
6. 将备份存储到安全备份位置中用于轮换的证书。 如果运行轮换时发生失败，请使用备份副本替换文件共享中的证书，然后重新运行轮换。 将备份副本保留在安全的备份位置。
7. 创建可从 ERCS VM 访问的文件共享。 该文件共享必须可供 **CloudAdmin** 标识读取和写入。
8. 在可以访问该文件共享的计算机上打开 PowerShell ISE 控制台。 导航到该文件共享。
9. 运行 **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** 创建外部证书所需的目录。

> [!IMPORTANT]
> CertDirectoryMaker 脚本将创建符合以下要求的文件夹结构：
>
> **.\Certificates\AAD**或 ***.\Certificates\ADFS*** ，具体取决于用于 Azure Stack 的标识提供者。
>
> 文件夹结构以**AAD**或**ADFS**文件夹结束，并且所有子目录都在此结构中，这是非常重要的。否则， **start-secretrotation**将会出现以下情况：
>
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> 错误按摩表示访问文件共享时出现问题，但实际上，它是在此处强制执行的文件夹结构。 有关详细信息，请参阅 Microsoft Test-azurestack [PublicCertHelper 模块](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)。
>
> 您的文件共享文件夹结构从 "**证书**" 文件夹开始也非常重要，否则验证也会失败。
> 文件共享装入点应该类似于 **\\\\\<IP 地址>\\\<共享名称>\\** ，并且应该包含文件夹 **Certificates\AAD** 或 **Certificates\ADFS**。
>
> 例如：
> - Fileshare = **\\\\\<IP 地址>\\\<共享名称>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IP 地址>\\\<共享名称>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>轮换外部机密

轮换外部机密：

1. 在新创建的 **\Certificates\\\<** 在预先步骤中创建的 IdentityProvider > 目录中，根据[Azure Stack PKI 证书要求](azure-stack-pki-certs.md#mandatory-certificates)的**强制性证书**部分中所述的格式，将新的替换外部证书集放在目录结构中。

    Azure AD 标识提供程序的文件夹结构的示例：
    ```powershell
        <ShareName>
        │   │
        │   ├───Certificates
        │   └───AAD
        │       ├───ACSBlob
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSQueue
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSTable
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Extension Host
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Portal
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Admin
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Public
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVault
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVaultInternal
        │       │       <CertName>.pfx
        │       │
        │       ├───Public Extension Host
        │       │       <CertName>.pfx
        │       │
        │       └───Public Portal
        │               <CertName>.pfx

    ```

2. 使用**CloudAdmin**帐户创建具有[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 会话，并将会话存储为变量。 在下一步中，将使用此变量作为参数。

    > [!IMPORTANT]  
    > 请勿输入会话。 将会话存储为变量。

3. 运行 **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** 。 将特权终结点 PowerShell 会话变量作为**session**参数进行传递。

4. 结合以下参数运行 **Start-SecretRotation**：
    - **PfxFilesPath**  
    将网络路径指定为前面创建的 Certificates 目录。  
    - **PathAccessCredential**  
    用于访问共享的凭据的 PSCredential 对象。
    - **CertificatePassword**  
    创建的所有 pfx 证书文件使用的密码安全字符串。

5. 等待机密完成轮换。 外部密钥旋转大约要花一小时。

    机密轮换成功完成后，控制台会显示“总体操作状态: 成功”。

    > [!Note]
    > 如果秘密旋转失败，请按照错误消息中的说明操作，然后重新运行带有 **-重新运行**参数的**start-secretrotation** 。

    ```powershell
    Start-SecretRotation -ReRun
    ```

    如果遇到重复的机密旋转失败，请联系支持人员。

6. 成功完成机密轮换后，请从前期步骤创建的共享中删除证书，并将其存储在安全的备份位置。

## <a name="use-powershell-to-rotate-secrets"></a>使用 PowerShell 轮换机密

以下 PowerShell 示例演示轮换机密时需要运行的 cmdlet 和参数。

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>仅轮换内部机密

> [!Note]
> 仅当您怀疑某个内部机密已被恶意实体泄露，或者您收到了一个警报（在版本1811或更高版本上），表示内部证书即将过期时，才应进行内部机密旋转。 在 1811 之前版本的 Azure Stack 环境中，可能会看到内部证书挂起或机密过期的警报。 这些警报并不正确，应将其忽略，且不运行内部机密轮换。 不准确的内部机密过期警报是在1811中解决的已知问题。 内部机密不会过期，除非环境处于活动状态两年内。

1. 创建具有[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 会话。
2. 在特权终结点会话中，运行 **Start-SecretRotation -Internal**。

    > [!Note]
    > 1811以前版本的 Azure Stack 环境不需要 **-Internal**标志。 **Start-SecretRotation** 仅轮换内部机密。

3. 等待机密完成轮换。

   机密轮换成功完成后，控制台会显示“总体操作状态: 成功”。
    > [!Note]
    > 如果机密轮换失败，请按照错误消息中的说明操作，并使用 **-Internal** 和 **-ReRun** 参数重新运行 **Start-SecretRotation**。  

```powershell
Start-SecretRotation -Internal -ReRun
```

如果遇到重复的机密旋转失败，请联系支持人员。

## <a name="start-secretrotation-reference"></a>Start-SecretRotation 参考

轮换 Azure Stack 系统的机密。 仅对 Azure Stack 特权终结点执行。

### <a name="syntax"></a>语法

#### <a name="for-external-secret-rotation"></a>对于外部机密轮换

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>对于内部机密轮换

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>对于外部机密轮换 rerun

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>对于内部机密轮换 rerun

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>说明

**Start-SecretRotation** cmdlet 轮换 Azure Stack 系统的基础结构机密。 默认情况下，它只旋转所有外部网络基础结构终结点的证书。 如果与-Internal 标志一起使用，将旋转内部基础结构密码。 轮换外部网络基础结构终结点时，应结合 **Invoke-Command** 脚本块，并结合以 **Session** 参数形式传入的 Azure Stack 环境特权终结点会话，来运行 **Start-SecretRotation**。

### <a name="parameters"></a>Parameters

| 参数 | 类型 | 必选 | 位置 | 默认 | 说明 |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | String  | False  | 名为  | 无  | 包含所有外部网络终结点证书的 **\Certificates** 目录的文件共享路径。 仅当轮换外部机密时才需要。 结尾目录必须是 **\Certificates**。 |
| `CertificatePassword` | SecureString | False  | 名为  | 无  | -PfXFilesPath 中提供的所有证书的密码。 如果在轮换外部机密时提供了 PfxFilesPath，则是必需的值。 |
| `Internal` | String | False | 名为 | 无 | 每当 Azure Stack 操作员想要轮换内部基础结构机密时，都必须使用 Internal 标志。 |
| `PathAccessCredential` | PSCredential | False  | 名为  | 无  | 包含所有外部网络终结点证书的 **\Certificates** 目录的文件共享的 PowerShell 凭据。 仅当轮换外部机密时才需要。  |
| `ReRun` | SwitchParameter | False  | 名为  | 无  | 每当尝试失败后重新尝试机密轮换时，都必须使用 ReRun。 |

### <a name="examples"></a>示例

#### <a name="rotate-only-internal-infrastructure-secrets"></a>仅轮换内部基础结构机密

必须通过 Azure Stack[环境的特权终结点](azure-stack-privileged-endpoint.md)运行此命令。

```powershell
PS C:\> Start-SecretRotation -Internal
```

此命令会将公开的所有基础结构机密旋转到 Azure Stack 内部网络。

#### <a name="rotate-only-external-infrastructure-secrets"></a>仅轮换外部基础结构机密  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

此命令轮换用于 Azure Stack 外部网络基础结构终结点的 TLS 证书。

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>轮换内部和外部基础结构机密（仅限 **1811 以前**的版本）

> [!IMPORTANT]
> 此命令仅适用于 Azure Stack **1811 以前**的版本，因为轮换将会针对内部和外部证书分开进行。
>
> **从*1811 +* 后，你无法再轮替内部和外部证书！**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

此命令轮换向 Azure Stack 内部网络公开的所有基础结构机密，以及用于 Azure Stack 外部网络基础结构终结点的 TLS 证书。 Start-SecretRotation 轮换堆栈生成的所有机密，由于提供了证书，因此也会轮换外部终结点证书。  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>更新基板管理控制器 (BMC) 凭据

基板管理控制器 (BMC) 监视服务器的物理状态。 请参阅原始设备制造商（OEM）硬件供应商，了解更新 BMC 的用户帐户名和密码的说明。

>[!NOTE]
> 你的 OEM 可能提供附加的管理应用。 更新其他管理应用程序的用户名或密码不会影响 BMC 用户名或密码。

1. **早于1910的版本**：按照 OEM 说明更新 Azure Stack 物理服务器上的 BMC。 环境中每个 BMC 的用户名和密码必须相同。 BMC 用户名不能超过 16 个字符。

   **版本1910及更高版本**：根据 OEM 说明，不再需要首先更新 Azure Stack 物理服务器上的 BMC 凭据。 环境中每个 BMC 的用户名和密码必须相同。 BMC 用户名不能超过 16 个字符。

2. 在 Azure Stack 会话中打开特权终结点。 有关说明，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。

3. 在 PowerShell 提示符更改为 **[IP 地址或 ERCS VM 名称]: PS>** 或更改为 **[azs-ercs01]: PS>** （取决于环境）后，通过运行 `Set-BmcCredential` 来运行 `Invoke-Command`。 将特权终结点会话变量作为参数传递。 例如：

    ```powershell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    也可以将静态 PowerShell 版本与密码搭配使用，如以下代码行所示：

    ```powershell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Stack 安全性](azure-stack-security-foundations.md)
