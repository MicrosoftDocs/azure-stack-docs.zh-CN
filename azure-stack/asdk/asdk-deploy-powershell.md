---
title: 使用 Powershell 从命令行部署 ASDK |Microsoft Docs
description: 了解如何使用 PowerShell 从命令行部署 ASDK。
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
ms.custom: ''
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 5b517eec23950380bf5f0fc8febe717683960b65
ms.sourcegitcommit: 4eb1766c7a9d1ccb1f1362ae1211ec748a7d708c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69579115"
---
# <a name="deploy-asdk-from-the-command-line-using-powershell"></a>使用 Powershell 从命令行部署 ASDK

Azure Stack 开发工具包 (ASDK) 是一个测试和开发环境, 可以部署它以评估和演示 Azure Stack 功能和服务。 若要启动并运行, 需要准备环境硬件并运行一些脚本。 脚本需要花费几个小时来运行。 之后便可以登录到管理员门户和用户门户，开始使用 Azure Stack。

## <a name="prerequisites"></a>先决条件

准备 ASDK 主计算机。 计划硬件、软件和网络。 承载 ASDK 的计算机必须满足硬件、软件和网络要求。 在使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 之间进行选择。 在开始部署之前, 请务必遵循这些先决条件, 使安装过程顺利运行。

在部署 ASDK 之前, 请确保计划的 ASDK 主机计算机的硬件、操作系统、帐户和网络配置符合安装 ASDK 的最低要求。

**[查看 ASDK 部署要求和注意事项](asdk-deploy-considerations.md)** 。

> [!TIP]
> 安装操作系统以后，可以使用 [Azure Stack 部署要求检查工具](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)来确认硬件是否满足所有要求。

## <a name="download-and-extract-the-deployment-package"></a>下载并提取部署包
确保 ASDK 主机计算机满足安装 ASDK 的基本要求后, 下一步是下载并提取 ASDK 部署包。 部署包包括 Cloudbuilder.vhdx 文件，该文件是一个虚拟硬盘驱动器，其中包括一个可启动的操作系统，以及 Azure Stack 安装文件。

可以将部署包下载到 ASDK 主机或另一台计算机。 提取的部署文件需要 60 GB 的可用磁盘空间, 因此使用另一台计算机有助于减少 ASDK 主机的硬件要求。

**[下载并提取 Azure Stack 开发工具包 (ASDK)](asdk-download.md)**

## <a name="prepare-the-asdk-host-computer"></a>准备 ASDK 主机
必须先准备环境并将系统配置为从 VHD 启动，然后才能在主机上安装 ASDK。 完成此步骤后, ASDK 主机将启动到 Cloudbuilder.vhdx (一个包含可启动操作系统和 Azure Stack 安装文件的虚拟硬盘)。

使用 PowerShell 将 ASDK 主机配置为从 CloudBuilder.vhdx 启动。 以下命令会将 ASDK 主机配置为从下载后提取的 Azure Stack 虚拟硬盘 (CloudBuilder.vhdx) 启动。 完成这些步骤后，请重启 ASDK 主机。

若要将 ASDK 主机配置为从 CloudBuilder.vhdx 启动，请执行以下操作：

  1. 以管理员身份启动命令提示符。
  2. 运行 `bcdedit /copy {current} /d "Azure Stack"`。
  3. 复制 (CTRL + C) 返回的 CLSID 值, 包括所需的大括号`{}`()。 此值称为`{CLSID}` , 需要在剩余步骤中粘贴 (CTRL + V 或右键单击)。
  4. 运行 `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx`。
  5. 运行 `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx`。
  6. 运行 `bcdedit /set {CLSID} detecthal on`。
  7. 运行 `bcdedit /default {CLSID}`。
  8. 若要验证启动设置，请运行 `bcdedit`。
  9. 确保已将 Cloudbuilder.vhdx 文件移动到 C:\ 的根目录驱动器 (`C:\CloudBuilder.vhdx`) 并重新启动 ASDK 主计算机。 重新启动 ASDK 主机计算机时, 它应从 Cloudbuilder.vhdx 虚拟机 (VM) 硬盘启动, 以开始 ASDK 部署。

> [!IMPORTANT]
> 请确保在重新启动 ASDK 主计算机之前, 对其进行直接物理或 KVM 访问。 VM 在第一次启动时会提示你完成 Windows Server 设置。 提供用于登录到 ASDK 主计算机的相同管理员凭据。

### <a name="prepare-the-asdk-host-using-powershell"></a>使用 PowerShell 准备 ASDK 主机 
在 ASDK 主机成功启动到 Cloudbuilder.vhdx 映像后, 使用与用于登录到 ASDK 主计算机相同的本地管理员凭据登录。 在从 VHD 启动主机时, 这些凭据也是你作为完成 Windows Server 安装程序的一部分提供的相同凭据。

> [!NOTE]
> 也可在安装 ASDK 之前配置 [Azure Stack 遥测设置](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)。

打开提升了权限的 PowerShell 控制台并运行此部分中的命令, 在 ASDK 主机上部署 ASDK。

> [!IMPORTANT]
> ASDK 安装支持使用一个网络接口卡 (NIC) 进行网络连接。 如果有多个 NIC，请确保只启用一个（所有其他 NIC 均禁用），然后再运行部署脚本。

可以将 Azure AD 或 Windows Server AD FS 作为标识提供者来部署 Azure Stack。 Azure Stack、资源提供程序和其他应用的工作方式与这两者相同。

