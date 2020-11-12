---
title: 验证 Azure 标识
titleSuffix: Azure Stack Hub
description: 使用 Azure Stack Hub 就绪性检查器来验证 Azure 标识。
author: BryanLa
ms.topic: how-to
ms.date: 10/19/2020
ms.author: bryanla
ms.reviewer: jerskine
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: c0fa7d69f0941a3a4867f8bec2d60ebc20c079b7
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545578"
---
# <a name="validate-azure-identity"></a>验证 Azure 标识

使用 Azure Stack Hub 就绪性检查器工具 ( **AzsReadinessChecker** ) 验证 Azure Active Directory (Azure AD) 是否已准备好与 Azure Stack Hub 配合使用。 在开始 Azure Stack Hub 部署之前，请验证 Azure 标识解决方案。  

就绪性检查器会验证下列项：

- Azure AD 用作 Azure Stack Hub 的标识提供者。
- 你打算使用的 Azure AD 帐户能够以 Azure AD 的全局管理员身份登录。

验证可以确保你的环境已针对 Azure Stack Hub 做好了准备，可以在 Azure AD 中存储 Azure Stack Hub 中的用户、应用程序、组和服务主体的相关信息。

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

从 [PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 Azure Stack Hub 就绪性检查器工具 (AzsReadinessChecker)。

## <a name="install-and-configure"></a>安装和配置

### <a name="az-powershell"></a>[Az PowerShell](#tab/az)

### <a name="prerequisites"></a>必备条件

需要以下先决条件：

#### <a name="az-powershell-modules"></a>Az PowerShell 模块

将需要安装 Az PowerShell 模块。 有关说明，请参阅[安装 PowerShell Az 预览模块](powershell-install-az-module.md)。

#### <a name="azure-active-directory-azure-ad-environment"></a>Azure Active Directory (Azure AD) 环境

- 标识将用于 Azure Stack Hub 的 Azure AD 帐户并确保它是 Azure AD 全局管理员。
- 标识你的 Azure AD 租户名称。 该租户名称必须是 Azure AD 的主域名。 例如， **contoso.onmicrosoft.com** 。

### <a name="steps-to-validate-azure-identity"></a>验证 Azure 标识的步骤

1. 在满足先决条件的计算机上，打开一个提升的 PowerShell 命令提示符，然后运行以下命令来安装 **AzsReadinessChecker** ：  

   ```powershell
   Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
   Install-AzProfile -Profile 2019-03-01-hybrid -Force
   Install-Module -Name Microsoft.AzureStack.ReadinessChecker -AllowPrerelease
   ```

2. 在 PowerShell 提示符下，运行以下命令。 将 `contoso.onmicrosoft.com` 替换为 Azure AD 租户名称：

   ```powershell
   Connect-AzAccount -tenant contoso.onmicrosoft.com
   ```

3. 在 PowerShell 提示符下，运行以下命令来启动对 Azure AD 的验证。 将 `contoso.onmicrosoft.com` 替换为 Azure AD 租户名称：

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADDirectoryTenantName contoso.onmicrosoft.com 
   ```

4. 运行该工具后，查看输出。 对于安装要求，确认状态为“正常”。 成功的验证如以下示例所示：

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.2005.1269 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```


### <a name="azurerm-powershell"></a>[AzureRM PowerShell](#tab/rm)

## <a name="prerequisites"></a>必备条件

需要以下先决条件：

#### <a name="azurerm-powershell-modules"></a>AzureRM PowerShell 模块

将需要安装 Az PowerShell 模块。 有关说明，请参阅[安装 PowerShell AzureRM 模块](powershell-install-az-module.md)。

#### <a name="the-computer-on-which-the-tool-runs"></a>运行该工具的计算机

- Windows 10 或 Windows Server 2016，已建立 Internet 连接。
- PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell 命令，然后查看主要版本和次要版本：  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [为 Azure Stack Hub 配置的 PowerShell](powershell-install-az-module.md)。
- 最新版本的 [Microsoft Azure Stack 集线器就绪检查](https://aka.ms/AzsReadinessChecker) 程序工具。

#### <a name="azure-ad-environment"></a>Azure AD 环境

- 标识将用于 Azure Stack Hub 的 Azure AD 帐户并确保它是 Azure AD 全局管理员。
- 标识你的 Azure AD 租户名称。 该租户名称必须是 Azure AD 的主域名。 例如， **contoso.onmicrosoft.com** 。
- 标识要使用的 Azure 环境。 环境名称参数支持的值包括 **AzureCloud** 、 **AzureChinaCloud** 或 **AzureUSGovernment** ，具体取决于所使用的 Azure 订阅。

## <a name="steps-to-validate-azure-identity"></a>验证 Azure 标识的步骤

1. 在满足先决条件的计算机上，打开一个提升的 PowerShell 命令提示符，然后运行以下命令来安装 **AzsReadinessChecker** ：  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
   ```

2. 从 PowerShell 提示符下，运行以下命令将 `$serviceAdminCredential` 设置为你的 Azure AD 租户的服务管理员。  将 `serviceadmin\@contoso.onmicrosoft.com` 替换为你的帐户和租户名称：

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. 从 PowerShell 提示符下，运行以下命令来启动对 Azure AD 的验证：

   - 为 **AzureEnvironment** 指定环境名称值。 环境名称参数支持的值包括 **AzureCloud** 、 **AzureChinaCloud** 或 **AzureUSGovernment** ，具体取决于所使用的 Azure 订阅。
   - `contoso.onmicrosoft.com`将替换为你的 Azure AD 租户名称。

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. 运行该工具后，查看输出。 对于安装要求，确认状态为“正常”。 成功的验证如以下示例所示：

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```
--- 


## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

这些文件可以帮助你在部署 Azure Stack Hub 之前共享验证状态，或者调查验证问题。 这两个文件都会持久保留每个后续验证检查的结果。 报表向你的部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。

这两个文件默认写入到 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`。  

- 在运行命令行的末尾使用 `-OutputPath <path>` 参数来指定不同的报表位置。
- 在运行命令的末尾使用 `-CleanReport` 参数从 **AzsReadinessCheckerReport.json** 中清除有关以前运行此工具的相关信息。

有关详细信息，请参阅 [Azure Stack Hub 验证报告](azure-stack-validation-report.md)。

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

**解决方法** - 在 PowerShell 中运行以下命令，然后根据提示重置密码：

```powershell
Login-AzureRMAccount
```

另一种方法是以帐户所有者身份登录到 [Azure 门户](https://portal.azure.com)，强制用户更改密码。

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

**原因** - 帐户无法登录到指定的 Azure AD ( **AADDirectoryTenantName** )。 在本例中，将 **AzureChinaCloud** 指定为了 **AzureEnvironment** 。

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

**原因** - 虽然帐户可以成功登录，但帐户不是 Azure AD ( **AADDirectoryTenantName** ) 的管理员。  

**解决方法** - 以帐户所有者身份登录到 [Azure 门户](https://portal.azure.com)，依次选择“Azure Active Directory”、“用户”、“选择用户”  。 然后选择“目录角色”并确保用户是“全局管理员” 。 如果帐户是“用户”，请转到“Azure Active Directory” > “自定义域名”，并确认你为 **AADDirectoryTenantName** 提供的名称已标记为此目录的主域名。   在此示例中，为 **contoso.onmicrosoft.com** 。

Azure Stack Hub 要求域名是主域名。

## <a name="next-steps"></a>后续步骤

[验证 Azure 注册](azure-stack-validate-registration.md)  
[查看就绪性报表](azure-stack-validation-report.md)  
[有关 Azure Stack Hub 集成的一般注意事项](azure-stack-datacenter-integration.md)  
