---
title: 管理 Azure Stack 的服务主体 | Microsoft Docs
description: 介绍如何管理新的服务主体，并在 Azure 资源管理器中将此服务主体与基于角色的访问控制配合使用以管理对资源的访问权限。
services: azure-resource-manager
documentationcenter: na
author: PatAltimore
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: patricka
ms.lastreviewed: 05/17/2019
ms.openlocfilehash: 57c5547c37fee58b37f8386983d43556ed0fd515
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268796"
---
# <a name="provide-applications-access-to-azure-stack"></a>向应用程序提供 Azure Stack 的访问权限

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

当应用程序需要在 Azure Stack 通过Azure 资源管理器部署或配置资源的访问权限时，请创建服务主体（它是应用程序的凭据）。 然后可以仅将必需的权限委派给该服务主体。  

例如，你可能有一个使用 Azure 资源管理器来清点 Azure 资源的配置管理工具。 在此方案中，可以创建服务主体，向该服务主体授予读者角色，并将配置管理工具限制为只能进行只读访问。 

与使用自己的凭据运行应用相比，服务主体更优，原因在于：

 - 可以向服务主体分配不同于自己的帐户权限的权限。 通常情况下，这些权限仅限于应用需执行的操作。
 - 职责变化时，无需更改应用的凭据。
 - 执行无人参与的脚本时，可使用证书自动进行身份验证。  

## <a name="getting-started"></a>入门

根据部署 Azure Stack 的方式，可以首先创建服务主体。 本文档介绍如何为以下对象创建服务主体：

- Azure Active Directory (Azure AD)。 Azure AD 是基于云的多租户目录和标识管理服务。 可将 Azure AD 与联网 Azure Stack 配合使用。
- Active Directory 联合身份验证服务 (AD FS)。 AD FS 提供简化、安全的标识联合与 Web 单一登录 (SSO) 功能。 可将 AD FS 与联网和离线 Azure Stack 实例配合使用。

创建服务主体后，将使用普遍适用于 AD FS 和 Azure Active Directory 的一组步骤向角色委派权限。

## <a name="manage-service-principal-for-azure-ad"></a>管理 Azure AD 的服务主体

