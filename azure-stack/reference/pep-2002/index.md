---
title: Azure Stack Hub 特权终结点参考
description: PowerShell Azure Stack 特权终结点的参考
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 76eb340da04e9254bcf8d8a626822c65362f44d2
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486030"
---
# <a name="azure-stack-hub-privileged-endpoint-reference"></a>Azure Stack Hub 特权终结点参考

PowerShell Azure Stack 特权终结点 (PEP) 是预配置的远程 PowerShell 控制台，可提供恰到好处的功能来帮助执行所需的任务。 该终结点使用 PowerShell JEA (Just Enough Administration)，只公开一组受限的 cmdlet。

## <a name="privilege-endpoint-cmdlets"></a>特权终结点 cmdlet

| Cmdlet | 说明 |
| --- | --- |
| [Close-PrivilegedEndpoint](Close-PrivilegedEndpoint.md) | 无说明。 |
| [Get-ActionStatus](Get-ActionStatus.md) | 为具有指定函数名称的操作获取最新操作的状态。 |
| [Get-AzureStackLog](Get-AzureStackLog.md) | 从具有超时的 Azure Stack 的各种角色中获取日志。 |
| [Get-AzureStackStampInformation](Get-AzureStackStampInformation.md) | 获取印花信息。 |
| [Get-AzureStackSupportConfiguration](Get-AzureStackSupportConfiguration.md) | 获取支持服务配置设置。 |
| [Get-CloudAdminPasswordRecoveryToken](Get-CloudAdminPasswordRecoveryToken.md) | 无说明。 |
| [Get-CloudAdminUserList](Get-CloudAdminUserList.md) | 无说明。 |
| [Get-ClusterLog](Get-ClusterLog.md) | 无说明。 |
| [Get-GraphApplication](Get-GraphApplication.md) | Get-GraphApplication 是一个包装器函数，用于获取指定应用程序名称或标识符的 Graph 应用程序信息。 |
| [Get-StorageJob](Get-StorageJob.md) | 无说明。 |
| [Get-SupportSessionInfo](Get-SupportSessionInfo.md) | 无说明。 |
| [Get-SupportSessionToken](Get-SupportSessionToken.md) | 无说明。 |
| [Get-SyslogClient](Get-SyslogClient.md) | 获取 syslog 客户端设置。 |
| [Get-SyslogServer](Get-SyslogServer.md) | 获取 syslog 服务器终结点。 |
| [Get-ThirdPartyNotices](Get-ThirdPartyNotices.md) | 无说明。 |
| [Get-TLSPolicy](Get-TLSPolicy.md) | 无说明。 |
| [Get-VirtualDisk](Get-VirtualDisk.md) | 无说明。 |
| [Invoke-AzureStackOnDemandLog](Invoke-AzureStackOnDemandLog.md) | 按需生成 Azure Stack 角色的日志（如果适用）。 |
| [New-AzureBridgeServicePrincipal](New-AzureBridgeServicePrincipal.md) | 在 Azure Active Directory 中创建新的服务主体。 |
| [New-AzureStackActivation](New-AzureStackActivation.md) | 激活 Azure Stack。 |
| [New-CloudAdminUser](New-CloudAdminUser.md) | 无说明。 |
| [New-GraphApplication](New-GraphApplication.md) | New-GraphApplication 是一个包装器函数，用于对 AD FS 调用 ADFS Graph cmdlet。 |
| [New-RegistrationToken](New-RegistrationToken.md) | 创建新的注册令牌 |
| [Register-CustomAdfs](Register-CustomAdfs.md) | 用于将自定义 Active Directory 联合身份验证服务 (ADFS) 作为声明提供程序注册到 Azure Stack AD FS 的脚本。 |
| [Register-CustomDnsServer](Register-CustomDnsServer.md) | 用于将自定义 DNS 服务器注册到 Azure Stack DNS 的脚本。 |
| [Register-DirectoryService](Register-DirectoryService.md) | 用于将客户 Active Directory 注册到 Graph 服务的脚本。 |
| [Remove-AzureStackActivation](Remove-AzureStackActivation.md) | 无说明。 |
| [Remove-CloudAdminUser](Remove-CloudAdminUser.md) | 无说明。 |
| [Remove-GraphApplication](Remove-GraphApplication.md) | Remove-GraphApplication 是一个包装器函数，用于对 AD FS 调用 ADFS Graph cmdlet。 |
| [Repair-VirtualDisk](Repair-VirtualDisk.md) | 无说明。 |
| [Reset-DatacenterIntegrationConfiguration](Reset-DatacenterIntegrationConfiguration.md) | 用于重置数据中心集成更改的脚本。 |
| [Send-AzureStackDiagnosticLog](Send-AzureStackDiagnosticLog.md) | 将 Azure Stack 诊断日志发送到 Microsoft。 |
| [Set-CloudAdminUserPassword](Set-CloudAdminUserPassword.md) | 无说明。 |
| [Set-GraphApplication](Set-GraphApplication.md) | Set-GraphApplication 是一个包装器函数，用于对 AD FS 调用 ADFS Graph cmdlet。 |
| [Set-ServiceAdminOwner](Set-ServiceAdminOwner.md) | 用于更新服务管理员的脚本。 |
| [Set-SyslogClient](Set-SyslogClient.md) | 导入并应用 syslog 客户端终结点证书。 |
| [Set-SyslogServer](Set-SyslogServer.md) | 设置 syslog 服务器终结点。 |
| [Set-Telemetry](Set-Telemetry.md) | 启用或禁用将遥测数据传输到 Microsoft 的功能。 |
| [Set-TLSPolicy](Set-TLSPolicy.md) | 无说明。 |
| [Start-AzureStack](Start-AzureStack.md) | 启动所有 Azure Stack 服务。 |
| [Start-SecretRotation](Start-SecretRotation.md) | 触发印花上的机密轮换。 |
| [Stop-AzureStack](Stop-AzureStack.md) | 停止所有 Azure Stack 服务。 |
| [Test-AzureStack](Test-AzureStack.md) | 验证 Azure Stack 的状态。 |
| [Unlock-SupportSession](Unlock-SupportSession.md) | 无说明。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Stack Hub 上的特权终结点的详细信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
