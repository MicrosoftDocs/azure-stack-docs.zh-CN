---
title: Azure Stack 1903 更新 | Microsoft Docs
description: 了解 Azure Stack 集成系统的 1903 更新，包括新增功能、已知问题和更新下载位置。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/20/2019
ms.openlocfilehash: 0038f36c8eaab9372152aebd6df3ba9103ec4552
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009003"
---
# <a name="azure-stack-1903-update"></a>Azure Stack 1903 更新

*适用于：Azure Stack 集成系统*

本文介绍 1903 更新包的内容。 该更新包含此版 Azure Stack 的改进、修复和新功能。 本文还描述了此版本中的已知问题，并包含一个用于下载该更新的链接。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1903 更新内部版本号为 **1.1903.0.35**。

### <a name="update-type"></a>更新类型

Azure Stack 1903 更新生成类型为“Express”。 有关更新内部版本类型的详细信息，请参阅[管理 Azure Stack 中的更新](../azure-stack-updates.md)一文。 完成 1903 更新预期所需的时间约为 16 小时，但具体时间可能会有所不同。 此运行时近似值特定于 1903 更新，不应与其他 Azure Stack 更新进行比较。

> [!IMPORTANT]
> 1903 有效负载不包括 ASDK 发行版。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1903 之前，请务必先安装 1902 的最新 Azure Stack 修补程序。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="azure-stack-hotfixes"></a>Azure Stack 修补程序

