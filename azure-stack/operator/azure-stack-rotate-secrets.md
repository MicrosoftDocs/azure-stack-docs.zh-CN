---
title: 轮换机密
titleSuffix: Azure Stack Hub
description: 了解如何在 Azure Stack Hub 中轮换机密。
author: BryanLa
ms.topic: how-to
ms.date: 06/29/2020
ms.reviewer: ppacent
ms.author: bryanla
ms.lastreviewed: 08/15/2020
monikerRange: '>=azs-1803'
ms.openlocfilehash: 7a5135b9b6610e8ceeca4f4d3e34dca1f2aafc88
ms.sourcegitcommit: 9a91dbdaa556725f51bcf3d8e79a4ed2dd5a209f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91847623"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>在 Azure Stack Hub 中轮换机密

*这些说明仅适用于 Azure Stack 集线器集成系统版本1803及更高版本。请勿在1803之前的版本上尝试机密旋转*

本文提供了用于机密旋转的指南和 PowerShell 脚本，以帮助维护与 Azure Stack 中心基础结构资源和服务的安全通信。 

## <a name="overview"></a>概述

Azure Stack 集线器使用机密来维护与基础结构资源和服务之间的安全通信。 为了保持 Azure Stack 中心基础结构的完整性，操作员需要能够根据其组织的安全要求来旋转机密。

### <a name="internal-vs-external-secrets"></a>内部和外部机密

从1811版开始，将为内部和外部证书分离机密旋转：

- **内部机密**： Azure Stack 中心基础结构使用的证书、密码、安全字符串和密钥，而无需 Azure Stack 中心运营商介入。

- **外部机密**：由 Azure Stack 中心操作员提供的面向外部的服务的基础结构服务证书。 外部机密包括以下服务的证书：

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

