---
title: 使用应用标识访问资源
description: 了解如何管理 Azure Stack 中心服务主体。 可以将服务主体与基于角色的访问控制配合使用来登录和访问资源。
author: BryanLa
ms.author: bryanla
ms.topic: how-to
ms.date: 11/11/2019
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 1c96ee9520285e0bc2b9784fa5d310a1ec2ae60f
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366118"
---
# <a name="use-an-app-identity-to-access-azure-stack-hub-resources"></a>使用应用标识访问 Azure Stack 集线器资源

需要通过 Azure 资源管理器部署或配置资源的应用程序必须由服务主体表示。 与用户主体表示的用户一样，服务主体是表示应用的一种安全主体。 服务主体提供应用的标识，允许你仅向该服务主体委派必要的权限。  

例如，你可能有一个使用 Azure 资源管理器清点 Azure 资源的配置管理应用程序。 在这种情况下，你可以创建服务主体，向该服务主体授予 "读取者" 角色，并将配置管理应用限制为只读访问。

## <a name="overview"></a>概述

与用户主体类似，服务主体在身份验证期间必须提供凭据。 此身份验证包含两个元素：

- **应用程序 id**，有时称为客户端 id。 这是一个 GUID，用于唯一标识 Active Directory 租户中的应用注册。
- 与应用程序 ID 相关联的**机密**。 可以生成客户端密码字符串（类似于密码），也可以指定 X509 证书（使用其公钥）。

使用服务主体的标识运行应用最好是在用户主体下运行应用，因为：

 - 服务主体可以使用 X509 证书获取**更强的凭据**。  
 - 可以向服务主体分配**更多限制的权限**。 通常，这些权限仅限于应用需要执行的操作，称为*最低权限原则*。
 - 服务主体**凭据和权限不会像用户凭据一样频繁更改**。 例如，当用户的责任发生变化时，密码要求决定更改，或者用户离开公司。

