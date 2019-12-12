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
ms.openlocfilehash: 14c1d6ba96cd8c9671b28f435db90b7b3ce3ad34
ms.sourcegitcommit: ae9d29c6a158948a7dbc4fd53082984eba890c59
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2019
ms.locfileid: "75007973"
---
# <a name="rotate-secrets-in-azure-stack"></a>在 Azure Stack 中旋转机密

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

Azure Stack 使用各种机密来维护 Azure Stack 基础结构资源与服务之间的安全通信。

- **内部机密**

    Azure Stack 基础结构使用的所有证书、密码、安全字符串和密钥，而无需 Azure Stack 操作员的干预。

- **外部机密**

    由 Azure Stack 运算符提供的面向外部的服务的基础结构服务证书。 外部机密包括以下服务的证书：

    - 管理员门户
    - 公共门户
    - 管理员 Azure 资源管理器
    - 全局 Azure 资源管理器
    - 管理员 Key Vault
    - Key Vault
    - 管理扩展主机
    - ACS （包括 blob、表和队列存储）
    - ADFS
    - 图形
    
    \* 仅适用于环境的标识提供者 Active Directory 联合服务（AD FS）。

> [!Note]
> 所有其他安全密钥和字符串（包括 BMC 和交换机密码以及用户和管理员帐户密码）仍由管理员手动更新。

> [!Important]
> 从 Azure Stack 的1811版开始，已为内部和外部证书分离了机密旋转。

为了保持 Azure Stack 基础结构的完整性，操作员需要能够定期定期旋转其基础结构的机密，其频率与组织的安全要求一致。

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>使用新证书颁发机构的外部证书轮换机密

在以下上下文中，Azure Stack 支持使用外部证书从新证书颁发机构（CA）进行密钥轮替：

|已安装证书 CA|要旋转到的 CA|受支持|支持 Azure Stack 版本|
|-----|-----|-----|-----|
|自签名|到企业|受支持|1903 & 更高版本|
|自签名|自签名|不支持||
|自签名|到公用<sup>*</sup>|受支持|1803 & 更高版本|
|从企业|到企业|。 从1803-1903：支持，只要客户使用部署时使用的相同企业 CA|1803 & 更高版本|
|从企业|自签名|不支持||
|从企业|到公用<sup>*</sup>|受支持|1803 & 更高版本|
|从公共<sup>*</sup>|到企业|受支持|1903 & 更高版本|
|从公共<sup>*</sup>|自签名|不支持||
|从公共<sup>*</sup>|到公用<sup>*</sup>|受支持|1803 & 更高版本|

<sup>*</sup>指示公共证书颁发机构是受 Windows 信任的根程序的一部分的证书颁发机构。 可以在[Microsoft 受信任的根证书程序：参与者（截至2017年6月27日）](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)中找到完整列表。

## <a name="fixing-alerts"></a>修复警报

当机密处于有效期30天内时，管理员门户中会生成以下警报：

- 挂起的服务帐户密码过期
- 挂起的内部证书过期
- 外部证书到期挂起

使用以下说明运行机密轮换将修复这些警报。

> [!Note]
> 在1811之前的版本上 Azure Stack 的环境可能会看到挂起内部证书或密钥过期的警报。 这些警报不准确，应忽略，而不会运行内部机密旋转。 不准确的内部机密过期警报是在1811中解决的已知问题。 内部机密不会过期，除非环境处于活动状态两年内。

## <a name="pre-steps-for-secret-rotation"></a>秘密旋转的预先步骤

   > [!IMPORTANT]
   > 如果已在 Azure Stack 环境上执行了机密旋转，则必须将系统更新到1811或更高版本，然后再次执行密钥轮换。 必须通过[特权终结点](azure-stack-privileged-endpoint.md)执行机密旋转，并要求 Azure Stack 操作员凭据。 如果你的环境 Azure Stack 操作员不知道是否在你的环境上运行了机密旋转，请在再次执行密钥轮换之前更新为1811。

1. 强烈建议将 Azure Stack 实例更新到版本1811。

    > [!Note]
    > 对于1811之前的版本，无需轮换机密即可添加扩展主机证书。 应按照文章[准备扩展主机以便 Azure Stack](azure-stack-extension-host-prepare.md)添加扩展主机证书中的说明进行操作。

2. 在 Azure Stack 机密轮换期间，操作员可能会注意到警报打开并自动关闭。  此行为是预期行为，可以忽略警报。  操作员可以通过运行**test-azurestack**来验证这些警报的有效性。  对于使用 System Center Operations Manager 监视 Azure Stack 系统的操作员，将系统置于维护模式时，会阻止这些警报到达其 ITSM 系统，但在 Azure Stack 系统变为不可访问状态时将继续发出警报。

