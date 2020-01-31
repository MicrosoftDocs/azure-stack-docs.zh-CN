---
title: 作为服务发行说明 Azure Stack 验证
description: Azure Stack 验证作为服务发行说明。
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 63602a3bd4f53eada398dc6959a59202e0614708
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884038"
---
# <a name="release-notes-for-validation-as-a-service"></a>验证即服务的发行说明

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

本文提供 Azure Stack 验证作为服务的发行说明。

## <a name="version-4421"></a>版本4.4.2。1

2020年1月9日

- 测试内容更新
  - OEM 验证工作流（版本 5.1.52.0-> 5.1.53.0）：减少了 "测试计划" 窗格中所需参数的数目。 
  - 用于计算测试的 bug 修复-TestVMOperations
    
- 已知问题
  - 如果下面的测试用例无法在 OEM 验证工作流过程中运行，请联系 vaashelp@microsoft.com：
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

2019 12 月3日

- 测试内容更新
  - 已更新每月 Azure Stack 更新工作流和 OEM 包验证工作流的联机文档。 请查看此处的更新文档验证 OEM 包，并从 Microsoft 验证软件更新
  - VaaS 包验证工作流更新： OEM 验证工作流是每月 Azure Stack 更新验证和 OEM 包验证所需的唯一测试。 该测试将用提供的 Test-azurestack/OEM 包更新 stamp，并运行云模拟引擎验证测试。
  - VaaS PowerShell 扩展更新：现在支持包验证工作流自动化。 有关使用此扩展的位置和分步说明的详细信息，请参阅 Azure Stack VaaS 自动使用 Powershell。

- 已知问题
  - 如果下面的测试用例无法在 OEM 验证工作流过程中运行，请联系 vaashelp@microsoft.com：
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk


## <a name="version-4353"></a>版本4.3.5。3

2019年11月7日

- 测试内容更新
  - 每月 Azure Stack 更新验证（版本 5.1.46.0-> 5.1.49.0）
  - OEM 扩展包验证（版本 5.1.46.0-> 5.1.49.0）
  - 已保留5.1.46.0 的结果。 如果在5.1.46.0 上成功运行，请在提交结果时通知 vaashelp@microsoft.com。

- Bug 修复
  - 修复了在更新 .zip 包含特殊字符的情况下，每月 Azure Stack 更新验证无法运行的问题。

- 已知问题
  - 如果找不到 mstest.exe，VaaS 测试将失败。 解决方法：
    1. 在 PowerShell 窗口中，按 CTRL + C。
    1. 键入 mstest.exe，验证 mstest.exe 是否为已识别的程序。
    1. 如果无法识别 mstest.exe，请关闭当前 PowerShell 窗口。
    1. 单击 "启动" （不是任务栏上的 PowerShell），查找 "PowerShell"，然后以管理员身份打开。
    1. 键入 mstest.exe 并验证它是否可用作命令。
    1. 重新启动代理并重新运行测试。
  - 有时，云模拟引擎会报告 \*vm 测试失败。 尝试重新运行之前，请联系 vaashelp@microsoft.com。 


2019年10月29日

- 已更新每月 Azure Stack 更新工作流和 OEM 包验证工作流的联机文档。

    请查看此处的更新文档验证 OEM 包，并从 Microsoft 验证软件更新
- VaaS 工作流更新：每月 Azure Stack 更新（版本 5.1.30.0-> 5.1.46.0）–每月 Azure Stack 更新验证测试工作流都已更新。

    工作流不再需要手动干预，并且可以计划无缝运行。
- VaaS 工作流更新： OEM 包验证（版本 5.1.30.0-> 5.1.46.0）– OEM 包验证工作流已更新。

    工作流不再需要手动干预，并且可以计划无缝运行。
