---
title: 使用应用程序标识来访问资源
description: 介绍如何管理可用于使用基于角色的访问控制，登录和资源的访问权限的服务主体。
services: azure-resource-manager
documentationcenter: na
author: BryanLa
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2019
ms.author: bryanla
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 8c27948185df5f98926a3500db0981a1ccddc321
ms.sourcegitcommit: c9d11be7d27c73797bdf279d4fcabb7a22451541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2019
ms.locfileid: "67397311"
---
# <a name="use-an-app-identity-to-access-resources"></a>使用应用程序标识来访问资源

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包 (ASDK)*

需要部署或配置资源通过 Azure 资源管理器中，应用程序必须由服务主体表示。 就像一个用户显示的用户主体，服务主体是表示应用程序的安全主体的类型。 服务主体提供标识为应用程序，使您可以将委托仅向该服务主体所需的权限。  

例如，您可能使用 Azure 资源管理器来清点 Azure 资源的配置管理应用程序。 在此方案中，可以创建服务主体，向该服务主体授予读者角色并限制为只读访问权限的配置管理应用程序。 

## <a name="overview"></a>概述

与用户主体类似，服务主体，必须提供凭据在身份验证，这两个元素组成：

- **应用程序 ID**，有时称为客户端 id。 这是一个 GUID，唯一标识 Active Directory 租户中的应用程序的注册。
- 一个**机密**与应用程序 id。 您可以生成客户端机密字符串 （类似于密码），也可以指定 X509 证书 （它使用其公钥）。 

运行应用程序下标识主体是服务的优于其下运行用户主体因为：

 - 服务主体可以使用 X509 证书**更强凭据**。  
 - 可以将分配**限制性更强的权限**向服务主体。 通常情况下，这些权限仅限于仅哪些应用需要执行操作，称为*最小特权原则*。
 - 服务主体**凭据和权限不频繁地更改**作为用户凭据。 例如，当用户的职责发生变化，密码要求规定了更改，或某位用户离开公司。