3. 通知用户任何维护操作。 在非工作时间，尽可能多地计划正常维护时段。 维护操作可能会影响用户工作负荷和门户操作。

    > [!Note]
    > 后续步骤仅适用于在轮换 Azure Stack 外部机密时使用。

4. 运行 **[test-azurestack](azure-stack-diagnostic-test.md)** ，并确认所有测试输出都处于正常状态，然后再旋转机密。
5. 准备一组新的替换外部证书。 新集与[AZURE STACK PKI 证书要求](azure-stack-pki-certs.md)中所述的证书规格相匹配。 你可以使用 "[准备 AZURE STACK Pki 证书](azure-stack-prepare-pki-certs.md)" 中所述的步骤，生成用于购买或创建新证书的证书签名请求（CSR） [，并使用在 Azure Stack](azure-stack-get-pki-certs.md)环境中概述的步骤来准备要使用的证书。 务必按照[验证 PKI 证书](azure-stack-validate-pki-certs.md)中所述的步骤验证你准备的证书。
6. 将备份存储到安全备份位置中用于轮换的证书。 如果旋转运行后出现故障，请将文件共享中的证书替换为备份副本，然后再重新运行轮换。 将备份副本保留在安全的备份位置。
7. 创建可从 ERCS Vm 访问的文件共享。 文件共享必须是可读且可写的**CloudAdmin**标识。
8. 从你有权访问该文件共享的计算机上打开 PowerShell ISE 控制台。 导航到你的文件共享。
9. 运行 **[CertDirectoryMaker](https://www.aka.ms/azssecretrotationhelper)** ，为外部证书创建所需的目录。

> [!IMPORTANT]
> CertDirectoryMaker 脚本将创建一个文件夹结构，该结构将遵循以下内容：
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
> 文件共享装载应类似于 **\\\\\<IPAddress >\\\<共享名 >** \\并且它应包含**Certificates\AAD**或**Certificates\ADFS**中的文件夹。
>
> 例如：
> - Fileshare = **\\\\\<IPAddress>\\\<ShareName>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>旋转外部机密

旋转外部机密：

1. 在新创建的 **\Certificates\\\<** 在预先步骤中创建的 IdentityProvider > 目录中，根据[Azure Stack PKI 证书要求](azure-stack-pki-certs.md#mandatory-certificates)的**强制性证书**部分中所述的格式，将新的替换外部证书集放在目录结构中。

    Azure AD 标识提供程序的文件夹结构的示例：
    ```powershell
        <ShareName>
        │   │
        │   └───Certificates
        │         └───AAD
        │             ├───ACSBlob
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSQueue
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSTable
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Extension Host
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Portal
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Admin
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Public
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVault
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVaultInternal
        │             │       <CertName>.pfx
        │             │
        │             ├───Public Extension Host
        │             │       <CertName>.pfx
        │             │
        │             └───Public Portal
        │                     <CertName>.pfx

    ```

2. 使用**CloudAdmin**帐户创建具有[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 会话，并将会话存储为变量。 在下一步中，将使用此变量作为参数。

    > [!IMPORTANT]  
    > 请勿输入会话。 将会话存储为变量。

3. 运行 **[Invoke 命令](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** 。 将特权终结点 PowerShell 会话变量作为**session**参数进行传递。

4. 用以下参数运行**start-secretrotation** ：
    - **PfxFilesPath**  
    指定前面创建的证书目录的网络路径。  
    - **PathAccessCredential**  
    共享的凭据的 PSCredential 对象。
    - **CertificatePassword**  
    用于创建的所有 pfx 证书文件的密码的安全字符串。

5. 请等待你的机密旋转。 外部密钥旋转大约要花一小时。

    当机密轮换成功完成后，控制台将显示**总体操作状态： "成功**"。

    > [!Note]
    > 如果秘密旋转失败，请按照错误消息中的说明操作，然后重新运行带有 **-重新运行**参数的**start-secretrotation** 。

    ```powershell
    Start-SecretRotation -ReRun
    ```

    如果遇到重复的机密旋转失败，请联系支持人员。

6. 成功完成密钥轮换后，请从在该步骤中创建的共享中删除证书，并将其存储在安全的备份位置。

## <a name="use-powershell-to-rotate-secrets"></a>使用 PowerShell 轮换机密

以下 PowerShell 示例演示了为了轮换机密而要运行的 cmdlet 和参数。

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
> 仅当您怀疑某个内部机密已被恶意实体泄露，或者您收到了一个警报（在版本1811或更高版本上），表示内部证书即将过期时，才应进行内部机密旋转。 在1811之前的版本上 Azure Stack 的环境可能会看到挂起内部证书或密钥过期的警报。 这些警报不准确，应忽略，而不会运行内部机密旋转。 不准确的内部机密过期警报是在1811中解决的已知问题。 内部机密不会过期，除非环境处于活动状态两年内。

1. 创建具有[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 会话。
2. 在特权终结点会话中，运行**start-secretrotation-内部**。

    > [!Note]
    > 1811以前版本的 Azure Stack 环境不需要 **-Internal**标志。 **Start-secretrotation**将只旋转内部机密。

3. 请等待你的机密旋转。

   当机密轮换成功完成后，控制台将显示**总体操作状态： "成功**"。
    > [!Note]
    > 如果秘密旋转失败，请按照错误消息中的说明操作，然后重新运行带有 **-Internal**和 **-重新运行**参数的**start-secretrotation** 。  

```powershell
Start-SecretRotation -Internal -ReRun
```

如果遇到重复的机密旋转失败，请联系支持人员。

## <a name="start-secretrotation-reference"></a>Start-secretrotation 引用

旋转 Azure Stack 系统的机密。 仅对 Azure Stack 特权终结点执行。

### <a name="syntax"></a>语法

#### <a name="for-external-secret-rotation"></a>对于外部机密旋转

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>用于内部机密旋转

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>若要重新运行外部机密循环

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>若要重新运行内部机密循环

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>描述

**Start-secretrotation** cmdlet 会旋转 Azure Stack 系统的基础结构机密。 默认情况下，它只旋转所有外部网络基础结构终结点的证书。 如果与-Internal 标志一起使用，将旋转内部基础结构密码。 旋转外部网络基础结构终结点时，应使用 Start-secretrotation 脚本块来运行 ，**并将 Azure Stack**环境的特权终结点会话作为**session**参数传入。

### <a name="parameters"></a>parameters

| 参数 | Type | 需要 | 位置 | 默认 | 描述 |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | 字符串  | 错误  | 名为  | None  | 包含所有外部网络终结点证书的 **\Certificates**目录的文件共享路径。 仅在轮换外部机密时是必需的。 结束目录必须为 **\Certificates**。 |
| `CertificatePassword` | SecureString | 错误  | 名为  | None  | -PfXFilesPath 中提供的所有证书的密码。 如果在轮换外部机密时提供了 PfxFilesPath，则为必需的值。 |
| `Internal` | 字符串 | 错误 | 名为 | None | 无论何时 Azure Stack 操作员希望轮换内部基础结构机密，都必须使用内部标志。 |
| `PathAccessCredential` | PSCredential | 错误  | 名为  | None  | 包含所有外部网络终结点证书的 **\Certificates**目录的文件共享的 PowerShell 凭据。 仅在轮换外部机密时是必需的。  |
| `ReRun` | SwitchParameter | 错误  | 名为  | None  | 尝试失败后，必须随时判断进行机密旋转。 |

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

此命令会旋转用于 Azure Stack 外部网络基础结构终结点的 TLS 证书。

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>旋转内部和外部基础结构机密（仅适用于**1811** ）

> [!IMPORTANT]
> 此命令仅适用于**1811 之前**的 Azure Stack，因为已为内部和外部证书拆分了旋转。
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

此命令会将公开的所有基础结构机密旋转到 Azure Stack 内部网络，以及用于 Azure Stack 的外部网络基础结构终结点的 TLS 证书。 Start-secretrotation 将旋转所有堆栈生成的机密，并因为提供了证书，所以还会旋转外部终结点证书。  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>更新基板管理控制器（BMC）凭据

基板管理控制器（BMC）监视服务器的物理状态。 请参阅原始设备制造商（OEM）硬件供应商，了解更新 BMC 的用户帐户名和密码的说明。

>[!NOTE]
> 你的 OEM 可能提供附加的管理应用。 更新其他管理应用程序的用户名或密码不会影响 BMC 用户名或密码。

1. **早于1910的版本**：按照 OEM 说明更新 Azure Stack 物理服务器上的 BMC。 环境中每个 BMC 的用户名和密码必须相同。 BMC 用户名不能超过16个字符。

   **版本1910及更高版本**：根据 OEM 说明，不再需要首先更新 Azure Stack 物理服务器上的 BMC 凭据。 环境中每个 BMC 的用户名和密码必须相同。 BMC 用户名不能超过16个字符。

2. 在 Azure Stack 会话中打开特权终结点。 有关说明，请参阅[在 Azure Stack 中使用特权终结点](azure-stack-privileged-endpoint.md)。

3. 在 PowerShell 提示符更改为 **[IP 地址或 ERCS VM 名称]： ps >** 或 to **[azs-ERCS01]： ps >** 时，根据环境，通过运行 `Invoke-Command`运行 `Set-BmcCredential`。 将特权终结点会话变量作为参数进行传递。 例如：

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

    你还可以使用密码作为代码行的静态 PowerShell 版本：

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
