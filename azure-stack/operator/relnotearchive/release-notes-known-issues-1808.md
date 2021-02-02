---
title: Azure Stack 1808 更新 |Microsoft Docs
description: 了解 Azure Stack 集成系统 1808 更新的新增功能，包括已知问题和更新下载位置。
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
ms.date: 10/02/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 7d7ba1e7e3b6686002d24df2e286af81ddda5ce7
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247955"
---
# <a name="azure-stack-1808-update"></a>Azure Stack 1808 更新

*适用于：Azure Stack 集成系统*

本文介绍 1808 更新包的内容。 此更新包包含此版 Azure Stack 的改进、修复和已知问题。 本文还包含一个用于下载更新的链接。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1808 更新内部版本号为 **1.1808.0.97**。  

### <a name="new-features"></a>新增功能

此更新包含对 Azure Stack 的以下改进。

<!--  2682594   | IS  --> 
- **所有 Azure Stack 环境现在都使用协调世界时 (UTC) 时区格式。**  所有日志数据和相关的信息现在都以 UTC 格式显示。 如果你从不是使用 UTC 安装的旧版进行更新，系统会将你的环境更新成使用 UTC。 

<!-- 2437250  | IS  ASDK --> 
- **支持托管磁盘。** 现在可以在 Azure Stack 虚拟机和虚拟机规模集中使用托管磁盘。 有关详细信息，请参阅 [Azure Stack 托管磁盘：差异和注意事项](../../user/azure-stack-managed-disk-considerations.md)。

<!-- 2563799  | IS  ASDK --> 
- **Azure Monitor**。 与 Azure 上的 Azure Monitor 一样，Azure Stack 上的 Azure Monitor 针对大多数服务提供基本级别的基础结构指标和日志。 有关详细信息，请参阅 [Azure Stack 上的 Azure Monitor](../../user/azure-stack-metrics-azure-data.md)。

<!-- 2487932| IS --> 
- **为扩展主机做准备**。 可以使用扩展主机来减少所需 TCP/IP 端口的数目，以便保护 Azure Stack。 安装 1808 更新后，就可以进行准备，使 Azure Stack 可以用于托管主机。 有关详细信息，请参阅[为 Azure Stack 准备扩展主机](../azure-stack-extension-host-prepare.md)。

<!-- IS --> 
- **虚拟机规模集的库项现在已内置**。 虚拟机规模集库项现在可以在用户和管理员门户中使用，不需下载。  如果升级到 1808，则升级完成后即可使用该项。  

<!-- IS, ASDK --> 
- **虚拟机规模集缩放**。 可以通过门户[缩放虚拟机规模集](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS)。    

<!-- 2489570 | IS ASDK--> 
- **支持自定义 IPSec/IKE 策略配置**，这适用于 [Azure Stack 中的 VPN 网关](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md)。

<!-- | IS ASDK--> 
- **Kubernetes marketplace 项**。 现在可以使用 [Kubernetes 市场项](../azure-stack-solution-template-kubernetes-cluster-add.md)来部署 Kubernetes 群集。 用户可以选择 Kubernetes 项并填充一些参数，以便将 Kubernetes 群集部署到 Azure Stack。 模板的用途是方便用户在几个步骤中设置开发/测试型 Kubernetes 部署。

<!-- | IS ASDK--> 
- **Blockchain 模板**。 现在可以在 Azure Stack 上执行[以太坊联盟部署](../../user/azure-stack-ethereum.md)。 可以在 [Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)中找到三个新的模板。 有了这些模板，用户不需多少 Azure 和以太坊知识即可部署和配置多成员联盟以太坊网络。 模板的用途是方便用户在几个步骤中设置开发/测试型 Blockchain 部署。

<!-- | IS ASDK--> 
- **API 版本配置文件 2017-03-09-profile 已更新到 2018-03-01-hybrid**。 API 配置文件指定 Azure 资源提供程序和 Azure REST 终结点的 API 版本。 有关配置文件的详细信息，请参阅[在 Azure Stack 中管理 API 版本配置文件](../../user/azure-stack-version-profiles.md)。

 ### <a name="fixed-issues"></a>已修复的问题
