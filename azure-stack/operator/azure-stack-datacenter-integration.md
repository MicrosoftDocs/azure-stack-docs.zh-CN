---
title: Azure Stack 集线器集成系统的数据中心集成规划注意事项
description: 了解如何规划和准备与 Azure Stack 集线器集成系统的数据中心集成。
author: ihenkel
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: b4809454f6bec18fbfd2ffdc3f1aa866786199c5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882468"
---
# <a name="datacenter-integration-planning-considerations-for-azure-stack-hub-integrated-systems"></a>Azure Stack 集线器集成系统的数据中心集成规划注意事项

如果你对 Azure Stack 集线器集成系统感兴趣，你应该了解有关部署和系统如何适应你的数据中心的主要规划注意事项。 本文概要介绍了这些注意事项，以帮助你为 Azure Stack 集线器集成系统做出重要的基础结构决策。 当使用 OEM 硬件供应商时，了解这些注意事项有助于将 Azure Stack 集线器部署到数据中心。  

> [!NOTE]  
> Azure Stack 集线器集成系统只能从已获授权的硬件供应商处购买。

若要部署 Azure Stack 中心，你需要在部署开始之前向解决方案提供商提供规划信息，以帮助过程快速顺畅地进行。 需要的信息包括网络、安全和标识信息，以及可能需要许多不同领域和决策者的知识的重要决策。 你将需要组织中多个团队的人员，以确保在部署之前已准备好所有必需的信息。 收集此信息时，此信息可帮助与硬件供应商联系，因为它们可能有有用的建议。

在研究和收集所需的信息时，可能需要对网络环境进行一些预部署配置更改。 这些更改可能包括保留 Azure Stack 中心解决方案的 IP 地址空间，以及配置路由器、交换机和防火墙，以便为连接到新的 Azure Stack 中心解决方案交换机做好准备。 请确保将主题区域专家排在一起，以帮助你进行规划。

## <a name="capacity-planning-considerations"></a>容量规划注意事项
评估用于获取的 Azure Stack 中心解决方案时，你可以进行硬件配置选择，这会直接影响 Azure Stack 中心解决方案的总体容量。 其中包括 CPU、内存密度、存储配置和整体解决方案规模（如服务器数量）的经典选项。 与传统的虚拟化解决方案不同，这些组件的简单算术功能不适用。 第一个原因是，Azure Stack 中心构建为在解决方案自身内托管基础结构或管理组件。 第二个原因是，解决方案的某些容量是通过以最大程度地减少租户工作负荷中断的方式更新解决方案的软件而保留的。

