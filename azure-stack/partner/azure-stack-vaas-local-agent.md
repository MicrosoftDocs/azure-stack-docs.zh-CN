---
title: 部署本地代理 |Microsoft Docs
description: 将 Azure Stack 验证的本地代理部署为服务。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b1a658b428d13cdd12c16b767430f87a80e89fdc
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418363"
---
# <a name="deploy-the-local-agent"></a>部署本地代理

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何使用验证即服务 (VaaS) 本地代理来运行验证测试。 运行验证测试之前, 必须先部署本地代理。

> [!Note]  
> 确保运行本地代理的计算机不会失去对 internet 的出站访问权限。 只有有权代表租户使用 VaaS 的用户才可以访问此计算机。

部署本地代理:

1. 安装本地代理。
2. 执行完整性检查。
3. 运行本地代理。

## <a name="download-and-start-the-local-agent"></a>下载并启动本地代理

将代理下载到满足你的数据中心先决条件的计算机, 并且有权访问所有 Azure Stack 终结点。 此计算机不应是 Azure Stack 系统的一部分, 也不应托管在 Azure Stack 云中。

### <a name="machine-prerequisites"></a>计算机先决条件

检查计算机是否符合以下条件：

- 能够访问所有 Azure Stack 终结点
- 已安装 .NET 4.6 和 PowerShell 5.0
- 至少 8 GB 的 RAM
- 至少为 8 核处理器
- 至少 200 GB 的磁盘空间
- 能够稳定地连接到 Internet

### <a name="download-and-install-the-agent"></a>下载并安装代理

1. 在将要用来运行测试的计算机的提升权限的提示符窗口中打开 Windows PowerShell。
2. 运行以下命令来下载本地代理：

    ```powershell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. 运行以下命令安装本地代理依赖项：

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Parameters**

    | 参数 | 描述 |
    | --- | --- |
    | aadServiceAdminUser | Azure AD 租户的全局管理员用户。 例如，它可以是 vaasadmin@contoso.onmicrosoft.com。 |
    | aadServiceAdminPassword | 全局管理员用户的密码。 |
    | AadTenantId | 注册到验证即服务的 Azure 帐户的 Azure AD 租户 ID。 |
    | ExternalFqdn | 可以从配置文件获取完全限定的域名。 有关说明, 请参阅[Azure Stack 验证即服务中的工作流常见参数](azure-stack-vaas-parameters.md)。 |
    | 地区 | Azure AD 租户的区域。 |

此命令下载公共映像存储库 (PIR) 映像 (OS VHD) 并将其从 Azure Blob 存储复制到 Azure Stack 存储。

![下载必备组件](media/installingprereqs.png)

> [!Note]
> 如果在下载这些映像时遇到网络速度过慢的问题，请将其分开下载到本地共享并指定参数 **-LocalPackagePath** *FileShareOrLocalPath*。 有关将[验证作为服务故障排除](azure-stack-vaas-troubleshoot.md)[的详细](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)信息, 请查看 PIR 下载中的更多指南。

## <a name="checks-before-starting-the-tests"></a>启动测试前的检查

这些测试运行远程操作。 运行测试的计算机必须具有对 Azure Stack 终结点的访问权限, 否则测试将不起作用。 如果使用的是 VaaS 本地代理, 请使用运行代理的计算机。 您可以通过运行以下检查来检查您的计算机是否可以访问 Azure Stack 终结点:

1. 检查是否可以访问基本 URI。 打开 CMD 提示符或 bash shell, 并运行以下命令, 并将替换`<EXTERNALFQDN>`为您的环境的外部 FQDN:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. 打开 web 浏览器并前往`https://adminportal.<EXTERNALFQDN>` , 以检查是否可以访问 ma 门户。

3. 使用创建测试轮次时提供的 Azure AD 服务管理员用户名和密码值进行登录。

4. 运行**Test-azurestack** PowerShell cmdlet 来检查系统的运行状况, 如[运行 Azure Stack 的验证测试](../operator/azure-stack-diagnostic-test.md)中所述。 在启动测试之前，请解决任何警告和错误。

## <a name="run-the-agent"></a>运行代理

1. 在提升权限的提示符窗口中打开 Windows PowerShell。

2. 运行下面的命令：

    ```powershell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Parameters**  

    | 参数 | 描述 |
    | --- | --- |
    | VaaSUserId | 用于登录到 VaaS 门户的用户 ID (例如, UserName\@Contoso.com) |
    | VaaSTenantId | 注册到验证即服务的 Azure 帐户的 Azure AD 租户 ID。 |

    > [!Note]  
    > 运行代理时，当前工作目录必须是任务引擎主机可执行文件 **Microsoft.VaaSOnPrem.TaskEngineHost.exe** 的位置。

如果没有看到系统报告任何错误，则本地代理已成功。 以下示例文本显示在控制台窗口中。

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![启动的代理](media/startedagent.png)

代理可以通过名称进行唯一标识。 默认情况下，它使用启动时所在计算机的完全限定的域名 (FQDN)。 您必须最小化窗口以避免任何意外选择窗口, 因为更改焦点将暂停所有其他操作。

## <a name="next-steps"></a>后续步骤

- [验证作为服务的验证](azure-stack-vaas-troubleshoot.md)
- [作为服务关键概念的验证](azure-stack-vaas-key-concepts.md)
- [快速入门：使用验证即服务门户计划第一次测试](azure-stack-vaas-schedule-test-pass.md)