---
title: 连接到 ASDK
description: 了解如何连接到 Azure Stack 开发工具包（ASDK）。
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: df4b6c770ef1dd93cddbeb748c0845f391305173
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874019"
---
# <a name="connect-to-the-asdk"></a>连接到 ASDK

若要管理资源，必须先连接到 Azure Stack 开发工具包（ASDK）。 本文介绍使用下列连接选项连接到 ASDK 所需的步骤：

* [远程桌面连接（RDP）](#connect-with-rdp)：通过使用远程桌面连接进行连接时，单个用户可以快速连接到 ASDK。
* [虚拟专用网络（VPN）](#connect-with-vpn)：使用 VPN 进行连接时，多个用户可以从 Azure Stack 基础结构外部的客户端同时连接到 Azure Stack 门户。 VPN 连接需要进行一些设置。

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>使用 RDP 连接到 Azure Stack

单个并发用户可以通过直接从 ASDK 主机计算机远程桌面连接，在 Azure Stack 管理员门户或用户门户中管理资源。

> [!TIP]
> 此选项还使你能够在登录到 ASDK 主计算机时再次使用 RDP，以便登录到 ASDK 主机计算机上创建的虚拟机（Vm）。

1. 打开远程桌面连接（mstc）并连接到 ASDK 主机的 IP 地址。 请确保使用有权远程登录到 ASDK 主计算机的帐户。 默认情况下， **AzureStack\AzureStackAdmin**具有对 ASDK 主机计算机上的远程的权限。  

2. 在 ASDK 主计算机上，打开服务器管理器（ServerManager）。 选择 "**本地服务器**"，关闭 " **IE 增强的安全配置**"，然后关闭服务器管理器。

3. 以**AzureStack\CloudAdmin**的身份登录到管理员门户，或者使用其他 Azure Stack 操作员凭据。 ASDK 管理员门户地址[https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)。

4. 以**AzureStack\CloudAdmin**的身份登录到用户门户，或者使用其他 Azure Stack 用户凭据。 ASDK 用户门户地址[https://portal.local.azurestack.external](https://portal.local.azurestack.external)。

> [!NOTE]
> 有关何时使用哪个帐户的详细信息，请参阅[ASDK 管理基础知识](asdk-admin-basics.md#what-account-should-i-use)。

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>使用 VPN 连接到 Azure Stack

可以建立与 ASDK 主机计算机的拆分隧道 VPN 连接，以访问 Azure Stack 门户和本地安装的工具（如 Visual Studio 和 PowerShell）。 使用 VPN 连接时，多个用户可以同时连接到 Azure Stack ASDK 托管的资源。

Azure AD 和 Active Directory 联合身份验证服务（AD FS）部署均支持 VPN 连接。

> [!NOTE]
> VPN 连接*不*提供与 Azure Stack vm 的连接。 通过 VPN 连接时，无法通过 RDP 进入 Azure Stack Vm。

### <a name="prerequisites"></a>必备组件
在建立与 ASDK 的 VPN 连接之前，请确保满足以下先决条件：

- 在本地计算机上安装[与 Azure Stack 兼容的 Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) 。  
- 下载[使用 Azure Stack 所需的工具](asdk-post-deploy.md#download-the-azure-stack-tools)。

### <a name="set-up-vpn-connectivity"></a>设置 VPN 连接

若要创建与 ASDK 的 VPN 连接，请在本地基于 Windows 的计算机上以管理员身份打开 PowerShell。 然后，运行以下脚本（更新环境的 IP 地址和密码值）：

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the ASDK host computer's IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

如果安装成功，则**Azure Stack**会出现在 VPN 连接列表中：

![网络连接](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

  使用以下方法之一连接到 Azure Stack 实例：  

  * 使用 `Connect-AzsVpn` 命令：
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * 在本地计算机上，选择 "**网络设置**" > **VPN** > **Azure Stack** > "**连接**"。 在登录提示符下，输入用户名（**AzureStack\AzureStackAdmin**）和密码。

首次连接时，系统将提示您在本地计算机的证书存储中从**AzureStackCertificateAuthority**安装 Azure Stack 根证书。 此步骤将 ASDK 证书颁发机构（CA）添加到受信任的主机列表。 单击 **"是"** 以安装证书。

![根证书](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > 此提示可能会被 PowerShell 窗口或其他应用程序隐藏。

### <a name="test-vpn-connectivity"></a>测试 VPN 连接

若要测试门户连接，请打开浏览器，然后前往用户门户（ https://portal.local.azurestack.external/) 或管理员门户（ https://adminportal.local.azurestack.external/) ）。

用适当的订阅凭据登录以创建和管理资源。  

## <a name="next-steps"></a>后续步骤

[故障排除](asdk-troubleshooting.md)