如果在使用 Azure Active Directory (Azure AD) 作为标识管理服务的情况下部署了 Azure Stack，可以像在 Azure 中那样创建服务主体。 本部分演示如何通过门户执行这些步骤。 检查你是否有 [必需在开始之前的 Azure AD permissions]((/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

### <a name="create-service-principal"></a>创建服务主体

在本部分中，将在 Azure AD 中创建表示你的应用程序的应用程序（服务主体）。

1. 通过 [Azure 门户](https://portal.azure.com)登录到 Azure 帐户。
2. 选择“Azure Active Directory” > “应用注册” > “新建注册”。   
3. 为应用提供名称和 URL。 
4. 选择“支持的帐户类型”。 
5.  添加应用程序的 URI。 选择“Web”  作为要创建的应用程序的类型。 设置这些值后，选择“注册”  。

已为应用程序创建服务主体。

### <a name="get-credentials"></a>获取凭据

以编程方式登录时，需要使用应用程序、Web 应用/API 的 ID 和身份验证密钥。 若要获取这些值，请使用以下步骤：

1. 选择“Azure Active Directory” > “应用注册”。   选择自己的应用程序。

2. 复制“应用程序 ID”  并将其存储在应用程序代码中。 “示例应用程序”部分的应用程序引用此值作为客户端 ID。

3. 若要生成的 Web 应用的身份验证密钥 / API 中，选择**证书和机密**。 选择“新建客户端机密”。 

4. 提供密钥说明和密钥持续时间。 完成后，选择**添加**。

保存密钥后, 会显示密钥的值。 将此值复制到记事本或其他某个临时位置，因为以后无法检索该密钥。 提供密钥值及应用程序 ID 登录为该应用程序。 将密钥值存储在应用程序可检索的位置。

![保存的密钥](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

完成后，可为应用程序分配角色。

## <a name="manage-service-principal-for-ad-fs"></a>管理 AD FS 的服务主体

如果在使用 Active Directory 联合身份验证服务 (AD FS) 作为标识管理服务的情况下部署了 Azure Stack，请使用 PowerShell 创建服务主体，分配用于进行访问的角色，然后使用该标识登录。

可以使用两种方法之一通过 AD FS 创建服务主体。 可以:
 - [使用证书创建服务主体](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [使用客户端机密创建服务主体](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

用于管理 AD FS 服务主体的任务。

| Type | 操作 |
| --- | --- |
| AD FS 证书 | [创建](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| AD FS 证书 | [更新](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| AD FS 证书 | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| AD FS 客户端机密 | [创建](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| AD FS 客户端机密 | [更新](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| AD FS 客户端机密 | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>使用证书创建服务主体

在使用 AD FS 作为标识创建服务主体时，可以使用证书。

#### <a name="certificate"></a>证书

需要证书。

**证书要求**

 - 加密服务提供程序 (CSP) 必须是旧密钥提供程序。
 - 证书格式必须是 PFX 文件，因为公钥和私钥都是必需的。 Windows 服务器使用包含公钥文件（SSL 证书文件）和关联的私钥文件的 .pfx 文件。
 - 对于生产环境，证书必须由内部证书颁发机构或公共证书颁发机构颁发。 如果你使用公共证书颁发机构，则必须将基础操作系统映像中的颁发机构包括为 Microsoft 信任根颁发机构计划的一部分。 可以在 [Microsoft 信任根证书计划：参与者](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)中找到完整列表。
 - Azure Stack 基础结构必须能够通过网络访问证书中发布的证书颁发机构的证书吊销列表 (CRL) 位置。 此 CRL 必须是一个 HTTP 终结点。

#### <a name="parameters"></a>parameters

以下信息是作为自动化参数的输入所必需的：

|参数|描述|示例|
|---------|---------|---------|
|名称|SPN 帐户的名称|MyAPP|
|ClientCertificates|证书对象的数组|X509 证书|
|ClientRedirectUris<br>(可选)|应用程序重定向 URI|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>使用 PowerShell 创建服务主体

1. 打开权限提升的 Windows PowerShell 会话，并运行以下 cmdlet：

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > 出于验证目的，可以使用以下示例创建一个自签名证书：

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. 自动化完成后，它将显示使用该 SPN 所需的详细信息。 建议存储该输出以供稍后使用。

   例如:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>更新 AD FS 服务主体的证书

如果已结合 AD FS 部署 Azure Stack，可以使用 PowerShell 来更新服务主体的机密。

从 ERCS 虚拟机上的特权终结点运行脚本。

#### <a name="parameters"></a>parameters

以下信息是作为自动化参数的输入所必需的：

|参数|描述|示例|
|---------|---------|---------|
|名称|SPN 帐户的名称|MyAPP|
|ApplicationIdentifier|唯一标识符|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|证书对象的数组|X509 证书|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>更新 AD FS 服务主体的示例

该示例创建一个自签名证书。 在生产部署中运行 cmdlet 时，请使用 [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) 检索要使用的证书的证书对象。

1. 打开权限提升的 Windows PowerShell 会话，并运行以下 cmdlet：

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. 自动化完成之后，会显示 SPN 身份验证所需的已更新指纹值。

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>使用客户端机密创建服务主体

在使用 AD FS 作为标识创建服务主体时，可以使用证书。 使用特权终结点运行 cmdlet。

从 ERCS 虚拟机上的特权终结点运行这些脚本。 有关特权终结点的详细信息，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。

#### <a name="parameters"></a>parameters

以下信息是作为自动化参数的输入所必需的：

| 参数 | 描述 | 示例 |
|----------------------|--------------------------|---------|
| 名称 | SPN 帐户的名称 | MyAPP |
| GenerateClientSecret | 创建机密 |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>使用 ERCS PrivilegedEndpoint 创建服务主体

1. 打开权限提升的 Windows PowerShell 会话，并运行以下 cmdlet：

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. 运行 cmdlet 后，shell 会显示使用 SPN 所需的详细信息。 请务必存储客户端机密。

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>更新 AD FS 服务主体的客户端机密

新的客户端机密由 PowerShell cmdlet 自动生成。

从 ERCS 虚拟机上的特权终结点运行脚本。

##### <a name="parameters"></a>parameters

以下信息是作为自动化参数的输入所必需的：

| 参数 | 描述 | 示例 |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | 唯一标识符。 | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | 在旧机密仍然有效的情况下，使用 2880 分钟的滚动更新期限来更改客户端机密。 |  |
| ResetClientSecret | 立即更改客户端机密 |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>更新 AD FS 客户端机密的示例

该示例使用 **ResetClientSecret** 参数，该参数可立即更改客户端密码。

1. 打开权限提升的 Windows PowerShell 会话，并运行以下 cmdlet：

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. 自动化完成之后，会显示 SPN 身份验证所需的新生成机密。 请务必存储新的客户端机密。

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>删除 AD FS 的服务主体

如果已结合 AD FS 部署 Azure Stack，可以使用 PowerShell 来删除服务主体。

从 ERCS 虚拟机上的特权终结点运行脚本。

#### <a name="parameters"></a>parameters

以下信息是作为自动化参数的输入所必需的：

|参数|描述|示例|
|---------|---------|---------|
| 参数 | 描述 | 示例 |
| ApplicationIdentifier | 唯一标识符 | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> 若要查看所有现有服务主体及其应用程序标识符的列表，可以使用 get-graphapplication 命令。

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>删除 AD FS 服务主体的示例

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>分配角色

要访问订阅中的资源，必须将应用程序分配到角色。 决定哪个角色表示应用程序的相应权限。 若要了解有关可用的角色信息，请参阅 [RBAC:内置的 Roles]((/azure/role-based-access-control/built-in-roles)。

可将作用域设置为订阅、资源组或资源级别。 较低级别的作用域将继承权限。 例如，将某个应用程序添加到资源组的“读取者”角色意味着该应用程序可以读取该资源组及其包含的所有资源。

1. 在 Azure Stack 门户中，导航到要将应用程序分配到的作用域级别。 例如，若要在订阅范围内分配角色，选择“订阅”  。 可改为选择资源组或资源。

2. 选择特定订阅（资源组或资源），向其中分配应用程序。

     ![选择要分配的订阅](./media/azure-stack-create-service-principal/image16.png)

3. 选择“访问控制 (IAM)”  。

     ![选择访问权限](./media/azure-stack-create-service-principal/image17.png)

4. 选择“添加角色分配”  。

5. 选择要分配到应用程序的角色。

6. 搜索你的应用程序，并选择它。

7. 选择“确定”  完成角色分配。 该应用程序会显示在分配到该范围角色的用户列表中。

既然已创建服务主体并已分配角色，可以开始在应用程序中使用此服务主体访问 Azure Stack 资源。  

## <a name="next-steps"></a>后续步骤

[为 AD FS 添加用户](azure-stack-add-users-adfs.md)  
[管理用户权限](azure-stack-manage-permissions.md)  
[Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory)  
[Active Directory 联合身份验证服务](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
