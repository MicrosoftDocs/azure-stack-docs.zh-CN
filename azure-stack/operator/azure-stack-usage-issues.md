---
title: Azure Stack 中的使用情况连接问题和错误 | Microsoft Docs
description: 排查 Azure Stack 使用情况问题和错误。
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
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 81ba613ec6a816d1ae6161d078452eea63ce1164
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974024"
---
# <a name="usage-connectivity-errors"></a>使用情况连接错误

Azure Stack 使用情况数据通过 Azure Stack 中的 [*Azure Bridge* 组件](azure-stack-usage-reporting.md)发送给 Azure。 如果 Azure Stack 中的桥无法连接到 Azure 使用情况服务，则会显示以下错误：

![使用情况网桥错误](media/azure-stack-usage-issues/usageerror2.png)

窗口中可能会提供有关错误和解决方法的详细信息：

![错误解决方法](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>解决连接问题

若要缓解此问题，请尝试以下步骤：

- 验证网络配置是否允许 Azure Bridge 连接到远程服务。

- 转到[“区域管理” > “属性”](azure-stack-registration.md#verify-azure-stack-registration)边栏选项卡，找到用于注册的 Azure 订阅 ID、资源组和注册资源名称。 检查注册资源是否位于 Azure 门户中的正确 Azure 订阅 ID 下。 为此，请转到 Azure 订阅 ID 下创建的**所有资源**，并选中“显示隐藏的类型”框。 如果找不到注册资源，请按照[续订或更改注册](azure-stack-registration.md#renew-or-change-registration)中的步骤重新注册 Azure Stack。

  ![门户](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>错误代码

本部分描述使用情况错误代码。

| 错误代码                 | 问题                                                                                                                                             | 更正                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Azure Stack 网桥无法将请求发送到 Azure 中的使用情况服务终结点。                                                            | 检查代理是否阻止或拦截了对使用情况服务终结点的访问。                                                                                                                                                                                                             |
| RequestTimedOut            | 请求已从 Azure Bridge 发出，但 Azure 中的使用情况服务无法在超时期限内做出响应。                             | 检查代理是否阻止或拦截了对使用情况服务终结点的访问。                                                                                                                                                                                                                        |
| LoginError                 | 无法对 Microsoft Azure Active Directory 进行身份验证。                                                                                                             | 确定可从 Azure Stack 中的所有 XRP VM 访问 Azure AD 登录终结点。                                                                                                                                                                                                                     |
| CertificateValidationError | Azure 桥无法发送请求，因为它无法通过 Azure 服务进行身份验证。                                    | 检查是否有代理拦截了 Azure Stack XRP 计算机与使用情况网关终结点之间的 HTTPS 流量。                                                                                                                                                                                      |
| 未经授权               | Azure 桥无法将数据推送到 Azure 中的使用率服务，因为 Azure 服务无法对 Azure Stack 桥进行身份验证。 | 检查注册资源是否已修改，如果是，请重新注册 Azure Stack。 <br><br> 有时，Azure Stack 与 Azure AD 之间的时间同步问题会导致此错误。 在此情况下，请确保 Azure Stack 中 XRP VM 的时间与 Azure AD 同步。 |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

此外，可能需要遵循[这些步骤](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs)来提供 Azure Bridge、WAS 和 WASPublic 组件的日志文件。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[向 Azure 报告 Azure Stack 使用情况数据](azure-stack-usage-reporting.md)。
- 若要查看注册过程中触发的错误消息，请参阅[租户注册错误消息](azure-stack-registration-errors.md)。
- 详细了解[适用于云服务提供商的使用情况报告基础结构](azure-stack-csp-ref-infrastructure.md)。
