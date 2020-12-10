---
title: 对 CredSSP 进行故障排除
description: 了解如何对 CredSSP 进行故障排除
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: f90e03c275c4ca7a28a9d8392351bf55d0adb2c0
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010831"
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

- 若要在服务器上而不是在电脑上运行 Windows 管理中心时使用创建群集向导，您必须是 Windows 管理中心服务器上的 "网关管理员" 组的成员。 有关详细信息，请参阅 [使用 Windows 管理中心的用户访问选项](/windows-server/manage/windows-admin-center/plan/user-access-options)。

- 运行创建群集向导时，如果未建立 Active Directory 信任或该信任中断，则 CredSSP 可能会报告问题。 当将基于工作组的服务器用于群集创建时，会产生这种结果。 在这种情况下，请尝试手动重启群集中的每个服务器。

- 在服务器（服务器模式）上运行 Windows 管理中心时，请确保用户帐户是网关管理员组的成员。

- 建议在与托管服务器位于同一域中的计算机上运行 Windows 管理中心。

- 若要在服务器上启用或禁用 CredSSP，请确保你是该计算机上的网关管理员组中的一员。 有关详细信息，请参阅[配置用户访问控制和权限](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)的前两个部分。

- 若要在群集中的服务器上重新启动 Windows 远程管理 (WinRM) 服务，可能会提示你在每个群集服务器和 Windows 管理中心之间重新建立 WinRM 连接。

    执行此操作的一种方法是转到每个群集服务器，然后在 Windows 管理中心的 " **工具** " 菜单上选择 " **服务**"，选择 " **WinRM**"，选择 " **重新启动**"，然后在 " **重新启动服务** " 提示符下，选择 **"是"**。

## <a name="next-steps"></a>后续步骤

有关 CredSSP 的详细信息，请参阅[凭据安全支持提供程序](/windows/win32/secauthn/credential-security-support-provider)。