<!-- IS ASDK--> 
- 我们修复了在门户中创建可用性集的问题，该问题导致集只能有 1 个容错域和 1 个更新域。 

<!-- IS ASDK --> 
- 现在可以在门户中使用虚拟机规模集的缩放设置。  

<!-- 2494144- IS, ASDK --> 
- 选择某个 VM 大小进行部署时妨碍某些 F 系列虚拟机大小显示的问题现在已解决。 

<!-- IS, ASDK --> 
- 改进了创建虚拟机时的性能以及对基础存储的优化使用。

- Azure Stack 所使用的性能、稳定性、安全性和操作系统的 **各种修补程序**。


### <a name="changes"></a>更改
<!-- 1697698  | IS, ASDK --> 
- 用户门户仪表板中的快速入门教程现在链接到 Azure Stack 在线文档中的相关文章。

<!-- 2515955   | IS ,ASDK--> 
- 在 Azure Stack 管理员门户和用户门户中，“所有服务”替换了“更多服务”。 现在可以在 Azure Stack 门户中使用“所有服务”作为替代来导航，就像在 Azure 门户中导航一样。

<!-- TBD | IS, ASDK --> 
- 在 Azure Stack 管理员门户和用户门户中，“+ 创建资源”替换了“+ 新建”。  现在可以在 Azure Stack 门户中使用“+ 创建资源”作为替代方式来导航，就像在 Azure 门户中导航一样。  

<!--  TBD | IS, ASDK --> 
- 通过门户[创建虚拟机规模集](../azure-stack-compute-add-scalesets.md) (VMSS) 时，不再使用“基本 A”虚拟机大小。 若要按照此大小来创建 VMSS，请使用 PowerShell 或模板。  

### <a name="common-vulnerabilities-and-exposures"></a>常见漏洞和风险

此更新安装以下更新：  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

