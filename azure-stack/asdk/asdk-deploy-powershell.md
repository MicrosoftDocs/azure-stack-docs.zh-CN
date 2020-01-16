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
ms.openlocfilehash: 0bced4d75b70b05eea42de763066f1d5b05e1976
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022915"
---
# <a name="deploy-asdk-from-the-command-line-using-powershell"></a>使用 Powershell 从命令行部署 ASDK

Azure Stack 开发工具包（ASDK）是一个测试和开发环境，可以部署它以评估和演示 Azure Stack 功能和服务。 若要启动并运行，需要准备环境硬件并运行一些脚本。 脚本需要花费几个小时来运行。 之后，你可以登录到管理员门户和用户门户，开始使用 Azure Stack。

## <a name="prerequisites"></a>必备组件

准备 ASDK 主计算机。 规划硬件、软件和网络。 承载 ASDK 的计算机必须满足硬件、软件和网络要求。 在使用 Azure Active Directory （Azure AD）或 Active Directory 联合身份验证服务（AD FS）之间进行选择。 在开始部署之前，请务必遵循这些先决条件，使安装过程顺利运行。

在部署 ASDK 之前，请确保计划的 ASDK 主机计算机的硬件、操作系统、帐户和网络配置符合安装 ASDK 的最低要求。

**[查看 ASDK 部署要求和注意事项](asdk-deploy-considerations.md)** 。

> [!TIP]
> 可以在安装操作系统后使用[Azure Stack 部署要求检查工具](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)来确认硬件是否满足所有要求。

## <a name="download-and-extract-the-deployment-package"></a>下载并提取部署包
确保 ASDK 主机计算机满足安装 ASDK 的基本要求后，下一步是下载并提取 ASDK 部署包。 部署包包含 Cloudbuilder.vhdx 文件，该文件是一个包含可启动操作系统和 Azure Stack 安装文件的虚拟硬盘。

可以将部署包下载到 ASDK 主机或另一台计算机。 提取的部署文件需要 60 GB 的可用磁盘空间，因此使用另一台计算机有助于减少 ASDK 主机的硬件要求。

**[下载并提取 Azure Stack 开发工具包（ASDK）](asdk-download.md)**

## <a name="prepare-the-asdk-host-computer"></a>准备 ASDK 主机计算机
在主计算机上安装 ASDK 之前，必须准备好环境，并将系统配置为从 VHD 启动。 完成此步骤后，ASDK 主机将启动到 Cloudbuilder.vhdx （一个包含可启动操作系统和 Azure Stack 安装文件的虚拟硬盘）。

使用 PowerShell 将 ASDK 主机配置为从 Cloudbuilder.vhdx 启动。 这些命令将 ASDK 主机配置为从 Azure Stack 虚拟硬盘（Cloudbuilder.vhdx）下载和提取的计算机启动。 完成这些步骤后，重新启动 ASDK 主计算机。

若要将 ASDK 主机配置为从 Cloudbuilder.vhdx 启动：

  1. 以管理员身份启动命令提示符。
  2. 运行 `bcdedit /copy {current} /d "Azure Stack"`。
  3. 复制（CTRL + C）返回的 CLSID 值，包括所需的大括号（`{}`）。 此值称为 `{CLSID}`，需要在其余步骤中粘贴（CTRL + V 或右键单击）。
  4. 运行 `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx`。
  5. 运行 `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx`。
  6. 运行 `bcdedit /set {CLSID} detecthal on`。
  7. 运行 `bcdedit /default {CLSID}`。
  8. 若要验证启动设置，请运行 `bcdedit`。
  9. 确保已将 Cloudbuilder.vhdx 文件移动到 C：\ 的根目录驱动器（`C:\CloudBuilder.vhdx`）并重新启动 ASDK 主计算机。 重新启动 ASDK 主机计算机时，它应从 Cloudbuilder.vhdx 虚拟机（VM）硬盘启动，以开始 ASDK 部署。

> [!IMPORTANT]
> 请确保在重新启动 ASDK 主计算机之前，对其进行直接物理或 KVM 访问。 VM 第一次启动时，会提示您完成 Windows Server 安装程序。 提供用于登录到 ASDK 主计算机的相同管理员凭据。

### <a name="prepare-the-asdk-host-using-powershell"></a>使用 PowerShell 准备 ASDK 主机 
在 ASDK 主机成功启动到 Cloudbuilder.vhdx 映像后，使用与用于登录到 ASDK 主计算机相同的本地管理员凭据登录。 在从 VHD 启动主机时，这些凭据也是你作为完成 Windows Server 安装程序的一部分提供的相同凭据。

> [!NOTE]
> 另外，还可以在安装 ASDK*之前*配置[Azure Stack 遥测设置](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)。

打开提升了权限的 PowerShell 控制台并运行此部分中的命令，在 ASDK 主机上部署 ASDK。

> [!IMPORTANT]
> ASDK 安装仅支持一个网络接口卡（NIC）用于网络。 如果有多个 Nic，请确保在运行部署脚本之前只启用了一个 Nic （和所有其他 Nic 已禁用）。

可以将 Azure Stack 与 Azure AD 或 Windows Server AD FS 作为标识提供程序进行部署。 Azure Stack、资源提供程序和其他应用的工作方式与这两者相同。

