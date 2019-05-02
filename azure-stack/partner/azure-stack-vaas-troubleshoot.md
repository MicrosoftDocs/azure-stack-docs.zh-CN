---
title: 对 Azure Stack 验证作为服务进行故障排除 |Microsoft Docs
description: 对验证作为 Azure Stack 的服务进行故障排除。
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
ROBOTS: NOINDEX
ms.openlocfilehash: fedfd7f83a35398586734fa647751e537b850bf8
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297817"
---
# <a name="troubleshoot-validation-as-a-service"></a>对验证作为服务进行故障排除

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

下面是与各个软件版本相关的常见问题及其解决方案。

## <a name="local-agent"></a>本地代理

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>门户在调试模式下显示本地代理

这可能是因为网络连接不稳定，导致代理无法向服务发送检测信号。 检测信号每五分钟发送一次。 如果服务 15 分钟内没有收到检测信号，则服务会认为代理处于非活动状态，并且将不再在其上安排测试。 请检查从中启动代理的目录中的 *Agenthost.log* 文件中的错误消息。

> [!Note]
> 已在该代理上运行的任何测试都将继续运行，但是，如果检测信号在测试结束之前没有恢复，则代理将无法更新测试状态，也无法上传日志。 测试将始终显示为**正在运行**并且需要取消。

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>在执行测试时，计算机上的代理进程已关闭。 要发生的情况

如果代理进程是非正常关闭的，例如计算机重启、进程被终止（在代理窗口上按 CTRL+C 被视为正常关闭），则之前正在其上运行的测试将继续显示为**正在运行**。 如果代理重启，则代理会将测试状态更新为**已取消**。 如果不重新启动代理，则该测试显示为**运行**并且您必须手动取消测试。

> [!Note]
> 工作流中的测试安排为按顺序运行。 在同一工作流中处于**正在运行**状态的测试完成之前，**挂起的**测试不会执行。

## <a name="vm-images"></a>VM 映像

### <a name="handle-slow-network-connectivity"></a>处理慢速网络连接

可以将 PIR 映像下载到本地数据中心内的某个共享。 然后可以检查该映像。

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>在网络流量慢速的情况下将 PIR 映像下载到本地共享

1. 下载 AzCopy：[vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. 解压缩 AzCopy.zip 并切换到包含 AzCopy.exe 的目录

3. 从提升的提示符下打开 Windows PowerShell。 运行以下命令：

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare 是共享路径或本地路径。

#### <a name="verifying-pir-image-file-hash-value"></a>验证 PIR 映像文件哈希值

可以使用 **Get-HashFile** cmdlet 获取所下载的公用映像存储库映像文件的哈希值来检查映像的完整性。

| 文件名 | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>上传 VM 映像时失败`VaaSPreReq`脚本

在环境处于正常状态的第一次检查：

1. 此 DVM 从 / 跳转盒，请检查您是否可以成功登录到管理门户中使用的管理员凭据。
1. 确认没有任何警报或警告。

如果在环境运行正常，手动上传 VaaS 测试运行所需的 5 个 VM 图像：

1. 登录到管理员门户服务管理员。 您可以找到在管理门户从 ECE 应用商店或戳信息文件的 URL。 有关说明，请参阅[环境的参数](azure-stack-vaas-parameters.md#environment-parameters)。
1. 选择**更多服务** > **资源提供程序** > **计算** > **VM 映像**。
1. 选择 **+ 添加**顶部的按钮**VM 映像**边栏选项卡。
1. 修改或检查的第一个 VM 映像的以下字段的值：
    > [!IMPORTANT]
    > 并非所有默认值为现有的应用商店项正确。

    | 字段  | 值  |
    |---------|---------|
    | 发布者 | MicrosoftWindowsServer |
    | 产品/服务 | WindowsServer |
    | OS 类型 | Windows |
    | SKU | 2012-R2-Datacenter |
    | Version | 1.0.0 |
    | OS 磁盘 Blob URI | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. 选择“创建”按钮。
1. 重复的剩余 VM 映像。

5 的所有 VM 映像的属性如下所示：

| 发布者  | 产品/服务  | OS 类型 | SKU | Version | OS 磁盘 Blob URI |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>后续步骤

- 审阅[作为服务的验证的发行说明](azure-stack-vaas-release-notes.md)中最新版本的更改。