---
title: 通过 syslog 转发将 Azure Stack 集线器与监视解决方案集成
description: 了解如何通过 Syslog 转发将 Azure Stack Hub 与监视解决方案集成。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 01/10/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 06/15/2020
ms.openlocfilehash: 9709c72233acdff710f4be2764adc7e408b32dde
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96934856"
---
# <a name="integrate-azure-stack-hub-with-monitoring-solutions-using-syslog-forwarding"></a>通过 Syslog 转发将 Azure Stack Hub 与监视解决方案集成

本文介绍如何使用 Syslog 将 Azure Stack Hub 基础结构与已经部署在数据中心的外部安全解决方案集成。 例如，安全信息和事件管理 (SIEM) 系统。 Syslog 通道会公开由 Azure Stack Hub 基础结构的所有组件提供的审核、警报和安全日志。 使用 Syslog 转发可以与安全监视解决方案集成，并且可以检索所有审核、警报和安全日志，将其以存储方式保留。

从 1809 更新开始，Azure Stack Hub 有了一个集成的 Syslog 客户端，该客户端在配置后可以通过通用事件格式 (CEF) 的有效负载发出 Syslog 消息。

下图描绘了 Azure Stack Hub 与外部 SIEM 的集成。 需要考虑两种集成模式：第一种模式（以蓝色表示）是包含基础结构虚拟机和 Hyper-V 节点的 Azure Stack Hub 基础结构。 来自这些组件的所有审核、安全日志和警报将通过包含 CEF 有效负载的 syslog 集中收集和公开。 本文档页将介绍此集成模式。
第二种集成模式以橙色表示，涵盖基板管理控制器 (BMC)、硬件生命周期主机 (HLH)、运行硬件合作伙伴监视和管理软件的虚拟机和虚拟设备，以及架顶式 (TOR) 交换机。 由于这些组件是特定于硬件合作伙伴的，因此请与硬件合作伙伴联系，以获取有关如何将这些组件与外部 SIEM 集成的文档。

![Syslog 转发图](media/azure-stack-integrate-security/azure-stack-hub-syslog-forwarding-diagram_bg.svg)

## <a name="configuring-syslog-forwarding"></a>配置 Syslog 转发

Azure Stack Hub 中的 Syslog 客户端支持以下配置：

1. **基于 TCP 的 Syslog，支持相互身份验证（客户端和服务器）和 TLS 1.2 加密：** 在此配置中，Syslog 服务器和 Syslog 客户端都可以通过证书验证彼此的标识。 消息通过 TLS 1.2 加密的通道发送。

2. **基于 TCP 的 Syslog，支持服务器身份验证和 TLS 1.2 加密：** 在此配置中，Syslog 客户端可以通过证书验证 Syslog 服务器的身份。 消息通过 TLS 1.2 加密的通道发送。

3. **基于 TCP 的 Syslog，不支持加密：** 在此配置中，不验证 syslog 客户端和 syslog 服务器标识。 消息通过 TCP 以明文形式发送。

4. **基于 UDP 的 Syslog，不支持加密：** 在此配置中，不验证 syslog 客户端和 syslog 服务器标识。 消息通过 UDP 以明文形式发送。

