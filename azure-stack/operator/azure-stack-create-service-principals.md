---
title: 使用应用标识访问资源
description: 了解如何使用应用标识访问 Azure Stack 集线器资源。 应用标识可与基于角色的访问控制结合使用，以便登录和访问资源。
author: BryanLa
ms.author: bryanla
ms.topic: how-to
ms.date: 05/07/2020
ms.lastreviewed: 05/07/2020
ms.openlocfilehash: 372df0bdb99ce06b22912e9e5c175af07620f5f4
ms.sourcegitcommit: 510bb047b0a78fcc29ac611a2a7094fc285249a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988316"
---
# <a name="use-an-app-identity-to-access-azure-stack-hub-resources"></a>使用应用标识访问 Azure Stack Hub 资源

需要通过 Azure 资源管理器部署或配置资源的应用程序必须由其自己的标识表示。 正如用户由称为用户主体的安全主体表示一样，应用由服务主体表示。 服务主体提供应用的标识，只允许向应用委托必要的权限。  

例如，你可能有一个使用 Azure 资源管理器来清点 Azure 资源的配置管理应用。 在这种情况下，你可以创建服务主体，向该服务主体授予 "读取者" 角色，并将配置管理应用限制为只读访问。

## <a name="overview"></a>概述

与用户类似，应用程序在身份验证期间必须提供凭据。 这种身份验证由两个要素构成：

- **应用程序 ID**，有时也称为客户端 ID。 一个 GUID，用于唯一标识 Active Directory 租户中的应用注册。
- 与应用程序 ID 关联的**机密**。 你可以生成客户端机密字符串（类似于密码），也可以指定 X509 证书（使用其公钥）。

当应用程序在其自己的标识下运行时，最好是在用户的标识下运行它，原因如下：

 - **更强的凭据**-应用可以使用 X509 证书（而不是文本共享机密/密码）登录。  
 - 可以向应用分配**更严格的权限**。 一般而言，这些权限限制为只能执行应用程序需要执行的操作，即所谓的“最低特权原则”。 
 - **凭据和权限不会随着**应用程序的用户凭据的频繁更改。 例如，当用户的责任发生变化时，密码要求决定更改，或者当用户离开公司时。