> [!Important]
> 所有其他安全密钥和字符串由管理员手动更新。 这包括用户和管理员帐户密码、 [网络交换机密码和权限](azure-stack-customer-defined.md)、基板管理控制器 (BMC) 凭据，这将 [在本文的后面部分介绍](#update-the-bmc-credential)。 
>
>此外，本文不会针对增值资源提供程序处理机密旋转。 若要轮换这些机密，请改为参阅以下文章：
>
> - [轮换 Azure Stack Hub 上应用服务的机密和证书](app-service-rotate-certificates.md)
> - [MySQL 资源提供程序-轮换机密](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)
> - [SQL 资源提供程序-轮换机密](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)

### <a name="expiration-alerts"></a>过期警报

机密过期后的 30 天内，管理员门户中会生成以下警报：

- 挂起的服务帐户密码过期
- 挂起的内部证书过期
- 挂起的外部证书过期

完成以下部分中的机密旋转步骤可解决这些警报。

> [!Note]
> 在 1811 之前版本的 Azure Stack Hub 环境中，可能会看到内部证书挂起或机密过期的警报。 这些警报并不正确，应将其忽略，且不运行内部机密轮换。 不正确的内部机密过期警报是已知问题，已在 1811 中得到解决。 除非环境处于活动状态的时间已达两年，否则内部机密不会过期。

### <a name="external-certificates-from-a-new-certificate-authority"></a>新证书颁发机构颁发的外部证书

在以下上下文中，Azure Stack Hub 支持使用新证书颁发机构 (CA) 颁发的外部证书进行机密轮换：

|已安装的证书 CA|要轮换到的 CA|支持|支持的 Azure Stack Hub 版本|
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

<sup>*</sup>指示公共证书颁发机构属于 Windows 受信任的根程序。 可以在 [参与者列表-Microsoft 受信任的根计划](/security/trusted-root/participants-list)中找到完整列表。

## <a name="prerequisites"></a>先决条件

用于旋转内部和外部机密：

1. 强烈建议先将 Azure Stack 中心实例更新到最新版本。

    ::: moniker range="<azs-1811"  
    >[!IMPORTANT]
    > 对于1811之前的版本：
    > - 如果已执行了机密旋转，则必须先更新到版本1811或更高版本，然后再再次执行密钥轮换。 必须通过[特权终结点](azure-stack-privileged-endpoint.md)执行机密轮换，且需要有 Azure Stack Hub 操作员凭据。 如果你不知道是否在你的环境上运行了机密旋转，请在执行秘密旋转之前更新到1811。
    > - 无需轮换机密即可添加扩展主机证书。 应该按照[准备 Azure Stack Hub 的扩展主机](azure-stack-extension-host-prepare.md)一文中的说明添加扩展主机证书。
    ::: moniker-end

2. 向用户通知计划内维护操作。 将普通的维护时间段尽量安排在非营业时间。 维护操作可能会同时影响用户工作负荷和门户操作。

3. 旋转机密期间，操作员可能会注意到警报打开并自动关闭。 此行为是预期行为，可以忽略警报。 操作员可以使用 [Test-azurestack PowerShell cmdlet](azure-stack-diagnostic-test.md)来验证这些警报的有效性。 对于使用 System Center Operations Manager 监视 Azure Stack Hub 系统的操作人员来说，将系统置于维护模式将阻止这些警报到达其 ITSM 系统，但如果 Azure Stack Hub 系统无法访问，则将继续发出警报。

若要轮替外部机密，请完成以下附加先决条件：

1. **[`Test-AzureStack`](azure-stack-diagnostic-test.md)** 使用参数运行 PowerShell cmdlet `-group SecretRotationReadiness` ，以在轮换机密之前确认所有测试输出都处于正常状态。
2. 准备一组新的替换外部证书：
    - 新集必须与 [Azure Stack 中心 PKI 证书要求](azure-stack-pki-certs.md)中所述的证书规格匹配。 
    - 你可以 (CSR 生成证书签名请求) 使用 "[准备 PKI 证书](azure-stack-prepare-pki-certs.md)" 中所述的步骤，使用 "[生成证书签名请求](azure-stack-get-pki-certs.md)并准备好用于 Azure Stack 中心环境中所述的步骤，将证书签名请求提交给证书颁发机构 () CA"。 
    - 务必按照[验证 PKI 证书](azure-stack-validate-pki-certs.md)中所述的步骤验证你准备的证书
    - 请确保密码中没有特殊字符，例如 `*` 或 `)`。
    - 请确保 PFX 加密为 **TripleDES-SHA1**。 如果遇到问题，请参阅[修复 Azure Stack Hub PKI 证书的常见问题](azure-stack-remediate-certs.md#pfx-encryption)。
3. 将用于轮换的证书备份存储在安全的备份位置。 如果运行轮换时发生失败，请使用备份副本替换文件共享中的证书，然后重新运行轮换。 将备份副本保存在安全的备份位置。
4. 创建可从 ERCS VM 访问的文件共享。 该文件共享必须可供 **CloudAdmin** 标识读取和写入。
5. 在可以访问该文件共享的计算机上打开 PowerShell ISE 控制台。 导航到你的文件共享，你可以在其中创建用于放置外部证书的目录。
6. 将 **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** 下载到可以在轮换期间访问的网络文件共享，并运行该脚本。 此脚本将创建符合 ***.\Certificates\AAD*** 或 ***.\Certificates\ADFS***的文件夹结构，具体取决于你的标识提供者。 文件夹结构必须以** \\ 证书**文件夹开头，后跟** \\ AAD**或** \\ ADFS**文件夹。 所有其他子目录都包含在前一结构中。 例如：
    - 文件共享 = **\\\\\<IPAddress>\\\<ShareName>**
    - Azure AD 提供程序的证书根文件夹 = ** \\ Certificates\AAD**
    - 完整路径 = ** \\ \\ \<IPAddress> \\ \<ShareName> \Certificates\AAD**

    > [!IMPORTANT]
    > 稍后运行时 `Start-SecretRotation` ，它将验证文件夹结构。 不符合的文件夹结构将引发以下错误：
    >
    > ```powershell
    > Cannot bind argument to parameter 'Path' because it is null.
    > + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
    > + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
    > + PSComputerName        : xxx.xxx.xxx.xxx
    > ```

7. 将在步骤 #2 中创建的一组新的替换外部证书复制到在步骤 #6 中创建的**\Certificates \\ \<IdentityProvider> **目录。 请确保遵循的 `cert.<regionName>.<externalFQDN>` 格式 \<CertName\> 。 

    下面是 Azure AD 标识提供者的文件夹结构的示例：
    ```powershell
        <ShareName>
            │
            └───Certificates
                  └───AAD
                      ├───ACSBlob
                      │       <CertName>.pfx
                      │
                      ├───ACSQueue
                      │       <CertName>.pfx
                      │
                      ├───ACSTable
                      │       <CertName>.pfx
                      │
                      ├───Admin Extension Host
                      │       <CertName>.pfx
                      │
                      ├───Admin Portal
                      │       <CertName>.pfx
                      │
                      ├───ARM Admin
                      │       <CertName>.pfx
                      │
                      ├───ARM Public
                      │       <CertName>.pfx
                      │
                      ├───KeyVault
                      │       <CertName>.pfx
                      │
                      ├───KeyVaultInternal
                      │       <CertName>.pfx
                      │
                      ├───Public Extension Host
                      │       <CertName>.pfx
                      │
                      └───Public Portal
                              <CertName>.pfx

    ```

## <a name="rotate-external-secrets"></a>旋转外部机密

完成以下步骤来轮换外部机密：

1. 使用以下 PowerShell 脚本轮换密钥。 脚本需要访问特权终结点， (PEP) 会话。 可以通过虚拟机上的远程 PowerShell 会话访问 PEP， (VM) 承载 PEP。 如果使用的是集成系统，则有三个 PEP 实例，每个实例都在一个 VM 中运行 (ERCS01、Prefix-ERCS02 或 ERCS03) 在不同的主机上。 如果使用的是 ASDK，则此 VM 名为 AzS-ERCS01。 `<placeholder>`在运行之前更新值：

    ```powershell
    # Create a PEP Session
    winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
    $PEPCreds = Get-Credential
    $PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS_Machine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    # Run Secret Rotation
    $CertPassword = ConvertTo-SecureString "<Cert_Password>" -AsPlainText -Force
    $CertShareCreds = Get-Credential
    $CertSharePath = "<Network_Path_Of_CertShare>"
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
    }
    Remove-PSSession -Session $PEPSession
    ```

    此脚本执行以下步骤：

    - 使用**CloudAdmin**帐户创建具有[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 会话，并将会话存储为变量。 此变量在下一步中用作参数。 

    - 运行 [调用-命令](/powershell/module/microsoft.powershell.core/Invoke-Command)，将 PEP 会话变量作为参数传递 `-Session` 。

    - `Start-SecretRotation`使用以下参数在 PEP 会话中运行：
        - `-PfxFilesPath`：之前创建的证书目录的网络路径。  
        - `-PathAccessCredential`：共享的凭据的 PSCredential 对象。
        - `-CertificatePassword`：用于创建的所有 pfx 证书文件的密码的安全字符串。

2. 外部机密轮换需要大约一小时。 成功完成后，控制台将显示 `ActionPlanInstanceID ... CurrentStatus: Completed` ，后跟 `DONE` 。 从 "先决条件" 部分中创建的共享中删除证书，并将其存储在安全的备份位置。

    > [!Note]
    > 如果秘密旋转失败，请按照错误消息中的说明进行操作，然后 `Start-SecretRotation` 用参数重新运行 `-ReRun` 。
    >
    >```powershell
    >Start-SecretRotation -ReRun
    >```  
    >
    >如果遇到反复的机密轮换失败，请联系技术支持。

## <a name="rotate-internal-secrets"></a>旋转内部机密

仅当你怀疑某个密钥已泄露，或者收到过期警报时，才需要内部密钥轮换。 1811之前的版本可能会显示挂起的内部证书或密钥过期的警报。 这些警报不准确并应忽略，这是1811中解决的已知问题。 除非环境处于活动状态的时间已达两年，否则内部机密不会过期。

参考第2步： [轮换外部机密](#rotate-external-secrets)中的 PowerShell 脚本。 此脚本提供一个示例，你可以通过进行一些更改来运行以下步骤，以适应内部机密旋转：

1. 在 "运行机密旋转" 部分，将参数添加 `-Internal` 到 [start-secretrotation cmdlet](/azure-stack/reference/pep-2002/start-secretrotation)，例如：

    ```powershell
    # Run Secret Rotation
    ...
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -Internal
    }
    ...
    ```

    ::: moniker range="<azs-1811"
    > [!Note]
    > 1811之前的版本不需要该 `-Internal` 标志。 
    ::: moniker-end

3. 成功完成后，控制台将显示 `ActionPlanInstanceID ... CurrentStatus: Completed` ，后跟 `DONE`

    > [!Note]
    > 如果秘密旋转失败，请按照错误消息中的说明进行操作，并返回 `Start-SecretRotation`  `-Internal` 和 `-ReRun` 参数。  
    >
    >```powershell
    >Start-SecretRotation -Internal -ReRun
    >```
    >
    > 如果遇到反复的机密轮换失败，请联系技术支持。

## <a name="update-the-bmc-credential"></a>更新 BMC 凭据

基板管理控制器监视服务器的物理状态。 请咨询原始设备制造商 (OEM) 硬件供应商，以获取有关更新 BMC 的用户帐户名和密码的说明。

>[!NOTE]
> OEM 可能提供附加的管理应用。 更新其他管理应用程序的用户名或密码不会影响 BMC 用户名或密码。 

::: moniker range="<azs-1910"
1. 按照 OEM 说明，更新 Azure Stack 集线器物理服务器上的 BMC。 环境中每个 BMC 的用户名和密码必须相同。 BMC 用户名不能超过 16 个字符。
::: moniker-end

::: moniker range=">=azs-1910"
1. 按照 OEM 说明，不再需要首先更新 Azure Stack 集线器物理服务器上的 BMC 凭据。 环境中每个 BMC 的用户名和密码必须相同，并且不能超过16个字符。 
::: moniker-end

2. 在 Azure Stack Hub 会话中打开特权终结点。 有关说明，请参阅[使用 Azure Stack Hub 中的特权终结点](azure-stack-privileged-endpoint.md)。 

3. 打开特权终结点会话后，运行下面的一个 PowerShell 脚本，该脚本使用 Invoke-Command 运行 BmcCredential。 如果将 BypassBMCUpdate 参数与 BMCCredential 一起使用，则不会更新 BMC 中的凭据。 仅更新 Azure Stack 集线器内部数据存储。将特权终结点会话变量作为参数进行传递。

    下面是一个示例 PowerShell 脚本，它将提示输入用户名和密码： 

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

    你还可以在变量中对用户名和密码进行编码，这可能会降低安全性：

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

## <a name="reference-start-secretrotation-cmdlet"></a>参考： Start-SecretRotation cmdlet

[Start-secretrotation cmdlet](/azure-stack/reference/pep-2002/start-secretrotation) 旋转 Azure Stack 中心系统的基础结构机密。 仅可通过使用  `Invoke-Command` 在参数中传递 PEP 会话的脚本块，对 Azure Stack 集线器特权终结点执行此 cmdlet `-Session` 。 默认情况下，它只轮换所有外部网络基础结构终结点的证书。

| 参数 | 类型 | 必须 | 位置 | 默认 | 说明 |
|--|--|--|--|--|--|
| `PfxFilesPath` | String  | False  | 名为  | 无  | 包含所有外部网络终结点证书的 **\Certificates** 目录的文件共享路径。 仅当轮换外部机密时才需要。 结尾目录必须是 **\Certificates**。 |
| `CertificatePassword` | SecureString | False  | 名为  | 无  | -PfXFilesPath 中提供的所有证书的密码。 如果在轮换外部机密时提供了 PfxFilesPath，则是必需的值。 |
| `Internal` | String | False | 名为 | 无 | 每当 Azure Stack Hub 操作员想要轮换内部基础结构机密时，都必须使用 Internal 标志。 |
| `PathAccessCredential` | PSCredential | False  | 名为  | 无  | 包含所有外部网络终结点证书的 **\Certificates** 目录的文件共享的 PowerShell 凭据。 仅当轮换外部机密时才需要。  |
| `ReRun` | SwitchParameter | False  | 名为  | 无  | 尝试失败后，无论何时判断，都必须使用密码。 |

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

### <a name="examples"></a>示例

#### <a name="rotate-only-internal-infrastructure-secrets"></a>仅轮换内部基础结构机密

此命令必须通过 Azure Stack Hub [环境的特权终结点](azure-stack-privileged-endpoint.md)运行。

```powershell
PS C:\> Start-SecretRotation -Internal
```

此命令轮换向 Azure Stack Hub 内部网络公开的所有基础结构机密。

#### <a name="rotate-only-external-infrastructure-secrets"></a>仅轮换外部基础结构机密  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

此命令轮换用于 Azure Stack Hub 外部网络基础结构终结点的 TLS 证书。

::: moniker range="<azs-1811"
#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>轮换内部和外部基础结构机密（仅限 **1811 以前**的版本）

> [!IMPORTANT]
> 此命令仅适用于 Azure Stack Hub **1811 以前**的版本，因为轮换将会针对内部和外部证书分开进行。
>
> **在 1811 以上的版本中，不再能够同时轮换内部和外部证书！** 

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

此命令会将公开的基础结构机密旋转到 Azure Stack 中心内部网络，使用 TLS 证书来 Azure Stack 中心的外部网络基础结构终结点。 Start-SecretRotation 轮换堆栈生成的所有机密，由于提供了证书，因此也会轮换外部终结点证书。  
::: moniker-end

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Stack Hub 安全性](azure-stack-security-foundations.md)