> [!IMPORTANT]
> Microsoft 强烈建议使用身份验证和加密 (配置 #1 或至少 #2) 生产环境中使用 TCP，以防止中间人攻击和窃听消息。

### <a name="cmdlets-to-configure-syslog-forwarding"></a>用于配置 Syslog 转发的 cmdlet
配置 Syslog 转发需要访问特权终结点 (PEP)。 已将两个 PowerShell cmdlet 添加到 PEP 来配置 Syslog 转发：


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <UInt16>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] [-OutputSeverity]
```
#### <a name="cmdlets-parameters"></a>cmdlet 参数

*Set-SyslogServer* cmdlet 的参数：

| 参数 | 说明 | 类型 | 必须 |
|---------|---------|---------|---------|
|*ServerName* | Syslog 服务器的 FQDN 或 IP 地址。 | String | 是|
|*ServerPort* | syslog 服务器侦听的端口号。 | UInt16 | 是|
|*NoEncryption*| 强制客户端以明文形式发送 Syslog 消息。 | 标志 | 否|
|*SkipCertificateCheck*| 在 TLS 初次握手期间跳过对 Syslog 服务器所提供证书的验证。 | 标志 | 否|
|*SkipCNCheck*| 在 TLS 初次握手期间跳过对 Syslog 服务器所提供证书的“公用名称”值的验证。 | 标志 | 否|
|*UseUDP*| 使用 Syslog 时将 UDP 用作传输协议。 |标志 | 否|
|*Remove*| 从客户端删除服务器的配置并停止 Syslog 转发。| 标志 | 否|

*Set-SyslogClient* cmdlet 的参数：

| 参数 | 说明 | 类型 |
|---------|---------| ---------|
| *pfxBinary* | pfx 文件的内容，通过管道传输到 Byte[]，其中包含的证书可供客户端用作对 Syslog 服务器进行身份验证的标识。  | Byte[] |
| *CertPassword* |  密码，用于导入与 pfx 文件关联的私钥。 | SecureString |
|*RemoveCertificate* | 从客户端删除证书。 | 标志|
| *OutputSeverity* | 输出日志记录的级别。 值为 **Default** 或 **Verbose**。 Default 包括严重性级别“警告”、“严重”或“错误”。 Verbose 包括所有严重性级别：“详细”、“信息”、“警告”、“严重”或“错误”。  | String |
### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>使用 TCP、相互身份验证和 TLS 1.2 加密配置 Syslog 转发

在此配置中，Azure Stack Hub 中的 Syslog 客户端使用 TLS 1.2 加密将消息通过 TCP 转发到 Syslog 服务器。 在初次握手期间，客户端会验证服务器是否提供了有效且可信的证书。 客户端也将证书提供给服务器，作为其标识的证明。 此配置是最安全的，因为它对客户端和服务器的标识都进行了完整的验证，且消息也是通过加密的通道发送。

> [!IMPORTANT]
> Microsoft 强烈建议对生产环境使用此配置。 

若要使用 TCP、相互身份验证和 TLS 1.2 加密配置 Syslog 转发，请在 PEP 会话中运行以下两个 cmdlet：

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

客户端证书的根必须与部署 Azure Stack Hub 期间提供的根相同。 它还必须包含私钥。

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

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>使用 TCP、服务器身份验证和 TLS 1.2 加密配置 Syslog 转发

在此配置中，Azure Stack Hub 中的 Syslog 客户端使用 TLS 1.2 加密将消息通过 TCP 转发到 Syslog 服务器。 在初次握手期间，客户端也会验证服务器是否提供了有效且可信的证书。 此配置可以防止客户端将消息发送至不受信任的目标。
使用身份验证和加密的 TCP 是默认配置，表示 Microsoft 为生产环境推荐的最低安全级别。

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

如果需要使用自签名证书或不受信任的证书来测试 Syslog 服务器与 Azure Stack Hub 客户端的集成，可以使用这些标记来跳过在初次握手期间由客户端执行的服务器验证。

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

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>使用 TCP 在不加密的情况下配置 Syslog 转发

在此配置中，Azure Stack Hub 中的 Syslog 客户端在不加密的情况下将消息通过 TCP 转发到 Syslog 服务器。 客户端既不验证服务器的标识，也不将自己的标识提供给服务器进行验证。

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> Microsoft 建议不要在生产环境中使用此配置。


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>使用 UDP 在不加密的情况下配置 Syslog 转发

在此配置中，Azure Stack Hub 中的 Syslog 客户端在不加密的情况下将消息通过 UDP 转发到 Syslog 服务器。 客户端既不验证服务器的标识，也不将自己的标识提供给服务器进行验证。

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

不加密的 UDP 是最容易配置的，但不能防范人为干预攻击和消息窃听。

> [!IMPORTANT]
> Microsoft 建议不要在生产环境中使用此配置。


## <a name="removing-syslog-forwarding-configuration"></a>删除 Syslog 转发配置

若要完全删除 Syslog 服务器配置并停止 Syslog 转发，请执行以下操作：

**从客户端删除 Syslog 服务器配置**

```powershell  
Set-SyslogServer -Remove
```

**从客户端删除客户端证书**

```powershell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>验证 Syslog 设置

