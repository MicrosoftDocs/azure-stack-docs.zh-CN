---
title: 轮换机密
titleSuffix: Azure Stack Hub
description: 了解如何在 Azure Stack Hub 中轮换机密。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 04/03/2020
ms.reviewer: ppacent
ms.author: inhenkel
ms.lastreviewed: 12/13/2019
monikerRange: '>=azs-1802'
ms.openlocfilehash: 5ffa1f2f03ca942192acdd881b7e785598c4c94b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80812367"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>在 Azure Stack Hub 中轮换机密

这些说明仅适用于 Azure Stack Hub 集成系统 1803 和更高版本。  请勿在低于 1802 的 Azure Stack Hub 版本上尝试使用机密轮换。

机密可帮助维持 Azure Stack Hub 基础结构资源与服务之间的安全通信。

## <a name="rotate-secrets-overview"></a>机密轮换概述

1. 准备好用于机密轮换的证书。
2. 查看 Azure Stack Hub [公钥基础结构证书要求](https://docs.microsoft.com/azure-stack/operator/azure-stack-pki-certs)。
3. [使用特权终结点](azure-stack-privileged-endpoint.md)并运行 **Test-azurestack** 以确认一切正常。  
4. 查看[机密轮换前的步骤](#pre-steps-for-secret-rotation)。
5. [验证 Azure Stack Hub PKI 证书](https://docs.microsoft.com/azure-stack/operator/azure-stack-validate-pki-certs)。 请确保密码中没有特殊字符，例如 `*` 或 `)`。
6. 请确保 PFX 加密为 **TripleDES-SHA1**。 如果遇到问题，请参阅[修复 Azure Stack Hub PKI 证书的常见问题](https://docs.microsoft.com/azure-stack/operator/azure-stack-remediate-certs#pfx-encryption)。
7. 准备文件夹结构。  可以在[轮换外部机密](https://docs.microsoft.com/azure-stack/operator/azure-stack-rotate-secrets#rotating-external-secrets)部分中找到示例。
8. [开始机密轮换](#use-powershell-to-rotate-secrets)。

## <a name="rotate-secrets"></a>轮换机密

Azure Stack Hub 使用各种机密来维持 Azure Stack Hub 基础结构资源与服务之间的安全通信。

- **内部机密**

    由 Azure Stack Hub 基础结构使用的所有证书、密码、安全字符串和密钥，无需 Azure Stack Hub 操作员的介入。

- **外部机密**

    对外服务的基础结构服务证书，由 Azure Stack Hub 操作员提供。 外部机密包括以下服务的证书：

    - 管理员门户
    - 公共门户
    - 管理员 Azure 资源管理器
    - 全局 Azure 资源管理器
    - 管理员 Key Vault
    - 密钥保管库
    - 管理扩展主机
    - ACS（包括 Blob、表和队列存储）
    - ADFS*
    - Graph*
    
    \*仅当环境的标识提供者是 Active Directory 联合身份验证服务 (AD FS) 时才适用。

> [!Note]
> 其他所有安全密钥和字符串（包括 BMC 和交换密码以及用户和管理员帐户密码）仍然由管理员手动更新。

> [!Important]
> 这些过程不会在 Azure Stack 中心资源提供程序上轮换 Azure App Service 的证书、机密和凭据。  若要进行轮替，你应按照[轮换应用服务机密和证书](app-service-rotate-certificates.md)一文中的步骤进行操作

> [!Important]
> 从 Azure Stack 集线器1811版开始，已为内部和外部证书分离了机密旋转。

为了保持 Azure Stack 中心基础结构的完整性，操作员需要能够定期定期旋转其基础结构的机密，其频率与组织的安全要求一致。

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>使用新证书颁发机构颁发的外部证书轮换机密

Azure Stack 中心支持在以下上下文中通过外部证书从新证书颁发机构（CA）进行密钥轮替：

|已安装的证书 CA|要轮换到的 CA|支持|支持 Azure Stack 集线器版本|
|-----|-----|-----|-----|
|从自签名|到企业|支持|1903 和更高版本|
|从自签名|到自签名|不受支持||
|从自签名|到公共<sup>*</sup>|支持|1803 和更高版本|
|从企业|到企业|支持。 从 1803-1903：只要客户使用与在部署时使用的相同的企业 CA，就可以支持|1803 和更高版本|
|从企业|到自签名|不受支持||
|从企业|到公共<sup>*</sup>|支持|1803 和更高版本|
|从公共<sup>*</sup>|到企业|支持|1903 和更高版本|
|从公共<sup>*</sup>|到自签名|不受支持||
|从公共<sup>*</sup>|到公共<sup>*</sup>|支持|1803 和更高版本|

<sup>*</sup>指示公共证书颁发机构是受 Windows 信任的根程序的一部分的证书颁发机构。 可以在[Microsoft 受信任的根证书程序：参与者（截至2017年6月27日）](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)中找到完整列表。

## <a name="fixing-alerts"></a>修复警报

机密过期后的 30 天内，管理员门户中会生成以下警报：

- 挂起的服务帐户密码过期
- 挂起的内部证书过期
- 外部证书到期挂起

使用以下说明运行机密轮换将会修复这些警报。

> [!Note]
> 在1811之前的版本上 Azure Stack 集线器环境可能会看到挂起内部证书或密钥过期的警报。 这些警报并不正确，应将其忽略，且不运行内部机密轮换。 不正确的内部机密过期警报是已知问题，已在 1811 中得到解决。 除非环境处于活动状态的时间已达两年，否则内部机密不会过期。

## <a name="pre-steps-for-secret-rotation"></a>机密轮换前的步骤

   > [!IMPORTANT]
   > 如果已在 Azure Stack 集线器环境上执行了机密旋转，则必须将系统更新到版本1811或更高版本，然后再次执行密钥轮换。 必须通过[特权终结点](azure-stack-privileged-endpoint.md)执行机密旋转，并要求 Azure Stack 中心操作员凭据。 如果你的环境 Azure Stack 中心操作员不知道是否在你的环境上运行了机密旋转，请在再次执行密钥轮换之前更新为1811。

1. 强烈建议将 Azure Stack 集线器实例更新到版本1811。

    > [!Note]
    > 对于 1811 之前的版本，无需轮换机密即可添加扩展主机证书。 应按照文章为[Azure Stack 中心准备扩展主机](azure-stack-extension-host-prepare.md)中的说明来添加扩展主机证书。

2. 操作员可能会注意到警报在 Azure Stack 集线器机密旋转期间打开并自动关闭。  此行为是预期行为，可以忽略警报。  操作员可以运行 **Test-AzureStack** 来验证这些警报的有效性。  对于使用 System Center Operations Manager 监视 Azure Stack 集线器系统的操作员，将系统置于维护模式时，将会阻止这些警报到达其 ITSM 系统，但如果 Azure Stack 集线器系统变得无法访问，则会继续发出警报。

3. 在执行任何维护操作之前通知用户。 将普通的维护时间段尽量安排在非营业时间。 维护操作可能会同时影响用户工作负荷和门户操作。

    > [!Note]
    > 后续步骤仅适用于循环 Azure Stack 集线器外部机密。

4. 在轮换机密之前，请运行 **[Test-AzureStack](azure-stack-diagnostic-test.md)** 并确认所有测试输出都正常。
5. 准备新的替换外部证书集。 新集与[Azure Stack 中心 PKI 证书要求](azure-stack-pki-certs.md)中所述的证书规格相匹配。 您可以使用 "[准备 Azure Stack 中心 Pki 证书](azure-stack-prepare-pki-certs.md)" 中的步骤，生成用于购买或创建新证书的证书签名请求（CSR） [，并使用在 Azure Stack](azure-stack-get-pki-certs.md)中心环境中所述的步骤准备要在中心环境中使用。 请务必使用[验证 PKI 证书](azure-stack-validate-pki-certs.md)中概述的步骤来验证准备的证书。
6. 将用于轮换的证书备份存储在安全的备份位置。 如果运行轮换时发生失败，请使用备份副本替换文件共享中的证书，然后重新运行轮换。 将备份副本保存在安全的备份位置。
7. 创建可从 ERCS VM 访问的文件共享。 该文件共享必须可供 **CloudAdmin** 标识读取和写入。
8. 在可以访问该文件共享的计算机上打开 PowerShell ISE 控制台。 导航到该文件共享。
9. 运行 **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** 创建外部证书所需的目录。

> [!IMPORTANT]
> CertDirectoryMaker 脚本将创建符合以下要求的文件夹结构：
>
> **.\Certificates\AAD**或 ***.\Certificates\ADFS*** ，具体取决于用于 Azure Stack 集线器的标识提供者。
>
> 至关重要的是，文件夹结构以 **AAD** 或 **ADFS** 文件夹结尾，并且所有子目录都在此结构中；否则，**Start-SecretRotation** 将会出现以下结果：
>
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> 错误消息指出访问文件共享时出现问题，但实际上它是此处强制实施的文件夹结构。 有关详细信息，请参阅 Microsoft Test-azurestack [PublicCertHelper 模块](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)。
>
> 同样重要的是，文件共享文件夹结构以 **Certificates** 文件夹开头，否则验证时也会失败。
> 文件共享装载应类似于** \\ \\ \<IPAddress \\ \<>共享\\名>** 并且它应包含**Certificates\AAD**或**Certificates\ADFS**中的文件夹。
>
> 例如：
> - 文件共享 = ** \\ \\ \<IPAddress \\ \<>共享名>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = ** \\ \\ \<IPAddress>\\共享名\<> \certificates\aad**

## <a name="rotating-external-secrets"></a>轮换外部机密

轮换外部机密：

1. 在前面的步骤中创建的新创建的**\Certificates\\\<IdentityProvider>** 目录中，根据[Azure Stack 中心 PKI 证书要求](azure-stack-pki-certs.md#mandatory-certificates)的**强制性证书**部分中所述的格式，将新的替换外部证书集放在目录结构中。

    Azure AD 标识提供者的文件夹结构示例：
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

2. 使用 **CloudAdmin** 帐户创建具有[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 会话，并将会话存储为变量。 在下一步骤中要使用此变量作为参数。

    > [!IMPORTANT]  
    > 不要输入会话。 请将会话存储为变量。

3. 运行 **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)**。 将特权终结点 PowerShell 会话变量作为 **Session** 参数传递。

4. 结合以下参数运行 **Start-SecretRotation**：
    - **PfxFilesPath**  
    将网络路径指定为前面创建的 Certificates 目录。  
    - **PathAccessCredential**  
    用于访问共享的凭据的 PSCredential 对象。
    - **CertificatePassword**  
    创建的所有 pfx 证书文件使用的密码安全字符串。

5. 等待机密完成轮换。 外部机密轮换需要大约一小时。

    机密轮换成功完成后，控制台会显示“总体操作状态: 成功”。****

    > [!Note]
    > 如果机密轮换失败，请按照错误消息中的说明操作，并结合 **-ReRun** 参数重新运行 **Start-SecretRotation**。

    ```powershell
    Start-SecretRotation -ReRun
    ```

    如果遇到反复的机密轮换失败，请联系技术支持。

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
> 仅当你怀疑内部机密已受到恶意实体的危害，或者收到指出内部证书即将过期的警报（在内部版本 1811 或更高版本上）时，才应该执行内部机密轮换。 在1811之前的版本上 Azure Stack 集线器环境可能会看到挂起内部证书或密钥过期的警报。 这些警报并不正确，应将其忽略，且不运行内部机密轮换。 不正确的内部机密过期警报是已知问题，已在 1811 中得到解决。 除非环境处于活动状态的时间已达两年，否则内部机密不会过期。

1. 创建具有[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 会话。
2. 在特权终结点会话中，运行 **Start-SecretRotation -Internal**。

    > [!Note]
    > 1811以前版本的 Azure Stack 集线器环境不需要 **-Internal**标志。 **Start-SecretRotation** 仅轮换内部机密。

3. 等待机密完成轮换。

   机密轮换成功完成后，控制台会显示“总体操作状态: 成功”。****
    > [!Note]
    > 如果机密轮换失败，请按照错误消息中的说明操作，并使用 **-Internal** 和 **-ReRun** 参数重新运行 **Start-SecretRotation**。  

```powershell
Start-SecretRotation -Internal -ReRun
```

如果遇到反复的机密轮换失败，请联系技术支持。

## <a name="start-secretrotation-reference"></a>Start-SecretRotation 参考

旋转 Azure Stack 中心系统的机密。 仅针对 Azure Stack 集线器特权终结点执行。

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

**Start-secretrotation** cmdlet 可旋转 Azure Stack 中心系统的基础结构机密。 默认情况下，它只轮换所有外部网络基础结构终结点的证书。 如果与 -Internal 标志配合使用，则会轮换内部基础结构机密。 旋转外部网络基础结构终结点时，应使用 Start-secretrotation 脚本块运行**Start-SecretRotation** ，**并将 Azure Stack**中心环境的特权终结点会话作为**session**参数传入。

### <a name="parameters"></a>参数

| 参数 | 类型 | 必需 | 位置 | 默认 | 说明 |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | 字符串  | False  | 已命名  | None  | 包含所有外部网络终结点证书的 **\Certificates** 目录的文件共享路径。 仅当轮换外部机密时才需要。 结尾目录必须是 **\Certificates**。 |
| `CertificatePassword` | SecureString | False  | 已命名  | None  | -PfXFilesPath 中提供的所有证书的密码。 如果在轮换外部机密时提供了 PfxFilesPath，则是必需的值。 |
| `Internal` | 字符串 | False | 已命名 | None | Azure Stack 中心操作员希望轮换内部基础结构机密时，必须使用内部标志。 |
| `PathAccessCredential` | PSCredential | False  | 已命名  | None  | 包含所有外部网络终结点证书的 **\Certificates** 目录的文件共享的 PowerShell 凭据。 仅当轮换外部机密时才需要。  |
| `ReRun` | SwitchParameter | False  | 已命名  | None  | 每当尝试失败后重新尝试机密轮换时，都必须使用 ReRun。 |

### <a name="examples"></a>示例

#### <a name="rotate-only-internal-infrastructure-secrets"></a>仅轮换内部基础结构机密

必须通过 Azure Stack 中心[环境的特权终结点](azure-stack-privileged-endpoint.md)运行此命令。

```powershell
PS C:\> Start-SecretRotation -Internal
```

此命令会将公开的所有基础结构机密旋转到 Azure Stack 中心内部网络。

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

此命令会旋转用于 Azure Stack 中心外部网络基础结构终结点的 TLS 证书。

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>轮换内部和外部基础结构机密（仅限 **1811 以前**的版本）

> [!IMPORTANT]
> 此命令仅适用于**1811** Azure Stack 集线器，因为已为内部和外部证书拆分了旋转。
>
> **在 1811 以上的版本中，不再能够同时轮换内部和外部证书！****

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

此命令会将公开的所有基础结构机密旋转到 Azure Stack 中心内部网络，以及用于 Azure Stack 中心的外部网络基础结构终结点的 TLS 证书。 Start-SecretRotation 轮换堆栈生成的所有机密，由于提供了证书，因此也会轮换外部终结点证书。  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>更新基板管理控制器 (BMC) 凭据

基板管理控制器 (BMC) 监视服务器的物理状态。 请咨询原始设备制造商 (OEM) 硬件供应商，以获取有关更新 BMC 的用户帐户名和密码的说明。

>[!NOTE]
> OEM 可能提供附加的管理应用。 更新其他管理应用的用户名或密码不会影响 BMC 用户名或密码。

1. **早于1910的版本**：按照 OEM 说明更新 Azure Stack 集线器物理服务器上的 BMC。 环境中每个 BMC 的用户名和密码必须相同。 BMC 用户名不能超过 16 个字符。

   **版本1910及更高版本**：根据 OEM 说明，不再需要首先更新 Azure Stack 集线器物理服务器上的 BMC 凭据。 环境中每个 BMC 的用户名和密码必须相同。 BMC 用户名不能超过 16 个字符。

    | 参数 | 说明 | State |
    | --- | --- | --- |
    | BypassBMCUpdate | 使用该参数时，不会更新 BMC 中的凭据。 仅更新 Azure Stack 集线器内部数据存储。 | 可选 |

2. 在 Azure Stack 集线器会话中打开特权终结点。 有关说明，请参阅[使用 Azure Stack 集线器中的特权终结点](azure-stack-privileged-endpoint.md)。

3. 在 PowerShell 提示符更改为 **[IP 地址或 ERCS VM 名称]: PS>** 或更改为 **[azs-ercs01]: PS>**（取决于环境）后，通过运行 `Invoke-Command` 来运行 `Set-BmcCredential`。 将特权终结点会话变量作为参数传递。 例如：

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

[详细了解 Azure Stack Hub 安全性](azure-stack-security-foundations.md)