> [!TIP]
> 如果不提供任何安装参数（请参阅下面的 Installazurestackpoc.ps1 可选参数和示例），系统会提示输入所需的参数。

### <a name="deploy-azure-stack-using-azure-ad"></a>使用 Azure AD 部署 Azure Stack 
若要**使用 Azure AD 作为标识提供者**来部署 Azure Stack，你必须直接或通过透明代理连接到 internet。 

运行以下 PowerShell 命令，使用 Azure AD 部署 ASDK：

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

几分钟到 ASDK 安装中，系统将提示你输入 Azure AD 凭据。 提供 Azure AD 租户的全局管理员凭据。

部署后，不需要 Azure Active Directory 全局管理员权限。 但是，某些操作可能需要全局管理员凭据。 此类操作的示例包括资源提供程序安装程序脚本或需要授予权限的新功能。 可以暂时恢复帐户的全局管理员权限，也可以使用*默认提供程序订阅*所有者的单独全局管理员帐户。

### <a name="deploy-azure-stack-using-ad-fs"></a>使用 AD FS 部署 Azure Stack 
若要**使用 AD FS 作为标识提供者**来部署 ASDK，请运行以下 PowerShell 命令（只需添加-UseADFS 参数）：

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

在 AD FS 部署中，默认的戳记目录服务用作标识提供者。 用于登录的默认帐户是 azurestackadmin@azurestack.local的，并且密码设置为 PowerShell 安装命令中提供的内容。

部署过程可能需要几个小时，在这段时间内系统将自动重新启动一次。 如果部署成功，PowerShell 控制台会显示 "**完成：操作" "部署"** 。 如果部署失败，请尝试使用-重新运行参数再次运行该脚本。 或者，你可以从头开始重新[部署 ASDK](asdk-redeploy.md) 。

> [!IMPORTANT]
> 如果要在 ASDK 主机重新启动之后监视部署进度，则必须以 Azurestack\azurestackadmin 身份身份登录。 如果在重新启动主计算机后以本地管理员身份登录（并加入 test-azurestack 域），则不会看到部署进度。 请不要重新运行部署，而是以与本地管理员相同的密码登录 AzureStack\AzureStackAdmin 来验证安装程序是否正在运行。


#### <a name="azure-ad-deployment-script-examples"></a>Azure AD 部署脚本示例
您可以为整个 Azure AD 部署编写脚本。 下面是一些带注释的示例，其中包括一些可选参数。

如果 Azure AD 标识仅与**一个**Azure AD 目录关联：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

如果 Azure AD 标识与多**个**Azure AD 目录关联：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

如果你的环境没有启用 DHCP，则必须将以下附加参数包括到上述选项之一（提供的示例用法）： 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK Installazurestackpoc.ps1 可选参数

|参数|必需/可选|Description|
|-----|-----|-----|
|AdminPassword|需要|设置在 ASDK 部署过程中创建的所有 Vm 上的本地管理员帐户和所有其他用户帐户。 此密码必须与主机上当前的本地管理员密码匹配。|
|InfraAzureDirectoryTenantName|需要|设置租户目录。 使用此参数可指定 Azure AD 帐户有权管理多个目录的特定目录。 Azure AD 租户的完整名称，格式为. onmicrosoft.com 或 Azure AD 验证的自定义域名。|
|TimeServer|需要|使用此参数指定特定的时间服务器。 此参数必须作为有效的时间服务器 IP 地址提供。 服务器名称不受支持。|
|InfraAzureDirectoryTenantAdminCredential|可选|设置 Azure Active Directory 用户名和密码。 这些 Azure 凭据必须是组织 ID。|
|InfraAzureEnvironment|可选|选择要用于注册此 Azure Stack 部署的 Azure 环境。 选项包括全球性 Azure、Azure-中国、Azure-美国政府。|
|DNSForwarder|可选|DNS 服务器作为 Azure Stack 部署的一部分进行创建。 若要允许解决方案内部的计算机解析 stamp 之外的名称，请提供现有的基础结构 DNS 服务器。 戳记 DNS 服务器将未知的名称解析请求转发到此服务器。|
|重新运行|可选|使用此标志重新运行部署。 使用以前的所有输入。 由于生成了几个唯一值并用于部署，因此不支持上一次提供的重新输入数据。|


## <a name="perform-post-deployment-configurations"></a>执行部署后配置
安装 ASDK 后，建议进行几次安装后检查和配置更改。 使用 Test-azurestack cmdlet 验证安装是否已成功安装，然后安装 Azure Stack PowerShell 和 GitHub 工具。

建议重置密码过期策略，以确保 ASDK 主机的密码不会在评估期结束之前过期。

> [!NOTE]
> 另外，还可以在安装 ASDK*后*配置[Azure Stack 遥测设置](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)。

**[Post ASDK 部署任务](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>注册到 Azure
必须将 Azure Stack 注册到 Azure，以便可以将[Azure Marketplace 项下载](../operator/azure-stack-create-and-publish-marketplace-item.md)到 Azure Stack 中。

**[向 Azure 注册 Azure Stack](asdk-register.md)**

## <a name="next-steps"></a>后续步骤
祝贺你！ 完成这些步骤后，你将拥有一个 ASDK 环境，其中包含[管理员](https://adminportal.local.azurestack.external)门户和[用户](https://portal.local.azurestack.external)门户。 

[Post ASDK 安装配置任务](asdk-post-deploy.md)

