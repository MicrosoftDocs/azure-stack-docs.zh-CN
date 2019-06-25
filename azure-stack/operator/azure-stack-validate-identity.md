---
title: 为 Azure Stack 验证 Azure 标识 | Microsoft Docs
description: 使用 Azure Stack 就绪性检查器来验证 Azure 标识。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 9f455d6614917c0365b2143f0523ff2a44b3c05d
ms.sourcegitcommit: fdeb2760845c9760ea7df1414b8e140b0624a823
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67334414"
---
# <a name="validate-azure-identity"></a>验证 Azure 标识

使用 Azure Stack 就绪性检查器工具 (**AzsReadinessChecker**) 验证 Azure Active Directory (Azure AD) 是否已准备好与 Azure Stack 配合使用。 在开始 Azure Stack 部署之前，请验证 Azure 标识解决方案。  

就绪性检查器会验证下列项：

- Azure Active Directory (Azure AD) 用作 Azure Stack 的标识提供程序。
- 你打算使用的 Azure AD 帐户能够以 Azure Active Directory 的全局管理员身份登录。

验证可以确保你的环境已针对 Azure Stack 做好了准备，可以在 Azure AD 中存储 Azure Stack 中的用户、应用程序、组和服务主体的相关信息。

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

从 [PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 Azure Stack 就绪性检查器工具 (AzsReadinessChecker)。  

## <a name="prerequisites"></a>必备组件

以下系统必备组件是必需的：

**运行该工具的计算机：**

- Windows 10 或 Windows Server 2016，具有 Internet 连接。
- PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell 命令，然后查看主要版本和次要版本：    

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [为 Azure Stack 配置的 PowerShell](azure-stack-powershell-install.md)。
- 最新版本的 [Microsoft Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。

**Azure Active Directory 环境：**

- 标识将用于 Azure Stack 的 Azure AD 帐户并确保它是 Azure Active Directory 全局管理员。
- 标识你的 Azure AD 租户名称。 租户名称必须是 Azure Active Directory; 的主域名例如， **contoso.onmicrosoft.com**。
- 标识你将使用的 Azure 环境。 支持的环境名称参数值为**AzureCloud**， **AzureChinaCloud**，或**AzureUSGovernment**，取决于您使用的 Azure 订阅。

## <a name="steps-to-validate-azure-identity"></a>验证 Azure 标识的步骤

1. 在满足先决条件的计算机上，打开一个提升的 PowerShell 命令提示符，然后运行以下命令来安装 **AzsReadinessChecker**：  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. 从 PowerShell 提示符下，运行以下命令将 **$serviceAdminCredential** 设置为你的 Azure AD 租户的服务管理员。  替换**serviceadmin\@contoso.onmicrosoft.com**与你的帐户和租户名称：

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. 从 PowerShell 提示符下，运行以下命令来启动对 Azure AD 的验证：

   - 为 **AzureEnvironment** 指定环境名称值。 支持的环境名称参数值为**AzureCloud**， **AzureChinaCloud**，或**AzureUSGovernment**，取决于您使用的 Azure 订阅。
   - 替换**contoso.onmicrosoft.com**替换为 Azure Active Directory 租户名称。

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. 运行该工具后，查看输出。 对于安装要求，确认状态为“正常”  。 成功的验证如下图所示：

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

这些文件可以帮助你在部署 Azure Stack 之前共享验证状态，或者调查验证问题。 这两个文件都会持久保留每个后续验证检查的结果。 报表向你的部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。

默认情况下，这两个文件都写入到 **C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**。  

- 可以在运行命令行的末尾使用 **-OutputPath** ***&lt;path&gt;*** 参数指定一个不同的报表位置。
- 可以在运行命令的末尾使用 **-CleanReport** 参数从 **AzsReadinessCheckerReport.json** 中清除以前运行此工具时的相关信息。

有关详细信息，请参阅 [Azure Stack 验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还会将信息记录到 AzsReadinessChecker.log 文件中。

下面的示例针对常见的验证失败提供了指导。

### <a name="expired-or-temporary-password"></a>过期的或临时密码

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**原因** - 因为密码已过期或者是临时的，所以帐户无法登录。

**解决方法** - 在 PowerShell 中运行以下命令，然后根据提示来重置密码：

```powershell
Login-AzureRMAccount
```

或者，以帐户所有者身份登录到 [Azure 门户](https://portal.azure.com)，强制用户更改密码。

### <a name="unknown-user-type"></a>未知用户类型 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**原因** - 帐户无法登录到指定的 Azure Active Directory (**AADDirectoryTenantName**)。 在本例中，将 **AzureChinaCloud** 指定为了 **AzureEnvironment**。

**解决方法** - 确认帐户对指定的 Azure 环境有效。 在 PowerShell 中运行以下命令，验证帐户对特定环境是否有效：

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>帐户不是管理员

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**原因** - 虽然帐户可以成功登录，但帐户不是 Azure Active Directory (**AADDirectoryTenantName**) 的管理员。  

**解决方法** - 以帐户所有者身份登录到 [Azure 门户](https://portal.azure.com)，转到“Azure Active Directory”、“用户”，单击“选择用户”、“目录角色”，然后确保该用户为**全局管理员**。     如果帐户是“用户”，请转到“Azure Active Directory” > “自定义域名”，并确认你为 **AADDirectoryTenantName** 提供的名称已标记为此目录的主域名。    在此示例中，这是**contoso.onmicrosoft.com**。

Azure Stack 要求域名是主域名。

## <a name="next-steps"></a>后续步骤

[验证 Azure 注册](azure-stack-validate-registration.md)  
[查看就绪性报表](azure-stack-validation-report.md)  
[有关 Azure Stack 集成的一般注意事项](azure-stack-datacenter-integration.md)  
