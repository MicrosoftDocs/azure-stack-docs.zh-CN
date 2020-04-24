---
title: 轮换 Azure Stack Hub 上的应用服务的机密和证书
description: 了解如何转换 Azure Stack Hub 上的 Azure 应用服务使用的机密和证书
author: apwestgarth
ms.topic: article
ms.date: 04/09/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/09/2020
ms.openlocfilehash: edbc0a0e9d0382f53503dddf4362a371b6369ebc
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81005393"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>轮换 Azure Stack Hub 上的应用服务的机密和证书

本文中的说明仅适用于 Azure Stack Hub 上的 Azure 应用服务。  Azure Stack Hub 的集中式机密轮换过程不包括 Azure Stack Hub 上的 Azure 应用服务机密轮换。  操作员可以监视系统中机密的有效性、上次更新日期，以及机密过期之前的剩余时间。

> [!Important]
> 操作员不会在 Azure Stack Hub 仪表板上收到机密过期警报，因为 Azure Stack Hub 上的 Azure 应用服务未与 Azure Stack Hub 警报服务集成。  操作员必须在 Azure Stack Hub 管理员门户中，使用 Azure Stack Hub 上的 Azure 应用服务管理体验定期监视其机密。

本文档包含轮换以下机密的过程：

* Azure Stack Hub 上的 Azure 应用服务中使用的加密密钥；
* Azure Stack Hub 上的 Azure 应用服务用来与宿主数据库和计量数据库交互的数据库连接凭据；
* Azure App Service 在 Azure Stack 中心使用的证书保护 Azure Active Directory （Azure AD）或 Active Directory 联合身份验证服务（ADFS）中标识应用程序证书的终结点和旋转
* Azure Stack Hub 上的 Azure 应用服务基础结构角色的系统凭据。

## <a name="rotate-encryption-keys"></a>轮换加密密钥

若要轮换 Azure Stack Hub 上的 Azure 应用服务中使用的加密密钥，请完成以下步骤：

1. 在 Azure Stack Hub 管理员门户中转到“应用服务管理”体验。

1. 导航到“机密”菜单选项 

1. 单击“加密密钥”部分中的“轮换”按钮 

1. 单击“确定”启动轮换过程。 

1. 加密密钥将会轮换，所有角色实例将会更新。 操作员可以使用“状态”按钮来监视该过程的状态。 

## <a name="rotate-connection-strings"></a>轮换连接字符串

若要更新应用服务宿主数据库和计量数据库的数据库连接字符串凭据，请完成以下步骤：

1. 在 Azure Stack Hub 管理员门户中转到“应用服务管理”体验。

1. 导航到“机密”菜单选项 

1. 单击“连接字符串”部分中的“轮换”按钮 

1. 提供 **SQL SA 用户名**和**密码**，然后单击“确定”启动轮换过程。  

1. 凭据将在整个 Azure 应用服务角色实例中轮换。 操作员可以使用“状态”按钮来监视该过程的状态。 

## <a name="rotate-certificates"></a>轮换证书

若要轮换 Azure Stack Hub 上的 Azure 应用服务中使用的证书，请完成以下步骤：

1. 在 Azure Stack Hub 管理员门户中转到“应用服务管理”体验。

1. 导航到“机密”菜单选项 

1. 单击“证书”部分中的“轮换”按钮 

1. 为要轮换的证书提供**证书文件**和关联的**密码**，然后单击“确定”。 

1. 这些证书将在 Azure Stack Hub 上的 Azure 应用服务角色实例中根据需要轮换。  操作员可以使用“状态”按钮来监视该过程的状态。 

当标识应用程序证书轮换时，必须同时用新证书更新 AAD 或 ADFS 中的相应应用程序。

> [!IMPORTANT]
> 如果无法使用新证书更新标识应用程序，则在轮换之后将中断 Azure Functions 的用户门户体验，阻止用户使用 KUDU 开发人员工具，并阻止管理员从应用服务管理体验中管理辅助角色层规模集。

### <a name="rotate-credential-for-the-azure-ad-identity-application"></a>旋转 Azure AD 标识应用程序的凭据 

标识应用程序是由操作员在 Azure Stack 中心部署 Azure App Service 之前创建的。  如果应用程序 ID 未知，请按照以下步骤进行发现：

