---
title: 将 Azure Stack 验证作为服务进行故障排除
description: 排查 Azure Stack 的服务验证问题。
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1525ed6ce9005fd8a64acefb44c730e2f172bcdc
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884982"
---
# <a name="troubleshoot-validation-as-a-service"></a>验证作为服务的验证

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

下面是与软件版本及其解决方案无关的常见问题。

## <a name="local-agent"></a>本地代理

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>门户在调试模式下显示本地代理

这很可能是因为由于网络连接不稳定，代理无法向服务发送检测信号。 每隔五分钟发送一次检测信号。 如果服务未收到15分钟的检测信号，则服务会将代理视为非活动状态，并且不会再对其计划测试。 检查*Agenthost*文件中的错误消息，该文件位于启动代理的目录中。

> [!Note]
> 已在代理上运行的任何测试将继续运行，但如果检测信号在测试结束之前未还原，则代理将无法更新测试状态或上载日志。 测试将始终显示为 "**正在运行**" 且需要取消。

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>在执行测试时，计算机上的代理进程已关闭。 要发生的情况

如果代理进程已关闭意外（例如，重新启动计算机，则代理窗口上的 CTRL + C 被视为正常关闭），则在该进程上运行的测试将继续显示为**正在运行**。 如果重新启动代理，则代理会将测试状态更新为 "**已取消**"。 如果代理未重启，则测试将显示为 "**正在运行**"，必须手动取消测试。

> [!Note]
> 工作流中的测试计划为按顺序运行。 在同一工作流中**运行**状态的测试完成之前，将不会执行**挂起**的测试。

## <a name="vm-images"></a>VM 映像

### <a name="handle-slow-network-connectivity"></a>处理慢速网络连接

可以将 PIR 映像下载到本地数据中心内的共享中。 然后，就可以检查映像了。

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>在网络流量缓慢的情况下将 PIR 映像下载到本地共享

1. 下载 AzCopy： [vaasexternaldependencies （AzCopy）](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. 提取 AzCopy，并更改为包含 AzCopy 的目录

3. 从权限提升的提示符打开 Windows PowerShell。 运行以下命令：

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'OpenLogic-CentOS-69-20180105.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Debian8_latest.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare 是共享路径或本地路径。

#### <a name="verifying-pir-image-file-hash-value"></a>正在验证 PIR 映像文件哈希值

你可以使用**HashFile** cmdlet 来获取已下载的公共映像存储库图像文件的哈希值，以检查映像的完整性。

| 文件名 | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619。1 | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |
| OpenLogic-CentOS-69-20180105 | C8B874FE042E33B488110D9311AF1A5C7DC3B08E6796610BF18FDD6728C7913C |
| Debian8_latest .vhd | 06F8C11531E195D0C90FC01DFF5DC396BB1DD73A54F8252291ED366CACD996C1 |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>在 `VaaSPreReq` 脚本中上传 VM 映像时出现故障

首先检查环境是否正常：

1. 从 "DVM"/"跳转" 框中检查是否可以使用管理员凭据成功登录到管理门户。
1. 确认没有任何警报或警告。

如果环境正常运行，请手动上传 VaaS 测试运行所需的5个 VM 映像：

1. 以服务管理员身份登录到管理门户。 你可以从 ECE 存储区或你的 stamp 信息文件中找到管理门户 URL。 有关说明，请参阅[环境参数](azure-stack-vaas-parameters.md#environment-parameters)。
1. 选择 "**更多服务**" > **资源提供程序** > **计算** > **VM 映像**。
1. 选择 " **VM 映像**" 边栏选项卡顶部的 " **+ 添加**" 按钮。
1. 修改或检查第一个 VM 映像的以下字段的值：
    > [!IMPORTANT]
    > 并非所有默认值对于现有 Marketplace 项都是正确的。

    | 字段  | 值  |
    |---------|---------|
    | 发布者 | MicrosoftWindowsServer |
    | 产品 | WindowsServer |
    | 操作系统类型 | Windows |
    | SKU | 2012-R2-Datacenter |
    | 版本 | 1.0.0 |
    | OS 磁盘 Blob URI | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. 选择“创建”按钮。
1. 为剩余的 VM 映像重复此操作。

所有5个 VM 映像的属性如下所示：

| 发布者  | 产品  | 操作系统类型 | SKU | 版本 | OS 磁盘 Blob URI |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |
| OpenLogic | CentOS | Linux | 6.9 | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/OpenLogic-CentOS-69-20180105.vhd |
| credativ | Debian | Linux | 8 | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Debian8_latest.vhd |

## <a name="next-steps"></a>后续步骤

- 查看[验证即服务的发行说明](azure-stack-vaas-release-notes.md)，了解最新版本中的更改。