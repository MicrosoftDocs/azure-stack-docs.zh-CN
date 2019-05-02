---
title: 作为一项服务的 azure Stack 验证发行说明 |Microsoft Docs
description: 作为一项服务的 azure Stack 验证发行说明。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: eefd39c751bdbd9ed9c8f3b9112fee1ddbffb9a0
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297927"
---
# <a name="release-notes-for-validation-as-a-service"></a>作为一项服务验证的发行说明

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

本文包含有关作为服务进行 Azure Stack 验证发行说明。

## <a name="version-405"></a>版本 4.0.5

2019 年 1 月 17日

- 更新地址存储池不一致的磁盘标识测试。 版本:5.1.14.0 -> 5.1.15.0
- Azure Stack 每月更新验证更新地址为已批准的软件和内容验证不一致。 版本:5.1.14.0 -> 5.1.17.0
- OEM 扩展包验证更新，以执行在 Azure Stack 更新步骤之前必要的检查。 版本:5.1.14.0 -> 5.1.16.0
- 内部的 bug 修复

## <a name="version-402"></a>版本 4.0.2

2019 年 1 月 7日

如果运行 Azure Stack 每月更新验证工作流和 OEM 更新包的版本不是 1810年或更高版本，一旦到达 OEM 更新步骤您将收到错误。 这是一个 bug。 正在开发了修复程序。缓解步骤如下所示：

1. OEM 更新作为正常运行。
2. 在包的成功的应用程序后执行 Test-azurestack 并保存输出。
3. 取消测试。
4. 发送到已保存的输出VaaSHelp@microsoft.com接收通过测试运行结果。

## <a name="version-402"></a>版本 4.0.2

2018 年 11 月 30日

- 内部的 bug 修复

## <a name="version-401"></a>版本 4.0.1

2018 年 10 月 8日

- VaaS 先决条件

    `Install-VaaSPrerequisites` 不再需要云管理员凭据。 如果运行此 cmdlet 的最新版本，请参阅[下载并安装代理](azure-stack-vaas-local-agent.md#download-and-install-the-agent)安装系统必备组件的修订后的命令。 命令如下：

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>版本 4.0.0

2018 年 8 月 29日

- 更新 VaaS 先决条件和 VHD

    `Install-VaaSPrerequisites` 现在需要云管理员凭据来在包验证过程中解决问题。 在文档[下载并安装代理](azure-stack-vaas-local-agent.md#download-and-install-the-agent)已使用以下更新：

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > `$CloudAdminCreds`所需的脚本适用于 Azure Stack 实例要验证。 它们不是由 VaaS 租户的 Azure Active Directory 凭据。

- 本地代理更新

    本地代理的上一版本不是与当前 4.0.0 兼容版本的服务。 所有用户必须都更新其本地代理。 请参阅[下载并安装代理](azure-stack-vaas-local-agent.md#download-and-install-the-agent)有关安装最新代理的说明。

- PowerShell 自动化更新

    对进行了更改`LaunchVaaSTests`需要脚本包的最新版本的 PowerShell 脚本。 请参阅[启动测试通过工作流](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow)有关安装脚本的包的最新版本的说明。

- 验证与服务门户

  - 包签名通知

    OEM 自定义包作为包验证工作流的一部分提交时，将验证的包格式，以确保它遵循已发布的规范。 如果包不符合，则运行将失败。 电子邮件通知将发送到租户的已注册 Azure Active Directory 联系人的电子邮件地址。

  - 交互式测试类别

    **交互式**添加测试类别。 这些测试执行交互式的、 非自动完成 Azure Stack 方案。

  - 交互式功能验证

    能够提供有针对性的反馈对于某些功能现已推出测试通过工作流。 `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0`测试进行检查以查看是否已正确应用特定更新，然后收集反馈。

## <a name="next-steps"></a>后续步骤

- [对验证作为服务进行故障排除](azure-stack-vaas-troubleshoot.md)
