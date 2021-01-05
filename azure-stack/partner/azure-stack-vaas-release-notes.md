---
title: 验证即服务发行说明
titleSuffix: Azure Stack Hub
description: Azure Stack 中心验证即服务的发行说明。
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 82e7820b49578139bbfb140e98fd740e6c1be8fb
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868193"
---
# <a name="release-notes-for-validation-as-a-service"></a>验证即服务的发行说明

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

本文提供 Azure Stack 中心验证作为服务的发行说明。

## <a name="version-4432"></a>版本4.4.3。2

2020 2 月2日

- 服务维护更新。

- Bug 修复
  - 修复了 Test101LinuxEmptyAttachedDiskManagedDisk、Test101WindowsEmptyAttachedDiskManagedDisk 的测试。

## <a name="version-4442"></a>版本4.4.4。2

2020 11 月11日

- CSE 验证工作流现已更新，可以安装测试签名 OEM 扩展包，并自动发布完整的 Test-azurestack 更新。
  - 在此修补之前，VaaS 无法在 Test-azurestack post 更新后安装 Test-Signed OEM 扩展包。 VaaS 将应用 Test-azurestack 更新，然后退出运行。
  - 此项已修复，你应该会看到 CSE 验证工作流正在安装提供的 Test-azurestack 更新和测试签名 OEM 扩展包。
- 已将 OEM 包验证扩展添加到 "OEM 验证工作流"
  - 此扩展将在开始例行标记上的任何更新之前运行。
  - 该扩展将验证 OEM 扩展包的内容和的元素 oemMetadata.xml
  - 如果 OEM 扩展包存在任何错误/问题，则在 VaaS 测试开始之前，我们会将其捕获。
  - 这些验证在对包进行签名时运行，请在发布 VaaS 测试运行后运行。  
- VaaS 预请求更新，用于安装较新版本的 Test-azurestack 和 AzureRM PowerShell 模块
  - Test-azurestack PS 模块版本1.8。2
  - AzureRM PS 模块版本2.5。0
- 次要服务更新。

## <a name="version-443112"></a>版本4.4.3.112

2020年8月23日

- 服务更新。
  - 服务部署更新。
  - 更新了服务身份验证方法。

## <a name="version-44368"></a>版本4.4.3.68

2020 6 月30日

- 服务更新。
  - 已移动服务以在 Service Fabric 中运行。

## <a name="version-4421"></a>版本4.4.2。1

2020年1月9日

- 测试内容更新：
  - OEM 验证工作流 (版本 5.1.52.0-> 5.1.53.0) ：减少 "测试计划" 窗格中所需参数的数目。
  - 用于计算测试的 bug 修复-TestVMOperations

- 已知问题：
  - vaashelp@microsoft.com如果下面的测试用例无法在 OEM 验证工作流过程中运行，请联系：
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

2019 12 月3日

- 测试内容更新：
  - 已更新每月 Azure Stack 中心更新工作流和 OEM 包验证工作流的联机文档。 查看更新的文档： [验证 OEM 包](azure-stack-vaas-validate-oem-package.md) 并验证 Microsoft 的 [软件更新](azure-stack-vaas-validate-microsoft-updates.md) 。
  - VaaS 包验证工作流更新： OEM 验证工作流是每月 Azure Stack 中心更新验证和 OEM 包验证所必需的唯一测试。 该测试将用提供的 Test-azurestack/OEM 包更新 stamp，并运行云模拟引擎验证测试。
  - VaaS PowerShell 扩展更新：现在支持包验证工作流自动化。 有关使用此扩展的位置和分步说明的详细信息，请参阅 Azure Stack 集线器 VaaS 自动使用 PowerShell。

- 已知问题：
  - vaashelp@microsoft.com如果下面的测试用例无法在 OEM 验证工作流过程中运行，请联系：
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

## <a name="version-4353"></a>版本4.3.5。3

2019年11月7日

- 测试内容更新：
  - 每月 Azure Stack 中心更新验证 (版本 5.1.46.0-> 5.1.49.0) 。
  - OEM 扩展包验证 (版本 5.1.46.0-> 5.1.49.0) 。
  - 已保留5.1.46.0 的结果。 如果已成功在5.1.46.0 上运行，请在 vaashelp@microsoft.com 提交结果时通知。

- Bug 修复
  - 修复了更新 .zip 包含特殊字符的情况下，每月 Azure Stack 集线器更新验证失败的问题。

- 已知问题
  - 如果找不到 mstest.exe，VaaS 测试将失败。 解决方法：
    1. 在 PowerShell 窗口中，按 CTRL + C。
    1. 键入 mstest.exe 验证 mstest.exe 是否为已识别的程序。
    1. 如果无法识别 mstest.exe，请关闭当前 PowerShell 窗口。
    1. 单击任务栏上的 "启动 (不是 PowerShell") ，查找 "PowerShell"，然后以管理员身份打开。
    1. 键入 mstest.exe 并验证它是否可用作命令。
    1. 重新启动代理并重新运行测试。
  - 有时，云模拟引擎会报告 \* vm 测试失败。 请 vaashelp@microsoft.com 在尝试重新运行前联系。


2019年10月29日

- 已更新每月 Azure Stack 中心更新工作流和 OEM 包验证工作流的联机文档。

    查看更新的文档： [验证 OEM 包](azure-stack-vaas-validate-oem-package.md) 并验证 Microsoft 的 [软件更新](azure-stack-vaas-validate-microsoft-updates.md) 。
