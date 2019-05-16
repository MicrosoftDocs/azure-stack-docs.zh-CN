---
title: Azure Stack 1903 更新 |Microsoft Docs
description: 了解有关 Azure Stack 集成系统，包括最新内容、 1903年更新的已知的问题，以及下载更新的位置。
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
ms.date: 05/15/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/20/2019
ms.openlocfilehash: 5e0b19e753380c519704f9b2064ff56245004896
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712325"
---
# <a name="azure-stack-1903-update"></a>Azure Stack 1903 更新

*适用于：Azure Stack 集成系统*

本文介绍了 1903年更新包的内容。 该更新包含此版 Azure Stack 的改进、修复和新功能。 本文还描述了此版本中的已知问题，并包含一个用于下载该更新的链接。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1903 更新内部版本号是**1.1903.0.35**。

> [!IMPORTANT]
> 1903 有效负载不包括 ASDK 发行。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 请务必安装[最新的 Azure Stack 修补程序](#azure-stack-hotfixes)为 1902 到 1903年更新 Azure Stack 之前。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

> [!TIP]
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修补程序

- **1902**：[KB 4500637-Azure Stack 修补程序 1.1902.3.75](https://support.microsoft.com/help/4500637)
- **1903**:[KB 4500638-Azure Stack 修补程序 1.1903.2.39](https://support.microsoft.com/help/4500638)

## <a name="improvements"></a>改进

- 1903 更新负载包含哪些主机 Azure Stack 不包括基础操作系统的 Azure Stack 组件的更新。 这使某些更新，确定其作用范围。 因此，预期时间它花费的 1903年更新完成小于 （约 16 个小时，但确切的时间而异）。 运行时此下降是特定于 1903年更新并进行后续更新可能包含操作系统，这意味着不同的运行时的更新。 将来的更新将在更新完成，具体取决于有效负载包含所需的预期时间上提供类似的指导。

- 在网络中，从而阻止对更改修复了 bug**空闲超时 （分钟）** 的值**公共 IP 地址**从生效。 以前，已忽略对此值的更改，因此而不考虑您所做的任何更改，则值将默认为 4 分钟。 此设置控制分钟数，TCP 连接保持打开，而不依赖于客户端发送保持活动状态的消息。 请注意此 bug 仅影响实例级别公共 Ip，而不是公共 Ip 分配给负载均衡器。

- 包括常见的问题的自动更新，以便不中断的情况下应用更新的更新引擎可靠性改进。

- 改进的检测和修正的磁盘空间不足情况。

- Azure Stack 现在支持 Windows Azure Linux 代理版本 2.2.35 比更高版本。 此支持，客户可以维护 Azure 和 Azure Stack 之间一致的 Linux 映像。 它已添加为 1901年和 1902年修补程序的一部分。

### <a name="secret-management"></a>机密管理

- Azure Stack 现在支持使用外部机密轮换证书的根证书的旋转。 有关详细信息[请参阅此文章](azure-stack-rotate-secrets.md)。

- 1903 包含对于机密轮换减少执行内部机密轮换花费的时间的性能改进。

## <a name="prerequisites"></a>必备组件

> [!IMPORTANT]
> 安装[最新的 Azure Stack 修补程序](#azure-stack-hotfixes)为之前更新到 1903年 1902 （如果有）。

- 请确保使用最新版[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)如何规划和选型工作负荷。 最新版本包含 bug 修复，并提供已发布的新功能与每个 Azure Stack 更新。

- 在开始安装此更新之前，请运行[Test-azurestack](azure-stack-diagnostic-test.md)使用以下参数来验证 Azure Stack 的状态并解决发现的任何操作问题，包括所有警告和失败。 另外，请查看活动警报，并解决所有需要采取措施的警报。

    ```powershell
   Test-AzureStack -Group UpdateReadiness
    ```

- 当由 System Center Operations Manager 管理 Azure Stack 时，请确保更新[适用于 Microsoft Azure Stack 管理包](https://www.microsoft.com/download/details.aspx?id=55184)到版本 1.0.3.11 应用 1903年之前。

- 从版本 1902 开始，Azure Stack 更新包的格式已从 **.bin/.exe/.xml** 更改为 **.zip/.xml**。 使用联网 Azure Stack 缩放单元的客户将在门户中看到“有可用更新”消息。 未建立连接的客户现在只需下载并导入包含相应 .xml 的 .zip 文件即可。

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 在尝试安装 Azure Stack 更新时，更新的状态可能失败，并将状态更改为**PreparationFailed**。 这被引起更新资源提供程序 (URP) 无法正确地将文件传输从存储容器到处理的内部基础结构共享。 从版本 1901 (1.1901.0.95) 开始，你可以解决此问题通过单击**立即更新**再次 (不**恢复**)。 URP 然后清理文件从上一次尝试，并重新开始下载。

- 运行 [Test-AzureStack](azure-stack-diagnostic-test.md) 时，会显示基板管理控制器 (BMC) 中的一条警告消息。 可以放心地忽略此警告。

<!-- 2468613 - IS -->
- 此更新的安装期间，可能会看到警报标题**错误-FaultType UserAccounts 的模板。新找不到。** 可以放心地忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

## <a name="post-update-steps"></a>更新后步骤

- 安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅[修补程序](#hotfixes)以及我们的[服务策略](azure-stack-servicing-policy.md)。

- 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](azure-stack-security-bitlocker.md)操作。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

- 用户门户仪表板中，当你尝试在单击时**反馈**磁贴中，将打开一个空的浏览器选项卡。 作为一种解决方法，你可以使用[Azure Stack 用户之声](https://aka.ms/azurestackuservoice)提交用户语音请求。

<!-- 2930820 - IS ASDK -->
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 不是 Azure Stack 中提供。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。

<!-- 2931230 - IS  ASDK -->
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。

<!-- TBD - IS ASDK -->
- 不应使用版本 1804 中引入的两种管理订阅类型。 这两种订阅类型为“计量订阅”和“消耗订阅”。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- 3557860 - IS ASDK -->
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- 1663805 - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是[使用 PowerShell 验证权限](/powershell/module/azurerm.resources/get-azurermroleassignment)。

<!-- Daniel 3/28 -->
- 在用户门户中，导航到存储帐户中的某个 blob，并尝试打开时**访问策略**导航树中，从后续窗口中将无法加载。 若要解决此问题，以下 PowerShell cmdlet 启用了创建、 检索、 设置和分别删除访问策略：

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

  
<!-- Daniel 3/28 -->
- 在用户门户中，当你尝试上传 blob 使用时**OAuth(preview)** 选项，则任务将失败并显示错误消息。 若要解决此问题，请使用上传 blob **SAS**选项。

- 在登录到 Azure Stack 门户时可能会看到有关公共 Azure 门户的通知。 您可以放心地忽略这些通知，因为它们不当前适用于 Azure Stack (例如，"1 个新更新的以下更新现已提供：Azure 门户 2019 年 4 月更新"）。

- 用户门户仪表板中，选择时**反馈**磁贴中，将打开一个空的浏览器选项卡。 作为一种解决方法，你可以使用[Azure Stack 用户之声](https://aka.ms/azurestackuservoice)提交 Uservoice 请求。

<!-- ### Health and monitoring -->

### <a name="compute"></a>计算

- 创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。 若要解决此问题，请使用以前所用的同一名称重新创建存储帐户。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 虚拟机规模集创建体验提供了基于 CentOS 7.2，作为一个选项来部署。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。

<!-- TBD - IS ASDK -->
- 更新应用 1903年后，部署包含托管磁盘的 Vm 时，您可能会遇到以下问题：

   - 如果 1808年更新，使用托管磁盘部署 VM 之前创建的订阅可能会失败并显示内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。 依次选择“资源提供程序”、“Microsoft.Compute”、“重新注册”。
      2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“Azure Stack - 托管磁盘”是否已列出。
   - 如果已配置多租户环境中，在与来宾目录关联的订阅中部署虚拟机内部的错误消息可能会失败。 若要解决此错误，请按照中的步骤[这篇文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)重新配置每个来宾目录。

- 启用 SSH 授权与创建的 Ubuntu 18.04 VM 将不允许要使用 SSH 密钥登录。 若要解决此问题，请在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。

- 如果不具有硬件生命周期主机 (HLH): 在之前生成 1902年，您必须将组策略设置**计算机配置 \windows 设置 \ 安全设置 \ 本地策略 \ 安全选项**到**发送 LM 和 NTLM-如果协商使用 NTLMv2 会话安全**。 从版本 1902 开始，必须将此策略保持为“未定义”，或将其设置为“仅发送 NTLMv2 响应”（默认值）。 否则为你将无法建立的 PowerShell 远程会话，你将看到**访问被拒绝**错误：

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

- 无法删除规模集从**虚拟机规模集**边栏选项卡。 解决方法是，选择的小数位数设置你想要删除，然后单击**删除**按钮从**概述**窗格。

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
- 在订阅中创建第一个 Azure 函数之前，租户必须注册存储资源提供程序。
- 某些租户门户的用户体验中 1903; 已断开由于与门户框架不兼容主要，UX 部署槽的测试生产站点和站点扩展中。 若要解决此问题，请使用[Azure 应用服务 PowerShell 模块](/azure/app-service/deploy-staging-slots#automate-with-powershell)或[Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest)。 门户体验会在即将发布的 Azure Stack 1.6 (更新 6) 上的 Azure 应用服务中还原。

<!-- ### Usage -->


<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Syslog

- Syslog 配置不会保留通过更新循环，导致 syslog 客户端会丢失其配置以及要停止正在转发的 syslog 消息。 Syslog 客户端 (1809) 正式发布以来，此问题适用于所有版本的 Azure Stack。 若要解决此问题，请重新应用 Azure Stack 更新后配置 syslog 客户端。

## <a name="download-the-update"></a>下载更新

可以从 Azure Stack 1903 更新包下载[此处](https://aka.ms/azurestackupdatedownload)。

只有在联网场景中，Azure Stack 部署才会定期检查安全的终结点，并在已发布云更新的情况下自动通知你。 有关详细信息，请参阅[管理 Azure Stack 的更新](azure-stack-updates.md#using-the-update-tile-to-manage-updates)。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。