- OEM 包验证工作流中的云模拟引擎（版本 > 5.1.30.0 5.1.46.0）已更新，以加快验证时间：运行时间缩短到1小时。
- OEM 包验证工作流中的云模拟引擎和 Azure Stack 更新工作流（版本 5.1.30.0-> 5.1.46.0）要求验证的更新位于2个不同的父文件夹中，子文件夹中没有其他更新。
- OEM 包验证工作流中的云模拟引擎和 Azure Stack 更新工作流（版本 5.1.30.0-> 5.1.46.0）要求按以下顺序计划测试–每月 Azure Stack 更新验证测试、OEM 扩展包验证测试，最后是云模拟引擎。
- VaaS 代理更新：更新的 VaaS 代理现在使用 Azure Stack 的云管理员凭据来查询 stamp 以获取 stamp 信息，以便自动填充工作流。 

    此更新需要更新并重新启动所有代理。 请参阅以下说明，了解如何更新 VaaS 代理： https://docs.microsoft.com/azure-stack/partner/azure-stack-vaas-local-agent
- VaaS 门户 UI 更新：代理选择表已移到测试计划窗格上方，以方便进行测试。

    计划作业时，如果 VaaS 代理已正确更新，则不再需要输入 stamp 信息。


## <a name="version-405"></a>版本4.0。5

2019年6月7日

- 已更新包验证工作流中的云模拟引擎，以加快验证时间：  
    运行时间：缩短到6小时  
    版本： 5.1.13.0-> 5.1.22。0  


2019年1月17日

- 磁盘标识测试已更新，以解决存储池不一致问题。 版本： 5.1.14.0-> 5.1.15。0
- Azure Stack 每月更新验证已更新，以解决批准的软件和内容验证不一致问题。 版本： 5.1.14.0-> 5.1.17。0
- OEM 扩展包验证已更新，可在 Azure Stack 更新步骤之前执行必要的检查。 版本： 5.1.14.0-> 5.1.16。0
- 内部 bug 修复

## <a name="version-402"></a>版本4.0。2

2019年1月7日

如果你运行的是 Azure Stack 每月更新验证工作流，并且你的 OEM 更新包的版本不是1810或更高版本，则在你进入 OEM 更新步骤之后，你将收到一条错误消息。 这是一个 bug。 正在开发修补程序。 缓解步骤如下所示：

1. 正常运行 OEM 更新。
2. 在成功应用包后执行 Test-azurestack，并保存输出。
3. 取消测试。
4. 将保存的输出发送到 VaaSHelp@microsoft.com，以接收通过运行的结果。

## <a name="version-402"></a>版本4.0。2

2018年11月30日

- 内部 bug 修复

## <a name="version-401"></a>版本 4.0.1

2018年10月8日

- VaaS 先决条件

    `Install-VaaSPrerequisites` 不再需要云管理员凭据。 如果运行的是最新版本的 cmdlet，请参阅[下载并安装本地代理](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent)以获取用于安装必备组件的已修改命令。 命令如下：

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

    `Install-VaaSPrerequisites` 现在需要云管理员凭据才能解决包验证期间的问题。 [下载并安装本地代理](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent)中的文档已更新为以下内容：

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
    > 脚本所需的 `$CloudAdminCreds` 适用于正在验证的 Azure Stack 实例。 它们不是 VaaS 租户使用的 Azure Active Directory 凭据。

- 本地代理更新

    本地代理的以前版本与该服务的当前4.0.0 版本不兼容。 所有用户必须更新其本地代理。 有关安装最新代理的说明，请参阅[下载并安装本地代理](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent)。

- PowerShell 自动化更新

    已对需要最新版本的脚本包 `LaunchVaaSTests` PowerShell 脚本进行了更改。 有关安装最新版本的脚本包的说明，请参阅[启动测试通过工作流](azure-stack-vaas-automate-with-powershell.md)。

- 作为服务门户的验证

  - 包签名通知

    在将 OEM 自定义包作为包验证工作流的一部分提交时，将验证包格式，以确保其遵循已发布的规范。 如果包不符合，则运行将失败。 电子邮件通知将发送到租户的已注册 Azure Active Directory 联系的电子邮件地址。

  - 交互式测试类别

    已添加**交互式**测试类别。 这些测试试验交互式的非自动 Azure Stack 方案。

  - 交互式功能验证

    现可在测试通过工作流中提供针对某些功能的集中式反馈。 `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` 测试检查是否已正确应用特定更新，然后收集反馈。

## <a name="next-steps"></a>后续步骤

- [验证作为服务的验证](azure-stack-vaas-troubleshoot.md)
