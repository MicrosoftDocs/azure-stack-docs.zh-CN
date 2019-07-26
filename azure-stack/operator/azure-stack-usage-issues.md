---
title: Azure Stack 中的使用情况连接问题和错误 |Microsoft Docs
description: 排查 Azure Stack 使用问题和错误。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 68bf47ab2a0842b0aeeae07030272e7106a63220
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494114"
---
# <a name="usage-connectivity-errors"></a>使用连接错误

Azure Stack 使用情况数据将由 Azure Stack 中[ *Azure Bridge*组件](azure-stack-usage-reporting.md)发送到 Azure。 如果 Azure Stack 中的桥无法连接到 Azure 使用情况服务, 则会看到以下错误:

![使用量桥错误](media/azure-stack-usage-issues/usageerror2.png)

此窗口可能会提供有关错误和解决方法的详细信息:

![错误解决](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>解决连接问题

若要缓解此问题, 请尝试执行以下步骤:

- 验证网络配置是否允许 Azure Bridge 连接到远程服务。

- 请参阅[**区域管理** > **属性**](azure-stack-registration.md#verify-azure-stack-registration)边栏选项卡, 查找用于注册、资源组和注册资源名称的 Azure 订阅 ID。 验证 Azure 门户中正确的 Azure 订阅 ID 下的注册资源是否存在。 为此, 请前往 Azure 订阅 ID 下创建的**所有资源**, 并选中 "**显示隐藏类型**" 框。 如果找不到注册资源, 请按照[续订或更改注册](azure-stack-registration.md#renew-or-change-registration)中的步骤重新注册 Azure Stack。

  ![门户](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>错误代码

本部分介绍使用错误代码。

| 错误代码                 | 问题                                                                                                                                             | 更正                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Azure Stack 桥无法将请求发送到 Azure 中的使用情况服务终结点。                                                            | 检查代理是否正在阻止或截获对使用情况服务终结点的访问。                                                                                                                                                                                                             |
| RequestTimedOut            | 请求是从 Azure Bridge 发送的, 但 Azure 中的使用率服务未能在超时期限内响应。                             | 检查代理是否正在阻止或截获对使用情况服务终结点的访问。                                                                                                                                                                                                                        |
| LoginError                 | 无法对 Microsoft Azure Active Directory 进行身份验证。                                                                                                             | 确保可从 Azure Stack 中的所有 XRP Vm 访问 Azure AD 的登录终结点。                                                                                                                                                                                                                     |
| CertificateValidationError | Azure 桥无法发送请求, 因为它无法在 Azure 服务中进行身份验证。                                    | 检查 Azure Stack XRP 计算机和使用网关终结点之间是否存在侦听 HTTPS 流量的代理。                                                                                                                                                                                      |
| 未经授权               | Azure 桥无法将数据推送到 Azure 中的使用率服务, 因为 Azure 服务无法对 Azure Stack 桥进行身份验证。 | 检查是否已修改注册资源, 如果是, 则重新注册 Azure Stack。 <br><br> 有时 Azure Stack 和 Azure AD 之间的时间同步问题可能会导致此失败。 在这种情况下, 请确保 Azure Stack 上的 XRP Vm 上的时间与 Azure AD 同步。 |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

此外, 可能还需要通过执行以下[步骤](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep), 为 AZURE BRIDGE、WAS 和 WASPublic 组件提供日志文件。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[向 Azure 报告 Azure Stack 使用情况数据](azure-stack-usage-reporting.md)。
- 若要查看在你的注册过程中触发的错误消息, 请参阅[租户注册错误消息](azure-stack-registration-errors.md)。
- 详细了解[云服务提供商的使用情况报告基础结构](azure-stack-csp-ref-infrastructure.md)。
