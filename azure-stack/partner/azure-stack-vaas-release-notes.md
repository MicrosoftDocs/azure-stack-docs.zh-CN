---
title: 作为服务发行说明 Azure Stack 验证 |Microsoft Docs
description: Azure Stack 验证作为服务发行说明。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: d42b1f161d1b74633c4e9924e45677a57b3493e1
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277821"
---
# <a name="release-notes-for-validation-as-a-service"></a>验证即服务的发行说明

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

本文提供 Azure Stack 验证作为服务的发行说明。

## <a name="version-405"></a>版本4.0。5

2019年6月7日

- 已更新包验证工作流中的云模拟引擎，以加快验证时间：  
    运行时间：缩短到6小时  
    版本：5.1.13.0 -> 5.1.22.0  


2019年1月17日

- 磁盘标识测试已更新，以解决存储池不一致问题。 版本：5.1.14.0 -> 5.1.15.0
- Azure Stack 每月更新验证已更新，以解决批准的软件和内容验证不一致问题。 版本：5.1.14.0 -> 5.1.17.0
- OEM 扩展包验证已更新，可在 Azure Stack 更新步骤之前执行必要的检查。 版本：5.1.14.0 -> 5.1.16.0
- 内部 bug 修复

## <a name="version-402"></a>版本4.0。2

2019年1月7日

如果你运行的是 Azure Stack 每月更新验证工作流，并且你的 OEM 更新包的版本不是1810或更高版本，则在你进入 OEM 更新步骤之后，你将收到一条错误消息。 这是一个 bug。 正在开发修补程序。 缓解步骤如下所示：

1. 正常运行 OEM 更新。
2. 在成功应用包后执行 Test-azurestack，并保存输出。
3. 取消测试。
4. 将保存的输出发送到 VaaSHelp@microsoft.com 以接收运行的传递结果。

## <a name="version-402"></a>版本4.0。2

2018年11月30日

- 内部 bug 修复

## <a name="version-401"></a>版本 4.0.1

2018年10月8日

- VaaS 先决条件

    `Install-VaaSPrerequisites` 不再需要云管理员凭据。 如果运行的是最新版本的 cmdlet，请参阅[下载并安装](azure-stack-vaas-local-agent.md#download-and-install-the-agent)用于安装必备组件的已修改命令的代理。 命令如下：

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>版本 4.0.0

2018年8月29日

- VaaS 先决条件和 VHD 更新

    `Install-VaaSPrerequisites` 现在需要云管理员凭据才能解决包验证期间的问题。 [下载和安装代理](azure-stack-vaas-local-agent.md#download-and-install-the-agent)中的文档已更新为以下内容：

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
    > 脚本所需的 @no__t 为 Azure Stack 正在验证的实例。 它们不是 VaaS 租户使用的 Azure Active Directory 凭据。

- 本地代理更新

    本地代理的以前版本与该服务的当前4.0.0 版本不兼容。 所有用户必须更新其本地代理。 有关安装最新代理的说明，请参阅[下载并安装代理](azure-stack-vaas-local-agent.md#download-and-install-the-agent)。

- PowerShell 自动化更新

    更改了需要最新版本的脚本包 `LaunchVaaSTests` 个 PowerShell 脚本。 有关安装最新版本的脚本包的说明，请参阅[启动测试通过工作流](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow)。

- 作为服务门户的验证

  - 包签名通知

    在将 OEM 自定义包作为包验证工作流的一部分提交时，将验证包格式，以确保其遵循已发布的规范。 如果包不符合，则运行将失败。 电子邮件通知将发送到租户的已注册 Azure Active Directory 联系的电子邮件地址。

  - 交互式测试类别

    已添加**交互式**测试类别。 这些测试试验交互式的非自动 Azure Stack 方案。

  - 交互式功能验证

    现可在测试通过工作流中提供针对某些功能的集中式反馈。 @No__t-0 测试将检查是否已正确应用特定更新，然后收集反馈。

## <a name="next-steps"></a>后续步骤

- [验证作为服务的验证](azure-stack-vaas-troubleshoot.md)
