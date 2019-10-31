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
ms.openlocfilehash: cc2299f32f02c4a825424309943d3f27d0fab6fb
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167218"
---
# <a name="deploy-the-local-agent"></a>部署本地代理

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何使用验证即服务（VaaS）本地代理来运行验证测试。 运行验证测试之前，必须先部署本地代理。

> [!Note]  
> 确保运行本地代理的计算机不会失去对 internet 的出站访问权限。 只有有权代表租户使用 VaaS 的用户才可以访问此计算机。

部署本地代理：

1. 下载并安装本地代理。
2. 在开始测试之前执行检查。
3. 运行本地代理。

## <a name="download-and-start-the-local-agent"></a>下载并启动本地代理

将代理下载到满足你的数据中心先决条件的计算机，并且有权访问所有 Azure Stack 终结点。 此计算机不应是 Azure Stack 系统的一部分，也不应托管在 Azure Stack 云中。

### <a name="machine-prerequisites"></a>计算机必备组件

检查您的计算机是否满足以下条件：

- 访问所有 Azure Stack 终结点
- 已安装 .NET 4.6 和 PowerShell 5。0
- 至少 8 GB RAM
- 最低8核处理器
- 最小 200-GB 磁盘空间
- 与 internet 的稳定网络连接

### <a name="download-and-install-the-local-agent"></a>下载并安装本地代理

1. 在将用于运行测试的计算机上，在提升的提示符下打开 Windows PowerShell。
2. 运行以下命令以下载并安装本地代理依赖项，并将公用映像存储库（PIR）映像（OS VHD）复制到 Azure Stack 环境。

    ```powershell
    # Review and update the following five parameters
    $RootFolder = "c:\VaaS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $AadServiceAdminUserName = "<AAD service admin user name>"
    $AadServiceAdminPassword = "<AAD service admin password>"

    if (-not(Test-Path($RootFolder))) {
        mkdir $RootFolder
    }
    Set-Location $RootFolder
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "$rootFolder\OnPremAgent.zip"
    Expand-Archive -Path "$rootFolder\OnPremAgent.zip" -DestinationPath "$rootFolder\VaaSOnPremAgent" -Force
    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"

    $cloudAdminCredential = New-Object System.Management.Automation.PSCredential($cloudAdmindUserName, (ConvertTo-SecureString $cloudAdminPassword -AsPlainText -Force))
    $getStampInfoUri = "https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation" 
    $stampInfo = Invoke-RestMethod -Method Get -Uri $getStampInfoUri -Credential $cloudAdminCredential -ErrorAction Stop
    $serviceAdminCreds = New-Object System.Management.Automation.PSCredential $aadServiceAdminUserName, (ConvertTo-SecureString $aadServiceAdminPassword -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $stampInfo.AADTenantID `
                            -ServiceAdminCreds $serviceAdminCreds `
                            -ArmEndpoint $stampInfo.AdminExternalEndpoints.AdminResourceManager `
                            -Region $stampInfo.RegionName
    ```

> [!Note]  
> VaaSPrerequisites cmdlet 下载大型 VM 映像文件。 如果网络速度缓慢，可以将文件下载到本地文件服务器，并手动将 VM 映像添加到测试环境。 有关详细信息，请参阅[处理慢速网络连接](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)。

**参数**

| 参数 | 描述 |
| --- | --- |
| AadServiceAdminUser | Azure AD 租户的全局管理员用户。 例如，可以 vaasadmin@contoso.onmicrosoft.com。 |
| AadServiceAdminPassword | 全局管理员用户的密码。 |
| CloudAdminUserName | 可以访问和运行特权终结点中的允许命令的云管理员用户。 例如，它可能是，AzusreStack\CloudAdmin。 有关详细信息，请参阅 [此处](azure-stack-vaas-parameters.md)。 |
| CloudAdminPassword | 云管理员帐户的密码。|

![下载必备组件](media/installing-prereqs.png)

## <a name="perform-sanity-checks-before-starting-the-tests"></a>在开始测试之前执行完整性检查

这些测试运行远程操作。 运行测试的计算机必须具有对 Azure Stack 终结点的访问权限，否则测试将不起作用。 如果使用的是 VaaS 本地代理，请使用运行代理的计算机。 您可以通过运行以下检查来检查您的计算机是否可以访问 Azure Stack 终结点：

1. 检查是否可以访问基 URI。 打开 CMD 提示符或 bash shell，并运行以下命令，将 `<EXTERNALFQDN>` 替换为您的环境的外部 FQDN：

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. 打开 web 浏览器并中转到 `https://adminportal.<EXTERNALFQDN>`，以检查是否可以访问 MA 门户。

3. 使用创建测试通过时提供的 "Azure AD 服务管理员名称" 和 "密码" 值登录。

4. 运行**Test-azurestack** PowerShell cmdlet 来检查系统的运行状况，如[运行 Azure Stack 的验证测试](../operator/azure-stack-diagnostic-test.md)中所述。 在启动任何测试之前修复所有警告和错误。

## <a name="run-the-local-agent"></a>运行本地代理

1. 在提升的提示符下打开 Windows PowerShell。

2. 运行以下命令：

    ```powershell
   # Review and update the following five parameters
    $RootFolder = "c:\VAAS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $VaaSUserId = "<VaaS user ID>"
    $VaaSTenantId = "<VaaS tenant ID>"

    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u $VaaSUserId -t $VaaSTenantId -x $CloudAdmindUserName -y $CloudAdminPassword
    ```

      **参数**  

    | 参数 | 描述 |
    | --- | --- |
    | CloudAdminUserName | 可以访问和运行特权终结点中的允许命令的云管理员用户。 例如，它可能是，AzusreStack\CloudAdmin。 有关详细信息，请参阅 [此处](azure-stack-vaas-parameters.md)。 |
    | CloudAdminPassword | 云管理员帐户的密码。|
    | VaaSUserId | 用于登录到 VaaS 门户的用户 ID （例如，UserName\@Contoso.com） |
    | VaaSTenantId | 为注册为服务的 Azure 帐户 Azure AD 租户 ID。 |

    > [!Note]  
    > 运行代理时，当前工作目录必须是任务引擎主机可执行文件**VaaSOnPrem**的位置。

如果看不到任何报告的错误，则本地代理已成功。 下面的示例文本显示在控制台窗口中。

`Heartbeat was sent successfully.`

![已启动代理](media/started-agent.png)

代理由其名称唯一标识。 默认情况下，它使用从其启动的计算机的完全限定的域名（FQDN）名称。 您必须最小化窗口以避免任何意外选择窗口，因为更改焦点将暂停所有其他操作。

## <a name="next-steps"></a>后续步骤

- [验证作为服务的验证](azure-stack-vaas-troubleshoot.md)
- [作为服务关键概念的验证](azure-stack-vaas-key-concepts.md)
- [快速入门：使用验证作为服务门户计划第一次测试](azure-stack-vaas-schedule-test-pass.md)