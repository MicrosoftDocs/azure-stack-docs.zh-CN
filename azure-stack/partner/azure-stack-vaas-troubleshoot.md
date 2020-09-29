---
title: 对验证即服务进行故障排除
titleSuffix: Azure Stack Hub
description: 排查 Azure Stack 中心服务的验证问题。
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 06e9958d2311d456e977d72fe37eb88d18008092
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764794"
---
# <a name="troubleshoot-validation-as-a-service"></a>验证作为服务的验证

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

下面是与各个软件版本相关的常见问题及其解决方案。

## <a name="local-agent"></a>本地代理

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>门户在调试模式下显示本地代理

出现此问题的原因可能是由于网络连接不稳定，代理无法向服务发送检测信号。 检测信号每五分钟发送一次。 如果服务未收到15分钟的检测信号，则服务会将代理视为非活动状态，并且不会再对其计划测试。 请检查从中启动代理的目录中的 *Agenthost.log* 文件中的错误消息。

> [!Note]
> 已在代理上运行的任何测试将继续运行，但如果检测信号在测试结束之前未还原，则代理将无法更新测试状态或上载日志。 测试将始终显示为**正在运行**并且需要取消。

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>在执行测试时，计算机上的代理进程已关闭。 要发生的情况

如果代理进程已关闭意外，则在其上运行的测试将继续显示为 " **正在运行**"。 非常规关闭的一个示例是：重新启动计算机并终止进程 (在代理窗口上按 CTRL + C) 。 如果代理重启，则代理会将测试状态更新为**已取消**。 如果代理未重启，则测试将显示为 " **正在运行** "，必须手动取消测试。

> [!Note]
> 工作流中的测试安排为按顺序运行。 在同一工作流中**运行**状态的测试完成之前，不会执行**挂起**的测试。

## <a name="vm-images"></a>VM 映像

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>在脚本中上传 VM 映像时出现故障 `VaaSPreReq`
请参阅下面的部分来 **处理慢速网络连接**。 它提供将 VM 映像上传到 Azure Stack 戳记的手动步骤。

### <a name="handle-slow-network-connectivity"></a>处理慢速网络连接

#### <a name="1-verify-that-the-environment-is-healthy"></a>1. 验证环境是否正常

1. 从 "DVM"/"跳转" 框中检查是否可以使用管理员凭据成功登录到管理门户。

2. 确认没有任何警报或警告。

3. 如果环境正常运行，请按照以下部分中的步骤，手动上传 VaaS 测试运行所需的 VM 映像。

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="2-download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>2. 将 PIR 映像下载到本地共享，以防网络流量缓慢

1. 下载 AzCopy： [vaasexternaldependencies (AzCopy) ](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)。

2. 提取 AzCopy.zip 并将其更改到包含的目录 `AzCopy.exe` 。

3. 从提升的提示符下打开 Windows PowerShell。 运行以下命令：

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

#### <a name="3-verifying-pir-image-file-hash-value"></a>3. 验证 PIR 图像文件哈希值

可以使用 **Get-HashFile** cmdlet 获取所下载的公用映像存储库映像文件的哈希值来检查映像的完整性。

| 文件名 | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |
| OpenLogic-CentOS-69-20180105 | C8B874FE042E33B488110D9311AF1A5C7DC3B08E6796610BF18FDD6728C7913C |
| Debian8_latest .vhd | 06F8C11531E195D0C90FC01DFF5DC396BB1DD73A54F8252291ED366CACD996C1 |

#### <a name="4-upload-vm-images-to-a-storage-account"></a>4. 将 VM 映像上传到存储帐户

1. 使用现有存储帐户，或在 Azure 中创建新的存储帐户。

2. 创建要将图像上传到的容器。

3. 使用 Azcopy 工具上传 (下载 VM 映像的 [*LocalFileShare*] 中的 vm 映像，以便将其) 到刚刚创建的容器中。
    > [!IMPORTANT]
    > 将容器的 "公共访问级别" 更改为 "Blob (仅限 blob 的匿名读取访问) "

#### <a name="5-upload-vm-images-to-azure-stack-environment"></a>5. 将 VM 映像上传到 Azure Stack 环境

1. 以服务管理员身份登录到管理门户。 你可以从 ECE 存储区或你的 stamp 信息文件中找到管理门户 URL。 有关说明，请参阅 [环境参数](azure-stack-vaas-parameters.md#environment-parameters)。

2. 选择 "**更多服务**" "  >  **资源提供程序**"  >  **Compute**  >  **VM Images**。

3. 选择 " **VM 映像**" 边栏选项卡顶部的 " **+ 添加**" 按钮。

4. 修改或检查第一个 VM 映像的以下字段的值：

    > [!IMPORTANT]
    > 并非所有默认值对于现有 marketplace 项都是正确的。

    | 字段  | 值  |
    |---------|---------|
    | Publisher | MicrosoftWindowsServer |
    | 产品/服务 | WindowsServer |
    | OS 类型 | Windows |
    | SKU | 2012-R2-Datacenter |
    | 版本 | 1.0.0 |
    | OS 磁盘 Blob URI | https://<*存储帐户* >/< *容器名称*>/windowsserver2012r2datacenterbyol.vhd |


5. 选择“创建”按钮。

6. 为剩余的 VM 映像重复此操作。

所有必需的 VM 映像的属性如下所示：

| 发布者  | 产品/服务  | OS 类型 | SKU | 版本 | OS 磁盘 Blob URI |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://[*存储帐户*]/[*容器名称*]/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter | 1.0.0 | https://[*存储帐户*]/[*容器名称*]/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://[*存储帐户*]/[*容器名称*]/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://[*存储帐户*]/[*容器名称*]/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://[*存储帐户*]/[*容器名称*]/Ubuntu1604-20170619.1.vhd |
| OpenLogic | CentOS | Linux | 6.9 | 1.0.0 | https://[*存储帐户*]/[*容器名称*]/OpenLogic-CentOS-69-20180105.vhd |
| Credativ | Debian | Linux | 8 | 1.0.0 | https://[*存储帐户*]/[*容器名称*]/Debian8_latest .vhd |

## <a name="next-steps"></a>后续步骤

- 查看 [验证即服务的发行说明](azure-stack-vaas-release-notes.md) ，了解最新版本中的更改。