[Azure Stack 集线器容量规划器电子表格](https://aka.ms/azstackcapacityplanner)可帮助您通过两种方式为规划容量作出明智的决策。 第一种方法是选择硬件产品并尝试调整资源的组合。 第二种方法是定义 Azure Stack 中心用于运行的工作负荷，以查看可支持它的可用硬件 Sku。 最后，此电子表格旨在作为指导，帮助做出与 Azure Stack 中心规划和配置相关的决策。

该电子表格并不旨在作为您自己的调查和分析的替代方案。 对于电子表格中提供的信息，Microsoft 不作任何明示或默示保证。

## <a name="management-considerations"></a>管理注意事项
Azure Stack 集线器是一个密封系统，其中的基础结构从权限和网络角度都被锁定。 应用网络访问控制列表（Acl）以阻止所有未经授权的传入流量以及基础结构组件之间的所有不必要的通信。 此系统会使未经授权的用户难以访问系统。

对于日常管理和操作，不会对基础结构进行无限制的管理访问。 Azure Stack 中心操作员必须通过管理员门户或 Azure 资源管理器（通过 PowerShell 或 REST API）管理系统。 其他管理工具（如 Hyper-v 管理器或故障转移群集管理器）无法访问系统。 为了帮助保护系统，第三方软件（例如代理）无法安装在 Azure Stack 中心基础结构的组件中。 通过 PowerShell 或 REST API 进行与外部管理和安全软件的互操作性。

当你需要更高的访问级别，以便对未通过警报中介步骤解决的问题进行故障排除时，请联系 Microsoft 支持部门。 通过支持，有一种方法可以为更高级的操作提供对系统的临时完全管理访问权限。

## <a name="identity-considerations"></a>标识注意事项

### <a name="choose-identity-provider"></a>选择标识提供者
你需要考虑要用于 Azure Stack 中心部署的标识提供者，Azure AD 或 AD FS。 部署完成后，不能切换标识提供程序，而无需完全重新部署系统。 如果你没有 Azure AD 帐户，并且使用的是云解决方案提供商提供给你的帐户，且你决定切换提供程序并使用不同的 Azure AD 帐户，则必须与你的解决方案提供商联系，以便在你的 c 中为你重新部署解决方案.ost.

你的标识提供者选择与租户虚拟机（Vm）、标识系统、他们使用的帐户或是否可以加入 Active Directory 域无关。 它们是不同的。

可以在[Azure Stack 集线器集成系统连接模型一文](./azure-stack-connection-models.md)中了解有关选择标识提供者的详细信息。

### <a name="ad-fs-and-graph-integration"></a>AD FS 和图形集成
如果选择使用 AD FS 作为标识提供者来部署 Azure Stack 集线器，则必须通过联合身份验证信任将 Azure Stack 中心上的 AD FS 实例与现有 AD FS 实例集成。 此集成允许现有 Active Directory 林中的标识向 Azure Stack 中心中的资源进行身份验证。

你还可以将 Azure Stack 中心中的图形服务与现有 Active Directory 集成。 通过这种集成，你可以在 Azure Stack 中心管理基于角色的访问控制（RBAC）。 委托对资源的访问权限时，图形组件将使用 LDAP 协议查找现有 Active Directory 林中的用户帐户。

下图显示了集成的 AD FS 和图形通信流。
显示 AD FS 和图形通信流 ![关系图](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>许可模型
您必须确定要使用的授权模型。 可用选项取决于你是否部署了连接到 internet Azure Stack 集线器：
- 对于[连接的部署](azure-stack-connected-deployment.md)，可以选择 "即用即付" 或 "基于容量的许可"。 即用即付要求与 Azure 建立连接，以报告使用情况，然后通过 Azure 商业计费。 
- 如果部署与 internet[断开连接](azure-stack-disconnected-deployment.md)，则仅支持基于容量的许可。 

有关许可模式的详细信息，请参阅[Microsoft Azure Stack 集线器打包和定价](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。


## <a name="naming-decisions"></a>命名决策

你需要考虑如何计划 Azure Stack 集线器命名空间，尤其是区域名称和外部域名。 面向公众的终结点的 Azure Stack 中心部署的外部完全限定的域名（FQDN）是以下两个名称的组合： &lt;*区域*&gt;。&lt;*fqdn*&gt;。 例如， *east.cloud.fabrikam.com*。 在此示例中，可通过以下 Url 获取 Azure Stack 中心门户：

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> 为 Azure Stack 中心部署选择的区域名称必须是唯一的，并会显示在门户中的 "地址" 中。 

下表总结了这些域命名决定。

| 名称 | Description | 
| -------- | ------------- | 
|区域名称 | 第一个 Azure Stack 中心区域的名称。 此名称用作 Azure Stack 中心管理的公共虚拟 IP 地址（Vip）的 FQDN 的一部分。 通常，区域名称是物理位置标识符，如数据中心位置。<br><br>区域名称只能包含0-9 之间的字母和数字。 不允许使用特殊字符（如 `-`、`#`等）。| 
| 外部域名 | 具有面向外部 Vip 的终结点的域名系统（DNS）区域的名称。 用于这些公共 Vip 的 FQDN 中。 | 
| 专用（内部）域名 | 在用于基础结构管理 Azure Stack 集线器上创建的域（和内部 DNS 区域）的名称。
| | |

## <a name="certificate-requirements"></a>证书要求

对于部署，需要为面向公众的终结点提供安全套接字层（SSL）证书。 在高级别中，证书具有以下要求：

- 你可以使用单个通配符证书，也可以使用一组专用证书，然后仅对存储和 Key Vault 等终结点使用通配符。
- 证书可由公共受信任的证书颁发机构（CA）或客户管理的 CA 颁发。

有关部署 Azure Stack 中心需要哪些 PKI 证书以及如何获取这些证书的详细信息，请参阅[Azure Stack 中心公钥基础结构证书要求](azure-stack-pki-certs.md)。  


> [!IMPORTANT]
> 提供的 PKI 证书信息应该用作常规指导。 在为 Azure Stack 中心获取任何 PKI 证书之前，请与 OEM 硬件合作伙伴合作。 它们将提供更详细的证书指南和要求。


## <a name="time-synchronization"></a>时间同步
您必须选择用于同步 Azure Stack 集线器的特定时间服务器。 时间同步对于 Azure Stack 中心及其基础结构角色至关重要，因为它用于生成 Kerberos 票证。 Kerberos 票证用于彼此验证内部服务。

您必须指定时间同步服务器的 IP。 尽管基础结构中的大多数组件都可以解析 URL，但某些组件仅支持 IP 地址。 如果你使用的是 "断开连接的部署" 选项，则必须在公司网络上指定一个时间服务器，确定你可以从 Azure Stack 集线器中的基础结构网络访问。

## <a name="connect-azure-stack-hub-to-azure"></a>将 Azure Stack 中心连接到 Azure

对于混合云方案，你需要计划如何将 Azure Stack 集线器连接到 Azure。 可以通过两种方法将 Azure Stack 集线器中的虚拟网络连接到 Azure 中的虚拟网络：

- **站点到站点**：通过 IPsec 的虚拟专用网络（VPN）连接（ike V1 和 ike v2）。 此类型的连接需要 VPN 设备或路由和远程访问服务（RRAS）。 有关 Azure 中的 VPN 网关的详细信息，请参阅[关于 Vpn 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 通过此隧道进行的通信已加密，并且安全可靠。 但是，带宽限制为隧道的最大吞吐量（100-200 Mbps）。

- **出站 nat**：默认情况下，Azure Stack 集线器中的所有 vm 都可以通过出站 NAT 连接到外部网络。 在 Azure Stack Hub 中创建的每个虚拟网络将获取分配给它的公共 IP 地址。 无论 VM 是直接分配到公共 IP 地址，还是位于具有公共 IP 地址的负载均衡器后面，都可以使用虚拟网络的 VIP 通过出站 NAT 进行出站访问。 此方法仅适用于由 VM 启动的通信，并针对外部网络（internet 或 intranet）。 它不能用于从外部与 VM 通信。

### <a name="hybrid-connectivity-options"></a>混合连接选项

对于混合连接，考虑要提供的部署类型以及部署位置，这一点很重要。 你需要考虑是否需要隔离每个租户的网络流量，以及你是使用 intranet 部署还是 internet 部署。

- **单租户 Azure Stack 中心**Azure Stack：从网络的角度来看，它至少是一个租户。 可能有多个租户订阅，但与任何 intranet 服务一样，所有流量都通过相同的网络传输。 来自一个订阅的网络流量将通过与另一个订阅相同的网络连接传输，而不需要通过加密隧道进行隔离。

- **多租户 Azure Stack 集线器**：一种 Azure Stack 的中心部署，其中每个租户订阅的流量被绑定到 Azure Stack 中心外部的网络，必须与其他租户的网络流量隔离。
 
- **Intranet 部署**：位于公司 Intranet 上的 Azure Stack 中心部署，通常在专用 IP 地址空间和一个或多个防火墙后面。 公共 IP 地址不是真正的公共 IP 地址，因为它们不能直接通过公共 internet 路由。

- **Internet 部署**：连接到公共 Internet 并为公共 VIP 范围使用可通过 Internet 路由的公共 IP 地址的 Azure Stack 中心部署。 部署仍可以位于防火墙后面，但公共 VIP 范围可从公共 internet 和 Azure 直接访问。
 
下表总结了混合连接方案的优点、缺点和用例。

| 方案 | 连接方法 | 优点 | 缺点 | 适用于 |
| -- | -- | --| -- | --|
| 单个租户 Azure Stack 中心、intranet 部署 | 出站 NAT | 更好的带宽以实现更快的传输。 易于实现;不需要网关。 | 未加密的流量;堆栈外无隔离或加密。 | 所有租户都平等信任的企业部署。<br><br>具有 azure ExpressRoute 线路到 Azure 的企业。 |
| 多租户 Azure Stack 集线器、intranet 部署 | 站点到站点 VPN | 从租户 VNet 到目标的流量是安全的。 | 带宽限制为站点到站点 VPN 隧道。<br><br>需要虚拟网络中的网关和目标网络上的 VPN 设备。 | 必须从其他租户保护某些租户流量的企业部署。 |
| 单个租户 Azure Stack 集线器，internet 部署 | 出站 NAT | 更好的带宽以实现更快的传输。 | 未加密的流量;堆栈外无隔离或加密。 | 托管方案，其中租户获取其自己的 Azure Stack 中心部署和 Azure Stack 中心环境的专用线路。 例如，ExpressRoute 和多协议标签交换（MPLS）。
| 多租户 Azure Stack 集线器，internet 部署 | 站点到站点 VPN | 从租户 VNet 到目标的流量是安全的。 | 带宽限制为站点到站点 VPN 隧道。<br><br>需要虚拟网络中的网关和目标网络上的 VPN 设备。 | 托管方案，在此方案中，提供程序想要提供一个多租户云，其中租户彼此不信任并且必须加密通信。
|  |  |  |  |  |

### <a name="using-expressroute"></a>使用 ExpressRoute

可以通过[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)为单租户 intranet 和多租户方案将 Azure Stack 集线器连接到 Azure。 你将需要通过[连接提供商](https://docs.microsoft.com/azure/expressroute/expressroute-locations)预配的 ExpressRoute 线路。

下图显示了单租户方案的 ExpressRoute （其中 "客户的连接" 是 ExpressRoute 线路）。

![显示单租户 ExpressRoute 方案的示意图](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

下图显示了多租户方案的 ExpressRoute。

![显示多租户 ExpressRoute 方案的示意图](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>外部监视
若要从 Azure Stack 中心部署和设备获取所有警报的单一视图，并将警报集成到用于票证的现有 IT 服务管理工作流，可将[Azure Stack 中心与外部数据中心监视解决方案集成](azure-stack-integrate-monitor.md)。

Azure Stack 中心解决方案附带的硬件生命周期主机是 Azure Stack 中心外的一台计算机，该计算机运行 OEM 供应商提供的硬件管理工具。 你可以使用这些工具或其他与你的数据中心内的现有监视解决方案直接集成的解决方案。

下表汇总了当前可用选项的列表。

| 区域 | 外部监视解决方案 |
| -- | -- |
| Azure Stack 中心软件 | [适用于 Operations Manager 的 Azure Stack 集线器管理包](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios 插件](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>基于 REST 的 API 调用 | 
| 物理服务器（通过 IPMI 的 Bmc） | OEM 硬件-Operations Manager 供应商管理包<br>OEM 硬件供应商提供的解决方案<br>硬件供应商 Nagios 插件。<br>OEM 合作伙伴支持的监视解决方案（包括） | 
| 网络设备（SNMP） | Operations Manager 网络设备发现<br>OEM 硬件供应商提供的解决方案<br>Nagios 切换插件 |
| 租户订阅运行状况监视 | [适用于 Windows Azure 的 System Center 管理包](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

请注意以下要求：
- 你使用的解决方案必须无代理。 无法在 Azure Stack Hub 组件内安装第三方代理。 
- 如果要使用 System Center Operations Manager，则需要 Operations Manager 2012 R2 或 Operations Manager 2016。

## <a name="backup-and-disaster-recovery"></a>备份和灾难恢复

规划备份和灾难恢复涉及到规划适用于托管 IaaS Vm 和 PaaS 服务的底层 Azure Stack 中心基础结构以及租户应用和数据。 请单独规划这些任务。

### <a name="protect-infrastructure-components"></a>保护基础结构组件

可以将[Azure Stack 中心](azure-stack-backup-back-up-azure-stack.md)基础结构组件备份到指定的 SMB 共享：

- 你将需要在现有的基于 Windows 的文件服务器或第三方设备上具有外部 SMB 文件共享。
- 使用此相同的共享来备份网络交换机和硬件生命周期主机。 你的 OEM 硬件供应商将帮助提供有关备份和还原这些组件的指南，因为这些组件是 Azure Stack 中心外部的。 你负责根据 OEM 供应商的建议运行备份工作流。

如果发生灾难性数据丢失，可以使用基础结构备份来重新设定部署数据的种子，如： 

- 部署输入和标识符
- 服务帐户
- CA 根证书
- fFederated 资源（在断开连接的部署中）
- 计划、产品/服务、订阅和配额
- RBAC 策略和角色分配
- Key Vault 机密

### <a name="protect-tenant-apps-on-iaas-vms"></a>保护 IaaS Vm 上的租户应用

Azure Stack 中心不会备份租户应用和数据。 你必须规划对 Azure Stack 中心外部的目标的备份和灾难恢复保护。 租户保护是租户驱动的活动。 对于 IaaS Vm，租户可以使用来宾内技术来保护文件文件夹、应用程序数据和系统状态。 然而，作为企业或服务提供商，你可能想要在同一数据中心内或在云中的外部提供备份和恢复解决方案。

若要备份 Linux 或 Windows IaaS Vm，你必须使用具有对来宾操作系统的访问权限的备份产品来保护文件、文件夹、操作系统状态和应用程序数据。 你可以使用 Azure 备份、System Center 数据中心保护管理器或支持的第三方产品。

若要将数据复制到辅助位置并在发生灾难时协调应用程序故障转移，可以使用 Azure Site Recovery 或支持的第三方产品。 另外，支持本机复制的应用（如 Microsoft SQL Server）可以将数据复制到应用运行的另一个位置。

## <a name="learn-more"></a>了解更多

- 有关用例、购买、合作伙伴和 OEM 硬件供应商的信息，请参阅[Azure Stack 中心](https://azure.microsoft.com/overview/azure-stack/)产品页。
- 有关 Azure Stack 中心集成系统的路线图和地域可用性的信息，请参阅白皮书： [Azure Stack 中心： Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 

## <a name="next-steps"></a>后续步骤
[Azure Stack 中心部署连接模型](azure-stack-connection-models.md)
