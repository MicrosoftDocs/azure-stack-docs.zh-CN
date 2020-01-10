---
title: 使用 syslog 转发将 Azure Stack 集线器与监视解决方案集成 |Microsoft Docs
description: 了解如何使用 syslog 转发将 Azure Stack 集线器与监视解决方案集成。
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 04/23/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 04/23/2019
keywords: ''
ms.openlocfilehash: 0462d2cac78109ad76cf8c2b8c58958fc32e5d07
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817357"
---
# <a name="integrate-azure-stack-hub-with-monitoring-solutions-using-syslog-forwarding"></a>使用 syslog 转发将 Azure Stack 集线器与监视解决方案集成

本文介绍如何使用 syslog 将 Azure Stack 集线器基础结构与已部署到数据中心的外部安全解决方案集成。 例如，安全信息事件管理（SIEM）系统。 Syslog 通道公开 Azure Stack 中心基础结构的所有组件的审核、警报和安全日志。 使用 syslog 转发功能与安全监视解决方案集成，并检索所有审核、警报和安全日志以存储这些日志以进行保留。

从1809更新开始，Azure Stack 集线器具有集成的 syslog 客户端，配置后，将使用公用事件格式（CEF）的负载发出 syslog 消息。

下图描述了 Azure Stack 中心与外部 SIEM 的集成。 需要考虑两种集成模式：第一个模式（蓝色）是包含基础结构虚拟机和 Hyper-v 节点的 Azure Stack 中心基础结构。 这些组件的所有审核、安全日志和警报都通过 syslog 和 CEF 负载进行集中收集和公开。 此集成模式在此文档页中进行了介绍。
第二个集成模式为橙色，涵盖了基板管理控制器（Bmc）、硬件生命周期主机（HLH）、运行硬件伙伴监视和管理软件的虚拟机和虚拟设备。和架顶（TOR）开关。 由于这些组件是特定于硬件合作伙伴的组件，因此请与硬件合作伙伴联系，以了解如何将它们与外部 SIEM 集成。

