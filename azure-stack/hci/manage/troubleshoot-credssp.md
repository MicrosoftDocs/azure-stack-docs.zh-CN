---
title: 对 CredSSP 进行故障排除
description: 了解如何对 CredSSP 进行故障排除
author: v-dasis
ms.topic: how-to
ms.date: 08/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 24e9483aa9658809adc9be7fbfa4a2cb13daab84
ms.sourcegitcommit: 952d26ad08fcc28ad3ad83e27644e61497623a44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87899901"
---
# <a name="troubleshoot-credssp"></a>对 CredSSP 进行故障排除

> 适用于 Azure Stack HCI 版本 v20H2

某些 Azure Stack HCI 操作使用 Windows 远程管理 (WinRM)，该功能默认情况下不允许凭据委派。 若要允许委派，计算机需要暂时启用凭据安全支持提供程序 (CredSSP)。 CredSSP 是一种安全支持提供程序，支持客户端将凭据委派给目标服务器以进行远程身份验证。 

启用 CredSSP 意味着安全状态降级，在大多数情况下，应在任务或操作完成后禁用。

需要启用 CredSSP 的某些任务包括：

- 创建群集向导工作流
- Active Directory 查询或更新
- SQL Server 查询或更新
- 查找不同域或未加入域的环境中的帐户或计算机

## <a name="troubleshooting-tips"></a>故障排除提示

如果遇到关于 CredSSP 的问题，以下故障排除方法可能会有所帮助：

- 运行创建群集向导时，如果未建立 Active Directory 信任或该信任中断，则 CredSSP 可能会报告问题。 当将基于工作组的服务器用于群集创建时，会产生这种结果。 在这种情况下，请尝试手动重启群集中的每个服务器。

- 在服务器（服务器模式）上运行 Windows 管理中心时，请确保用户帐户是网关管理员组的成员。

- 若要在服务器上启用或禁用 CredSSP，请确保你是该计算机上的网关管理员组中的一员。 有关详细信息，请参阅[配置用户访问控制和权限](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)的前两个部分。

## <a name="next-steps"></a>后续步骤

有关 CredSSP 的详细信息，请参阅[凭据安全支持提供程序](/windows/win32/secauthn/credential-security-support-provider)。