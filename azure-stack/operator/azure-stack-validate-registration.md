---
title: 为 Azure Stack 验证 Azure 注册 | Microsoft Docs
description: 使用 Azure Stack 就绪性检查器来验证 Azure 注册。
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
ms.date: 06/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: ca2ea89ab73c7a8e056e4b4700e92a872e628f34
ms.sourcegitcommit: 3f52cf06fb5b3208057cfdc07616cd76f11cdb38
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316177"
---
# <a name="validate-azure-registration"></a>验证 Azure 注册

使用 Azure Stack 就绪性检查器工具 (**AzsReadinessChecker**) 验证 Azure 订阅是否已准备好与 Azure Stack 配合使用。 在开始 Azure Stack 部署之前，请验证注册。 就绪性检查器会验证下列项：

- 你使用的 Azure 订阅是受支持的类型。 订阅必须是云服务提供商 (CSP) 或企业协议 (EA)。
- 用来向 Azure 注册订阅的帐户可以登录到 Azure 并且是订阅所有者。

有关 Azure Stack 注册的详细信息，请参阅[向 Azure 注册 Azure Stack](azure-stack-registration.md)。

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

从 [PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的 **AzsReadinessChecker**。  

## <a name="prerequisites"></a>必备组件

以下系统必备组件是必需的：

### <a name="the-computer-on-which-the-tool-runs"></a>运行该工具的计算机

- Windows 10 或 Windows Server 2016，具有 Internet 连接。
- PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmdlet，然后查看主要  版本和次要  版本：  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [为 Azure Stack 配置的 PowerShell](azure-stack-powershell-install.md)。
- 最新版本的 [Microsoft Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)。  

### <a name="azure-active-directory-environment"></a>Azure Active Directory 环境

- 标识将与 Azure Stack 配合使用的帐户的用户名和密码，该帐户必须是 Azure 订阅所有者。  
- 标识将使用的 Azure 订阅的订阅 ID。
- 标识将使用的 **AzureEnvironment**。 支持的环境名称参数值为**AzureCloud**， **AzureChinaCloud**，或**AzureUSGovernment**，取决于你的 Azure 订阅使用。

## <a name="steps-to-validate-azure-registration"></a>验证 Azure 注册的步骤

1. 在满足先决条件的计算机上，打开一个提升的 PowerShell 提示符，然后运行以下命令来安装 **AzsReadinessChecker**：

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. 在 PowerShell 提示符下运行以下命令，将 `$registrationCredential` 设置为身为订阅所有者的帐户。 将 `subscriptionowner@contoso.onmicrosoft.com` 替换为你的帐户和租户名称：

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > 作为 CSP，当使用共享服务或 IUR 订阅，必须从该各自 AAD 提供的用户的凭据。 通常这将类似于 `subscriptionowner@iurcontoso.onmicrosoft.com`。 该用户必须具有相应的凭据，如上一步所述。

3. 在 PowerShell 提示符下运行以下命令，将 `$subscriptionID` 设置为要使用的 Azure 订阅。 将 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 替换为你自己的订阅 ID：

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. 在 PowerShell 提示符下运行以下命令，开始验证订阅：

   - 指定的值`AzureEnvironment`作为**AzureCloud**， **AzureGermanCloud**，或**AzureChinaCloud**。  
   - 提供 Azure Active Directory 管理员用户名和 Azure Active Directory 租户名称。

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. 运行该工具后，查看输出。 确认状态是否符合登录和注册要求。 验证成功时会显示类似于以下示例的输出：

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

这些文件可以帮助你在部署 Azure Stack 之前共享验证状态，或者调查验证问题。 这两个文件都会持久保留每个后续验证检查的结果。 报表向你的部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。

默认情况下，这两个文件写入到**C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**。  

- 可以在运行命令行的末尾使用 **-OutputPath** ***&lt;path&gt;*** 参数指定一个不同的报表位置。
- 可以在运行命令的末尾使用 **-CleanReport** 参数从 **AzsReadinessCheckerReport.json** 中清除以前运行此工具时的相关信息。

有关详细信息，请参阅 [Azure Stack 验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还会将信息记录到 **AzsReadinessChecker.log** 文件中。

下面的示例针对常见的验证失败提供了指导：

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

**解决方法** - 使用要根据 Azure Stack 部署中的资源使用量而被收费的 Azure 订阅的管理员帐户。

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

或者，以帐户所有者身份登录到 [Azure 门户](https://portal.azure.com)，强制用户更改密码。

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

**原因** - 帐户无法登录到指定的 Azure Active Directory 环境。 在本例中，将 **AzureChinaCloud** 指定为了 **AzureEnvironment**。  

**解决方法** - 确认帐户对指定的 Azure 环境有效。 在 PowerShell 中运行以下命令，验证帐户对特定环境是否有效：

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>后续步骤

- [验证 Azure 标识](azure-stack-validate-identity.md)
- [查看就绪性报表](azure-stack-validation-report.md)
- [有关 Azure Stack 集成的一般注意事项](azure-stack-datacenter-integration.md)
