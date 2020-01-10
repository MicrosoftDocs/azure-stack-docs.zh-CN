---
title: AzsReadinessChecker cmdlet 引用 |Microsoft Docs
description: Azure Stack 中心就绪检查器模块的 PowerShell cmdlet 帮助。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: d6b7525657696792bd72d968e8888bd8f7bc62fb
ms.sourcegitcommit: b9d520f3b7bc441d43d489e3e32f9b89601051e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75727439"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>AzsReadinessChecker cmdlet 参考

模块： **test-azurestack. ReadinessChecker**

此模块只包含一个 cmdlet。 Cmdlet 为 Azure Stack 中心执行一个或多个预先部署或预服务函数。

## <a name="syntax"></a>语法

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Description

**AzsReadinessChecker** cmdlet 验证证书、azure 帐户、azure 订阅和 Azure Active directory （Azure AD）。 在部署 Azure Stack 集线器之前或 Azure Stack 中心服务操作（例如机密旋转）之前，请运行验证。 此 cmdlet 还可用于为基础结构证书和 PaaS 证书（可选）生成证书签名请求。 最后，此 cmdlet 可以重新打包 PFX 证书，以修正常见的打包问题。

## <a name="examples"></a>示例

### <a name="example-generate-certificate-signing-request"></a>示例：生成证书签名请求

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

在此示例中，`Start-AzsReadinessChecker` 为 AD FS 的证书生成多个证书签名请求（Csr），该证书适用于区域名称为 "**东部**" 的 Azure Stack 中心部署，外部 FQDN 为 " **azurestack.contoso.com**"。

### <a name="example-validate-certificates"></a>示例：验证证书

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

在此示例中，PFX 密码是安全性必需的，`Start-AzsReadinessChecker` 会检查相对文件夹**证书**，以查找 Azure AD 部署的有效证书，其区域名称为 "**东部**"，外部 FQDN 为 " **azurestack.contoso.com**"。

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>示例：验证包含部署数据的证书（部署和支持）

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

在此部署和支持示例中，PFX 密码是安全所必需的，并且 `Start-AzsReadinessChecker` 会检查相对文件夹**证书**中是否存在对部署有效的证书，其中标识、区域和外部 FQDN 是从为部署生成的部署数据 JSON 文件中读取的。

