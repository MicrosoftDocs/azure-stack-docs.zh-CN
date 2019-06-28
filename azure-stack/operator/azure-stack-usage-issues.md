---
title: 使用连接性问题和在 Azure Stack 中的错误 |Microsoft Docs
description: Azure Stack 使用情况问题和错误进行故障排除。
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
ms.openlocfilehash: 57c546ec3583c9e04594e4da542a3c2ce3f72c62
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419754"
---
# <a name="usage-connectivity-errors"></a>使用连接错误

Azure Stack 使用情况数据发送到 Azure [ *Azure Bridge*组件](azure-stack-usage-reporting.md)Azure Stack 中。 如果 Azure Stack 中的桥不能连接到 Azure 的使用情况服务，您将看到以下错误：

![使用情况桥错误](media/azure-stack-usage-issues/usageerror2.png)

在窗口可能提供有关错误和解决方法的详细信息：

![错误解决方法](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>解决连接问题

若要缓解此问题，请尝试以下步骤：

- 验证该网络的配置允许 Azure 桥连接到远程服务。

- 转到[**区域管理** > **属性**](azure-stack-registration.md#verify-azure-stack-registration)边栏选项卡，找到用于注册、 资源组和名称的 Azure 订阅 ID注册资源。 验证在 Azure 门户中的正确的 Azure 订阅 ID 存在注册资源。 若要执行此操作，请转到**的所有资源**下的 Azure 订阅 ID，并检查创建**显示隐藏的类型**框。 如果找不到注册资源，请按照中的步骤[续订或更改注册](azure-stack-registration.md#renew-or-change-registration)重新注册 Azure Stack。

  ![门户](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>错误代码

本部分介绍的使用情况的错误代码。

| 错误代码                 | 问题                                                                                                                                             | 补救                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Azure Stack 桥不能将请求发送到 Azure 中的使用情况服务终结点。                                                            | 检查代理是否阻止或者截获到使用服务终结点的访问权限。                                                                                                                                                                                                             |
| RequestTimedOut            | 请求从 Azure 桥发送，但在 Azure 中的使用情况服务无法在超时期限内做出响应。                             | 检查代理是否阻止或者截获到使用服务终结点的访问权限。                                                                                                                                                                                                                        |
| LoginError                 | 无法使用 Microsoft Azure Active Directory 进行身份验证。                                                                                                             | 确保可从 Azure Stack 中的所有 XRP Vm 访问 Azure AD 登录终结点。                                                                                                                                                                                                                     |
| CertificateValidationError | Azure 的桥不能发送请求，因为它不是能够使用 Azure 服务进行身份验证。                                    | 检查是否有拦截 HTTPS 流量，Azure Stack XRP 计算机与使用网关终结点之间的代理。                                                                                                                                                                                      |
| 未授权               | Azure 桥不能将数据推送到 Azure 中的使用情况服务，因为 Azure 服务不能进行身份验证的 Azure Stack 桥。 | 检查是否已修改的注册资源，以及如果是这样，重新注册 Azure Stack。 <br><br> 有时，Azure Stack 与 Azure AD 之间的同步问题可能会导致此失败的时间。 在这种情况下，确保 Azure Stack 上的 XRP Vm 上的时间与 Azure AD 同步。 |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

此外，您可能需要按照提供的 Azure Bridge，WAS 和 WASPublic 组件的日志文件[这些步骤](azure-stack-diagnostics.md#log-collection-tool)。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[向 Azure 报告 Azure Stack 使用情况数据](azure-stack-usage-reporting.md)。
- 若要在注册过程中触发，请查看错误消息，请参阅[租户注册错误消息](azure-stack-registration-errors.md)。
- 详细了解如何[云服务提供商的使用情况报告基础结构](azure-stack-csp-ref-infrastructure.md)。