- VaaS 工作流更新：每月 Azure Stack 中心更新 (版本 5.1.30.0-> 5.1.46.0) –每月 Azure Stack 中心更新验证测试工作流都已更新。

    工作流不再需要手动干预，并且可以计划无缝运行。
- VaaS 工作流更新： OEM 包验证 (版本 5.1.30.0-> 5.1.46.0) – OEM 包验证工作流已更新。

    工作流不再需要手动干预，并且可以计划无缝运行。
- OEM 包验证工作流中的云模拟引擎 (版本 5.1.30.0-> 5.1.46.0) 已更新，以加快验证时间：运行时间缩短到1小时。
- OEM 包验证工作流中的云模拟引擎和 Azure Stack 中心更新工作流 (版本 5.1.30.0-> 5.1.46.0) 要求在两个不同的父文件夹中验证更新，子文件夹中没有其他更新。
- OEM 包验证工作流中的云模拟引擎和 Azure Stack 中心更新工作流 (版本 5.1.30.0-> 5.1.46.0) 要求按以下顺序计划测试–每月 Azure Stack 中心更新验证测试、OEM 扩展包验证测试，以及最终的云模拟引擎。
- VaaS 代理更新：更新的 VaaS 代理现在使用 Azure Stack 中心云管理员凭据来查询 stamp，以便自动填充数据工作流。

    此更新需要更新并重新启动所有代理。 有关如何更新 VaaS 代理的说明，请参阅以下说明： https://docs.microsoft.com/azure-stack/partner/azure-stack-vaas-local-agent
- VaaS 门户 UI 更新：代理选择表已移到测试计划窗格上方，以方便进行测试。

    计划作业时，如果 VaaS 代理已正确更新，则不再需要输入 stamp 信息。

## <a name="version-405"></a>版本4.0。5

2019年6月7日

- 已更新包验证工作流中的云模拟引擎，以加快验证时间：  
    运行时间：缩短到6小时  
    版本： 5.1.13.0-> 5.1.22。0  


2019年1月17日

- 磁盘标识测试已更新，以解决存储池不一致问题。 版本： 5.1.14.0-> 5.1.15。0
- Azure Stack 集线器每月更新验证已更新，以解决批准的软件和内容验证不一致问题。 版本： 5.1.14.0-> 5.1.17。0
- 在 Azure Stack 集线器更新步骤之前，已更新 OEM 扩展包验证以执行必要的检查。 版本： 5.1.14.0-> 5.1.16。0
- 内部 bug 修复。

## <a name="version-403"></a>版本4.0。3

2019年1月7日

如果你运行的是 Azure Stack 集线器每月更新验证工作流，并且你的 OEM 更新包的版本不是1810或更高版本，则在你进入 OEM 更新步骤之后，你将收到一条错误消息。 此错误是一个 bug。 正在开发修补程序。 缓解步骤如下所示：

1. 正常运行 OEM 更新。
2. 成功应用包后执行 Test-AzureStack，并保存输出。
3. 取消测试。
4. 将保存的输出发送到 VaaSHelp@microsoft.com ，以接收该运行的传递结果。

## <a name="version-402"></a>版本4.0。2

2018年11月30日

- 内部 bug 修复。

## <a name="version-401"></a>版本 4.0.1

2018年10月8日

- VaaS 先决条件：

    `Install-VaaSPrerequisites` 不再需要云管理员凭据。 如果运行的是最新版本的 cmdlet，请参阅 [下载并安装本地代理](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) 以获取用于安装必备组件的已修改命令。 命令如下：

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

- VaaS 先决条件和 VHD 更新：

    `Install-VaaSPrerequisites` 现在需要云管理员凭据才能解决在包验证期间出现的问题。 [下载并安装本地代理](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent)中的文档已更新为以下代码：

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
    > `$CloudAdminCreds`脚本所需的脚本适用于正在验证的 Azure Stack 中心实例。 它们不是 VaaS 租户使用的 Azure Active Directory 凭据。

- 本地代理更新：

    以前版本的本地代理与服务的当前4.0.0 版本不兼容。 所有用户必须更新其本地代理。 有关安装最新代理的说明，请参阅 [下载并安装本地代理](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) 。

- PowerShell 自动化更新：

    对 `LaunchVaaSTests` 需要最新版本的脚本包的 PowerShell 脚本进行了更改。 有关安装最新版本的脚本包的说明，请参阅 [启动测试通过工作流](azure-stack-vaas-automate-with-powershell.md) 。

- 作为服务门户的验证：

  - 包签名通知

    在将 OEM 自定义包作为包验证工作流的一部分提交时，将验证包格式，以确保其遵循发布的规范。 如果包不符合，则运行将失败。 电子邮件通知将发送到租户的已注册 Azure Active Directory 联系的电子邮件地址。

  - 交互式测试类别：

    已添加 **交互式** 测试类别。 这些测试试验交互式的非自动 Azure Stack 中心方案。

  - 交互式功能验证：

    现可在测试通过工作流中提供针对某些功能的集中式反馈。 `OEM Update on Azure Stack Hub 1806 RC Validation 5.1.4.0`测试将检查是否已正确应用了特定的更新，然后再收集反馈。

## <a name="next-steps"></a>后续步骤

- [验证作为服务的验证](azure-stack-vaas-troubleshoot.md)
