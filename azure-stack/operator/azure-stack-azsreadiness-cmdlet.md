---
title: Start-AzsReadinessChecker cmdlet 参考 | Microsoft Docs
description: Azure Stack 就绪性检查器模块的 PowerShell cmdlet 帮助。
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
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 1f8d7573d9d3da54ddb5fa7aae85ba15d1db4c3c
ms.sourcegitcommit: 2b6a0b3b4dc63c26df3d0535d630d640ff232fb0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521256"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Start-AzsReadinessChecker cmdlet 参考

模块：**Microsoft.AzureStack.ReadinessChecker**

此模块只包含一个 cmdlet。 该 cmdlet 将执行一个或多个预先部署或维护预先函数适用于 Azure Stack。

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

## <a name="description"></a>描述

**Start-AzsReadinessChecker** cmdlet 验证证书、Azure 帐户、Azure 订阅和 Azure Active Directory。 运行验证前部署 Azure Stack 或 Azure Stack 服务操作，例如机密轮换之前。 该 cmdlet 还可用于生成证书签名请求的基础结构证书和 （可选） 的 PaaS 证书。 最后，该 cmdlet 可以重新打包 PFX 证书，以解决常见的打包问题。

## <a name="examples"></a>示例

### <a name="example-generate-certificate-signing-request"></a>示例： 生成证书签名请求

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

在此示例中，`Start-AzsReadinessChecker`为适用于 AD FS Azure Stack 部署使用的区域名称的证书生成多个证书签名请求 (Csr)**东部**和外部 FQDN 的**azurestack.contoso.com**。

### <a name="example-validate-certificates"></a>示例： 验证证书

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

在此示例中，PFX 密码是必需的安全，并`Start-AzsReadinessChecker`检查相对文件夹**证书**AAD 部署中使用区域名称的有效证书**东部**和外部 FQDN **azurestack.contoso.com**。

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>示例： 使用验证证书部署数据 （的部署和支持）

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

在此示例中的部署和支持，PFX 密码是必需的安全，并`Start-AzsReadinessChecker`检查相对文件夹**证书**部署其中标识、 区域和外部 FQDN 是有效的证书从部署生成的部署数据 JSON 文件中读取。

### <a name="example-validate-paas-certificates"></a>示例： 验证的 PaaS 证书

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

此示例使用每个 PaaS 证书的路径和密码构造了哈希表。 可以省略证书。 `Start-AzsReadinessChecker` 检查是否每个 PFX 路径存在，并对其使用的区域进行验证**东部**和外部 FQDN **azurestack.contoso.com**。

### <a name="example-validate-paas-certificates-with-deployment-data"></a>示例： 验证部署数据的 PaaS 证书

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

此示例使用每个 PaaS 证书的路径和密码构造了哈希表。 可以省略证书。 `Start-AzsReadinessChecker` 检查每个 PFX 路径存在，并对其使用的区域进行验证，并从部署生成的部署数据 JSON 文件中读取外部 FQDN。

### <a name="example-validate-azure-identity"></a>示例： 验证 Azure 标识

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

在此示例中，服务管理员帐户凭据所需的安全，并`Start-AzsReadinessChecker`检查 Azure 帐户和 Azure Active Directory 是 AAD 部署中使用租户目录名称的有效**azurestack.contoso.com**。

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>示例： 验证部署数据 （部署支持） 与 Azure 标识

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

在此示例中，服务管理员帐户凭据所需的安全，并`Start-AzsReadinessChecker`检查 Azure 帐户和 Azure Active Directory 是有效的 AAD 部署，其中**AzureCloud**和**TenantName**从生成的部署的部署数据 JSON 文件读取。

### <a name="example-validate-azure-registration"></a>示例： 验证 Azure 注册

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

在此示例中，订阅所有者凭据所需的安全性，和`Start-AzsReadinessChecker`然后执行针对给定的帐户和订阅以确保它可以用于 Azure Stack 注册的验证。

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>示例： 验证部署数据 （部署团队） 向 Azure 注册

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

在此示例中，订阅所有者凭据所需的安全性，和`Start-AzsReadinessChecker`然后执行针对给定的帐户和订阅以确保它可以用于 Azure Stack 注册，从读取更多详细信息的位置的验证为部署生成部署数据的 JSON 文件。

### <a name="example-importexport-pfx-package"></a>示例： 导入/导出 PFX 包

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

在此示例中，PFX 密码是安全的要求。 Ssl.pfx 文件是导入到本地计算机证书存储中，使用相同的密码，重新导出并保存为 Ssl_new.pfx。 私钥不具有证书验证标记时使用此过程**本地计算机**属性集，证书链已中断、 不相关的证书是否存在在 PFX 中，或证书链按错误的顺序。

### <a name="example-view-validation-report-deployment-and-support"></a>示例： 查看验证报告 （的部署和支持）

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

在此示例中，部署或支持团队接收从客户 (Contoso) 的准备情况报告，并使用`Start-AzsReadinessChecker`若要查看 Contoso 执行验证执行的状态。

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>示例： 查看验证报告摘要证书验证唯一 （部署和支持）

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

在此示例中，部署或支持团队接收从客户 (Contoso) 的准备情况报告，并使用`Start-AzsReadinessChecker`查看汇总的 Contoso 执行证书验证执行的状态。

## <a name="required-parameters"></a>必需的参数

### <a name="-regionname"></a>-RegionName

指定 Azure Stack 部署区域名称。

|  |  |
|----------------------------|--------------|
|键入：                       |String        |
|职位:                   |名为         |
|默认值：              |无          |
|接受管道输入：      |False         |
|接受通配符： |False         |