首先，在目录中创建新的应用注册，这会创建一个关联的[服务主体对象](/azure/active-directory/develop/developer-glossary#service-principal-object)来表示该目录中的应用标识。 本文档介绍了创建和管理服务主体的过程，具体取决于为 Azure Stack 中心实例选择的目录：

- Azure Active Directory (Azure AD)。 Azure AD 是一种多租户、基于云的目录和标识管理服务。 可以将 Azure AD 与连接的 Azure Stack 中心实例一起使用。
- Active Directory 联合身份验证服务 (AD FS)。 AD FS 提供简化、安全的联合身份验证和 web 单一登录（SSO）功能。 可以将 AD FS 与连接的和已断开的 Azure Stack 中心实例一起使用。

首先，您将了解如何管理服务主体，以及如何将服务主体分配到角色，从而限制其资源访问权限。

## <a name="manage-an-azure-ad-service-principal"></a>管理 Azure AD 服务主体

如果你将 Azure Stack 集线器部署 Azure AD 作为标识管理服务，则可以创建服务主体，就像对 Azure 执行的操作一样。 本部分演示如何执行 Azure 门户的步骤。 在开始之前，请检查是否具有[所需的 Azure AD 权限](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>创建使用客户端密钥凭据的服务主体

在本部分中，将使用 Azure 门户注册应用，这将在 Azure AD 租户中创建服务主体对象。 在此示例中，使用客户端机密凭据创建服务主体，但门户还支持基于 X509 证书的凭据。

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择 " **Azure Active Directory** > **应用注册**" > "**新注册**"。
3. 提供应用程序的**名称**。
4. 选择适当的**支持帐户类型**。
5. 在 "**重定向 uri**" 下，选择 " **Web** " 作为应用类型，（可选）如果你的应用需要重定向 uri，则指定。
6. 设置值后，选择 "**注册**"。 将创建应用注册并显示 "**概述**" 页。
7. 复制**应用程序 ID** ，以便在应用代码中使用。 此值也称为 "客户端 ID"。
8. 若要生成客户端密钥，请选择 "**证书 & 机密**" 页。 选择“新建客户端机密”。
9. 提供机密的**说明**和**有效期**。
10. 完成后，选择“添加”。
11. 机密的值显示。 复制此值并将其保存到其他位置，因为以后无法检索它。 在服务主体登录期间，你将在客户端应用中提供应用程序 ID 的机密。

    ![客户端密码中保存的密钥](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>管理 AD FS 服务主体

如果使用 AD FS 作为标识管理服务部署 Azure Stack 集线器，则必须使用 PowerShell 来管理服务主体。 下面提供了有关管理服务主体凭据的示例，同时演示了 X509 证书和客户端机密。

脚本必须在已提升（"以管理员身份运行"）的 PowerShell 控制台中运行，这将打开一个 VM 的其他会话，该 VM 承载 Azure Stack 中心实例的特权终结点。 一旦建立了特权终结点会话，其他 cmdlet 就会执行并管理服务主体。 有关特权终结点的详细信息，请参阅[使用 Azure Stack 集线器中的特权终结点](azure-stack-privileged-endpoint.md)。

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>创建使用证书凭据的服务主体

为服务主体凭据创建证书时，必须满足以下要求：

 - 对于生产，证书必须由内部证书颁发机构或公共证书颁发机构颁发。 如果你使用公用证书颁发机构，则必须将该颁发机构作为 Microsoft 受信任的根颁发机构计划的一部分包括在基本操作系统映像中。 可以在[Microsoft 受信任的根证书程序：参与者](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)处找到完整列表。 稍后将在[更新服务主体的证书凭据](#update-a-service-principals-certificate-credential)期间显示创建 "自签名" 测试证书的示例。 
 - 加密提供程序必须指定为 Microsoft 旧版加密服务提供程序（CSP）密钥提供程序。
 - 证书格式必须为 PFX 文件，因为公钥和私钥都是必需的。 Windows 服务器使用 .pfx 文件，其中包含公钥文件（SSL 证书文件）和关联的私钥文件。
 - Azure Stack 中心基础结构必须对证书中发布的证书颁发机构的证书吊销列表（CRL）位置具有网络访问权限。 此 CRL 必须是 HTTP 终结点。

获得证书后，请使用下面的 PowerShell 脚本注册应用并创建服务主体。 你还可以使用服务主体登录到 Azure。 将以下占位符替换为你自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack 中心实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<YourCertificateLocation\> | X509 证书在本地证书存储中的位置。 | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<YourAppName\> | 新应用注册的描述性名称。 | "我的管理工具" |

1. 打开提升的 Windows PowerShell 会话，然后运行以下脚本：

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

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. 脚本完成后，会显示应用注册信息，包括服务主体的凭据。 如下所示，`ClientID` 和 `Thumbprint` 用于在服务主体标识下登录。 成功登录后，将使用服务主体标识进行后续授权，并访问 Azure 资源管理器管理的资源。

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

将 PowerShell 控制台会话保持打开状态，因为使用下一部分中的 `ApplicationIdentifier` 值。

### <a name="update-a-service-principals-certificate-credential"></a>更新服务主体的证书凭据

现在，你已创建服务主体，此部分将演示如何执行以下操作：

1. 创建用于测试的新的自签名 X509 证书。
2. 更新服务主体的凭据，方法是更新其**指纹**属性以匹配新证书。

使用 PowerShell 更新证书凭据，将以下占位符替换为你自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack 中心实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<YourAppName\> | 新应用注册的描述性名称。 | "我的管理工具" |
| \<YourCertificateLocation\> | X509 证书在本地证书存储中的位置。 | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<AppIdentifier\> | 分配给应用程序注册的标识符。 | "S-1-5-21-1512385356-3796245103-1243299919-1356" |

1. 使用提升的 Windows PowerShell 会话运行以下 cmdlet：

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

2. 脚本完成后，将显示更新后的应用注册信息，其中包括新的自签名证书的指纹值。

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

> [!IMPORTANT]
> 使用客户端机密不如使用 X509 证书凭据安全。 身份验证机制不仅更安全，而且通常还需要在客户端应用程序源代码中嵌入机密。 因此，对于生产应用，强烈建议使用证书凭据。

现在，你创建了另一个应用注册，但这次指定了客户端密钥凭据。 与证书凭据不同，目录可以生成客户端密钥凭据。 使用 `-GenerateClientSecret` 开关来请求生成它，而不是指定客户端机密。 将以下占位符替换为你自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack 中心实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<YourAppName\> | 新应用注册的描述性名称。 | "我的管理工具" |

1. 打开提升的 Windows PowerShell 会话，并运行以下 cmdlet：

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

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. 脚本完成后，会显示应用注册信息，包括服务主体的凭据。 如下所示，`ClientID` 和生成的 `ClientSecret` 用于在服务主体标识下登录。 成功登录后，将使用服务主体标识进行后续授权，并访问 Azure 资源管理器管理的资源。

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

将 PowerShell 控制台会话保持打开状态，因为使用下一部分中的 `ApplicationIdentifier` 值。

### <a name="update-a-service-principals-client-secret"></a>更新服务主体的客户端机密

使用 PowerShell （使用**ResetClientSecret**参数）更新客户端密钥凭据，这会立即更改客户端密码。 将以下占位符替换为你自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack 中心实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<AppIdentifier\> | 分配给应用程序注册的标识符。 | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

1. 使用提升的 Windows PowerShell 会话运行以下 cmdlet：

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. 脚本完成后，将显示更新后的应用注册信息，包括新生成的客户端密钥。

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

现在，你将了解如何使用 PowerShell 从目录中删除/删除应用注册及其关联的服务主体对象。 

将以下占位符替换为你自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack 中心实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
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

在特权终结点上调用 GraphApplication cmdlet 不会返回任何输出，但在执行 cmdlet 的过程中，会看到原义确认输出到控制台：

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>分配角色

可以通过基于角色的访问控制（RBAC）授权用户和应用对 Azure 资源的访问权限。 若要允许应用使用其服务主体访问订阅中的资源，你必须将服务主体*分配*给特定*资源*的*角色*。 首先决定哪个角色表示应用程序的正确*权限*。 若要了解有关可用角色的信息，请参阅[Azure 资源的内置角色](/azure/role-based-access-control/built-in-roles)。

你选择的资源类型还会为服务主体建立*访问作用域*。 可以在订阅、资源组或资源级别设置访问作用域。 较低级别的作用域将继承权限。 例如，将应用添加到资源组的 "读取者" 角色意味着它可以读取资源组及其包含的所有资源。

1. 根据在 Azure Stack 集线器安装期间指定的目录（例如，Azure AD 的 Azure 门户或 AD FS 的 Azure Stack 集线器用户门户）登录到相应的门户。 在此示例中，我们将显示登录到 Azure Stack 集线器用户门户的用户。

   > [!NOTE]
   > 若要为给定资源添加角色分配，你的用户帐户必须属于声明 `Microsoft.Authorization/roleAssignments/write` 权限的角色。 例如，"[所有者](/azure/role-based-access-control/built-in-roles#owner)" 或 "[用户访问管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)" 内置角色。  
2. 导航到要允许服务主体访问的资源。 在此示例中，通过依次选择 "**订阅**" 和特定订阅，将服务主体分配给订阅范围内的角色。 您可以选择资源组或特定资源（例如虚拟机）。

     ![选择要分配的订阅](./media/azure-stack-create-service-principal/select-subscription.png)

3. 选择 "**访问控制（IAM）** " 页，该页面在支持 RBAC 的所有资源中是通用的。
4. 选择 **+ 添加**
5. 在 "**角色**" 下，选择要分配给应用的角色。
6. 在 "**选择**" 下，使用完整或部分应用程序名称搜索应用。 注册过程中，应用程序名称以*test-azurestack-\<YourAppName\>-\<ClientId\>* 生成。 例如，如果你使用了应用程序名称*App2*，而 ClientId *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*是在创建过程中分配的，则完整名称将为*test-azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*。 可以搜索确切的字符串或部分，例如*test-azurestack*或*test-azurestack-App2*。
7. 找到应用后，将其选中，它将显示在 "**所选成员**" 下。
8. 选择“保存”完成角色分配。

     [![分配角色](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. 完成后，该应用将显示在为当前作用域分配的主体列表中，针对给定角色。

     [![分配的角色](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

创建服务主体并分配角色后，你可以开始在应用中使用此服务主体来访问 Azure Stack 集线器资源。  

## <a name="next-steps"></a>后续步骤

[为 AD FS 添加用户](azure-stack-add-users-adfs.md)  
[管理用户权限](azure-stack-manage-permissions.md)  
[Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory)  
[Active Directory 联合身份验证服务](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
