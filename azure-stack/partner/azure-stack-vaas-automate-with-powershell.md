---
title: 通过 PowerShell 自动 Azure Stack 验证 |Microsoft Docs
description: 可以通过 PowerShell 自动 Azure Stack 验证。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5a49685da010fd7188fd04514a17b91dff7404f7
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418445"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>利用 PowerShell 自动 Azure Stack 验证

验证即服务 (VaaS) 可让你使用**LaunchVaaSTests**脚本自动启动测试。

> [!NOTE]  
> 自动化仅适用于测试通过工作流。 仅通过 VaaS 门户支持包验证和解决方案验证工作流。

此脚本可用于:

> [!div class="checklist"]
> * 安装先决条件
> * 安装并启动本地代理
> * 启动一类测试, 如*集成*、*功能*、*可靠性*
> * 报表测试结果

## <a name="launch-the-test-pass-workflow"></a>启动测试通过工作流

1. 打开提升的 PowerShell 命令提示符。

2. 运行以下脚本来下载自动化脚本：

    ```powershell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. 运行以下具有适当参数值的脚本:

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

    **Parameters**

    | 参数 | 描述 |
    | --- | --- |
    | VaaSUserId | VaaS 用户 ID。 |
    | VaaSUserPassword | VaaS 密码。 |
    | VaaSAccountTenantId | 你的 VaaS 租户 GUID。 |
    | VaaSSolutionName | 将运行测试通过的 VaaS 解决方案的名称。 |
    | VaaSTestPassName | 要创建的 VaaS 测试通过工作流的名称。 |
    | VaaSTestCategories | `Integration`、 `Functional`或。 `Reliability`。 如果使用多个值，请用逗号分隔各个值。  |
    | ServiceAdminUserName | Azure Stack 服务管理员帐户。  |
    | ServiceAdminPassword | Azure Stack 服务密码。  |
    | TenantAdminUserName | 主租户的管理员。  |
    | TenantAdminPassword | 主租户的密码。  |
    | CloudAdminUserName | 云管理员用户名。  |
    | CloudAdminPassword | 云管理员的密码。  |

4. 查看测试结果。 有关其他选项, 请参阅[在 VaaS 门户中监视和管理测试](azure-stack-vaas-monitor-test.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Stack 上的 PowerShell, 请查看最新的模块。

> [!div class="nextstepaction"]
> [Azure Stack 模块](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
