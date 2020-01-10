---
title: 验证 Azure Stack 集线器的 Azure 注册 |Microsoft Docs
description: 使用 Azure Stack 集线器就绪状态检查器来验证 Azure 注册。
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
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: a1ac34b39bc1628314c1ba2f05202c4a1454c189
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812835"
---
# <a name="validate-azure-registration"></a>验证 Azure 注册

在开始 Azure Stack 中心部署之前，请使用 Azure Stack 集线器就绪检查器工具（**AzsReadinessChecker**）来验证 Azure 订阅是否已准备好与 Azure Stack 中心一起使用。 就绪检查程序将验证：

- 你使用的 Azure 订阅是受支持的类型。 订阅必须是云解决方案提供商（CSP）或企业协议（EA）。
- 用于在 Azure 中注册订阅的帐户可以登录到 Azure，它是订阅所有者。

有关 Azure Stack 中心注册的详细信息，请参阅将[Azure Stack 中心注册到 Azure](azure-stack-registration.md)。

## <a name="get-the-readiness-checker-tool"></a>获取就绪状态检查器工具

从[PowerShell 库](https://aka.ms/AzsReadinessChecker)下载最新版本的**AzsReadinessChecker** 。  

## <a name="prerequisites"></a>必备组件

需要以下先决条件：

### <a name="the-computer-on-which-the-tool-runs"></a>运行该工具的计算机

- Windows 10 或 Windows Server 2016，具有 internet 连接。
- PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmdlet，并查看**主要**和**次要**版本：  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [为 Azure Stack 中心配置的 PowerShell](azure-stack-powershell-install.md)。
- 最新版本的[Microsoft Azure Stack 集线器就绪检查](https://aka.ms/AzsReadinessChecker)程序工具。  

### <a name="azure-active-directory-environment"></a>Azure Active Directory 环境

- 标识一个帐户的用户名和密码，该帐户是将与 Azure Stack 中心一起使用的 Azure 订阅的所有者。  
- 确定要使用的 Azure 订阅的订阅 ID。
- 确定要使用的**AzureEnvironment** 。 环境名称参数支持的值包括**AzureCloud**、 **AzureChinaCloud**或**AzureUSGovernment**，具体取决于所使用的 Azure 订阅。

## <a name="steps-to-validate-the-azure-registration"></a>验证 Azure 注册的步骤

1. 在满足先决条件的计算机上，打开提升的 PowerShell 提示符，然后运行以下命令以安装**AzsReadinessChecker**：

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. 在 PowerShell 提示符下，运行以下命令，将 `$registrationCredential` 设置为作为订阅所有者的帐户。 将 `subscriptionowner@contoso.onmicrosoft.com` 替换为你的帐户和租户名称：

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > 作为 CSP，使用共享服务或 IUR 订阅时，必须提供该用户各自 Azure AD 的凭据。 通常，这与 `subscriptionowner@iurcontoso.onmicrosoft.com`类似。 该用户必须具有适当的凭据，如前一步骤中所述。

3. 在 PowerShell 提示符下，运行以下命令，将 `$subscriptionID` 设置为要使用的 Azure 订阅。 将 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 替换为你自己的订阅 ID：

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. 在 PowerShell 提示符下，运行以下命令以开始验证你的订阅：

   - 将 `AzureEnvironment` 的值指定为**AzureCloud**、 **AzureGermanCloud**或**AzureChinaCloud**。  
   - 提供 Azure Active Directory 管理员和 Azure Active Directory 租户名称。

      ```powershell
      Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
      ```

5. 运行该工具后，查看输出。 确认登录和注册要求的状态是否正确。 成功的验证输出类似于以下示例：

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>报表和日志文件

每次验证运行时，都会将结果记录到**AzsReadinessChecker**和**AzsReadinessCheckerReport**中。 这些文件的位置与 PowerShell 中的验证结果一起显示在一起。

这些文件可帮助你在部署 Azure Stack 中心或调查验证问题之前共享验证状态。 这两个文件都保留每个后续验证检查的结果。 该报表提供部署团队确认身份配置。 日志文件可帮助您的部署或支持团队调查验证问题。

默认情况下，这两个文件都将写入**C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**。  

- 使用 "运行" 命令行的末尾的 `-OutputPath <path>` 参数来指定不同的报表位置。
- 使用 "运行" 命令末尾的 "`-CleanReport`" 参数可以从**AzsReadinessCheckerReport**清除有关以前运行的工具的信息。

有关详细信息，请参阅[Azure Stack 中心验证报告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，将在 PowerShell 窗口中显示有关失败的详细信息。 该工具还会将信息记录到**AzsReadinessChecker**文件。

下面的示例提供了有关常见验证失败的详细信息。

### <a name="user-must-be-an-owner-of-the-subscription"></a>用户必须是订阅的所有者

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

**原因**-帐户不是 Azure 订阅的管理员。

**解决方法**-使用 Azure 订阅的管理员帐户，该帐户将按照 Azure Stack 中心部署的使用情况进行计费。

### <a name="expired-or-temporary-password"></a>过期或临时密码

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

**原因**-帐户无法登录，因为密码已过期或为临时密码。

**解决方法**：在 PowerShell 中运行以下命令，然后按照提示重置密码。

```powershell
Login-AzureRMAccount
```

或者，以帐户所有者身份登录到[Azure 门户](https://portal.azure.com)，并将强制用户更改密码。

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

**原因**-帐户无法登录到指定的 Azure Active Directory 环境。 在此示例中，将**AzureChinaCloud**指定为**AzureEnvironment**。  

**解决方法**-确认该帐户对于指定的 Azure 环境有效。 在 PowerShell 中运行以下命令，验证该帐户对于特定环境是否有效：

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>后续步骤

- [验证 Azure 标识](azure-stack-validate-identity.md)
- [查看准备情况报表](azure-stack-validation-report.md)
- [一般 Azure Stack 集线器集成注意事项](azure-stack-datacenter-integration.md)
