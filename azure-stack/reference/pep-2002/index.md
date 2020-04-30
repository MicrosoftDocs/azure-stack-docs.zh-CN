---
title: Azure Stack 中心特权终结点引用
description: PowerShell Azure Stack 特权终结点引用
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 218aff04912abcce97cf0f3568b6bff1321b4cba
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563264"
---
# <a name="azure-stack-hub-privileged-endpoint-reference"></a>Azure Stack 中心特权终结点引用

PowerShell Azure Stack 特权终结点（PEP）是预配置的远程 PowerShell 控制台，它为你提供了足够的功能来帮助你执行所需的任务。 该终结点使用 PowerShell JEA (Just Enough Administration)，只公开一组受限的 cmdlet。

## <a name="privilege-endpoint-cmdlets"></a>特权终结点 cmdlet

| Cmdlet | 描述 |
| --- | --- |
| [Close-PrivilegedEndpoint](Close-PrivilegedEndpoint.md) | 无说明。 |
| [ActionStatus](Get-ActionStatus.md) | 获取具有指定的函数名称的操作的最新操作的状态。 |
| [Get-AzureStackLog](Get-AzureStackLog.md) | 通过 Test-azurestack 的各种角色获取日志。 |
| [Get-AzureStackStampInformation](Get-AzureStackStampInformation.md) | 获取标记信息。 |
| [AzureStackSupportConfiguration](Get-AzureStackSupportConfiguration.md) | 获取支持服务配置设置。 |
| [CloudAdminPasswordRecoveryToken](Get-CloudAdminPasswordRecoveryToken.md) | 无说明。 |
| [CloudAdminUserList](Get-CloudAdminUserList.md) | 无说明。 |
| [Get-ClusterLog](Get-ClusterLog.md) | 无说明。 |
| [GraphApplication](Get-GraphApplication.md) | GraphApplication 是一个包装函数，用于获取指定应用程序名称或标识符的图形应用程序信息。 |
| [Get-StorageJob](Get-StorageJob.md) | 无说明。 |
| [SupportSessionInfo](Get-SupportSessionInfo.md) | 无说明。 |
| [SupportSessionToken](Get-SupportSessionToken.md) | 无说明。 |
| [SyslogClient](Get-SyslogClient.md) | 获取 syslog 客户端设置。 |
| [SyslogServer](Get-SyslogServer.md) | 获取 syslog 服务器终结点。 |
| [Get-ThirdPartyNotices](Get-ThirdPartyNotices.md) | 无说明。 |
| [TLSPolicy](Get-TLSPolicy.md) | 无说明。 |
| [Get-VirtualDisk](Get-VirtualDisk.md) | 无说明。 |
| [Invoke-AzureStackOnDemandLog](Invoke-AzureStackOnDemandLog.md) | 在适用的情况下，根据 Test-azurestack 角色生成按需日志。 |
| [新-AzureBridgeServicePrincipal](New-AzureBridgeServicePrincipal.md) | 在 Azure Active Directory 中创建新的服务主体。 |
| [新-AzureStackActivation](New-AzureStackActivation.md) | 激活 Azure Stack。 |
| [新-CloudAdminUser](New-CloudAdminUser.md) | 无说明。 |
| [新-GraphApplication](New-GraphApplication.md) | GraphApplication 是一个包装函数，用于调用 AD FS 上的 ADFS Graph cmdlet。 |
| [新-RegistrationToken](New-RegistrationToken.md) | 创建新的注册令牌 |
| [注册-CustomAdfs](Register-CustomAdfs.md) | 用于将自定义 Active Directory 联合身份验证服务（ADFS）注册为声明提供程序的脚本，Azure Stack AD FS。 |
| [注册-CustomDnsServer](Register-CustomDnsServer.md) | 用于向 Azure Stack DNS 注册自定义 DNS 服务器的脚本。 |
| [注册-DirectoryService](Register-DirectoryService.md) | 用于向图形服务注册客户 Active Directory 的脚本。 |
| [AzureStackActivation](Remove-AzureStackActivation.md) | 无说明。 |
| [CloudAdminUser](Remove-CloudAdminUser.md) | 无说明。 |
| [GraphApplication](Remove-GraphApplication.md) | GraphApplication 是一个包装函数，用于调用 AD FS 上的 ADFS Graph cmdlet。 |
| [修复-VirtualDisk](Repair-VirtualDisk.md) | 无说明。 |
| [重置-DatacenterIntegrationConfiguration](Reset-DatacenterIntegrationConfiguration.md) | 用于重置数据中心集成更改的脚本。 |
| [AzureStackDiagnosticLog](Send-AzureStackDiagnosticLog.md) | 向 Microsoft 发送 Azure Stack 诊断日志。 |
| [CloudAdminUserPassword](Set-CloudAdminUserPassword.md) | 无说明。 |
| [GraphApplication](Set-GraphApplication.md) | GraphApplication 是一个包装函数，用于调用 AD FS 上的 ADFS Graph cmdlet。 |
| [ServiceAdminOwner](Set-ServiceAdminOwner.md) | 用于更新服务管理员的脚本。 |
| [SyslogClient](Set-SyslogClient.md) | 导入并应用 syslog 客户端终结点证书。 |
| [SyslogServer](Set-SyslogServer.md) | 设置 syslog 服务器终结点。 |
| [设置-遥测](Set-Telemetry.md) | 启用或禁用将遥测数据传输到 Microsoft。 |
| [TLSPolicy](Set-TLSPolicy.md) | 无说明。 |
| [Test-azurestack](Start-AzureStack.md) | 启动所有 Azure Stack 服务。 |
| [Start-secretrotation](Start-SecretRotation.md) | 触发戳记上的秘密旋转。 |
| [Stop-AzureStack](Stop-AzureStack.md) | 停止所有 Azure Stack 的服务。 |
| [Test-AzureStack](Test-AzureStack.md) | 验证 Azure Stack 的状态。 |
| [SupportSession](Unlock-SupportSession.md) | 无说明。 |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[使用 Azure Stack 中的特权终结](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)点了解 Azure Stack 集线器上的特权终结点。