有关这些漏洞的详细信息，请单击上述链接，或者查看 Microsoft 知识库文章 [4343887](https://support.microsoft.com/help/4343887)。

此更新还包含推理执行旁道漏洞的缓解措施，该漏洞称为 L1 终端故障 (L1TF)，详见 [Microsoft 安全公告 ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018)。  

### <a name="prerequisites"></a>必备条件

- 在应用 Azure Stack 1808 更新之前安装 Azure Stack 1807 更新。 
- 在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](../azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看活动警报，并解决所有需要采取措施的警报。  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```   

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 在 1808 更新后运行 [Test-AzureStack](../azure-stack-diagnostic-test.md) 时，会显示基板管理控制器 (BMC) 中的一条警告消息。 可以放心地忽略此警告。

<!-- 2468613 - IS --> 
- 在此更新的安装过程中，你可能会看到 *FaultType UserAccounts* 的标题为 "错误" 的警报。  可以放心地忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

<!-- 2489559 - IS --> 
- 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。

<!-- 2830461 - IS --> 
- 在某些情况下，当某个更新需要关注时，相应的警报可能没有生成。 准确的状态仍会反映在门户中，不受影响。

### <a name="post-update-steps"></a>更新后步骤
安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请查看以下知识库文章，以及我们的[服务策略](../azure-stack-servicing-policy.md)。 
- [KB 4465859 Azure Stack 修补程序 Azure Stack 修补程序1.1808.2.104](https://support.microsoft.com/help/4465859/)

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

- Azure Stack 技术文档重点介绍了最新版本。 由于版本之间的门户更改，你在使用 Azure Stack 门户时看到的内容可能与在文档中看到的内容不同。 

<!-- TBD - IS ASDK --> 
- 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请单击“编辑仪表板”，然后右键单击并选择“重置为默认状态”。

<!-- 2930718 - IS ASDK --> 
-  在管理员门户中访问用户订阅详细信息时，在关闭边栏选项卡并单击“最新”以后，用户订阅名称不显示。

<!-- 3060156 - IS ASDK --> 
-  在管理员门户和用户门户中，单击门户设置并选择“删除所有设置和专用仪表板”的操作不正常。 此时会显示错误通知。 

<!-- 2930799 - IS ASDK --> 
-  在管理员门户和用户门户中的“所有服务”下，资产“DDoS 防护计划”未正确列出。 实际上，它在 Azure Stack 中不可用。 如果尝试创建它，则会显示错误，指出门户无法创建此市场项。 

<!-- 2930820 - IS ASDK --> 
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 实际上，它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。 

<!-- 2967387 IS, ASDK --> 
-  登录 Azure Stack 管理员门户或用户门户时使用的帐户显示为“未标识的用户”。 如果帐户未指定“名”或“姓”，则会发生这种情况。 若要解决此问题，请编辑用户帐户，提供名或姓。 然后必须注销，再重新登录门户。 

<!--  2873083 - IS ASDK --> 
-  使用门户创建 (VMSS) 的虚拟机规模集时，当你使用 Internet Explorer 时， *实例大小* 下拉列表将不会正确加载。 若要解决此问题，请在通过门户创建 VMSS 时使用其他浏览器。  

<!-- 2931230 IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!--2760466 IS  ASDK --> 
- 安装运行此版本的新 Azure Stack 环境时，指示“需要激活”的警报可能不显示。 必须先[激活](../azure-stack-registration.md)，然后才能使用市场联合。  

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 订阅类型为 **计数订阅** 和 **消耗订阅**。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- TBD - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- TBD - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。


### <a name="health-and-monitoring"></a>运行状况和监视
<!-- 1264761 - IS ASDK --> 
- 可能会看到包含以下详细信息的“运行状况控制器”组件的警报：  

   警报 #1：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  警报 #2：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器故障扫描仪不可用。 这可能会影响运行状况报告和指标。

  可以放心忽略这两条警报，它们在一段时间后会自动关闭。  


<!-- 2812138 | IS --> 
- 可能会看到具有以下详细信息的 **存储** 组件警报：

   - 名称: 存储服务内部通信错误  
   - 严重性：严重  
   - 组件: 存储  
   - 说明: 将请求发送到以下节点时发生存储服务内部通信错误。  

    可以放心地忽略此警报，但需手动关闭它。

<!-- 2368581 - IS. ASDK --> 
- 如果 Azure Stack 操作员收到内存不足的警报，并且租户虚拟机无法部署并出现“Fabric VM 创建错误”，则可能表示 Azure Stack 模组的可用内存不足。 请使用 [Azure Stack 容量规划工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)来充分了解可供工作负荷使用的容量。


### <a name="compute"></a>计算

<!-- 3099544 IS, ASDK --> 
- 使用 Azure Stack 门户创建新的虚拟机 (VM) 并选择 VM 大小时，“美元/月”列在显示时会出现“不可用”消息。 此列不应显示；Azure Stack 不支持显示 VM 定价列。

<!-- 3090289 IS, ASDK --> 
- 应用 1808 更新后，可能会在通过托管磁盘部署 VM 时遇到以下问题：

   1. 如果订阅是在 1808 更新之前创建的，通过托管磁盘部署 VM 可能会失败并出现内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。 依次单击“资源提供程序”、“Microsoft.Compute”、“重新注册”。
      2. 在同一订阅下，请参阅 **访问控制 (IAM)**，并验证是否列出了 **Azure Stack 托管磁盘** 。
   2. 如果已配置多租户环境，在与来宾目录相关联的订阅中部署 VM 可能会失败并出现内部错误消息。 若要解决该错误，请执行以下步骤：
      1. 应用 [1808 Azure Stack 修补程序](https://support.microsoft.com/help/4465859)。
      2. 执行[此文](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory)中的步骤，重新配置每个来宾目录。

<!-- 2869209 IS, ASDK --> 
- 使用 [**Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0&preserve-view=true) 时，必须使用 **-OsUri** 参数作为存储帐户 URI（在其中上传磁盘）。 如果使用磁盘的本地路径，则 cmdlet 会失败，并出现以下错误： *长时间运行操作失败，状态为 "失败*"。 

<!--  2966665 IS, ASDK --> 
- 将 SSD 数据磁盘附加到高级大小托管磁盘虚拟机 (DS、DSv2、Fs、Fs_V2) 失败并出现错误：  *未能更新虚拟机的磁盘 Vmname 错误：无法执行请求的操作，因为 VM 大小不支持存储帐户类型 Premium_LRS Standard_DS/Ds_V2/fs/Fs_v2)*

   若要解决此问题，请使用 *Standard_LRS* 数据磁盘而不是 *Premium_LRS* 磁盘。 使用 *Standard_LRS* 数据磁盘不会造成 IOPS 或账单费用变化。 

<!--  2795678 IS, ASDK --> 
- 通过门户创建“高级”VM 大小（DS、Ds_v2、FS、FSv2）的虚拟机 (VM) 时，该 VM 在标准存储帐户中创建。 在标准存储帐户中创建不影响功能、IOPS 或计费。 

   你可以放心地忽略该警告，指出： *你选择使用的磁盘大小支持高级磁盘。这可能会影响操作系统的性能，不建议这样做。请考虑改用高级存储 (SSD) 。*

<!-- 2967447 - IS, ASDK --> 
- 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一 OS，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。  

<!-- 2724873 - IS --> 
- 使用 PowerShell cmdlet **Start-AzsScaleUnitNode** 或 **Stop-AzsScaleunitNode** 管理缩放单元时，首次尝试启动或停止缩放单元可能会失败。 如果 cmdlet 在第一次运行时失败，请再次运行 cmdlet。 第二次运行应该能够成功地完成操作。 

<!-- TBD - IS ASDK --> 
- 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

<!-- TBD - IS ASDK --> 
- 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

<!-- 1662991 IS ASDK --> 
- Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  

<!-- 2724961- IS ASDK --> 
- 在订阅设置中注册 **Microsoft.Insight** 资源提供程序并创建支持来宾 OS 诊断的 Windows VM 时，VM 概览页中的“CPU 百分比”图表将无法显示指标数据。

   若要查找 VM 的“CPU 百分比”图表，请转到“指标”边栏选项卡并查看所有受支持的 Windows VM 来宾指标。



### <a name="networking"></a>网络  

<!-- 1766332 - IS ASDK --> 
- 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

<!-- 1902460 - IS ASDK --> 
-  Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

<!-- 16309153 - IS ASDK --> 
- 在使用 *自动* 的 DNS 服务器设置创建的虚拟网络上，更改为自定义 DNS 服务器失败。 更新的设置不推送到该 Vnet 中的 VM。

<!-- 2702741 -  IS ASDK --> 
- 在发出“停止-解除分配”命令后，无法保证系统会保留使用动态分配方法部署的公共 IP。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack 机密轮换期间，有一个时段（两到五分钟）会无法访问公共 IP 地址。

<!-- 2664148 - IS ASDK --> 
- 租户通过 S2S VPN 隧道访问其虚拟机时，可能会遇到这样的情况：如果在创建网关后向本地网关添加本地子网，连接尝试会失败。 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>应用服务

<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

<!-- 2489178 - IS ASDK --> 
- 若要横向扩展基础结构（辅助角色、管理、前端角色），必须按照针对计算的发行说明中的说明来使用 PowerShell。



### <a name="usage"></a>使用情况  
<!-- TBD - IS ASDK --> 
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，不能使用此数据来精确地统计公共 IP 地址的使用情况。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>下载更新
可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1808 更新包。
  

## <a name="next-steps"></a>后续步骤
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](../azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](../azure-stack-monitor-update.md)。  
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](../azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](../azure-stack-apply-updates.md)。  