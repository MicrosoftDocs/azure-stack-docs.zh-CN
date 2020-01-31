---
title: 验证 Azure Stack 集线器的 Azure 标识
description: 使用 Azure Stack 集线器就绪状态检查器来验证 Azure 标识。
author: ihenkel
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: a6646f6c96faa15dd8ddd2ada24ef1eafe4d94f5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882604"
---
# <a name="validate-azure-identity"></a>验证 Azure 标识

使用 Azure Stack 集线器就绪检查器工具（**AzsReadinessChecker**）验证 Azure Active Directory （Azure AD）是否已准备好与 Azure Stack 中心一起使用。 开始 Azure Stack 中心部署之前，请验证 Azure 身份解决方案。  

就绪检查程序将验证：

- Azure Active Directory （Azure AD）作为 Azure Stack 中心的标识提供者。
- 你计划使用的 Azure AD 帐户可以作为你的 Azure Active Directory 的全局管理员登录。

验证可确保你的环境已准备好 Azure Stack 中心，以便在 Azure AD 的 Azure Stack 中心存储有关用户、应用程序、组和服务主体的信息。

## <a name="get-the-readiness-checker-tool"></a>获取就绪状态检查器工具

从[PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 Azure Stack 集线器就绪检查器工具（AzsReadinessChecker）。  

## <a name="prerequisites"></a>必备组件

需要以下先决条件：

**运行该工具的计算机：**

- Windows 10 或 Windows Server 2016，具有 internet 连接。
- PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell 命令，然后查看**主要**版本和**次要**版本：  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [为 Azure Stack 中心配置的 PowerShell](azure-stack-powershell-install.md)。
- 最新版本的[Microsoft Azure Stack 集线器就绪检查](https://aka.ms/AzsReadinessChecker)程序工具。

**Azure Active Directory 环境：**

- 确定要用于 Azure Stack 中心的 Azure AD 帐户，并确保该帐户是 Azure Active Directory 全局管理员。
- 确定 Azure AD 租户名称。 租户名称必须是 Azure Active Directory 的主域名;例如， **contoso.onmicrosoft.com**。
- 确定要使用的 Azure 环境。 环境名称参数支持的值包括**AzureCloud**、 **AzureChinaCloud**或**AzureUSGovernment**，具体取决于所使用的 Azure 订阅。

## <a name="steps-to-validate-azure-identity"></a>验证 Azure 标识的步骤

1. 在满足先决条件的计算机上，打开提升的 PowerShell 命令提示符，然后运行以下命令以安装**AzsReadinessChecker**：  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. 在 PowerShell 提示符下，运行以下命令，将 **$serviceAdminCredential**设置为 Azure AD 租户的服务管理员。  将**serviceadmin\@contoso.onmicrosoft.com**替换为你的帐户和租户名称：

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. 在 PowerShell 提示符下，运行以下命令以开始验证 Azure AD：

   - 指定**AzureEnvironment**的环境名称值。 环境名称参数支持的值包括**AzureCloud**、 **AzureChinaCloud**或**AzureUSGovernment**，具体取决于所使用的 Azure 订阅。
   - 将**contoso.onmicrosoft.com**替换为你的 Azure Active Directory 租户名称。

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. 运行该工具后，查看输出。 确认 "状态 **" 为 "正常"** 安装要求。 验证成功的方式如下图所示：

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

每次验证运行时，都会将结果记录到**AzsReadinessChecker**和**AzsReadinessCheckerReport**中。 这些文件的位置在 PowerShell 中显示验证结果。

这些文件可帮助你在部署 Azure Stack 中心或调查验证问题之前共享验证状态。 这两个文件都保留每个后续验证检查的结果。 该报表提供部署团队确认身份配置。 日志文件可帮助您的部署或支持团队调查验证问题。

默认情况下，这两个文件都将写入到**C:\Users\<用户名 > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**。  

- 在运行命令行的末尾使用 **-OutputPath** ***&lt;path&gt;*** 参数来指定不同的报表位置。
- 在 run 命令的末尾使用 **-CleanReport**参数来从**AzsReadinessCheckerReport**中清除有关以前运行的工具的信息。

有关详细信息，请参阅[Azure Stack 中心验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，将在 PowerShell 窗口中显示有关失败的详细信息。 该工具还会将信息记录到 AzsReadinessChecker 文件。

下面的示例提供了有关常见验证失败的指导。

### <a name="expired-or-temporary-password"></a>过期或临时密码

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

**原因**-帐户无法登录，因为密码已过期或为临时密码。

**解决方法**：在 PowerShell 中运行以下命令，然后按照提示重置密码：

```powershell
Login-AzureRMAccount
```

或者，以帐户所有者身份登录到[Azure 门户](https://portal.azure.com)，并将强制用户更改密码。

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

**原因**-帐户无法登录到指定的 Azure Active Directory （**AADDirectoryTenantName**）。 在此示例中，将**AzureChinaCloud**指定为**AzureEnvironment**。

**解决方法**-确认该帐户对于指定的 Azure 环境有效。 在 PowerShell 中运行以下命令，验证该帐户对于特定环境是否有效：

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

**原因**-尽管帐户可以成功登录，但该帐户不是 Azure Active Directory （**AADDirectoryTenantName**）的管理员。  

**解决方法**-以帐户所有者身份登录到[Azure 门户](https://portal.azure.com)，依次进入 " **Azure Active Directory**" 和 "**用户**"，然后依次**选择 "用户**"、"**目录角色**" 和 "用户 **"。** 如果帐户是**用户**，请参阅**Azure Active Directory** > **自定义域名**，并确认你为**AADDirectoryTenantName**提供的名称被标记为此目录的主域名。 在此示例中，为**contoso.onmicrosoft.com**。

Azure Stack 中心要求域名为主要域名。

## <a name="next-steps"></a>后续步骤

[验证 Azure 注册](azure-stack-validate-registration.md)  
[查看准备情况报表](azure-stack-validation-report.md)  
[一般 Azure Stack 集线器集成注意事项](azure-stack-datacenter-integration.md)  
