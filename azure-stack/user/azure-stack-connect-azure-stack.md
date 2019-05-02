---
title: 连接到 Azure Stack | Microsoft Docs
description: 了解如何连接到 Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cbffe391c6295ede6c985f38a836198a1ffba84c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986200"
---
# <a name="connect-to-azure-stack"></a>连接到 Azure Stack

若要管理资源，必须连接到 Azure Stack 开发工具包。 本文详细介绍连接到开发工具包的步骤。 可以使用以下任一连接选项：

* 远程桌面：可让单个并发用户快速地从开发工具包进行连接。
* 虚拟专用网络 (VPN)：可让多个并发用户从 Azure Stack 基础结构外部的客户端进行连接（需要配置）。

## <a name="connect-to-azure-stack-with-remote-desktop"></a>使用远程桌面连接到 Azure Stack
单个并发用户可以使用门户通过远程桌面连接来管理资源。

1. 打开远程桌面连接并连接到开发工具包。 输入 **AzureStack\AzureStackAdmin** 作为用户名，并输入设置 Azure Stack 期间提供的管理密码。  

2. 在开发工具包计算机上打开服务器管理器，单击“本地服务器”，关闭“Internet Explorer 增强的安全性”，然后关闭服务器管理器。

3. 若要打开门户，请导航到 https://portal.local.azurestack.external/)，并使用用户凭据登录。


## <a name="connect-to-azure-stack-with-vpn"></a>使用 VPN 连接到 Azure Stack

可与 Azure Stack 开发工具包建立拆分隧道虚拟专用网络 (VPN) 连接。 通过 VPN 连接，可以访问管理员门户、用户门户，并使用本地安装的工具（例如 Visual Studio 和 PowerShell）来管理 Azure Stack 资源。 基于 Azure Active Directory (AAD) 和 Active Directory 联合身份验证服务 (AD FS) 的部署均支持 VPN 连接。 多个客户端可以使用 VPN 连接同时连接到 Azure Stack。 

> [!NOTE] 
> 此 VPN 连接不提供与 Azure Stack 基础结构 VM 的连接。 

### <a name="prerequisites"></a>必备组件

* 在本地计算机上安装[与 Azure Stack 兼容的 Azure PowerShell](../operator/azure-stack-powershell-install.md)。  
* 下载[使用 Azure Stack 所需的工具](../operator/azure-stack-powershell-download.md)。 

### <a name="configure-vpn-connectivity"></a>配置 VPN 连接

若要与开发工具包建立 VPN 连接，请在基于 Windows 的本地计算机上打开权限提升的 PowerShell 会话并运行以下脚本（请务必更新环境的 IP 地址和密码值）：

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer's host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

如果设置成功，VPN 连接列表中应会显示 **azurestack**。

![网络连接](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

使用以下两种方法之一连接到 Azure Stack 实例：  

* 使用 `Connect-AzsVpn` 命令： 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  出现提示时，信任 Azure Stack 主机，并安装中的证书**AzureStackCertificateAuthority**到本地计算机的证书存储。 （该提示可能被 PowerShell 会话窗口覆盖。） 

* 打开本地计算机的**网络设置** > **VPN** > 单击**azurestack** > **连接**。 在登录提示符下，输入用户名 (AzureStack\AzureStackAdmin) 和密码。

### <a name="test-the-vpn-connectivity"></a>测试 VPN 连接

若要测试门户连接，请打开 Internet 浏览器并导航到用户门户 (https://portal.local.azurestack.external/))，然后登录并创建资源。  

## <a name="next-steps"></a>后续步骤