> [!TIP]
> 如果不提供任何安装参数 (请参阅下面的 Installazurestackpoc.ps1 可选参数和示例), 系统会提示输入所需的参数。

### <a name="deploy-azure-stack-using-azure-ad"></a>使用 Azure AD 部署 Azure Stack 
若要**使用 Azure AD 作为标识提供者**来部署 Azure Stack，必须通过直接方式或透明代理方式建立 Internet 连接。 

运行以下 PowerShell 命令, 使用 Azure AD 部署 ASDK:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

几分钟到 ASDK 安装中, 系统将提示你输入 Azure AD 凭据。 提供 Azure AD 租户的全局管理员凭据。

部署后, 不需要 Azure Active Directory 全局管理员权限。 但是, 某些操作可能需要全局管理员凭据。 此类操作的示例包括资源提供程序安装程序脚本或需要授予权限的新功能。 可以暂时恢复帐户的全局管理员权限, 也可以使用*默认提供程序订阅*所有者的单独全局管理员帐户。

### <a name="deploy-azure-stack-using-ad-fs"></a>使用 AD FS 部署 Azure Stack 
若要**使用 AD FS 作为标识提供者**来部署 ASDK, 请运行以下 PowerShell 命令 (只需添加-UseADFS 参数):

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

在 AD FS 部署中，默认的标记目录服务用作标识提供者。 用于登录的默认帐户是azurestackadmin@azurestack.local, 密码设置为 PowerShell 安装命令中提供的内容。

部署过程可能需要数小时，在此期间系统会自动重启一次。 如果部署成功，PowerShell 控制台会显示“COMPLETE:Action 'Deployment'”。 如果部署失败, 请尝试使用-重新运行参数再次运行该脚本。 也可从头开始[重新部署 ASDK](asdk-redeploy.md)。

> [!IMPORTANT]
> 若要在 ASDK 主机重启后监视部署进度，必须以 AzureStack\AzureStackAdmin 身份登录。 如果在重新启动主计算机后以本地管理员身份登录 (并加入 test-azurestack 域), 则不会看到部署进度。 请不要重新运行部署, 而是以与本地管理员相同的密码登录 AzureStack\AzureStackAdmin 来验证安装程序是否正在运行。


#### <a name="azure-ad-deployment-script-examples"></a>Azure AD 部署脚本示例
可以编写整个 Azure AD 部署的脚本。 下面是一些加注的示例，其中包括一些可选参数。

如果 Azure AD 标识只与**一个** Azure AD 目录相关联，请执行以下代码：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

如果 Azure AD 标识与**多个** Azure AD 目录相关联，请执行以下代码：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

如果你的环境没有启用 DHCP, 则必须将以下附加参数包括到上述选项之一 (提供的示例用法): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 可选参数

|参数|必需/可选|描述|
|-----|-----|-----|
|AdminPassword|必填|设置在 ASDK 部署过程中创建的所有 Vm 上的本地管理员帐户和所有其他用户帐户。 此密码必须与主机上当前的本地管理员密码匹配。|
|InfraAzureDirectoryTenantName|必填|设置租户目录。 使用此参数指定一个具体的目录，使 AAD 帐户有权在其中管理多个目录。 AAD 租户的完整名称, 格式为. onmicrosoft.com 或 Azure AD 验证的自定义域名。|
|TimeServer|必填|使用此参数指定具体的时间服务器。 必须以有效的时间服务器 IP 地址的形式提供此参数。 服务器名称不受支持。|
|InfraAzureDirectoryTenantAdminCredential|可选|设置 Azure Active Directory 用户名和密码。 这些 Azure 凭据必须是组织 ID。|
|InfraAzureEnvironment|可选|选择 Azure 环境，以便将此 Azure Stack 部署注册到其中。 选项包括“全局 Azure”、“Azure - 中国”、“Azure - 美国政府”。|
|DNSForwarder|可选|在 Azure Stack 部署过程中会创建 DNS 服务器。 若要允许解决方案中的计算机解析标记外部的名称，请提供现有的基础结构 DNS 服务器。 标记内 DNS 服务器将未知的名称解析请求转发至此服务器。|
|重新运行|可选|使用此标志重新运行部署。 将使用所有以前的输入。 由于生成了几个唯一值并用于部署, 因此不支持上一次提供的重新输入数据。|


## <a name="perform-post-deployment-configurations"></a>执行部署后配置
安装 ASDK 之后，建议进行一些安装后检查和配置更改。 使用 Test-azurestack cmdlet 验证安装是否已成功安装, 然后安装 Azure Stack PowerShell 和 GitHub 工具。

建议重置密码过期策略, 以确保 ASDK 主机的密码不会在评估期结束之前过期。

> [!NOTE]
> 也可在安装 ASDK 之后配置 [Azure Stack 遥测设置](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)。

**[ASDK 后部署任务](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>注册到 Azure
必须将 Azure Stack 注册到 Azure，以便[将 Azure 市场项下载](../operator/azure-stack-create-and-publish-marketplace-item.md)到 Azure Stack。

**[将 Azure Stack 注册到 Azure](asdk-register.md)**

## <a name="next-steps"></a>后续步骤
祝贺你！ 完成这些步骤后, 你将拥有一个 ASDK 环境, 其中包含[管理员](https://adminportal.local.azurestack.external)门户和[用户](https://portal.local.azurestack.external)门户。 

[安装 ASDK 后的配置任务](asdk-post-deploy.md)

