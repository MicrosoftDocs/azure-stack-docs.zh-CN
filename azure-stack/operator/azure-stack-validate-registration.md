---
title: 验证 Azure 注册
titleSuffix: Azure Stack Hub
description: 了解如何使用 Azure Stack Hub 就绪性检查器工具来验证 Azure 注册。
author: PatAltimore
ms.topic: how-to
ms.date: 10/19/2020
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: ffe992c4a2db39f5b2e29d80a002f1486099baaa
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868788"
---
# <a name="validate-azure-registration"></a>验证 Azure 注册

开始 Azure Stack Hub 部署之前，使用 Azure Stack Hub 就绪性检查器工具 (**AzsReadinessChecker**) 验证 Azure 订阅是否已准备好与 Azure Stack Hub 配合使用。 就绪性检查器会验证下列项：

- 你使用的 Azure 订阅是受支持的类型。 订阅必须是云解决方案提供商 (CSP) 或企业协议 (EA)。
- 用来向 Azure 注册订阅的帐户可以登录到 Azure 并且是订阅所有者。

有关 Azure Stack Hub 注册的详细信息，请参阅[向 Azure 注册 Azure Stack Hub](azure-stack-registration.md)。

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

从 [PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 **AzsReadinessChecker**。  

## <a name="install-and-configure"></a>安装和配置

### <a name="az-powershell"></a>[Az PowerShell](#tab/az)

### <a name="prerequisites"></a>必备条件

需要以下先决条件：

#### <a name="az-powershell-modules"></a>Az PowerShell 模块

将需要安装 Az PowerShell 模块。 有关说明，请参阅[安装 PowerShell Az 预览版模块](powershell-install-az-module.md)。

#### <a name="azure-active-directory-aad-environment"></a>Azure Active Directory (AAD) 环境

- 标识将与 Azure Stack Hub 配合使用的帐户的用户名和密码，该帐户必须是 Azure 订阅所有者。  
- 标识将使用的 Azure 订阅的订阅 ID。

### <a name="steps-to-validate-the-azure-registration"></a>验证 Azure 注册的步骤

1. 打开一个提升的 PowerShell 提示符，然后运行以下命令来安装 **AzsReadinessChecker**：

   ```powershell
   Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
   Install-AzProfile -Profile 2019-03-01-hybrid -Force
   Install-Module -Name Microsoft.AzureStack.ReadinessChecker -AllowPrerelease
   ```

2. 在 PowerShell 提示符下运行以下命令，将 `$subscriptionID` 设置为要使用的 Azure 订阅。 将 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 替换为你自己的订阅 ID：

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

3. 在 PowerShell 提示符下，运行以下命令： 

   ```powershell
   Connect-AzAccount -subscription $subscriptionID
   ```

4. 在 PowerShell 提示符下运行以下命令，开始验证订阅。 提供 Azure AD 管理员和 Azure AD 租户名称：

   ```powershell
   Invoke-AzsRegistrationValidation  -RegistrationSubscriptionID $subscriptionID
   ```

5. 运行该工具后，查看输出。 确认状态是否符合登录和注册要求。 验证成功时会显示类似于以下示例的输出：

   ```powershell
   Invoke-AzsRegistrationValidation v1.2005.1269 started.
   Checking Registration Requirements: OK

   Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

### <a name="azurerm-powershell"></a>[AzureRM PowerShell](#tab/rm)

### <a name="prerequisites"></a>必备条件

需要以下先决条件：

#### <a name="azurerm-powershell-modules"></a>AzureRM PowerShell 模块

将需要安装 Az PowerShell 模块。 有关说明，请参阅[安装 PowerShell AzureRM 模块](powershell-install-az-module.md)。

#### <a name="the-computer-on-which-the-tool-runs"></a>运行该工具的计算机

- Windows 10 或 Windows Server 2016，具有 Internet 连接。
- PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmdlet，然后查看主要版本和次要版本：  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [为 Azure Stack Hub 配置的 PowerShell](powershell-install-az-module.md)。
- 最新版本的 [Microsoft Azure Stack Hub 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。  

#### <a name="azure-active-directory-azure-ad-environment"></a>Azure Active Directory (Azure AD) 环境

- 标识将与 Azure Stack Hub 配合使用的帐户的用户名和密码，该帐户必须是 Azure 订阅所有者。  
- 标识将使用的 Azure 订阅的订阅 ID。
- 标识将使用的 **AzureEnvironment**。 环境名称参数支持的值包括 **AzureCloud**、 **AzureChinaCloud** 或 **AzureUSGovernment**，具体取决于所使用的 Azure 订阅。

### <a name="steps-to-validate-the-azure-registration"></a>验证 Azure 注册的步骤

1. 在满足先决条件的计算机上，打开一个提升的 PowerShell 提示符，然后运行以下命令来安装 **AzsReadinessChecker**：

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
   ```

2. 在 PowerShell 提示符下运行以下命令，将 `$registrationCredential` 设置为身为订阅所有者的帐户。 将 `subscriptionowner@contoso.onmicrosoft.com` 替换为你的帐户和租户名称：

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > 作为 CSP，在使用共享服务或 IUR 订阅时，你必须提供来自相应 Azure AD 的用户的凭据。 通常这将类似于 `subscriptionowner@iurcontoso.onmicrosoft.com`。 该用户必须具有相应的凭据，如上一步所述。

3. 在 PowerShell 提示符下运行以下命令，将 `$subscriptionID` 设置为要使用的 Azure 订阅。 将 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 替换为你自己的订阅 ID：

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. 在 PowerShell 提示符下运行以下命令，开始验证订阅：

   - 将的值指定 `AzureEnvironment` 为 **AzureCloud**、 **AzureGermanCloud** 或 **AzureChinaCloud**。  
   - 提供 Azure AD 管理员和 Azure AD 租户名称。
      ```powershell
      Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
      ```

5. 运行该工具后，查看输出。 确认状态是否符合登录和注册要求。 验证成功时会显示类似于以下示例的输出：

   ```powershell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```
---

## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

这些文件可以帮助你在部署 Azure Stack Hub 之前共享验证状态，或者调查验证问题。 这两个文件都会持久保留每个后续验证检查的结果。 报表向你的部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。

这两个文件默认写入到 `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`。  

- 在运行命令行的末尾使用 `-OutputPath <path>` 参数来指定不同的报表位置。
- 在运行命令的末尾使用 `-CleanReport` 参数从 **AzsReadinessCheckerReport.json** 中清除有关以前运行此工具的相关信息。

有关详细信息，请参阅 [Azure Stack Hub 验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还会将信息记录到 **AzsReadinessChecker.log** 文件中。

以下示例提供了有关常见验证失败的更多信息。

### <a name="user-must-be-an-owner-of-the-subscription"></a>用户必须是订阅所有者

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**原因** - 帐户不是 Azure 订阅的管理员。

**解决方法** - 使用要根据 Azure Stack Hub 部署中的资源使用量而被收费的 Azure 订阅的管理员帐户。

### <a name="expired-or-temporary-password"></a>过期的或临时密码

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**原因** - 因为密码已过期或者是临时的，所以帐户无法登录。

**解决方法** - 在 PowerShell 中运行以下命令，然后根据提示来重置密码。

```powershell
Login-AzureRMAccount
```

另一种方法是以帐户所有者身份登录到 [Azure 门户](https://portal.azure.com)，强制用户更改密码。

### <a name="unknown-user-type"></a>未知用户类型  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**原因** - 帐户无法登录到指定的 Azure AD 环境。 在本例中，将 **AzureChinaCloud** 指定为了 **AzureEnvironment**。  

**解决方法** - 确认帐户对指定的 Azure 环境有效。 在 PowerShell 中运行以下命令，验证帐户对特定环境是否有效：

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>后续步骤

- [验证 Azure 标识](azure-stack-validate-identity.md)
- [查看就绪性报表](azure-stack-validation-report.md)
- [有关 Azure Stack Hub 集成的一般注意事项](azure-stack-datacenter-integration.md)
