---
title: Azure Stack 遥测
description: 了解如何使用 PowerShell 配置 Azure Stack 遥测设置。
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: df742ed6c0a2b082aaddd4498c313474a47c6227
ms.sourcegitcommit: 19e9b6d6ce24d74ff396a5dc48208671aeda432a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80362177"
---
# <a name="azure-stack-telemetry"></a>Azure Stack 遥测

Azure Stack 系统数据或遥测数据会通过连接的用户体验自动上载到 Microsoft。 Microsoft 团队使用从 Azure Stack 遥测收集的数据来改善客户体验。 它还用于安全性、运行状况、质量和性能分析。

作为 Azure Stack 运算符，遥测可为企业部署提供有价值的见解，并为你提供有助于调整 Azure Stack 的将来版本的声音。

> [!NOTE]
> 还可以将 Azure Stack 配置为将使用情况信息转发到 Azure 以进行计费。 对于选择即用即付计费的多节点 Azure Stack 客户，这是必需的。 使用情况报告独立于遥测进行控制，对于选择容量模型或用于 Azure Stack 开发工具包（ASDK）用户的多节点客户来说不是必需的。 对于这些情况，可以[使用注册脚本](../operator/azure-stack-usage-reporting.md)关闭使用情况报告。

Azure Stack 遥测基于*Windows Server 2016 连接的用户体验和 Telemetr*y 组件，后者使用[windows 事件跟踪（ETW）](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx)跟踪日志记录技术来收集和存储遥测事件和数据。 Azure Stack 组件使用相同的日志记录技术来发布使用公共操作系统事件日志记录和跟踪 Api 收集的事件和数据。 Azure Stack 组件的示例包括网络资源提供程序、存储资源提供程序、监视资源提供程序和更新资源提供程序。 连接的用户体验和遥测组件使用 SSL 来加密数据，并使用证书固定通过 HTTPS 将遥测数据传输到 Microsoft 数据管理服务。

> [!NOTE]
> 若要支持遥测数据流，必须在网络中打开端口443（HTTPS）。 连接的用户体验和遥测组件连接到 `https://v10.vortex-win.data.microsoft.com` 的 Microsoft 数据管理服务，并 `https://settings-win.data.microsoft.com` 下载配置信息。

