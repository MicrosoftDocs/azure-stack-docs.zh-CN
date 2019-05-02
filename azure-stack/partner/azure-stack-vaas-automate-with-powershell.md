---
title: 自动执行使用 PowerShell 的 Azure Stack 验证 |Microsoft Docs
description: 可以自动使用 PowerShell 的 Azure Stack 验证。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 39ed9ee9dab7f2ec97d2fb6a0148db333648b227
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297476"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>自动执行使用 PowerShell 的 Azure Stack 验证

作为一项服务 (VaaS) 验证提供的功能来自动执行的测试使用启动**LaunchVaaSTests.ps1**脚本。

> [!NOTE]  
> 自动化功能仅适用于测试通过工作流。 通过 VaaS 门户仅支持在包验证和解决方案验证工作流。

此脚本可用于：

> [!div class="checklist"]
> * 安装必备组件
> * 安装并启动本地代理
> * 启动类别的测试，如*集成*，*功能*，*可靠性*
> * 报告测试结果

## <a name="launch-the-test-pass-workflow"></a>启动测试通过工作流

1. 打开提升的 PowerShell 命令提示符。

2. 运行以下脚本来下载自动化脚本：

    ```powershell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. 使用适当的参数值运行以下脚本：

    ```powershell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **参数**

    | 参数 | 描述 |
    | --- | --- |
    | VaaSUserId | VaaS 用户 ID。 |
    | VaaSUserPassword | VaaS 密码。 |
    | VaaSAccountTenantId | VaaS 租户 GUID。 |
    | VaaSSolutionName | 将运行测试通过在其下的 VaaS 解决方案的名称。 |
    | VaaSTestPassName | VaaS 测试的名称将传递要创建工作流。 |
    | VaaSTestCategories | `Integration``Functional`，或。 `Reliability`。 如果使用多个值，请用逗号分隔各个值。  |
    | ServiceAdminUserName | Azure Stack 服务管理员帐户。  |
    | ServiceAdminPassword | Azure Stack 服务密码。  |
    | TenantAdminUserName | 主租户的管理员。  |
    | TenantAdminPassword | 主租户的密码。  |
    | CloudAdminUserName | 云管理员用户名。  |
    | CloudAdminPassword | 云管理员的密码。  |

4. 查看测试结果。 有关其他选项，请参阅[监视和管理 VaaS 门户中的测试](azure-stack-vaas-monitor-test.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure Stack 上的 PowerShell 的详细信息，请查看最新的模块。

> [!div class="nextstepaction"]
> [Azure Stack 模块](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
