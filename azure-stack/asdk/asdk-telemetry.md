---
title: Azure Stack 遥测 | Microsoft Docs
description: 介绍如何使用 PowerShell 配置 Azure Stack 遥测设置。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 24948fb53ed4c5bdbbe0490f581daff7c66c33bc
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267431"
---
# <a name="azure-stack-telemetry"></a>Azure Stack 遥测

Azure Stack 系统数据或遥测数据通过互连用户体验自动上传到 Microsoft。 Microsoft 团队主要使用从 Azure Stack 遥测收集的数据来改善客户体验，以及进行安全、运行状况、质量和性能方面的分析。

遥测可为 Azure Stack 操作员提供宝贵的见解来让他们洞察企业部署，并提供有助于构思 Azure Stack 新版本的看法。

> [!NOTE]
> Azure Stack 还可以配置为将用量信息转发到 Azure 进行计费。 选择即用即付计费模式的多节点 Azure Stack 客户一定要这样做。 用量报告通过遥测单独进行控制，选择容量模式的多节点 Azure Stack 客户或 Azure Stack 开发工具包用户无需使用此功能。 对于上述方案，可以[使用注册脚本](../operator/azure-stack-usage-reporting.md)来关闭用量报告。

Azure Stack 遥测基于 Windows Server 2016 互连用户体验与遥测组件，该组件使用 [Windows 事件跟踪 (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) 跟踪日志记录技术来收集和存储遥测事件与数据。 Azure Stack 组件使用相同的日志记录技术，发布使用公共操作系统事件日志记录和跟踪 API 收集的事件与数据。 Azure Stack 组件的示例包括网络资源提供程序、存储资源提供程序、监视资源提供程序和更新资源提供程序。 互连用户体验与遥测组件使用 SSL 加密数据，并使用证书关联通过 HTTPS 将遥测数据传输到 Microsoft 数据管理服务。

> [!NOTE]
> 若要支持遥测数据流，必须在网络中开放端口 443 (HTTPS)。 互连用户体验与遥测组件连接到 Microsoft 数据管理服务（位于 https://v10.vortex-win.data.microsoft.com ）。 互连用户体验与遥测组件还连接到 https://settings-win.data.microsoft.com 来下载配置信息。

## <a name="privacy-considerations"></a>隐私注意事项
ETW 服务将遥测数据发回到受保护的云存储。 最小特权原则支配遥测数据的访问。 只有具有有效业务需求的 Microsoft 人员才能访问遥测数据。 Microsoft 不与第三方我们客户的个人数据除了在客户自行要求或共享中所述的受限目的[Azure Stack 隐私声明](https://privacy.microsoft.com/PrivacyStatement)。 我们与 OEM 和合作伙伴共享业务报告，其中包含匿名的聚合遥测信息。 数据共享决策由 Microsoft 内部团队（包括隐私、法律和数据管理利益干系人）做出。

Microsoft 相信并实践信息最小化。 我们致力于以收集仅和的信息，我们需要我们将其存储仅时间期限，需要提供服务或进行分析。 许多有关 Azure Stack 系统和 Azure 服务工作原则的信息在六个月内删除。 汇总或聚合的数据保留更长一段时间。

我们了解到，隐私和安全性的客户信息很重要。 我们采用深思熟虑的综合方法，通过 Azure Stack 保护客户隐私和客户数据。 IT 管理员随时可以控制功能和隐私的自定义设置。 我们对于透明度和信任的承诺很明确：
- 我们向客户公开我们收集的数据类型。
- 企业客户有控制权 — 他们可以自定义自己的隐私设置。
- 我们将客户隐私和安全放在第一位。
- 我们以公开透明的方式使用遥测。
- 我们使用遥测来改善客户体验。

Microsoft 无意收集敏感信息，例如信用卡号、用户名和密码、电子邮件地址或其他同样敏感的信息。 如果我们确定敏感信息是无意中收集到的，我们会予以删除。

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Microsoft 如何使用遥测数据的示例
遥测起着重要作用，帮助我们快速找到并解决客户的部署和配置的严重可靠性问题。 洞察我们收集的遥测数据可帮助我们快速找到服务或硬件配置的问题。 获取此数据的客户和生态系统驱动器改进 Microsoft 的功能，有助于提高我们集成的 Azure Stack 解决方案的质量。

遥测还能帮助 Microsoft 进一步了解客户如何部署组件、使用功能以及使用服务来实现业务目标。 从数据获取见解可帮助确定优先级可以直接影响客户的体验和工作负荷的领域中的工程投资。

示例包括：与 Azure Stack 角色关联的容器、存储用量和网络配置的客户用量。 我们还使用见解来推动某些管理和监视解决方案的改善与智能化。 这可以帮助客户诊断质量问题，减少向 Microsoft 拨打支持电话的次数，从而节省资金。

## <a name="manage-telemetry-collection"></a>管理遥测数据的收集
我们不建议在组织中关闭遥测，因为遥测可以提供推动产品功能和稳定性改进的数据。 但我们认识到，在某些情况下有必要关闭遥测。

在这种情况下，可以使用部署前的注册表设置或使用部署后的遥测终结点，配置发送给 Microsoft 的遥测级别。

### <a name="set-telemetry-level-in-the-windows-registry"></a>在 Windows 注册表中设置遥测级别
在部署 Azure Stack 之前，可以使用 Windows 注册表编辑器在物理主计算机上手动设置遥测级别。 如果管理策略（例如组策略）已存在，它会覆盖此注册表设置。

在开发工具包主机上部署 Azure Stack 之前，请先引导至 CloudBuilder.vhdx，然后在权限提升的 PowerShell 窗口中运行以下脚本：

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

遥测级别可累积，分类为四个级别 (0-3)：

**0（安全）** 。 仅限安全数据。 安全帮助保护操作系统所需的信息，包括有关互连用户体验与遥测组件设置和 Windows Defender 的数据。 在此级别不会发出任何 Azure Stack 特定的遥测数据。

**1（基本）** 。 安全数据，以及基本运行状况和质量数据。 基本设备信息，包括：质量相关的数据、应用兼容性、应用用量数据，以及来自安全级别的数据。 将遥测级别设置为“基本”可启用 Azure Stack 遥测。 在此级别收集的数据包括：

- **基本设备信息**，帮助了解生态系统中本机和虚拟化 Windows Server 2016 实例的类型与配置，其中包括：
  - 计算机属性，例如 OEM、型号。
  - 网络属性，例如网络适配器的数目和速度。
  - 处理器和内存属性，例如核心数、内存大小。
  - 存储属性，例如驱动器数目、类型和大小。
- **遥测功能**，包括已上传事件、已删除事件的百分比，以及上次上传时间。
- **质量相关的信息**，帮助 Microsoft 初步了解 Azure Stack 的运行情况。 示例是针对特定硬件配置发出的严重警报计数。
- **兼容性数据**，帮助了解系统和虚拟机上已安装哪些资源提供程序，以及识别潜在的兼容性问题。

**2（增强）** 。 其他见解，包括：操作系统和其他 Azure Stack 服务的用法、工作原理、高级可靠性数据，以及来自“基本”和“安全”级别的数据。

**3（完整）** 。 识别及帮助解决问题所需的全部数据，加上来自“安全”、“基本”和“增强”级别的数据。   

> [!NOTE]
> 默认遥测级别值为 2（增强）。

关闭 Windows 和 Azure Stack 遥测会禁用 SQL 遥测。 有关 Windows Server 遥测设置的含义的详细信息，请参阅 [Windows 遥测白皮书](https://aka.ms/winservtelemetry)。

> [!IMPORTANT]
> 这些遥测级别仅适用于 Microsoft Azure Stack 组件。 Azure Stack 硬件合作伙伴在硬件生命周期主机中运行的非 Microsoft 软件组件和服务可能与这些遥测级别以外的云服务通信。 应该咨询 Azure Stack 硬件解决方案提供商，以了解其遥测策略，以及如何启用或禁用。

### <a name="enable-or-disable-telemetry-after-deployment"></a>在部署后启用或禁用遥测

若要在部署之后启用或禁用遥测，必须能够访问 ERCS VM 上公开的特权终结点 (PEP)。
1.  若要启用：`Set-Telemetry -Enable`
2.  若要禁用：`Set-Telemetry -Disable`

PARAMETER 详细信息：
> .PARAMETER Enable - 启用遥测数据上传
> 
> .PARAMETER Disable - 禁用遥测数据上传  

**用于启用遥测的脚本：**
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