1. 中转到**Azure Stack 集线器管理门户**

1. 中转到**订阅**并选择**默认提供程序订阅**

1. 选择 "**访问控制（IAM）** "，并选择 "**应用服务**应用程序"

1. 记下**应用程序 id**，此值是必须在 Azure Active Directory 中更新的标识应用程序的应用程序 id。

若要在 Active Directory 中为应用程序旋转证书，请执行以下步骤：

1. 中转到**Azure 门户**并使用用于部署 Azure Stack 中心的全局管理员登录

1. 转到**Azure Active Directory**并浏览到 "**应用注册**"

1. 搜索**应用程序 id**，指定标识应用程序 id

1. 选择应用程序，然后前往**证书 & 机密**

1. 单击 "**上传证书**"，并上传具有以下文件类型之一的标识应用程序的新证书： .cer、pem、.crt

1. 确认 "Azure Stack 中心管理" 门户中的 "应用服务管理" 体验中列出的**指纹**是否匹配

1. 删除旧证书

### <a name="rotate-certificate-for-adfs-identity-application"></a>为 ADFS 标识应用程序旋转证书

标识应用程序是由操作员在 Azure Stack 中心部署 Azure App Service 之前创建的。  如果应用程序的对象 ID 未知，请按照以下步骤进行发现：

1. 中转到**Azure Stack 集线器管理门户**

1. 中转到**订阅**并选择**默认提供程序订阅**

1. 选择 "**访问控制（IAM）** "，并选择**test-azurestack-<guid> AppService**

1. 记下**对象 ID**，此值是在 ADFS 中必须更新的服务主体的 ID。

若要在 ADFS 中轮替应用程序的证书，你将需要具有访问特权终结点（PEP）的权限，然后使用 PowerShell 更新证书凭据，并将以下占位符替换为你自己的值：

| 占位符 | 说明 | 示例 |
| ----------- | ----------- | ------- |
| \<PepVM\> | Azure Stack Hub 实例上特权终结点 VM 的名称。 | "AzS-ERCS01" |
| \<CertificateFileLocation\> | X509 证书在磁盘上的位置。 | "d:\certs\sso.cer" |
| \<ApplicationObjectId\> | 分配给标识应用程序的标识符。 | "S-1-5-21-401916501-2345862468-1451220656-1451" |

 1. 打开提升的 Windows PowerShell 会话，然后运行以下脚本

    ```powershell
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint
    $Creds = Get-Credential

    # Create a new Certificate object from the identity application certificate exported as .cer file
    $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<CertificateFileLocation>")

    # Create a new PSSession to the PrivelegedEndpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with the App Service identity application
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<ApplicationObjectId>" -ClientCertificates $using:Cert}
    $Session | Remove-PSSession

    # Output the updated service principal details
    $SpObject

    ```

1. 脚本完成后，将显示更新的应用注册信息，包括证书的指纹值。

    ```shell
    ApplicationIdentifier : S-1-5-21-401916501-2345862468-1451220656-1451
    ClientId              : 
    Thumbprint            : FDAA679BF9EDDD0CBB581F978457A37BFD73CA3B
    ApplicationName       : Azurestack-AppService-d93601c2-1ec0-4cac-8d1c-8ccde63ef308
    ClientSecret          : 
    PSComputerName        : AzS-ERCS01
    RunspaceId            : cb471c79-a0d3-40ec-90ba-89087d104510
    ```


## <a name="rotate-system-credentials"></a>轮换系统凭据

若要在 Azure Stack 中心旋转 Azure App Service 中使用的系统凭据，请完成以下步骤：

1. 在 Azure Stack 中心管理员门户中，请参阅应用服务管理体验。

1. 导航到**机密**菜单选项

1. 单击 "系统凭据" 部分中的 "**旋转**" 按钮

1. 选择要旋转的系统凭据的**作用域**。  操作员可以选择为所有角色或单个角色轮换系统凭据。

1. 指定**新的本地管理员用户名**、新**密码**并确认**密码**，然后单击 **"确定"**

1. 在 Azure Stack 中心角色实例的相应 Azure App Service 中，凭据将按需进行旋转。  操作员可以使用 "**状态**" 按钮监视过程的状态。