首先请在目录中创建新的应用注册，这会创建关联的[服务主体对象](/azure/active-directory/develop/developer-glossary#service-principal-object)来代表应用在目录中的标识。 

本文首先介绍了创建和管理服务主体的过程，具体取决于为 Azure Stack 中心实例选择的目录：

- **Azure Active Directory （Azure AD）**。 Azure AD 是基于云的多租户目录和标识管理服务。 可将 Azure AD 与联网 Azure Stack Hub 实例配合使用。
- **Active Directory 联合身份验证服务（AD FS）**。 AD FS 提供简化、安全的标识联合与 Web 单一登录 (SSO) 功能。 可将 AD FS 与联网和离线 Azure Stack Hub 实例配合使用。

然后，了解如何将服务主体分配到角色，从而限制其资源访问权限。

## <a name="manage-an-azure-ad-app-identity"></a>管理 Azure AD 应用标识

如果你将 Azure Stack 集线器部署 Azure AD 作为标识管理服务，则可以创建服务主体，就像对 Azure 执行的操作一样。 本部分介绍如何通过 Azure 门户执行这些步骤。 在开始之前，请检查是否具有[所需的 Azure AD 权限](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>创建使用客户端机密凭据的服务主体

在本部分，你将使用 Azure 门户注册应用，这会在 Azure AD 租户中创建服务主体对象。 在此示例中，指定了客户端机密凭据，但门户还支持基于 X509 证书的凭据。

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “应用注册” > “新建注册”。   
3. 为应用提供一个**名称**。
4. 选择相应的**受支持帐户类型**。
5. 在“重定向 URI”下，选择“Web”作为应用类型，并（可选）指定重定向 URI（如果应用需要它）。  
6. 设置这些值后，选择“注册”  。 随即会创建应用注册，并显示“概述”页。 
7. 复制“应用程序 ID”以便在应用代码中使用。  此值也称为“客户端 ID”。
8. 若要生成客户端机密，请选择“证书和机密”页。  选择“新建客户端机密”。 
9. 提供机密的**说明**以及**过期**时间。
10. 完成后，选择“添加”  。
11. 此时会显示机密值。 请复制此值并将其保存到另一位置，因为以后无法检索它。 在客户端应用程序中提供具有应用程序 ID 的机密，以便登录。

    ![客户端机密中保存的密钥](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

现在，请转到[分配角色](#assign-a-role)，了解如何为应用的标识建立基于角色的访问控制。

## <a name="manage-an-ad-fs-app-identity"></a>管理 AD FS 应用标识

如果使用 AD FS 作为标识管理服务部署 Azure Stack 集线器，则必须使用 PowerShell 来管理应用的标识。 以下示例说明如何管理服务主体凭据，同时演示 X509 证书和客户端机密。

脚本必须在权限提升（“以管理员身份运行”）的 PowerShell 控制台中运行，这会打开另一个会话来连接到托管了 Azure Stack Hub 实例特权终结点的 VM。 建立特权终结点会话后，其他 cmdlet 将会执行并管理服务主体。 有关特权终结点的详细信息，请参阅[使用 Azure Stack Hub 中的特权终结点](azure-stack-privileged-endpoint.md)。

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>创建使用证书凭据的服务主体

创建证书凭据时，必须满足以下要求：

 - 对于生产环境，证书必须由内部证书颁发机构或公共证书颁发机构颁发。 使用公用颁发机构时，必须将该颁发机构作为 Microsoft 受信任的根颁发机构计划的一部分包括在基本操作系统映像中。 可以在[Microsoft 受信任的根证书程序：参与者](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)处找到完整列表。 稍后会在[更新证书凭据](#update-a-certificate-credential)期间显示创建 "自签名" 测试证书的示例。 
 - 加密提供程序必须指定为 Microsoft 旧版加密服务提供程序 (CSP) 密钥提供程序。
 - 证书格式必须是 PFX 文件，因为公钥和私钥都是必需的。 Windows 服务器使用 .pfx 文件，其中包含公钥文件（TLS/SSL 证书文件）和关联的私钥文件。
 - Azure Stack Hub 基础结构必须能够通过网络访问证书中发布的证书颁发机构的证书吊销列表 (CRL) 位置。 此 CRL 必须是一个 HTTP 终结点。

创建证书后，使用以下 PowerShell 脚本来注册应用，并创建服务主体。 还要使用服务主体登录到 Azure。 请将以下占位符替换为自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack Hub 实例上特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<YourCertificateLocation\> | X509 证书在本地证书存储中的位置。 | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<YourAppName\> | 新应用注册的描述性名称。 | "My management tool" |

1. 打开权限提升的 Windows PowerShell 会话，并运行以下脚本：

   ```powershell  
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
    $Creds = Get-Credential

    # Create a PSSession to the Privileged Endpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the Get-Item cmdlet to retrieve your certificate.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    # Use the privileged endpoint to create the new app registration (and service principal object)
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # Using the stamp info for your Azure Stack Hub instance, populate the following variables:
    # - AzureRM endpoint used for Azure Resource Manager operations 
    # - Audience for acquiring an OAuth token used to access Graph API 
    # - GUID of the directory tenant
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
    $TenantID = $AzureStackInfo.AADTenantID

    # Register and set an AzureRM environment that targets your Azure Stack Hub instance
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

    # Sign in using the new service principal
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. 脚本完成后，会显示应用注册信息，包括服务主体的凭据。 `ClientID`和`Thumbprint`经过身份验证，并在以后被授权访问 Azure 资源管理器管理的资源。

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

请将 PowerShell 控制台会话保持打开状态，因为在下一部分要将它与 `ApplicationIdentifier` 值配合使用。

### <a name="update-a-certificate-credential"></a>更新证书凭据

本部分介绍在创建服务主体后如何执行以下操作：

1. 创建新的自签名 X509 证书用于测试。
2. 更新服务主体的凭据，方法是更新其 **Thumbprint** 属性，以匹配新的证书。

使用 PowerShell 更新证书凭据（请将以下占位符替换为自己的值）：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack Hub 实例上特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<YourAppName\> | 新应用注册的描述性名称。 | "My management tool" |
| \<YourCertificateLocation\> | X509 证书在本地证书存储中的位置。 | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<AppIdentifier\> | 分配给应用程序注册的标识符。 | "S-1-5-21-1512385356-3796245103-1243299919-1356" |

1. 使用权限提升的 Windows PowerShell 会话运行以下 cmdlet：

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Create a self-signed certificate for testing purposes. 
     $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
     # In production, use Get-Item and a managed certificate instead.
     # $Cert = Get-Item "<YourCertificateLocation>"

     # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ClientCertificates $using:NewCert}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. 脚本完成后，会显示更新后的应用注册信息，包括新的自签名证书的指纹值。

     ```Shell  
     ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
     ClientId              : 
     Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
     ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
     ClientSecret          : 
     PSComputerName        : azs-ercs01
     RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-that-uses-client-secret-credentials"></a>创建使用客户端机密凭据的服务主体

> [!WARNING]
> 使用客户端机密不如使用 X509 证书凭据那么安全。 这不仅会降低身份验证机制的安全性，而且通常还要求在客户端应用的源代码中嵌入机密。 因此，我们强烈建议在生产应用中使用证书凭据。

现在你将创建另一个应用注册，但这次需要指定客户端机密凭据。 不同于证书凭据，目录能够生成客户端机密凭据。 无需指定客户端机密，而可以使用 `-GenerateClientSecret` 开关来请求生成客户端机密。 请将以下占位符替换为自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack Hub 实例上特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<YourAppName\> | 新应用注册的描述性名称。 | "My management tool" |

1. 打开权限提升的 Windows PowerShell 会话，并运行以下 cmdlet：

     ```powershell  
     # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
     $Creds = Get-Credential

     # Create a PSSession to the Privileged Endpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to create the new app registration (and service principal object)
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Using the stamp info for your Azure Stack Hub instance, populate the following variables:
     # - AzureRM endpoint used for Azure Resource Manager operations 
     # - Audience for acquiring an OAuth token used to access Graph API 
     # - GUID of the directory tenant
     $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
     $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
     $TenantID = $AzureStackInfo.AADTenantID

     # Register and set an AzureRM environment that targets your Azure Stack Hub instance
     Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

     # Sign in using the new service principal
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. 脚本完成后，会显示应用注册信息，包括服务主体的凭据。 `ClientID`和`ClientSecret`经过身份验证，并在以后被授权访问 Azure 资源管理器管理的资源。

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

请将 PowerShell 控制台会话保持打开状态，因为在下一部分要将它与 `ApplicationIdentifier` 值配合使用。

### <a name="update-a-client-secret"></a>更新客户端密码

在 PowerShell 中使用 **ResetClientSecret** 参数更新客户端机密凭据，以立即更改客户端机密。 请将以下占位符替换为自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack Hub 实例上特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<AppIdentifier\> | 分配给应用程序注册的标识符。 | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

1. 使用权限提升的 Windows PowerShell 会话运行以下 cmdlet：

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. 脚本完成后，会显示更新后的应用注册信息，包括新生成的客户端机密。

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
     PSComputerName        : azs-ercs01
     RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal"></a>删除服务主体

现在介绍如何使用 PowerShell 从目录中删除应用注册及其关联的服务主体对象。 

请将以下占位符替换为自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack Hub 实例上特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<AppIdentifier\> | 分配给应用程序注册的标识符。 | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

```powershell  
# Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
$Creds = Get-Credential

# Create a PSSession to the PrivilegedEndpoint VM
$Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

# OPTIONAL: Use the privileged endpoint to get a list of applications registered in AD FS
$AppList = Invoke-Command -Session $Session -ScriptBlock {Get-GraphApplication}

# Use the privileged endpoint to remove the application and associated service principal object for <AppIdentifier>
Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier "<AppIdentifier>"}
```

在特权终结点上调用 Remove-GraphApplication cmdlet 后不会返回任何输出，但在运行 cmdlet 期间，控制台中会显示原义确认输出：

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>分配角色

可以通过基于角色的访问控制 (RBAC) 来授权用户和应用访问 Azure 资源。 若要允许某个应用访问你的订阅中的资源，你必须将其服务主体*分配*给特定*资源*的*角色*。 首先决定哪个角色表示应用的相应权限。** 若要了解可用的角色，请参阅 [Azure 资源的内置角色](/azure/role-based-access-control/built-in-roles)。

你选择的资源类型还会为应用程序建立*访问作用域*。 可将访问范围设置为订阅、资源组或资源级别。 较低级别的作用域会继承权限。 例如，将某个应用添加到资源组的“读取者”角色意味着该应用程序可以读取该资源组及其包含的所有资源。

1. 根据在安装 Azure Stack Hub 期间指定的目录登录到相应的门户（例如，如果指定了 Azure AD，则登录到 Azure 门户；如果指定了 AD FS，则登录到 Azure Stack Hub 用户门户）。 在本示例中，用户已登录到 Azure Stack Hub 用户门户。

   > [!NOTE]
   > 若要为给定的资源添加角色分配，你的用户帐户必须属于声明 `Microsoft.Authorization/roleAssignments/write` 权限的角色。 例如，[所有者](/azure/role-based-access-control/built-in-roles#owner)或[用户访问管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)内置角色。  
2. 导航到要允许应用访问的资源。 在此示例中，通过依次选择 "**订阅**" 和特定订阅，将应用的服务主体分配给订阅范围内的角色。 也可以改为选择资源组，或者虚拟机之类的特定资源。

     ![选择要分配的订阅](./media/azure-stack-create-service-principal/select-subscription.png)

3. 选择“访问控制(IAM)”页。支持 RBAC 的所有资源都会提供此页。****
4. 选择 **+ 添加**
5. 在“角色”下，选择要将应用分配到哪个角色。****
6. 在“选择”下，使用完整或部分应用名称来搜索你的应用程序。**** 在注册期间，应用程序名称将生成*为 test-azurestack\<-\>-\<YourAppName\>ClientId*。 例如，如果使用的应用程序名为 *App2*，在创建期间分配的客户端 ID 为 *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*，则完整名称为 *Azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*。 可以搜索确切的字符串，也可以只搜索其一部分，例如 *Azurestack* 或 *Azurestack-App2*。
7. 找到应用后，请选择它，然后它会显示在“已选择的成员”下。****
8. 选择“保存”  完成角色分配。

     [![分配角色](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. 完成后，在当前范围分配到给定角色的主体列表中会显示该应用。

     [![分配的角色](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

现在，你已为应用提供标识并授权其访问资源，接下来，你可以启用脚本或代码以登录并安全地访问 Azure Stack 集线器资源。  

## <a name="next-steps"></a>后续步骤

[管理用户权限](azure-stack-manage-permissions.md)  
[Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory)  
[Active Directory 联合身份验证服务](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