![Syslog 转发关系图](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>配置 syslog 转发

Azure Stack 集线器中的 syslog 客户端支持以下配置：

1. **TCP 上的 Syslog，具有相互身份验证（客户端和服务器）和 TLS 1.2 加密：** 在此配置中，syslog 服务器和 syslog 客户端都可以通过证书验证彼此的标识。 消息通过 TLS 1.2 加密通道发送。

2. 通过**TCP 和服务器身份验证和 TLS 1.2 加密的 Syslog：** 在此配置中，syslog 客户端可以通过证书验证 syslog 服务器的身份。 消息通过 TLS 1.2 加密通道发送。

3. **TCP 上的 Syslog，无加密：** 在此配置中，系统日志客户端和 syslog 服务器标识未经过验证。 通过 TCP 以明文形式发送消息。

4. **UDP 上的 Syslog，无加密：** 在此配置中，系统日志客户端和 syslog 服务器标识未经过验证。 消息通过 UDP 以明文形式发送。

> [!IMPORTANT]
> Microsoft 强烈建议对生产环境使用身份验证和加密（配置 #1 或，最少 #2）使用 TCP，以防止中间人攻击和窃听消息。

### <a name="cmdlets-to-configure-syslog-forwarding"></a>用于配置 syslog 转发的 cmdlet
配置 syslog 转发需要访问特权终结点（PEP）。 已将两个 PowerShell cmdlet 添加到 PEP 来配置 syslog 转发：


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] [-OutputSeverity]
```
#### <a name="cmdlets-parameters"></a>Cmdlet 参数

*SyslogServer* cmdlet 的参数：

| 参数 | Description | 类型 | 需要 |
|---------|---------|---------|---------|
|*ServerName* | Syslog 服务器的 FQDN 或 IP 地址。 | String | 是|
|*ServerPort* | Syslog 服务器正在侦听的端口号。 | String | 是|
|*NoEncryption*| 强制客户端以明文形式发送 syslog 消息。 | flag | 否|
|*SkipCertificateCheck*| 初次 TLS 握手期间，跳过 syslog 服务器提供的证书验证。 | flag | 否|
|*SkipCNCheck*| 跳过对 syslog 服务器在初始 TLS 握手期间提供的证书的公用名值的验证。 | flag | 否|
|*UseUDP*| 结合使用 syslog 和 UDP 作为传输协议。 |flag | 否|
|*删除*| 删除客户端的服务器配置并停止 syslog 转发。| flag | 否|

*SyslogClient* cmdlet 的参数：

| 参数 | Description | 类型 |
|---------|---------| ---------|
| *pfxBinary* | pfx 文件，其中包含客户端作为标识对 syslog 服务器进行身份验证时要使用的证书。  | Byte[] |
| *CertPassword* |  用于导入与 pfx 文件关联的私钥的密码。 | SecureString |
|*RemoveCertificate* | 从客户端中删除证书。 | flag|
| *OutputSeverity* | 输出日志记录级别。 值为 "**默认**值" 或 "**详细**"。 默认值包括严重性级别： "警告"、"严重" 或 "错误"。 详细包括所有严重性级别：详细、信息性、警告、严重或错误。  | String |
### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>用 TCP、相互身份验证和 TLS 1.2 加密配置 syslog 转发

在此配置中，Azure Stack 集线器中的 syslog 客户端通过 TCP 将消息转发到 syslog 服务器，并采用 TLS 1.2 加密。 在初始握手期间，客户端将验证服务器是否提供有效的受信任证书。 客户端还向服务器提供证书，作为其身份证明。 此配置最安全，因为它提供了客户端和服务器的标识的完全验证，并且它通过加密通道发送消息。

> [!IMPORTANT]
> Microsoft 强烈建议对生产环境使用此配置。 

若要使用 TCP、相互身份验证和 TLS 1.2 加密配置 syslog 转发，请在 PEP 会话上运行这两个 cmdlet：

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

客户端证书必须与部署 Azure Stack 中心时提供的根证书具有相同的根。 它还必须包含私钥。

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>用 TCP、服务器身份验证和 TLS 1.2 加密配置 syslog 转发

在此配置中，Azure Stack 集线器中的 syslog 客户端通过 TCP 将消息转发到 syslog 服务器，并采用 TLS 1.2 加密。 在初始握手期间，客户端还会验证服务器是否提供有效的受信任证书。 此配置可防止客户端将消息发送到不受信任的目标。
使用身份验证和加密的 TCP 是默认配置，表示 Microsoft 为生产环境推荐的最低安全级别。

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

如果要使用自签名的或不受信任的证书测试 syslog 服务器与 Azure Stack 中心客户端的集成，则可以使用这些标志来跳过初始握手期间由客户端完成的服务器验证。

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> Microsoft 建议将-SkipCertificateCheck 标志用于生产环境。 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>配置通过 TCP 和无加密的 syslog 转发

在此配置中，Azure Stack 集线器中的 syslog 客户端通过 TCP 将消息转发到 syslog 服务器，而不进行加密。 客户端不会验证服务器的身份，也不会向服务器提供自己的标识用于验证。

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> Microsoft 建议不要在生产环境中使用此配置。


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>用 UDP 配置 syslog 转发和无加密

在此配置中，Azure Stack 集线器中的 syslog 客户端通过 UDP 将消息转发到 syslog 服务器，而不进行加密。 客户端不会验证服务器的身份，也不会向服务器提供自己的标识用于验证。

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

尽管不进行加密的 UDP 是最容易配置的，但它不会对中间人攻击和消息窃听提供任何保护。

> [!IMPORTANT]
> Microsoft 建议不要在生产环境中使用此配置。


## <a name="removing-syslog-forwarding-configuration"></a>删除 syslog 转发配置

若要完全删除 syslog 服务器配置并停止 syslog 转发，请执行以下操作：

**从客户端中删除 syslog 服务器配置**

```powershell  
Set-SyslogServer -Remove
```

**删除客户端中的客户端证书**

```powershell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>验证 syslog 设置

如果已成功将 syslog 客户端连接到 syslog 服务器，应该很快就会开始接收事件。 如果看不到任何事件，请通过运行以下 cmdlet 来验证 syslog 客户端的配置：

