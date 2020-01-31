---
title: 将 AD FS 标识与 Azure Stack 中心数据中心集成
description: 了解如何将 Azure Stack 中心 AD FS 标识提供程序与数据中心 AD FS 集成。
author: ihenkel
ms.topic: article
ms.date: 05/10/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 1e55ae573d67775389e1e8e8ebac1b9ba094e5a7
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882133"
---
# <a name="integrate-ad-fs-identity-with-your-azure-stack-hub-datacenter"></a>将 AD FS 标识与 Azure Stack 中心数据中心集成

你可以使用 Azure Active Directory （Azure AD）或 Active Directory 联合身份验证服务（AD FS）作为标识提供者来部署 Azure Stack 中心。 在部署 Azure Stack 集线器之前，必须进行选择。 在连接的方案中，可以选择 Azure AD 或 AD FS。 对于断开连接的情况，仅支持 AD FS。 本文介绍如何将 Azure Stack 中心 AD FS 与数据中心 AD FS 集成。

> [!IMPORTANT]
> 不需要重新部署整个 Azure Stack 中心解决方案，就无法切换标识提供者。

## <a name="active-directory-federation-services-and-graph"></a>Active Directory 联合身份验证服务和图形

使用 AD FS 部署时，允许现有 Active Directory 林中的标识通过 Azure Stack 集线器中的资源进行身份验证。 此现有 Active Directory 林要求部署 AD FS，以允许创建 AD FS 联合身份验证信任。

身份验证是标识的一部分。 若要在 Azure Stack 中心管理基于角色的访问控制（RBAC），必须配置图形组件。 委托对资源的访问权限时，图形组件将使用 LDAP 协议查找现有 Active Directory 林中的用户帐户。

