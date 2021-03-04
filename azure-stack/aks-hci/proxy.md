---
title: 在 Azure Stack HCI 上配置 AKS 的代理设置
description: 了解如何规划和配置代理支持以连接到 internet。
author: abha
ms.topic: how-to
ms.date: 01/27/2021
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 83a3a4928f5df1b6b4520cdc6ffd277e497425ff
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873626"
---
# <a name="configure-proxy-settings-on-aks-on-azure-stack-hci"></a>在 Azure Stack HCI 上的 AKS 上配置代理设置

如果你的网络需要使用代理服务器连接到 internet，本文档将指导你完成使用 AksHci 和 WinInetProxy PowerShell 模块在 Azure Stack HCI 上设置 AKS 的代理支持所需的步骤。 

如果不想使用 WinInetProxy，还可以使用 (inetcpl.cpl) 的 **Internet 属性** ，然后单击 " **连接** " 选项卡和 " **LAN 设置** "，配置和 veriy 代理服务器设置。


## <a name="before-you-begin"></a>准备阶段

在 PowerShell 管理窗口中运行以下命令，安装 [WinInetProxy](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0) PowerShell 模块以配置代理服务器：

```Powershell
Install-Module -Name WinInetProxy -Repository PSGallery
```

有关其他先决条件，请访问 [系统要求](./system-requirements.md)。 若要下载 AksHci PowerShell 模块，请访问 [设置 AksHci powershell 模块](./setup-powershell.md)。

## <a name="configure-a-proxy-server-without-authentication"></a>配置代理服务器而不进行身份验证

若要配置不需要进行身份验证的代理服务器，请在每个 Azure Stack HCI 群集节点上运行以下命令：

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
```

## <a name="configure-a-proxy-server-with-authentication"></a>使用身份验证配置代理服务器

若要配置需要身份验证的代理服务器，需要在 Azure Stack 主机上配置 AKS 的凭据，以用于 WinINet 代理。 这取决于代理所需的身份验证类型，可以是 NTLM/Kerberos 或基本身份验证。

> [!NOTE]
> 如果要使用证书连接到代理服务器，则需负责将该证书预配到主机上的适当证书存储，以确保其受信任。

### <a name="configure-a-proxy-server-with-ntlmkerberos-authentication"></a>使用 NTLM/Kerberos 身份验证配置代理服务器

使用以下命令添加新的 Windows 凭据。 出现提示时，输入安全密码。 或者，还可以使用 windows **凭据** 下的 Windows 凭据管理器添加 windows 凭据的新项。 

```powershell
cmdkey /generic:proxy.contoso.com /user:username /pass
```
当你运行此命令时，系统将要求你输入密码。

在 PowerShell 配置文件中，添加以下命令，以允许 AKS-HCI 代理使用缓存的凭据：

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $PROFILE
[System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials
```  

### <a name="configure-a-proxy-server-with-basic-authentication"></a>使用基本身份验证配置代理服务器

需要将基本身份验证凭据添加到 PowerShell 配置文件中，以便下载代理模块可以使用它们。 

> [!NOTE]
> PowerShell 配置文件中的凭据不会加密，并以明文形式出现。 请确保 PowerShell 配置文件受访问控制列表 (Acl) 的保护，因此只有管理员和 LocalSystem 帐户可以查看它们。

编辑 PowerShell 配置文件，将用户名和密码替换为基本身份验证凭据，如下所示：

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $profile
[System.Net.WebRequest]::DefaultWebProxy.Credentials = new-object System.Net.NetworkCredential("username", "password")
```

## <a name="deploy-aks-on-azure-stack-hci-host-using-a-proxy-server"></a>使用代理服务器在 Azure Stack HCI 主机上部署 AKS

配置代理服务器后，可以使用命令设置 AKS 主机安装的代理配置 `Set-AksHciConfig` 。 具体步骤取决于代理服务器是否需要身份验证。

使用下面列出的选项配置部署后，可以 [在 AZURE STACK HCI 上安装 AKS 主机](./setup-powershell.md) ，并 [使用 PowerShell 创建 AKS 群集](./create-kubernetes-cluster-powershell.md)。

### <a name="configure-an-aks-host-for-a-proxy-server-with-basic-authentication"></a>为具有基本身份验证的代理服务器配置 AKS 主机  

如果代理服务器要求身份验证，请运行以下命令获取凭据，并设置配置详细信息。

```powershell
$proxyCred = Get-Credential
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCredential $ProxyCred
```

## <a name="configure-an-aks-host-for-a-proxy-server-with-ntlmkerberos-authentication"></a>为使用 NTLM/Kerberos 身份验证的代理服务器配置 AKS 主机

```powershell
$credential = Get-Credential # get the credential for the proxy server
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerCredential $credential
```

### <a name="configure-an-aks-host-for-a-proxy-server-without-authentication"></a>为代理服务器配置 AKS 主机而不进行身份验证  

如果代理服务器不需要身份验证，请以管理员身份打开 PowerShell 并运行以下命令：

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888"
```

### <a name="configure-an-aks-host-for-a-proxy-server-with-a-trusted-certificate"></a>为具有可信证书的代理服务器配置 AKS 主机

如果代理服务器要求代理客户端信任证书，请在运行时指定证书文件 `Set-AksHciConfig` 。 证书文件的格式为 *64 编码的 509*。 这使我们能够在整个堆栈中预配和信任证书：

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCertFile "C:\proxycertificate.crt"
```

> [!NOTE]
> 尚未在 Windows Kubernetes 工作节点上设置/信任代理证书。 未来版本中将启用对 Windows 辅助角色的支持。


## <a name="exclude-specific-hosts-or-domains-from-using-the-proxy-server"></a>使用代理服务器排除特定主机或域

在大多数网络中，你将需要排除某些网络、主机或域，使其无法通过代理服务器进行访问。 为此，可以使用中的参数豁免地址字符串 `-proxyServerNoProxy` `Set-AksHciConfig` 。

的默认值 `proxyServerNoProxy` 为 `localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16`

运行此命令时，会排除以下内容：

- Localhost 流量 (localhost、127.0.0.1) 
- 内部 Kubernetes 服务流量 ( .svc) 其中， _.svc_ 表示通配符名称。 这类似于 .svc，*但* 在此架构中未使用。
- 专用网络地址空间 (10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) 。 请注意，专用网络地址空间包含重要网络，如 Kubernetes Service CIDR (10.96.0.0/12) 和 Kubernetes POD CIDR (10.244.0.0/16) 。

尽管这些默认值适用于许多网络，但你可能需要将更多子网范围和/或名称添加到例外列表中。 例如，你可能想要免除企业命名空间， ( 的 contoso.com) 通过代理进行定向。 可以通过在 proxyServerNoProxy 列表中指定值来实现此目的：

```powershell
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,.contoso.com"
```

## <a name="next-steps"></a>后续步骤

- [在 Kubernetes 群集上部署 Linux 应用程序](./deploy-linux-application.md)。
- [在 Kubernetes 群集上部署 Windows 应用程序](./deploy-windows-application.md)。