**验证 syslog 客户端中的服务器配置**

```powershell  
Get-SyslogServer
```

**验证 syslog 客户端中的证书设置**

```powershell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Syslog 消息架构

Azure Stack 中心基础结构的 syslog 转发将发送采用通用事件格式（CEF）格式的消息。
基于此架构对每个 syslog 消息进行结构化：

```Syslog
<Time> <Host> <CEF payload>
```

CEF 有效负载基于下面的结构，但每个字段的映射根据消息类型（Windows 事件、创建的警报、警报已关闭）而有所不同。

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack Hub
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>特权终结点事件的 CEF 映射

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

特权终结点的事件表：

| 事件 | PEP 事件 ID | PEP 任务名称 | 严重性 |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|
|PrivilegedEndpointSessionTimedOut |1017|PrivilegedEndpointSessionTimedOutEvent|5|

PEP 严重性表：

| 严重性 | 级别 | 数值 |
|----------|-------| ----------------|
|0|Undefined|值：0。 指示所有级别的日志|
|10|严重|值：1。 指示关键警报的日志|
|8|错误| 值：2。 指示错误日志|
|5|警告|值：3。 指示警告的日志|
|2|信息|值：4。 指示信息性消息的日志|
|0|“详细”|值：5。 指示所有级别的日志|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>恢复终结点事件的 CEF 映射

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

恢复终结点的事件表：

| 事件 | 代表事件 ID | 代表任务名称 | 严重性 |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

代表严重性表：

| 严重性 | 级别 | 数值 |
|----------|-------| ----------------|
|0|Undefined|值：0。 指示所有级别的日志|
|10|严重|值：1。 指示关键警报的日志|
|8|错误| 值：2。 指示错误日志|
|5|警告|值：3。 指示警告的日志|
|2|信息|值：4。 指示信息性消息的日志|
|0|“详细”|值：5。 指示所有级别的日志|

### <a name="cef-mapping-for-windows-events"></a>Windows 事件的 CEF 映射

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Windows 事件的严重性表：

| CEF 严重性值 | Windows 事件级别 | 数值 |
|--------------------|---------------------| ----------------|
|0|Undefined|值：0。 指示所有级别的日志|
|10|严重|值：1。 指示关键警报的日志|
|8|错误| 值：2。 指示错误日志|
|5|警告|值：3。 指示警告的日志|
|2|信息|值：4。 指示信息性消息的日志|
|0|“详细”|值：5。 指示所有级别的日志|

Azure Stack 中心中的 Windows 事件的自定义扩展表：

| 自定义扩展名称 | Windows 事件示例 | 
|-----------------------|---------|
|MasChannel | 系统|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost！！4132，G，0!!!!EseDiskFlushConsistency!!ESENT！！0x800000|
|MasEventDescription| 已成功处理用户的组策略设置。 自上一次成功处理组策略后，未检测到任何更改。|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |审核成功|
|MasLevel |4|
|MasOpcode |第|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Microsoft-windows-grouppolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| 进程创建|
|MasUserData|KB4093112!!5112！！已安装！！！0x0！！WindowsUpdateAgent Xpath：/Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>已创建警报的 CEF 映射

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

警报严重性表：

| 严重性 | 级别 |
|----------|-------|
|0|Undefined|
|10|严重|
|5|警告|

Azure Stack 中心中创建的警报的自定义扩展表：

| 自定义扩展名称 | 示例 | 
|-----------------------|---------|
|MasEventDescription|说明：为 \<TestDomain\>创建的用户帐户 \<TestUser\>。 这是潜在的安全风险。 --修正：联系支持人员。 若要解决此问题，必须提供客户帮助。 如果没有帮助，请不要尝试解决此问题。 在打开支持请求之前，请使用 https://aka.ms/azurestacklogfiles 中的指南启动日志文件收集过程。

### <a name="cef-mapping-for-alerts-closed"></a>已关闭警报的 CEF 映射

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

下面的示例显示了包含 CEF 负载的 syslog 消息：
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack Hub|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>后续步骤

[服务策略](azure-stack-servicing-policy.md)