### <a name="-fqdn"></a>-FQDN

指定 Azure Stack 部署外部 FQDN，也可使用别名作为**ExternalFQDN**并**ExternalDomainName**。

|  |  |
|----------------------------|--------------|
|键入：                       |String        |
|职位:                   |名为         |
|默认值：              |ExternalFQDN、ExternalDomainName |
|接受管道输入：      |False         |
|接受通配符： |False         |

### <a name="-identitysystem"></a>-IdentitySystem

指定的 Azure Stack 部署标识系统有效值，AAD 或 ADFS，Azure Active Directory 和 Active Directory 联合服务分别。

|  |  |
|----------------------------|--------------|
|键入：                       |String        |
|职位:                   |名为         |
|默认值：              |无          |
|有效值：               |'AAD'、'ADFS'  |
|接受管道输入：      |False         |
|接受通配符： |False         |

### <a name="-pfxpassword"></a>-PfxPassword

指定与 PFX 证书文件关联的密码。

|  |  |
|----------------------------|---------|
|键入：                       |SecureString |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

指定包含路径和密码的 PaaS 证书的哈希表。

|  |  |
|----------------------------|---------|
|键入：                       |Hashtable |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

指定 Azure Stack 部署数据 JSON 配置文件。 此文件是针对部署生成的。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-pfxpath"></a>-PfxPath

指定有问题的证书，需要导入/导出的例程，以解决问题，此工具中的证书验证所示的路径。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

指定结果的 PFX 文件导入/导出例程的目标路径。  

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-subject"></a>-Subject

指定用于生成证书请求的主题的排序字典。

|  |  |
|----------------------------|---------|
|键入：                       |OrderedDictionary   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-requesttype"></a>-RequestType

指定证书请求的 SAN 类型。 有效的值为**MultipleCSR**， **SingleCSR**。

- **MultipleCSR** 生成多个证书请求，针对每个服务各生成一个。
- **SingleCSR** 为所有服务生成一个证书请求。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|有效值：               |'MultipleCSR'、'SingleCSR' |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

指定证书请求文件的目标路径。 目录必须已存在。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

指定 Azure Active Directory 服务管理员要用于 Azure Stack 部署。

|  |  |
|----------------------------|---------|
|键入：                       |PSCredential   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

指定用于 Azure Stack 部署的 Azure Active Directory 名称。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

指定用于 Azure Stack 部署和注册的帐户、目录和订阅所在的 Azure 服务实例。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|有效值：               |AzureCloud，AzureChinaCloud，AzureUSGovernment |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

指定要用于 Azure Stack 注册的注册帐户。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

指定要用于 Azure Stack 注册的注册的订阅 ID。

|  |  |
|----------------------------|---------|
|键入：                       |Guid     |
|职位:                   |名为    |
|默认值：              |无     |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-reportpath"></a>-ReportPath

指定用于准备情况报表的路径，则默认值为当前目录和默认的报表名称。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |全部      |
|接受管道输入：      |False    |
|接受通配符： |False    |

## <a name="optional-parameters"></a>可选参数

### <a name="-certificatepath"></a>-CertificatePath

指定只包含证书所需证书文件夹的路径。

包含 Azure Active Directory 标识系统的 Azure Stack 部署的所需文件夹为：

ACSBlob、ACSQueue、ACSTable、Admin Portal、ARM Admin、ARM Public、KeyVault、KeyVaultInternal、Public Portal

包含 Active Directory 联合身份验证服务标识系统的 Azure Stack 部署的所需文件夹为：

ACSBlob、ACSQueue、ACSTable、ADFS、Admin Portal、ARM Admin、ARM Public、Graph、KeyVault、KeyVaultInternal、Public Portal

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |.\Certificates |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-includepaas"></a>-IncludePaaS  

指定是否应将 PaaS 服务/主机名添加到证书请求。

|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |

### <a name="-reportsections"></a>-ReportSections

指定是否仅显示报告摘要，并省略详细信息。

|  |  |
|----------------------------|---------|
|键入：                       |String   |
|职位:                   |名为    |
|默认值：              |全部      |
|有效值：               |'Certificate'、'AzureRegistration'、'AzureIdentity'、'Jobs'、'All' |
|接受管道输入：      |False    |
|接受通配符： |False    |

### <a name="-summary"></a>-Summary

指定是否仅显示报告摘要，并省略详细信息。

|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |

### <a name="-cleanreport"></a>-CleanReport

删除以往的执行和验证历史记录，并将验证结果写入新报告。

|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|别名：                    |cf                |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |

### <a name="-outputpath"></a>-OutputPath

指定自定义 JSON 的准备情况报告和详细日志文件的保存路径。 如果路径不存在，该命令将尝试创建目录。

|  |  |
|----------------------------|------------------|
|键入：                       |String            |
|职位:                   |名为             |
|默认值：              |$ENV:TEMP\AzsReadinessChecker  |
|接受管道输入：      |False             |
|接受通配符： |False             |

### <a name="-confirm"></a>-Confirm

提示你在运行 cmdlet 之前进行确认。

|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|别名：                    |cf                |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |

### <a name="-whatif"></a>-WhatIf

显示在此 cmdlet 运行的情况下将会发生什么。 此 cmdlet 未运行。

|  |  |
|----------------------------|------------------|
|键入：                       |SwitchParameter   |
|别名：                    |wi                |
|职位:                   |名为             |
|默认值：              |False             |
|接受管道输入：      |False             |
|接受通配符： |False             |
