---
title: 对 CredSSP 进行故障排除
description: 了解如何对 CredSSP 进行故障排除
author: v-dasis
ms.topic: how-to
ms.date: 12/14/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 472f0cf6103ac1559a9f8a0f757d66bc545f9a5d
ms.sourcegitcommit: f4a1a7e9d0b64ca84105d48170a23e1f473e976c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743533"
---
# <a name="troubleshoot-credssp"></a>对 CredSSP 进行故障排除

> 适用于 Azure Stack HCI 版本 v20H2

某些 Azure Stack HCI 操作使用 Windows 远程管理 (WinRM)，该功能默认情况下不允许凭据委派。 若要允许委派，计算机需要暂时启用凭据安全支持提供程序 (CredSSP)。 CredSSP 是一种安全支持提供程序，它允许客户端将凭据委派给服务器进行远程身份验证。

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

- 在服务器上运行 Windows 管理中心时，请确保用户帐户是网关管理员组的成员。

- 建议在与托管服务器位于同一域中的计算机上运行 Windows 管理中心。

- 若要在服务器上启用或禁用 CredSSP，请确保你是该计算机上的网关管理员组中的一员。 有关详细信息，请参阅[配置用户访问控制和权限](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)的前两个部分。

- 如果在群集中的服务器上重新启动 WinRM 服务，可能会提示你在每个群集服务器和 Windows 管理中心之间重新建立 WinRM 连接。

    执行此操作的一种方法是转到每个群集服务器，然后在 Windows 管理中心的 " **工具** " 菜单上选择 " **服务**"，选择 " **WinRM**"，选择 " **重新启动**"，然后在 " **重新启动服务** " 提示符下，选择 **"是"**。

## <a name="manual-troubleshooting"></a>手动故障排除

如果收到以下 WinRM 错误消息，请尝试使用此部分中的手动验证步骤来解决该错误。 示例错误消息：

`Connecting to remote <sever name> failed with the following error message: The WinRM client cannot process the request. A computer policy does not allow the delegation of the user credentials to the target computer because the computer is not trusted. The identity of the target computer can be verified if you configure the WSMAN service to use a valid certificate.`

本部分中的手动验证步骤要求你配置以下计算机：
- 运行 Windows 管理中心的计算机
- 接收到错误消息的服务器

若要解决此错误，请根据需要尝试以下补救步骤：

**补救措施1：**
1. 重新启动运行 Windows 管理中心和服务器的计算机。
1. 尝试再次运行创建群集向导。

    有关运行此向导的详细信息，请参阅 [使用 Windows 管理中心创建 AZURE STACK HCI 群集](../deploy/create-cluster.md)。

**补救措施2：**
1. 在运行 Windows 管理中心的计算机上，以管理员身份打开 Windows PowerShell 并运行以下命令：

    ```powershell
    Disable-WsmanCredSSP -Role Client  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Client -DelagateComputer <Server FQDN Name>
    ```

1. 使用 RDP 功能连接到服务器，然后运行以下 PowerShell 命令：

    ```powershell  
    Disable-WsmanCredSSP -Role Server  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Server  
    ```
    
1. 尝试再次运行创建群集向导。

    有关运行此向导的详细信息，请参阅 [使用 Windows 管理中心创建 AZURE STACK HCI 群集](../deploy/create-cluster.md)。

**更正3：**
1. 在运行 Windows 管理中心的计算机上，运行以下 PowerShell 命令以检查服务主体名称 (SPN) ：

    ```powershell
    setspn -Q WSMAN/<Windows Admin Center Computer Name>  
    ```
    
    结果应列出以下输出：

    `WSMAN/<Windows Admin Center Computer Name>`

    `WSMAN/<Windows Admin Center Computer FQDN Name>`

1. 如果未列出结果，请运行以下 PowerShell 命令来注册 SPN：

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer FQDN Name>  
    ```

1. 使用 RDP 功能连接到服务器，然后运行以下 PowerShell 命令以检查 SPN：

    ```powershell
    setspn -Q WSMAN/<Server Name>  
    ```

    结果应列出以下输出：

    `WSMAN/<Server Name>`

    `WSMAN/<Server FQDN Name>`

1. 如果未列出结果，请运行以下 PowerShell 命令来注册 SPN：

    ```powershell
    setspn -S WSMAN/<Server Name> <Server Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Server Name> <Server FQDN Name>  
    ```

1. 尝试再次运行创建群集向导。

    有关运行此向导的详细信息，请参阅 [使用 Windows 管理中心创建 AZURE STACK HCI 群集](../deploy/create-cluster.md)。


**补救措施4：**

如果前面的任何补救步骤失败或未完成，则这可能表示 Active Directory 的记录发生冲突。 你可以使用不同的计算机名称将记录重置为 Active Directory 中的新记录。

若要重置 Active Directory 中的记录，请使用新的计算机名称重新安装 Azure Stack HCI 操作系统。

## <a name="next-steps"></a>后续步骤

有关 CredSSP 的详细信息，请参阅[凭据安全支持提供程序](/windows/win32/secauthn/credential-security-support-provider)。