![Azure Stack 中心 AD FS 体系结构](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

现有 AD FS 是将声明发送到 Azure Stack 中心 AD FS （资源 STS）的帐户 security token service （STS）。 在 Azure Stack 中心，自动化会创建声明提供方信任，其中包含现有 AD FS 的元数据终结点。

在现有 AD FS 上，必须配置信赖方信任。 此步骤不是由自动化实现的，并且必须由运算符进行配置。 可以使用模式 `https://adfs.<Region>.<ExternalFQDN>/`创建 AD FS 的 Azure Stack 集线器 VIP 终结点。

依赖方信任配置还要求你配置 Microsoft 提供的声明转换规则。

对于图形配置，必须提供在现有 Active Directory 中具有 "读取" 权限的服务帐户。 此帐户是自动化的输入，用于启用 RBAC 方案。

对于最后一个步骤，为默认提供商订阅配置新的所有者。 登录到 Azure Stack 中心管理员门户时，此帐户对所有资源具有完全访问权限。

要求：

|组件|要求|
|---------|---------|
|图形|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>设置 Graph 集成

Graph 仅支持与单个 Active Directory 林集成。 如果存在多个林，则仅使用配置中指定的林来提取用户和组。

以下信息是自动化参数的输入所必需的：

|参数|部署工作表参数|Description|示例|
|---------|---------|---------|---------|
|`CustomADGlobalCatalog`|AD FS 林 FQDN|要与之集成的目标 Active Directory 林的 FQDN|Contoso.com|
|`CustomADAdminCredentials`| |具有 LDAP 读取权限的用户|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>配置 Active Directory 站点

对于具有多个站点的 Active Directory 部署，请将最近的 Active Directory 站点配置为你的 Azure Stack 中心部署。 配置避免了使用远程站点中的全局编录服务器 Azure Stack 集线器图形服务解析查询。

将 Azure Stack 中心[公共 VIP 网络](azure-stack-network.md#public-vip-network)子网添加到离 Azure Stack 中心最近的 Active Directory 站点。 例如，假设您的 Active Directory 有两个站点：西雅图和 Redmond。 如果 Azure Stack 中心部署在西雅图站点，则需要将 Azure Stack 中心公共 VIP 网络子网添加到西雅图的 Active Directory 站点。

有关 Active Directory 站点的详细信息，请参阅[设计站点拓扑](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)。

> [!Note]  
> 如果 Active Directory 包含单一站点，则可以跳过此步骤。 如果已配置 "全部捕获" 子网，请验证 Azure Stack 中心公共 VIP 网络子网是否不属于该网络子网。

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>在现有 Active Directory 中创建用户帐户（可选）

或者，您可以在现有 Active Directory 中为图形服务创建帐户。 如果还没有要使用的帐户，请执行此步骤。

1. 在现有 Active Directory 中，创建以下用户帐户（建议）：
   - **用户名**： graphservice
   - **密码**：使用强密码并将密码配置为永不过期。

   不需要任何特殊权限或成员身份。

#### <a name="trigger-automation-to-configure-graph"></a>触发自动化以配置关系图

对于此过程，请使用数据中心网络中的计算机，该计算机可以与 Azure Stack 集线器中的特权终结点进行通信。

1. 打开提升的 Windows PowerShell 会话（以管理员身份运行），并连接到特权终结点的 IP 地址。 使用**CloudAdmin**的凭据进行身份验证。

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 现在，你已连接到特权终结点，请运行以下命令： 

   ```powershell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   出现提示时，请指定要用于图形服务的用户帐户的凭据（如 graphservice）。 DirectoryService cmdlet 的输入必须是林中的林名称/根域，而不是林中的任何其他域。

   > [!IMPORTANT]
   > 等待 "凭据" 弹出窗口（特权终结点中不支持 "获取凭据"），然后输入图形服务帐户凭据。

3. **DirectoryService** cmdlet 具有可在现有 Active Directory 验证失败的某些情况下使用的可选参数。 执行此 cmdlet 时，它会验证提供的域是否为根域、是否可以访问全局编录服务器，以及是否向提供的帐户授予读取访问权限。

   |参数|Description|
   |---------|---------|
   |`-SkipRootDomainValidation`|指定必须使用子域，而不是推荐的根域。|
   |`-Force`|跳过所有验证检查。|

#### <a name="graph-protocols-and-ports"></a>图形协议和端口

Azure Stack 集线器中的图形服务使用以下协议和端口与可以处理目标 Active Directory 林中的登录请求的可写全局编录服务器（GC）和密钥发行中心（KDC）进行通信。

Azure Stack 中心中的图形服务使用以下协议和端口与目标 Active Directory 通信：

|类型|Port|协议|
|---------|---------|---------|
|LDAP|389|TCP & UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>通过下载联合元数据设置 AD FS 集成

以下信息是自动化参数的输入所必需的：

|参数|部署工作表参数|Description|示例|
|---------|---------|---------|---------|
|CustomAdfsName|AD FS 提供程序名称|声明提供程序的名称。<br>它在 AD FS 登陆页上以这种方式出现。|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|AD FS 元数据 URI|联合元数据链接。| https：\//ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |
|SigningCertificateRevocationCheck|不可用|用于跳过 CRL 检查的可选参数。|无|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>触发自动化以在 Azure Stack 中心配置声明提供方信任

对于此过程，请使用能够与 Azure Stack 中心中的特权终结点进行通信的计算机。 Azure Stack 中心信任帐户**STS AD FS**所使用的证书。

1. 打开提升的 Windows PowerShell 会话并连接到特权终结点。

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 连接到特权终结点后，请使用适用于环境的参数运行以下命令：

   ```powershell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. 运行以下命令，使用适用于你的环境的参数更新默认提供程序订阅的所有者：

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>通过提供联合元数据文件设置 AD FS 集成

从版本1807开始，如果满足下列任一条件，请使用此方法：

- 与 Azure Stack 中心中的所有其他终结点相比，证书链 AD FS 不同。
- 与现有 AD FS 服务器的网络连接不能 Azure Stack 中心的 AD FS 实例中。

以下信息是自动化参数的输入所必需的：


|参数|Description|示例|
|---------|---------|---------|
|CustomAdfsName|声明提供程序的名称。 它在 AD FS 登陆页上以这种方式出现。|Contoso|
|CustomADFSFederationMetadataFileContent|元数据内容。|$using： federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>创建联合元数据文件

对于以下过程，必须使用与现有 AD FS 部署的网络连接的计算机，该计算机将成为帐户 STS。 还必须安装所需的证书。

1. 打开提升的 Windows PowerShell 会话，并使用适用于你的环境的参数运行以下命令：

   ```powershell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. 将元数据文件复制到可与特权终结点通信的计算机。

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>触发自动化以在 Azure Stack 中心配置声明提供方信任

对于此过程，请使用能够与 Azure Stack 中心中的特权终结点进行通信的计算机，并且有权访问在上一步中创建的元数据文件。

1. 打开提升的 Windows PowerShell 会话并连接到特权终结点。

   ```powershell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 连接到特权终结点后，请使用适用于环境的参数运行以下命令：

    ```powershell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. 运行以下命令以更新默认提供程序订阅的所有者。 使用适合你的环境的参数。

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > 在现有 AD FS （帐户 STS）上旋转证书时，必须重新设置 AD FS 集成。 即使元数据终结点是可访问的或通过提供元数据文件配置的，也必须设置集成。

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>在现有 AD FS 部署上配置信赖方（帐户 STS）

Microsoft 提供了一种用于配置信赖方信任的脚本，包括声明转换规则。 使用脚本是可选的，因为你可以手动运行这些命令。

可以从 GitHub 上的[Azure Stack 集线器工具](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity)下载帮助器脚本。

如果决定手动运行这些命令，请遵循以下步骤：

1. 将以下内容复制到数据中心的 AD FS 实例或场成员上的 .txt 文件中（例如，保存为 c:\ClaimRules.txt）：

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. 验证是否已启用 extranet 和 intranet 基于 Windows 窗体的身份验证。 可以通过运行以下 cmdlet 来检查它是否已启用：

   ```powershell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Windows 集成身份验证（WIA）支持的用户代理字符串可能已过时 AD FS 部署，可能需要更新以支持最新的客户端。 若要详细了解如何更新 WIA 支持的用户代理字符串，请参阅[为不支持 wia 的设备配置基于 intranet 窗体的身份验证](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia)。<br><br>有关启用基于窗体的身份验证策略的步骤，请参阅[配置身份验证策略](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies)。

3. 若要添加信赖方信任，请在 AD FS 实例或场成员上运行以下 Windows PowerShell 命令。 请确保更新 AD FS 终结点，并指向在步骤1中创建的文件。

   **对于 AD FS 2016**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **对于 AD FS 2012/2012 R2**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > 使用 Windows Server 2012 或 2012 R2 AD FS 时，必须使用 AD FS MMC 管理单元来配置颁发授权规则。

4. 使用 Internet Explorer 或 Microsoft Edge 浏览器访问 Azure Stack 中心时，必须忽略令牌绑定。 否则，登录尝试将失败。 在 AD FS 实例或场成员上运行以下命令：

   > [!note]  
   > 使用 Windows Server 2012 或 2012 R2 AD FS 时，此步骤不适用。 在这种情况下，可以放心地跳过此命令并继续集成。

   ```powershell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>SPN 创建

在许多情况下，需要使用服务主体名称（SPN）进行身份验证。 下面是一些示例：

- CLI 用法与 Azure Stack 中心的 AD FS 部署。
- 与 AD FS 一起部署时，适用于 Azure Stack 中心的 System Center 管理包。
- 部署时，Azure Stack 中心中的资源提供程序 AD FS。
- 各种应用程序。
- 需要非交互式登录。

> [!Important]  
> AD FS 仅支持交互式登录会话。 如果需要自动方案的非交互式登录，则必须使用 SPN。

有关创建 SPN 的详细信息，请参阅[创建 AD FS 的服务主体](azure-stack-create-service-principals.md)。


## <a name="troubleshooting"></a>故障排除

### <a name="configuration-rollback"></a>配置回滚

如果发生了导致环境处于无法再进行身份验证的状态的错误，则可以使用回滚选项。

1. 打开提升的 Windows PowerShell 会话并运行以下命令：

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 然后运行以下 cmdlet：

   ```powershell  
   Reset-DatacenterIntegrationConfiguration
   ```

   运行回滚操作后，将回滚所有配置更改。 仅可通过内置的**CloudAdmin**用户进行身份验证。

   > [!IMPORTANT]
   > 必须配置默认提供程序订阅的原始所有者。

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>收集其他日志

如果任何 cmdlet 失败，可以使用 `Get-Azurestacklogs` cmdlet 来收集其他日志。

1. 打开提升的 Windows PowerShell 会话并运行以下命令：

   ```powershell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 然后运行以下 cmdlet：

   ```powershell  
   Get-AzureStackLog -OutputPath \\myworkstation\AzureStackLogs -FilterByRole ECE
   ```

## <a name="next-steps"></a>后续步骤

[集成外部监视解决方案](azure-stack-integrate-monitor.md)