首先，在您创建一个关联的目录中创建新的应用注册[服务主体对象](/azure/active-directory/develop/developer-glossary#service-principal-object)以表示该目录中的应用程序的标识。 本文档介绍了创建和管理服务主体，具体取决于为 Azure Stack 实例选择的目录的过程：

- Azure Active Directory (Azure AD)。 Azure AD 是基于云的多租户目录和标识管理服务。 与已连接的 Azure Stack 实例，可以使用 Azure AD。
- Active Directory 联合身份验证服务 (AD FS)。 AD FS 提供简化、安全的标识联合与 Web 单一登录 (SSO) 功能。 可将 AD FS 与联网和离线 Azure Stack 实例配合使用。

首先介绍如何管理服务主体，然后介绍如何将服务主体分配到角色，限制其资源的访问权限。

## <a name="manage-an-azure-ad-service-principal"></a>管理 Azure AD 服务主体 

如果你部署 Azure Stack 与 Azure Active Directory (Azure AD) 作为标识管理服务，可以创建服务主体就像 Azure。 本部分演示如何执行通过 Azure 门户的步骤。 在开始之前，请检查是否具有[所需的 Azure AD 权限](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>创建使用客户端机密凭据的服务主体

在本部分中，可以注册应用程序中使用 Azure 门户中，在你的 Azure AD 租户中创建服务主体对象。 在此示例中，将使用客户端的机密凭据，创建服务主体，但该门户还支持 X509 基于证书的凭据。

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “应用注册” > “新建注册”。   
3. 提供**名称**应用程序。 
4. 选择适当**支持的帐户类型**。
5. 下**重定向 URI**，选择**Web**作为应用程序类型，并 （可选） 指定的重定向 URI，如果应用程序需要它。 
6. 设置这些值后，选择“注册”  。 创建应用程序注册并**概述**显示出页。
7. 复制**应用程序 ID**在应用程序代码中使用。 此值是也称为客户端 id。
8. 若要生成的客户端机密，请选择**证书和机密**页。 选择“新建客户端机密”。 
9. 提供**描述**机密，和一个**过期**持续时间。 
10. 完成后，选择**添加**。
11. 显示机密的值。 复制并保存此值在另一个位置，因为您无法检索它。 服务主体登录过程中在客户端应用程序中，可以使用应用程序 ID 提供的机密。 

    ![保存的密钥](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>管理 AD FS 服务主体

如果在部署与 Active Directory 联合身份验证服务 (AD FS) 的 Azure Stack 作为标识管理服务，必须使用 PowerShell 来管理服务主体。 下面提供了示例用于管理服务主体凭据，演示这两个 X509 证书和客户端机密。

脚本必须在提升的 （"以管理员身份运行"） 的 PowerShell 控制台，这将打开到托管的 Azure Stack 实例的特权终结点的虚拟机的另一个会话中运行。 建立特权终结点会话之后，其他 cmdlet 将执行和管理服务主体。 有关特权终结点的详细信息，请参阅[在 Azure Stack 中使用特权终结点](azure-stack-privileged-endpoint.md)。

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>创建使用证书凭据的服务主体

在创建服务主体凭据的证书时，必须满足以下要求：

 - 加密服务提供程序 (CSP) 必须是旧密钥提供程序。
 - 证书格式必须是 PFX 文件，因为公钥和私钥都是必需的。 Windows 服务器使用包含公钥文件（SSL 证书文件）和关联的私钥文件的 .pfx 文件。
 - 对于生产环境，证书必须由内部证书颁发机构或公共证书颁发机构颁发。 如果你使用公共证书颁发机构，则必须将基础操作系统映像中的颁发机构包括为 Microsoft 信任根颁发机构计划的一部分。 可以在 [Microsoft 信任根证书计划：参与者](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)中找到完整列表。
 - Azure Stack 基础结构必须能够通过网络访问证书中发布的证书颁发机构的证书吊销列表 (CRL) 位置。 此 CRL 必须是一个 HTTP 终结点。

证书后，使用下面的 PowerShell 脚本注册你的应用程序并创建服务主体。 此外可以使用服务主体登录到 Azure。 以下占位符替换为你自己的值：

| 占位符 | 描述 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | 将 Azure Stack 实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<YourCertificateLocation\> | 位置的 X509 证书的本地证书存储中。 | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<YourAppName\> | 新的应用注册一个描述性名称 | "我的管理工具" |

1. 打开提升的 Windows PowerShell 会话，并运行以下脚本：

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

    # Using the stamp info for your Azure Stack instance, populate the following variables:
    # - AzureRM endpoint used for Azure Resource Manager operations 
    # - Audience for acquiring an OAuth token used to access Graph API 
    # - GUID of the directory tenant
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
    $TenantID = $AzureStackInfo.AADTenantID

    # Register and set an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint
    Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. 在脚本完成后，它将显示应用程序注册信息，包括服务主体的凭据。 如下所示，`ClientID`和`Thumbprint`用于服务主体的标识下登录。 成功登录后，服务主体标识将用于后续的授权和访问管理通过 Azure 资源管理器中的资源。 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

可以将它与保持你的 PowerShell 控制台会话打开，`ApplicationIdentifier`下一节中的值。

### <a name="update-a-service-principals-certificate-credential"></a>更新服务主体的证书凭据

现在，创建服务主体，本部分将介绍如何为：

1. 创建新的自签名的 X509 证书以便进行测试。
2. 通过更新来更新服务主体的凭据，其**指纹**属性以匹配新的证书。

更新证书凭据使用 PowerShell，并自己的值替换以下占位符：

| 占位符 | 描述 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | 将 Azure Stack 实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<YourAppName\> | 新的应用注册一个描述性名称 | "我的管理工具" |
| \<YourCertificateLocation\> | 位置的 X509 证书的本地证书存储中。 | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<AppIdentifier\> | 分配给应用程序注册的标识符 | "S-1-5-21-1512385356-3796245103-1243299919-1356" |

1. 使用提升的 Windows PowerShell 会话，运行以下 cmdlet:

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

2. 在脚本完成后，它将显示更新的应用程序注册信息，包括新的自签名证书的指纹值。

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
> 使用客户端密码安全级别较低比使用 X509 证书凭据。 不仅是身份验证机制不太安全，而且它通常还需要在客户端应用程序源代码中嵌入密钥。 在这种情况下，对于生产应用程序，则强烈建议的要使用的证书凭据。

现在创建另一个应用注册，但这次指定客户端机密凭据。 与不同的证书凭据，该目录已生成客户端机密凭据的功能。 因此，而不是指定的客户端机密，您使用`-GenerateClientSecret`开关来请求它生成的。 以下占位符替换为你自己的值：

| 占位符 | 描述 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | 将 Azure Stack 实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<YourAppName\> | 新的应用注册一个描述性名称 | "我的管理工具" |

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

     # Using the stamp info for your Azure Stack instance, populate the following variables:
     # - AzureRM endpoint used for Azure Resource Manager operations 
     # - Audience for acquiring an OAuth token used to access Graph API 
     # - GUID of the directory tenant
     $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
     $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
     $TenantID = $AzureStackInfo.AADTenantID

     # Register and set an AzureRM environment that targets your Azure Stack instance
     Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint
     Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. 在脚本完成后，它将显示应用程序注册信息，包括服务主体的凭据。 如下所示，`ClientID`和生成`ClientSecret`用于服务主体的标识下登录。 成功登录后，服务主体标识将用于后续的授权和访问管理通过 Azure 资源管理器中的资源。

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

可以将它与保持你的 PowerShell 控制台会话打开，`ApplicationIdentifier`下一节中的值。

### <a name="update-a-service-principals-client-secret"></a>更新服务主体的客户端机密

更新客户端机密凭据使用 PowerShell，并使用**ResetClientSecret**参数，这会立即更改客户端机密。 以下占位符替换为你自己的值：

| 占位符 | 描述 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | 将 Azure Stack 实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<AppIdentifier\> | 分配给应用程序注册的标识符 | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

1. 使用提升的 Windows PowerShell 会话，运行以下 cmdlet:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. 在脚本完成后，它将显示更新的应用程序注册信息，包括新生成的客户端机密。

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

现在您将了解如何移除/删除的应用注册，从你的目录和其关联的服务主体对象，使用 PowerShell。 

以下占位符替换为你自己的值：

| 占位符 | 描述 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | 将 Azure Stack 实例上的特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<AppIdentifier\> | 分配给应用程序注册的标识符 | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

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

不将从特权终结点上调用删除 GraphApplication cmdlet 返回任何输出，但您将执行该 cmdlet 期间看到逐字确认输出到控制台：

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>分配角色

通过基于角色的访问控制 (RBAC) 授权的用户和应用程序的 Azure 资源的访问权限。 若要允许访问资源的应用程序使用其服务主体在订阅中，您必须*分配*服务主体向*角色*特定*资源*. 首先确定哪个角色表示右*权限*应用程序。 若要了解有关可用的角色信息，请参阅[Azure 资源的内置角色](/azure/role-based-access-control/built-in-roles)。

您还选择的资源类型建立*访问作用域*为服务主体。 可以在订阅、 资源组或资源级别设置访问作用域。 较低级别的作用域将继承权限。 例如，将添加到资源组的"读取者"角色的应用程序，意味着它可以读取资源组和它所包含的所有资源。

1. 登录到相应的门户中，基于在 Azure Stack 时所指定的目录 （Azure AD 中，在 Azure 门户或适用于 AD FS，例如在 Azure Stack 用户门户） 的安装。 在此示例中，我们显示用户登录到 Azure Stack 用户门户。

   > [!NOTE]
   > 若要添加给定的资源的角色分配，你的用户帐户必须属于角色声明`Microsoft.Authorization/roleAssignments/write`权限。 例如，可以[所有者](/azure/role-based-access-control/built-in-roles#owner)或[用户访问管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)内置角色。  
2. 导航到你想要允许服务主体访问资源。 在此示例中，服务主体向角色分配订阅范围内，通过选择**订阅**，然后特定订阅。 可改为选择资源组或对特定资源，例如虚拟机。 

     ![选择要分配的订阅](./media/azure-stack-create-service-principal/select-subscription.png)

3. 选择**访问控制 (IAM)** 页上，这是通用支持 RBAC 的所有资源。
4. 选择 **+ 添加**
5. 下**角色**，选择您想要分配到应用程序角色。
6. 下**选择**，搜索你的应用程序使用完整或部分应用程序名称。 在注册期间，应用程序生成的名称是*Azurestack-\<YourAppName\>-\<ClientId\>* 。 例如，如果使用的应用程序名称*App2*，和 ClientId *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*已分配的完整名称是在创建期间， *Azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*。 您可以搜索确切的字符串或一部分，如*Azurestack*或*Azurestack App2*。
7. 后查找应用，选择它并将显示在下**所选成员**。
8. 选择“保存”  完成角色分配。 

     [ ![将角色分配](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. 完成后，应用程序将显示在当前作用域，为给定角色分配给主体的列表。

     [ ![已分配的角色](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

既然已创建服务主体并已分配角色，可以开始在应用程序中使用此服务主体访问 Azure Stack 资源。  

## <a name="next-steps"></a>后续步骤

[为 AD FS 添加用户](azure-stack-add-users-adfs.md)  
[管理用户权限](azure-stack-manage-permissions.md)  
[Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory)  
[Active Directory 联合身份验证服务](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