如果已成功地将 Syslog 客户端连接到 Syslog 服务器，则很快就可以开始接收事件。 如果看不到任何事件，请运行以下 cmdlet，对 Syslog 客户端的配置进行验证：

**验证 Syslog 客户端中的服务器配置**

```powershell  
Get-SyslogServer
```

**验证 Syslog 客户端中的证书设置**

```powershell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Syslog 消息架构

Azure Stack Hub 基础结构的 Syslog 转发在发送消息时采用通用事件格式 (CEF)。
每条 Syslog 消息的结构基于以下架构：

```Syslog
<Time> <Host> <CEF payload>
```

CEF 有效负载基于下面的结构，但每个字段的映射因消息类型（Windows 事件、创建的警报、关闭的警报）而异。

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
* Who: account used to connect to the PEP
* WhichIP: IP address of the device used to connect to the PEP
```

特权终结点的事件表：

| 事件 | PEP 事件 ID | PEP 任务名称 | severity |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10 个|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10 个|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10 个|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10 个|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10 个|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10 个|
|PrivilegedEndpointSessionTimedOut |1017|PrivilegedEndpointSessionTimedOutEvent|5|

PEP 严重性表：

| severity | Level | 数字值 |
|----------|-------| ----------------|
|0|Undefined|值：0. 指示所有级别的日志|
|10 个|关键|值：1. 指示严重警报的日志|
|8|错误| 值：2. 指示错误的日志|
|5|警告|值：3. 指示警告的日志|
|2|信息|值：4. 指示信息性消息的日志|
|0|详细|值：5. 指示所有级别的日志|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>恢复终结点事件的 CEF 映射

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
* Who: account used to connect to the REP
* WhichIP: IP address of the device used to connect to the REP
```

恢复终结点的事件表：

| 事件 | REP 事件 ID | REP 任务名称 | severity |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10 个|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10 个|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

REP 严重性表：

| severity | Level | 数字值 |
|----------|-------| ----------------|
|0|Undefined|值：0. 指示所有级别的日志|
|10 个|关键|值：1. 指示严重警报的日志|
|8|错误| 值：2. 指示错误的日志|
|5|警告|值：3. 指示警告的日志|
|2|信息|值：4. 指示信息性消息的日志|
|0|详细|值：5. 指示所有级别的日志|

### <a name="cef-mapping-for-windows-events"></a>Windows 事件的 CEF 映射

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Windows 事件的严重性表：

| CEF 严重性值 | Windows 事件级别 | 数字值 |
|--------------------|---------------------| ----------------|
|0|Undefined|值：0. 指示所有级别的日志|
|10 个|关键|值：1. 指示严重警报的日志|
|8|错误| 值：2. 指示错误的日志|
|5|警告|值：3. 指示警告的日志|
|2|信息|值：4. 指示信息性消息的日志|
|0|详细|值：5. 指示所有级别的日志|

Azure Stack Hub 中 Windows 事件的自定义扩展表：

| 自定义扩展名称 | Windows 事件示例 | 
|-----------------------|---------|
|MasChannel | 系统|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132,G,0!!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| 用户的组策略设置已成功处理。 自从上次成功处理组策略后，尚未检测到任何更改。|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |审核成功|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| 创建进程|
|MasUserData|KB4093112!!5112!!Installed!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>已创建警报的 CEF 映射

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

警报严重性表：

| 严重性 | Level |
|----------|-------|
|0|Undefined|
|10|严重|
|5|警告|

Azure Stack Hub 中已创建警报的自定义扩展表：

| 自定义扩展名称 | 示例 | 
|-----------------------|---------|
|MasEventDescription|说明：为 \<TestDomain\> 创建了一个用户帐户 \<TestUser\>。 它是潜在的安全风险。 -- 补救措施：联系支持部门。 解决此问题需要客户协助。 不要试图在没有他们协助的情况下解决此问题。 在提交支持请求之前，请根据 https://aka.ms/azurestacklogfiles 中的指南启动日志文件收集过程。

### <a name="cef-mapping-for-alerts-closed"></a>已关闭警报的 CEF 映射

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

以下示例显示一条包含 CEF 有效负载的 Syslog 消息：
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack Hub|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="syslog-event-types"></a>Syslog 事件类型  

该表列出了通过 syslog 通道发送的所有事件类型、事件、消息架构或属性。 仅当 SIEM 集成需要 Windows 信息事件时才应使用安装程序详细信息开关。 

| 事件类型                                 | 事件或消息架构                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | 需要详细设置         | 事件说明 (可选)                                                                                                                                    |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Stack Hub 警报                     | 对于警报消息架构，请参阅 [CEF 映射以查找已关闭的警报](#cef-mapping-for-alerts-closed)。 <br> <br>在单独的文档中共享的所有警报的列表。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 否                               | 系统运行状况警报                                                                                                                                           |
| 特权终结点事件                 | 有关特权终结点消息架构，请参阅 [CEF 映射获取特权终结点事件](#cef-mapping-for-privileged-endpoint-events)。<br> <br>PrivilegedEndpointAccessed <br>SupportSessionTokenRequested <br>SupportSessionDevelopmentTokenRequested <br>SupportSessionUnlocked <br>SupportSessionFailedToUnlock <br>PrivilegedEndpointClosed <br>NewCloudAdminUser <br>RemoveCloudAdminUser <br>SetCloudAdminUserPassword <br>GetCloudAdminPasswordRecoveryToken <br>ResetCloudAdminPassword <br>PrivilegedEndpointSessionTimedOut                                                                                                                                                                                                                                                                                                      | 否                               |                                                                                                                                                                |
| 恢复终结点事件                   | 对于恢复终结点消息架构，请参阅 [CEF 映射获取恢复终结点事件](#cef-mapping-for-recovery-endpoint-events)。 <br>RecoveryEndpointAccessed <br>RecoverySessionTokenRequested <br>RecoverySessionDevelopmentTokenRequested <br>RecoverySessionUnlocked <br>RecoverySessionFailedToUnlock <br>Recovand RecoveryEndpointClosed                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 否                               |                                                                                                                                                                |
| Windows 安全事件                    |   <br>有关 Windows 事件消息架构，请参阅 [windows 事件的 CEF 映射](#cef-mapping-for-windows-events)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 是 (获取信息事件)   | 类型： <br>-信息 <br>- Warning（警告） <br>- Error（错误） <br>- 关键                                                                                               |
| ARM 事件                                 | 消息属性： <br> <br>AzsSubscriptionId <br>AzsCorrelationId <br>AzsPrincipalOid <br>AzsPrincipalPuid <br>AzsTenantId <br>AzsOperationName <br>AzsOperationId <br>AzsEventSource <br>AzsDescription <br>AzsResourceProvider <br>AzsResourceUri <br>AzsEventName <br>AzsEventInstanceId <br>AzsChannels <br>AzsEventLevel <br>AzsStatus <br>AzsSubStatus <br>AzsClaims <br>AzsAuthorization <br>AzsHttpRequest <br>AzsProperties <br>AzsEventTimestamp <br>AzsAudience <br>AzsIssuer <br>AzsIssuedAt <br>AzsApplicationId <br>AzsUniqueTokenId <br>AzsArmServiceRequestId <br>AzsEventCategory <br> <br>                                                                                                                                                                                                                                | 否 <br>                          | 每个已注册的 ARM 资源都可以引发事件。                                                                                                               |
| BCDR 事件                                | 消息架构： <br> <br>AuditingManualBackup { <br>} <br>AuditingConfig <br>{ <br>时间间隔 <br>保留 <br>IsSchedulerEnabled <br>BackupPath <br>} <br>AuditingPruneBackupStore { <br>IsInternalStore <br>} <br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | 否                               | 这些事件跟踪客户手动完成的基础备份管理操作，包括触发器备份、更改备份配置和修剪备份数据。       |
| 排除故障创建和关闭事件    | 消息架构： <br> <br>InfrastructureFaultOpen { <br>AzsFaultId, <br>AzsFaultTypeName, <br>AzsComponentType, <br>AzsComponentName, <br>AzsFaultHash, <br>AzsCreatedTimeUtc, <br>AzsSource <br>} <br>  <br>InfrastructureFaultClose {  <br>AzsFaultId, <br>AzsFaultTypeName, <br>AzsComponentType, <br>AzsComponentName, <br>AzsFaultHash, <br>AzsLastUpdatedTimeUtc, <br>AzsSource <br>}                                                                                                                                                                                                                                                                                                                                                                                                                                                    | 否                               | 故障触发工作流，尝试更正可能导致警报的错误。 如果错误没有修正，则会直接导致警报。            |
| 服务故障创建和关闭事件  | 消息架构： <br> <br>ServiceFaultOpen { <br>AzsFaultId, <br>AzsFaultTypeName, <br>AzsSubscriptionId, <br>AzsResourceGroup, <br>AzsServiceName, <br>AzsResourceId <br>AzsFaultHash, <br>AzsCreatedTimeUtc, <br>AzsSource <br>} <br>  <br>ServiceFaultClose { <br>AzsFaultId, <br>AzsFaultTypeName, <br>AzsSubscriptionId, <br>AzsResourceGroup, <br>AzsServiceName, <br>AzsResourceId <br>AzsFaultHash, <br>AzsLastUpdatedTimeUtc, <br>AzsSource <br>}                                                                                                                                                                                                                                                                                                                                                                                     | 否                               | 故障触发工作流，尝试更正可能导致警报的错误。  <br>如果错误没有修正，则会直接导致警报。 <br>  |
| PEP WAC 事件                             | 消息架构： <br> <br>前缀字段  <br>* 签名 ID： Test-azurestack-PrivilegedEndpoint： <PEP Event ID>  <br>路径名 <PEP Task Name>  <br>* 严重性：从 PEP 级别映射 (详细信息，请参阅下面的 PEP 严重性表)   <br>* 谁：用于连接到 PEP 的帐户  <br>* WhichIP：用于连接到 PEP 的设备的 IP 地址 <br><br>WACServiceStartFailedEvent <br>WACConnectedUserNotRetrievedEvent <br>WACEnableExceptionEvent  <br>WACUserAddedEvent <br>WACAddUserToLocalGroupFailedEvent <br>WACIsUserInLocalGroupFailedEvent  <br>WACServiceStartTimeoutEvent  <br>WACServiceStartInvalidOperationEvent <br>WACGetSidFromUserFailedEvent <br>WACDisableFirewallFailedEvent <br>WACCreateLocalGroupIfNotExistFailedEvent <br>WACEnableFlagIsTrueEvent <br>WACEnableFlagIsFalseEvent <br>WACServiceStartedEvent | 否                               |                                                                                                                                                                |

## <a name="next-steps"></a>后续步骤

[服务策略](azure-stack-servicing-policy.md)