## <a name="privacy-considerations"></a>隐私注意事项
ETW 服务将发送遥测数据的路由返回到受保护的云存储。 最小特权指南访问遥测数据的原则。 只有具有有效业务需求的 Microsoft 人员才能访问遥测数据。 Microsoft 不会与第三方共享客户的个人数据，除非客户自行决定或[Azure Stack 隐私声明](https://privacy.microsoft.com/PrivacyStatement)中所述的有限目的。 我们与 Oem 和合作伙伴共享业务报告，其中包括聚合的匿名遥测信息。 数据共享决定由 Microsoft 内部团队组成，其中包括隐私、法律和数据管理利益干系人。

Microsoft 相信和实践信息的最小化。 我们努力只收集我们所需的信息，只需提供服务或进行分析即可存储此信息。 在六个月内删除有关 Azure Stack 系统和 Azure 服务的运行方式的大部分信息。 汇总或聚合的数据保留更长时间。

我们了解客户信息的隐私和安全非常重要。 我们为客户隐私和客户数据提供了一种精心的综合方法，Azure Stack。 IT 管理员可随时使用控件自定义功能和隐私设置。 我们对透明性和信任的承诺非常明确：
- 我们与客户一起介绍我们收集的数据类型。
- 我们将企业客户投入控制，他们可以自定义自己的隐私设置。
- 首先，我们将客户的隐私和安全性放在一起。
- 我们对遥测的使用方式是透明的。
- 我们使用遥测来改善客户体验。

Microsoft 不打算收集敏感信息，如信用卡号、用户名和密码、电子邮件地址。 如果我们确定敏感信息已被意外接收，则将其删除。

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Microsoft 如何使用遥测数据的示例
遥测在帮助我们快速识别和修复客户部署和配置中的严重可靠性问题方面扮演着重要的角色。 深入了解我们收集的遥测数据可帮助我们快速识别服务或硬件配置的问题。 Microsoft 能否从客户那里获取此数据并推动对生态系统的改进，有助于提高集成 Azure Stack 解决方案的质量。

遥测还可以帮助 Microsoft 更好地了解客户如何部署组件、如何使用功能以及如何使用服务来实现其业务目标。 从数据中获取见解有助于在直接影响客户体验和工作负荷的领域中确定工程投资的优先级。

一些示例包括与 Azure Stack 角色关联的容器、存储和网络配置的客户使用情况。 我们还使用此见解来推动我们的一些管理和监视解决方案中的改进和情报。 此改进可帮助客户诊断质量问题，通过减少对 Microsoft 的支持呼叫来节省资金。

## <a name="manage-telemetry-collection"></a>管理遥测集合
不建议在组织中关闭遥测，因为遥测提供的数据可提高产品功能和稳定性。 但我们确实认识到，在某些情况下，这可能是必需的。

在这些情况下，你可以使用注册表设置预部署或在部署后使用遥测终结点来配置发送到 Microsoft 的遥测级别。

### <a name="set-telemetry-level-in-the-windows-registry"></a>在 Windows 注册表中设置遥测级别
Windows 注册表编辑器用于在部署 Azure Stack 之前，在物理主机计算机上手动设置遥测级别。 如果管理策略已存在，如组策略，则会覆盖此注册表设置。

在 ASDK 主机上部署 Azure Stack 之前，启动到 Cloudbuilder.vhdx 并在提升的 PowerShell 窗口中运行以下脚本：

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

遥测级别是累计的，分为四个级别（0-3）：

**0 （安全）** ：仅安全数据。 帮助保护操作系统安全所需的信息，包括有关连接的用户体验和遥测组件设置和 Windows Defender 的数据。 不会在此级别发出 Azure Stack 特定遥测数据。

**1 （基本）** ：安全数据和基本运行状况和质量数据。 基本设备信息，包括：与质量相关的数据、应用程序兼容性、应用使用情况数据以及来自安全级别的数据。 将遥测级别设置为 "基本" 可启用 Azure Stack 遥测。 在此级别收集的数据包括：

- **基本设备信息**，帮助你了解生态系统中本机和虚拟化 Windows Server 2016 实例的类型和配置，包括：
  - 计算机属性，例如 OEM 和型号。
  - 网络属性，如网络适配器的数量和速度。
  - 处理器和内存属性，例如内核数和内存大小。
  - 存储属性，如驱动器数量、类型和大小。
- **遥测功能**，包括上传事件百分比、已删除事件和上次上传时间。
- **与质量相关的信息**，可帮助 Microsoft 大致了解 Azure Stack 的执行方式。 例如，针对特定硬件配置的严重警报计数。
- **兼容性数据**，帮助了解系统和 VM 上安装了哪些资源提供程序，并识别潜在的兼容性问题。

**2 （增强）** ：其他见解，包括如何使用操作系统和其他 Azure Stack 服务、它们的执行方式、高级可靠性数据以及来自基本级别和安全级别的数据。

**3 （完整）** ：确定和帮助解决问题所需的所有数据，以及安全、基本和增强级别的数据。

> [!NOTE]
> 默认遥测级别值为2（增强）。

关闭 Windows 和 Azure Stack 遥测会禁用 SQL 遥测。 有关 Windows Server 遥测设置的含义的其他信息，请参阅[Windows 遥测白皮书](https://aka.ms/winservtelemetry)。

> [!IMPORTANT]
> 这些遥测级别仅适用于 Microsoft Azure Stack 组件。 在硬件生命周期主机中从 Azure Stack 硬件合作伙伴运行的非 Microsoft 软件组件和服务可与其在这些遥测级别以外的云服务进行通信。 你应该与 Azure Stack 硬件解决方案提供商联系，了解其遥测策略，以及如何选择加入或退出。

### <a name="enable-or-disable-telemetry-after-deployment"></a>部署后启用或禁用遥测

若要在部署后启用或禁用遥测，你需要有权访问在 ERCS Vm 上公开的特权终结点（PEP）。
1.  若要启用： `Set-Telemetry -Enable`
2.  若要禁用： `Set-Telemetry -Disable`

参数详细信息：
> .参数 Enable-启用遥测数据上传
> 
> .参数禁用-关闭遥测数据上传  

**启用遥测的脚本：**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**用于禁用遥测的脚本：**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>后续步骤
[启动和停止 ASDK](asdk-start-stop.md)