- **1902**： [KB 4500637-Azure Stack 修补程序 1.1902.3.75](https://support.microsoft.com/help/4500637)
- **1903**： [KB 4500638-Azure Stack 修补程序 1.1903.2.39](https://support.microsoft.com/help/4500638)

## <a name="improvements"></a>改进

- 修复了以下网络 bug：阻止“公共 IP 地址”的“空闲超时(分钟)”值更改生效。 以前，对此值的更改将被忽略，因此，不管做出哪种更改，该值始终默认为 4 分钟。 此设置控制在不依赖客户端发送保持连接消息的情况下，TCP 连接持续打开的分钟数。 请注意，此 bug 仅影响实例级公共 IP，而不影响分配给负载均衡器的公共 IP。

- 改进了更新引擎的可靠性（包括常见问题的自动补救），以便在不造成中断的情况下应用更新。

- 改进了磁盘空间不足情况的检测和补救措施。

- Azure Stack 现在支持 2.2.35 版以上的 Windows Azure Linux 代理。 此项支持可让客户在 Azure 与 Azure Stack 之间保持一致的 Linux 映像。 它已添加为 1901 和 1902 修补程序的一部分。

### <a name="secret-management"></a>机密管理

- Azure Stack 现在支持使用外部机密来轮换证书使用的根证书。 有关详细信息， [请参阅此文](../azure-stack-rotate-secrets.md)。

- 1903 包含机密轮换的性能改进，可以减少执行内部机密轮换所需的时间。

## <a name="prerequisites"></a>必备条件

> [!IMPORTANT]
> 在更新到 1903 之前，请先安装 1902 的最新 Azure Stack 修补程序（如果有）。

- 请确保使用最新版本的 [Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)来执行工作负荷规划和大小调整。 最新版本包含 bug 修复，并提供与每个 Azure Stack 更新一起发布的新功能。

- 在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](../azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看活动警报，并解决所有需要采取措施的警报。

    ```powershell
   Test-AzureStack -Group UpdateReadiness
    ```

- Azure Stack 由 System Center Operations Manager 管理时，请确保在应用1903之前，将 [Microsoft Azure Stack 的管理包](https://www.microsoft.com/download/details.aspx?id=55184) 更新到版本1.0.3.11。

- 从版本 1902 开始，Azure Stack 更新包的格式已从 **.bin/.exe/.xml** 更改为 **.zip/.xml**。 使用联网 Azure Stack 缩放单元的客户将在门户中看到“有可用更新”消息。 未建立连接的客户现在只需下载并导入包含相应 .xml 的 .zip 文件即可。

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 尝试安装 Azure Stack 更新时，更新状态可能会显示失败并更改为 **PreparationFailed**。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。 从版本 1901 (1.1901.0.95) 开始，可以通过再次单击“立即更新”（而不是“恢复”）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。

- 运行 [Test-AzureStack](../azure-stack-diagnostic-test.md) 时，会显示基板管理控制器 (BMC) 中的一条警告消息。 可以放心地忽略此警告。

<!-- 2468613 - IS -->
- 在此更新的安装过程中，你可能会看到 FaultType UserAccounts 的标题为 "错误" 的警报 **。缺少新的。** 可以放心地忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

## <a name="post-update-steps"></a>更新后步骤

- 安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅[修补程序](#hotfixes)以及我们的[服务策略](../azure-stack-servicing-policy.md)。

- 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](../azure-stack-security-bitlocker.md)操作。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

- 在用户门户仪表板中尝试单击“反馈”磁贴时，会打开一个空的浏览器标签页。 作为一种解决方法，你可以使用 [Azure Stack 用户语音](https://aka.ms/azurestackuservoice) 来向用户发出语音请求。

<!-- 2930820 - IS ASDK -->
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。

<!-- 2931230 - IS  ASDK -->
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。

<!-- TBD - IS ASDK -->
- 不应使用版本 1804 中引入的两种管理订阅类型。 订阅类型为 **计数订阅** 和 **消耗订阅**。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- 3557860 - IS ASDK -->
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- 1663805 - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是[使用 PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。

<!-- Daniel 3/28 -->
- 在用户门户上导航到存储帐户中的某个 Blob 并尝试从导航树中打开“访问策略”时，后续的窗口无法加载。 若要解决此问题，可以分别使用以下 PowerShell cmdlet 来创建、检索、设置和删除访问策略：

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

  
<!-- Daniel 3/28 -->
- 在用户门户中，当你尝试使用 **OAuth (preview)** 选项上传 blob 时，该任务将失败并出现错误消息。 若要解决此问题，请使用“SAS”选项上传 Blob。

- 登录到 Azure Stack 门户后，可能会看到有关公共 Azure 门户的通知。 你可以放心地忽略这些通知，因为它们目前不适用于 Azure Stack (例如，"1 新更新-现已推出以下更新： Azure 门户2019年4月版更新" ) 。

- 在用户门户仪表板中选择“反馈”磁贴时，会打开一个空的浏览器标签页。 解决方法之一是使用 [Azure Stack User Voice](https://aka.ms/azurestackuservoice) 来提出 User Voice 请求。

<!-- ### Health and monitoring -->

### <a name="compute"></a>计算

- 创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。 若要解决此问题，请使用以前所用的同一名称重新创建存储帐户。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 虚拟机规模集创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 市场上不可用，因此请为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。

<!-- TBD - IS ASDK -->
- 应用 1903 更新后，在部署包含托管磁盘的 VM 时可能会遇到以下问题：

   - 如果订阅是在 1808 更新之前创建的，则部署具有托管磁盘的 VM 可能会失败并出现内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。 依次选择“资源提供程序”、“Microsoft.Compute”、“重新注册”。
      2. 在同一订阅下，请参阅 **访问控制 (IAM)**，并验证是否列出了 **Azure Stack 托管磁盘** 。
   - 如果已配置多租户环境，在与来宾目录相关联的订阅中部署 VM 可能会失败并出现内部错误消息。 若要解决错误，请执行[此文章](../azure-stack-enable-multitenancy.md#register-a-guest-directory)中的步骤来重新配置每个来宾目录。

- 如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。 若要解决此问题，请在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。

- 如果没有硬件生命周期主机 (HLH)：在版本 1902 之前，必须将组策略“计算机配置\Windows 设置\安全设置\本地策略\安全选项”设置为“发送 LM 和 NTLM - 如果已协商，则使用 NTLMv2 会话安全”。 从版本 1902 开始，必须将此策略保持为“未定义”，或将其设置为“仅发送 NTLMv2 响应”（默认值）。 否则无法建立 PowerShell 远程会话，并且会看到“拒绝访问”错误：

   ```powershell
   $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint -Credential $Cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

- 不能从 " **虚拟机规模集** " 边栏选项卡中删除规模集。 解决方法是，选择要删除的规模集，然后在“概述”窗格中单击“删除”按钮。

- 在包含 3 个容错域的可用性集中创建 VM 以及创建虚拟机规模集实例失败，在一个 4 节点 Azure Stack 环境中进行更新时出现 **FabricVmPlacementErrorUnsupportedFaultDomainSize** 错误。 可以在包含 2 个容错域的可用性集中成功创建单一 VM。 但是，在 4 节点 Azure Stack 上进行更新时，仍然不能创建规模集实例。

### <a name="networking"></a>网络

<!-- 3239127 - IS, ASDK -->
- 在 Azure Stack 门户中，对于已附加到 VM 实例的网络适配器，在更改与其绑定的 IP 配置的静态 IP 地址时，会看到一条警告消息，其中指出

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

    可以放心忽略此消息；即使 VM 实例未重启，IP 地址也会更改。

<!-- 3632798 - IS, ASDK -->
- 在门户中，如果添加入站安全规则并选择“服务标记”作为源，“服务标记”列表中会显示多个不适用于 Azure Stack 的选项。 在 Azure Stack 中有效的选项仅限以下几个：

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**

  在 Azure Stack 中，不支持将其他选项用作源标记。 同样，如果添加出站安全规则并选择“服务标记”作为目标，则显示与“源标记”相同的选项列表。 仅有的有效选项与“源标记”的有效选项相同，如以上列表中所述。

- 网络安全组 (NSG) 无法像在全球 Azure 中一样在 Azure Stack 中运行。 在 Azure 中，可以在一个 NSG 规则中设置多个端口（使用门户、PowerShell 和资源管理器模板）。 但是，在 Azure Stack 中，无法通过门户在一个 NSG 规则中设置多个端口。 若要解决此问题，请使用资源管理器模板或 PowerShell 设置这些附加的规则。

<!-- 3203799 - IS, ASDK -->
- 目前，无论实例大小是什么，Azure Stack 都不支持将 4 个以上的网络接口 (NIC) 附加到 VM 实例。

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>应用服务

<!-- 2352906 - IS ASDK -->
- 在订阅中创建第一个 Azure 函数之前，租户必须先注册存储资源提供程序。
- 由于与 1903 中的门户框架不兼容，某些租户门户用户体验会中断，其中主要为部署槽的 UX、在生产环境中进行的测试，以及站点扩展。 若要解决此问题，请使用 [Azure App Service PowerShell 模块](/azure/app-service/deploy-staging-slots#automate-with-powershell) 或 [Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest&preserve-view=true)。 在即将发布的基于 Azure Stack 1.6 (Update 6) 的 Azure 应用服务中，将还原门户体验。

<!-- ### Usage -->


<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Syslog

- syslog 配置不会在整个更新周期中保留，导致 syslog 客户端丢失其配置，并停止转发 syslog 消息。 此问题适用于自 syslog 客户端正式版 (1809) 发布以来的所有 Azure Stack 版本。 若要解决此问题，请在应用 Azure Stack 更新之后重新配置 syslog 客户端。

## <a name="download-the-update"></a>下载更新

可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1903 更新包。

只有在连接的情况下，Azure Stack 部署才会定期检查某个安全的终结点，并在已发布云更新的情况下自动通知你。 有关详细信息，请参阅[管理 Azure Stack 的更新](../azure-stack-updates.md#how-to-know-an-update-is-available)。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](../azure-stack-servicing-policy.md)。
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](../azure-stack-monitor-update.md)。