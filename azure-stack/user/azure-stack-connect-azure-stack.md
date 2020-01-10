---
title: 连接到 Azure Stack 集线器 |Microsoft Docs
description: 了解如何连接 Azure Stack 集线器。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: bd93eed23d00edd772a1bbfc2d88cad03ce83dd9
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816439"
---
# <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack 集线器

若要管理资源，你必须连接到 Azure Stack 开发工具包。 本文详细介绍连接到开发工具包所需的步骤。 您可以使用下列任一连接选项：

* 远程桌面：允许单个并行用户快速连接开发工具包。
* 虚拟专用网络（VPN）：允许多个并发用户从 Azure Stack 中心基础结构外部的客户端进行连接（需要配置）。

## <a name="connect-to-azure-stack-hub-with-remote-desktop"></a>通过远程桌面连接到 Azure Stack 集线器
使用远程桌面连接，单个并发用户可以使用门户来管理资源。

1. 打开远程桌面连接并连接到开发工具包。 输入**AzureStack\AzureStackAdmin**作为用户名，并输入在 Azure Stack Hub 安装过程中提供的管理密码。  

2. 从开发工具包计算机上，打开服务器管理器，单击 "**本地服务器**"，关闭 "Internet Explorer 增强的安全性"，然后关闭服务器管理器。

3. 若要打开门户，请参阅（ https://portal.local.azurestack.external/) 并使用用户凭据登录。


## <a name="connect-to-azure-stack-hub-with-vpn"></a>通过 VPN 连接到 Azure Stack 集线器

可以建立与 Azure Stack 开发工具包的拆分隧道 VPN 连接。 通过 VPN 连接，你可以访问管理员门户、用户门户和本地安装的工具（如 Visual Studio 和 PowerShell）来管理 Azure Stack 集线器资源。 Azure Active Directory （Azure AD）和基于 Active Directory 联合身份验证服务（AD FS）的部署中均支持 VPN 连接。 VPN 连接允许多个客户端同时连接到 Azure Stack 集线器。 

> [!NOTE] 
> 此 VPN 连接不提供与 Azure Stack 集线器基础结构 Vm 的连接。 

### <a name="prerequisites"></a>必备组件

* 在本地计算机上安装[Azure Stack 集线器兼容 Azure PowerShell](../operator/azure-stack-powershell-install.md) 。  
* 下载[处理 Azure Stack 中心所需的工具](../operator/azure-stack-powershell-download.md)。 

### <a name="configure-vpn-connectivity"></a>配置 VPN 连接

若要创建与开发工具包的 VPN 连接，请从基于 Windows 的本地计算机中打开提升的 PowerShell 会话并运行以下脚本（请确保更新环境的 IP 地址和密码值）：

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer's host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack Hub host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack Hub>" `
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

如果安装成功，你会在 VPN 连接列表中看到 `azurestack`。

![网络连接](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack-hub"></a>连接到 Azure Stack 集线器

使用以下两种方法之一连接到 Azure Stack 集线器实例：  

* 使用 `Connect-AzsVpn` 命令： 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  出现提示时，请信任 Azure Stack 中心主机，并将证书从**AzureStackCertificateAuthority**安装到本地计算机的证书存储中。 此提示可能出现在 PowerShell 会话窗口之后。 

* 在本地计算机上， > **VPN**中转到 "**网络设置**" > 选择 "`azurestack` > "**连接**"。 在登录提示符下，输入用户名（AzureStack\AzureStackAdmin）和密码。

### <a name="test-the-vpn-connectivity"></a>测试 VPN 连接

若要测试门户连接，请打开浏览器并中转到用户门户（ https://portal.local.azurestack.external/) 、登录，然后创建资源。  

## <a name="next-steps"></a>后续步骤



