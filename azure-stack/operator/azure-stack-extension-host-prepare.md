---
title: 准备 Azure Stack 集线器中的扩展主机 |Microsoft Docs
description: 了解如何在 Azure Stack Hub 中准备扩展主机，该主机在1808版后通过 Azure Stack 中心更新包自动启用。
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 1/22/2020
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 070e7bd8a933b3d04185b40f322931ea60f3bbae
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535461"
---
# <a name="prepare-for-extension-host-in-azure-stack-hub"></a>准备 Azure Stack 集线器中的扩展主机

扩展主机通过减少所需的 TCP/IP 端口数来保护 Azure Stack 集线器。 本文介绍如何准备在1808更新后通过 Azure Stack 中心更新包自动启用的扩展主机 Azure Stack 集线器。 本文适用于 Azure Stack 中心更新1808、1809和1811。

## <a name="certificate-requirements"></a>证书要求

扩展主机实现了两个新的域命名空间，以保证每个门户扩展的唯一主机条目。 新域命名空间需要另外两个通配符证书，以确保安全通信。

该表显示新的命名空间和关联的证书：

| 部署文件夹 | 必需的证书使用者和使用者可选名称（SAN） | 范围（每个区域） | 子域命名空间 |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| 管理扩展主机 | *.adminhosting.>\<区域。\<fqdn > （通配符 SSL 证书） | 管理扩展主机 | adminhosting.>\<区域。\<fqdn > |
| 公用扩展主机 | *。托管。>\<区域。\<fqdn > （通配符 SSL 证书） | 公用扩展主机 | 提供.>\<区域。\<fqdn > |

有关详细证书要求，请参阅[Azure Stack 中心公钥基础结构证书要求](azure-stack-pki-certs.md)。

## <a name="create-certificate-signing-request"></a>创建证书签名请求

利用 Azure Stack 集线器准备情况检查程序工具，你可以为两个新的和必需的 SSL 证书创建证书签名请求。 按照[Azure Stack 集线器证书签名请求生成](azure-stack-get-pki-certs.md)一文中的步骤进行操作。

> [!Note]  
> 你可以跳过此步骤，具体取决于你请求 SSL 证书的方式。

## <a name="validate-new-certificates"></a>验证新证书

1. 在硬件生命周期主机或 Azure Stack 集线器管理工作站上以提升的权限打开 PowerShell。
2. 运行以下 cmdlet 以安装 Azure Stack 集线器就绪检查器工具：

    ```powershell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. 运行以下脚本以创建所需的文件夹结构：

    ```powershell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > 如果你通过部署 Azure Active Directory 联合服务（AD FS），则必须在脚本中将以下目录添加到 **$directories** ： `ADFS`，`Graph`。

4. 将当前在 Azure Stack 集线器中使用的现有证书放在相应的目录中。 例如，将**管理 ARM**证书放在 `Arm Admin` 文件夹中。 然后将新创建的托管证书放在 `Admin extension host` 和 `Public extension host` 目录中。
5. 运行以下 cmdlet 以开始证书检查：

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. 检查输出和所有证书是否通过所有测试。


## <a name="import-extension-host-certificates"></a>导入扩展主机证书

使用可连接到 Azure Stack 集线器特权终结点的计算机，以执行后续步骤。 确保你可以从该计算机访问新的证书文件。

1. 使用可连接到 Azure Stack 集线器特权终结点的计算机，以执行后续步骤。 请确保从该计算机访问新的证书文件。
2. 打开 PowerShell ISE 以执行下一个脚本块。
3. 导入管理宿主终结点的证书。

    ```powershell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. 导入托管终结点的证书。
    ```powershell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>更新 DNS 配置

> [!Note]  
> 如果你使用 DNS 区域委派进行 DNS 集成，则不需要执行此步骤。
如果已将单个主机 A 记录配置为发布 Azure Stack 集线器终结点，则需要创建两个其他主机 A 记录：

| IP | 主机名 | 类型 |
|----|------------------------------|------|
| \<IP > | *.Adminhosting.>\<区域。\<FQDN > | A |
| \<IP > | *.提供.>\<区域。\<FQDN > | A |

可以通过运行 cmdlet AzureStackStampInformation，使用特权终结点检索分配**的**ip。

### <a name="ports-and-protocols"></a>端口和协议

[Azure Stack 中心数据中心集成-发布终结点一](azure-stack-integrate-endpoints.md)文介绍了需要入站通信的端口和协议，这些端口和协议需要入站通信才能在扩展主机推出之前发布 Azure Stack 中心。

### <a name="publish-new-endpoints"></a>发布新终结点

需要通过防火墙发布两个新的终结点。 可以使用以下代码检索公共 VIP 池中分配的 Ip，这些代码必须从 Azure Stack 中心[环境的特权终结点](azure-stack-privileged-endpoint.md)中运行。

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and extension host information from Azure Stack Hub Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>示例输出

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> 请在启用扩展主机之前进行此更改。 这使得 Azure Stack 中心门户可以连续访问。

| 终结点（VIP） | 协议 | 端口 |
|----------------|----------|-------|
| 管理宿主 | HTTPS | 443 |
| 托管 | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>更新现有发布规则（扩展主机的启用后）

> [!Note]  
> 1808 Azure Stack 集线器更新包**尚未启用扩展**主机。 它允许您通过导入所需的证书来准备扩展主机。 在1808更新后，不要通过 Azure Stack 集线器更新包在扩展主机自动启用之前关闭任何端口。

必须在现有防火墙规则中关闭以下现有终结点端口。

> [!Note]  
> 建议在成功验证后关闭这些端口。

| 终结点（VIP） | 协议 | 端口 |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| 门户（管理员） | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| 门户（用户） | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure 资源管理器（管理员） | HTTPS | 30024 |
| Azure 资源管理器（用户） | HTTPS | 30024 |

## <a name="next-steps"></a>后续步骤

- 了解[防火墙集成](azure-stack-firewall.md)。
- 了解[Azure Stack 集线器证书签名请求生成](azure-stack-get-pki-certs.md)。