### <a name="example-validate-paas-certificates"></a>示例：验证 PaaS 证书

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com
```

在此示例中，将使用每个 PaaS 证书的路径和密码来构造哈希表。 可以省略证书。 `Start-AzsReadinessChecker` 检查每个 PFX 路径是否存在，并**使用 region 和**external FQDN **azurestack.contoso.com**验证它们。

### <a name="example-validate-paas-certificates-with-deployment-data"></a>示例：验证部署数据的 PaaS 证书

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

在此示例中，将使用每个 PaaS 证书的路径和密码来构造哈希表。 可以省略证书。 `Start-AzsReadinessChecker` 检查每个 PFX 路径是否存在，并使用从为部署生成的部署数据 JSON 文件中读取的区域和外部 FQDN 验证它们。

### <a name="example-validate-azure-identity"></a>示例：验证 Azure 标识

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

在此示例中，需要提供服务管理员帐户凭据才能确保安全，并 `Start-AzsReadinessChecker` 检查 Azure 帐户和 Azure AD 对于租户目录名称为**azurestack.contoso.com**的 Azure AD 部署是否有效。

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>示例：通过部署数据验证 Azure 标识（部署支持）

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

在此示例中，服务管理员帐户凭据是安全必需的，`Start-AzsReadinessChecker` 检查 Azure 帐户和 Azure AD 对于 Azure AD 部署是否有效，其中**AzureCloud**和**TenantName**是从为部署生成的部署数据 JSON 文件中读取的。

### <a name="example-validate-azure-registration"></a>示例：验证 Azure 注册

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

在此示例中，需要订阅所有者凭据才能确保安全，然后 `Start-AzsReadinessChecker` 针对给定的帐户和订阅执行验证，以确保它可用于 Azure Stack 中心注册。

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>示例：通过部署数据验证 Azure 注册（部署团队）

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

在此示例中，需要订阅所有者凭据才能确保安全，然后 `Start-AzsReadinessChecker` 针对给定的帐户和订阅执行验证，以确保可将其用于 Azure Stack 中心注册，并从为部署生成的部署数据 JSON 文件中读取更多详细信息。

### <a name="example-importexport-pfx-package"></a>示例：导入/导出 PFX 包

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

在此示例中，需要 PFX 密码才能实现安全性。 Ssl .pfx 文件将导入到本地计算机证书存储中，用同一密码重新导出，并另存为 Ssl_new .pfx。 当证书验证标志私钥未设置**本地计算机**属性、证书链断开、PFX 中存在不相关的证书或证书链的顺序不正确时，将使用此过程。

### <a name="example-view-validation-report-deployment-and-support"></a>示例：查看验证报告（部署和支持）

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

在此示例中，部署或支持团队从客户（Contoso）接收就绪状态报告，并使用 `Start-AzsReadinessChecker` 查看 Contoso 执行的验证执行的状态。

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>示例：仅查看验证报表摘要仅用于证书验证（部署和支持）

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

在此示例中，部署或支持团队从客户（Contoso）接收就绪状态报告，并使用 `Start-AzsReadinessChecker` 查看 Contoso 执行的证书验证执行的汇总状态。

## <a name="required-parameters"></a>必需的参数

### <a name="-regionname"></a>-RegionName

指定 Azure Stack 中心部署区域名称。

|  |  |
|----------------------------|--------------|
|类型：                       |String        |
|置于                   |已命名         |
|默认值：              |无          |
|接受管道输入：      |错误         |
|接受通配符： |错误         |

### <a name="-fqdn"></a>-FQDN

指定 Azure Stack 中心部署外部 FQDN，还可以指定为**ExternalFQDN**和**ExternalDomainName**。

|  |  |
|----------------------------|--------------|
|类型：                       |String        |
|置于                   |已命名         |
|默认值：              |ExternalFQDN, ExternalDomainName |
|接受管道输入：      |错误         |
|接受通配符： |错误         |

### <a name="-identitysystem"></a>-IdentitySystem

分别为 Azure Active Directory 和 Active Directory 联合服务指定 Azure Stack 中心部署标识系统有效值（AAD 或 ADFS）。

|  |  |
|----------------------------|--------------|
|类型：                       |String        |
|置于                   |已命名         |
|默认值：              |无          |
|有效值：               |"AAD"、"ADFS"  |
|接受管道输入：      |错误         |
|接受通配符： |错误         |

### <a name="-pfxpassword"></a>-PfxPassword

指定与 PFX 证书文件关联的密码。

|  |  |
|----------------------------|---------|
|类型：                       |SecureString |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-paascertificates"></a>-PaaSCertificates

指定包含 PaaS 证书的路径和密码的哈希表。

|  |  |
|----------------------------|---------|
|类型：                       |哈希表 |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

指定 Azure Stack 中心部署数据 JSON 配置文件。 此文件是为部署而生成的。

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-pfxpath"></a>-PfxPath

指定有问题的证书的路径，该路径需要导入/导出例程，如此工具中的证书验证所指示。

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

指定导入/导出例程中生成的 PFX 文件的目标路径。  

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-subject"></a>-Subject

指定证书请求生成的主题的有序字典。

|  |  |
|----------------------------|---------|
|类型：                       |OrderedDictionary   |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-requesttype"></a>-RequestType

指定证书请求的 SAN 类型。 有效值为**MultipleCSR**、 **SingleCSR**。

- **MultipleCSR**会生成多个证书请求，每个服务一个。
- **SingleCSR**为所有服务生成一个证书请求。

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |无     |
|有效值：               |'MultipleCSR','SingleCSR' |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

指定证书请求文件的目标路径。 目录必须已存在。

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

指定用于 Azure Stack 中心部署的 Azure AD 服务管理员。

|  |  |
|----------------------------|---------|
|类型：                       |PSCredential   |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

指定要用于 Azure Stack 中心部署的 Azure AD 名称。

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-azureenvironment"></a>-AzureEnvironment

指定包含要用于 Azure Stack 中心部署和注册的帐户、目录和订阅的 Azure 服务实例。

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |无     |
|有效值：               |' AzureCloud '、' AzureChinaCloud '、' AzureUSGovernment ' |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-registrationaccount"></a>-RegistrationAccount

指定要用于 Azure Stack 集线器注册的注册帐户。

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

指定要用于 Azure Stack 集线器注册的注册订阅 ID。

|  |  |
|----------------------------|---------|
|类型：                       |GUID     |
|置于                   |已命名    |
|默认值：              |无     |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-reportpath"></a>-ReportPath

指定准备情况报表的路径，默认为当前目录和默认报表名称。

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |所有      |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

## <a name="optional-parameters"></a>可选参数

### <a name="-certificatepath"></a>-CertificatePath

指定仅存在证书所需证书文件夹的路径。

Azure AD 标识系统 Azure Stack 中心部署所需的文件夹为：

- ACSBlob、ACSQueue、ACSTable、管理门户、ARM 管理员、ARM 公有、KeyVault、KeyVaultInternal、公有门户

Active Directory 联合身份验证服务标识系统 Azure Stack 中心部署所需的文件夹为：

- ACSBlob，ACSQueue，ACSTable，ADFS，管理门户，ARM 管理员，ARM 公共，图形，KeyVault，KeyVaultInternal，公共门户

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |.\Certificates |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-includepaas"></a>-IncludePaaS  

指定是否应将 PaaS 服务/主机名添加到证书请求中。

|  |  |
|----------------------------|------------------|
|类型：                       |SwitchParameter   |
|置于                   |已命名             |
|默认值：              |错误             |
|接受管道输入：      |错误             |
|接受通配符： |错误             |

### <a name="-reportsections"></a>-ReportSections

指定是否仅显示报表摘要、省略详细信息。

|  |  |
|----------------------------|---------|
|类型：                       |String   |
|置于                   |已命名    |
|默认值：              |所有      |
|有效值：               |"证书"、"AzureRegistration"、"AzureIdentity"、"作业"、"全部" |
|接受管道输入：      |错误    |
|接受通配符： |错误    |

### <a name="-summary"></a>-Summary

指定是否仅显示报表摘要、省略详细信息。

|  |  |
|----------------------------|------------------|
|类型：                       |SwitchParameter   |
|置于                   |已命名             |
|默认值：              |错误             |
|接受管道输入：      |错误             |
|接受通配符： |错误             |

### <a name="-cleanreport"></a>-CleanReport

删除以前的执行和验证历史记录，并将验证写入新报表。

|  |  |
|----------------------------|------------------|
|类型：                       |SwitchParameter   |
|别名                    |cf                |
|置于                   |已命名             |
|默认值：              |错误             |
|接受管道输入：      |错误             |
|接受通配符： |错误             |

### <a name="-outputpath"></a>-OutputPath

指定用于保存准备情况 JSON 报表和详细日志文件的自定义路径。 如果路径不存在，则该命令将尝试创建目录。

|  |  |
|----------------------------|------------------|
|类型：                       |String            |
|置于                   |已命名             |
|默认值：              |$ENV： TEMP\AzsReadinessChecker  |
|接受管道输入：      |错误             |
|接受通配符： |错误             |

### <a name="-confirm"></a>-Confirm

提示你在运行 cmdlet 之前进行确认。

|  |  |
|----------------------------|------------------|
|类型：                       |SwitchParameter   |
|别名                    |cf                |
|置于                   |已命名             |
|默认值：              |错误             |
|接受管道输入：      |错误             |
|接受通配符： |错误             |

### <a name="-whatif"></a>-WhatIf

显示在此 cmdlet 运行的情况下将会发生什么。 Cmdlet 不会运行。

|  |  |
|----------------------------|------------------|
|类型：                       |SwitchParameter   |
|别名                    |保真                |
|置于                   |已命名             |
|默认值：              |错误             |
|接受管道输入：      |错误             |
|接受通配符： |错误             |
