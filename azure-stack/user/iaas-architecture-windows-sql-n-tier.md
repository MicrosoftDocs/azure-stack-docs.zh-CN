---
title: SQL Server 的 Azure Stack 集线器上的 Windows N 层应用程序 |Microsoft Docs
description: 了解如何使用 SQL Server 在 Azure Stack 集线器上运行 Windows N 层应用程序。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 2c8eb46ecf53ba0bcab5d38ebe6a7e9aac79708e
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75815436"
---
# <a name="windows-n-tier-application-on-azure-stack-hub-with-sql-server"></a>SQL Server 的 Azure Stack 集线器上的 Windows N 层应用程序

此参考体系结构演示了如何使用 Windows 上的 SQL Server 在数据层上部署为[N 层](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/n-tier)应用程序配置的虚拟机（vm）和虚拟网络。 

## <a name="architecture"></a>体系结构

该体系结构具有以下组件。

![](./media/iaas-architecture-windows-sql-n-tier/image1.png)

## <a name="general"></a>常规

-   资源组。 [资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)用于对 Azure 资源进行分组，以便可以按生存期、所有者或其他条件对其进行管理。

-   **可用性集。** 可用性集是用于提供 VM 冗余和可用性的数据中心配置。 Azure Stack 集线器戳记中的此配置可确保在计划内或计划外维护事件期间，至少有一个虚拟机可用。 Vm 放置在一个可用性集中，可跨多个容错域（Azure Stack 集线器主机）进行传播

## <a name="networking-and-load-balancing"></a>网络和负载均衡

-   **虚拟网络和子网**。 每个 Azure VM 都部署到一个可细分为子网的虚拟网络中。 为每个层创建一个单独的子网。

-   **第7层负载均衡器。** 由于应用程序网关在 Azure Stack 集线器上尚不可用，因此[Azure Stack 中心市场位置](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1908)有一些替代项，例如： [KEMP LoadMaster 负载均衡器 ADC 内容交换机](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [f5 大 IP 虚拟版](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best)或[A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **负载均衡器**。 使用[Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)将网络流量从 web 层分发到业务层，并从业务层分发到 SQL Server。

-   **网络安全组**（nsg）。 使用 Nsg 限制虚拟网络中的网络流量。 例如，在此处显示的三层体系结构中，数据库层不接受来自 Web 前端的流量，仅接受来自业务层和管理子网的流量。

-   **DNS**。 Azure Stack 中心未提供自己的 DNS 托管服务，因此请使用添加中的 DNS 服务器。

**虚拟机**

-   **SQL Server Always On 可用性组**。 通过启用复制和故障转移，在数据层提供高可用性。 它使用 Windows Server 故障转移群集 (WSFC) 技术进行故障转移。

-   **Active Directory 域服务 (AD DS) 服务器**。 故障转移群集及其关联的群集角色的计算机对象在 Active Directory 域服务 (AD DS) 中创建。 在同一虚拟网络中的 Vm 上设置 AD DS 服务器是将其他 Vm 加入 AD DS 的首选方法。 还可以通过使用 VPN 连接将虚拟网络连接到企业网络，将 Vm 加入现有企业 AD DS。 对于这两种方法，都需要将虚拟网络 DNS 更改为 AD DS 的 DNS 服务器（位于虚拟网络或现有企业网络中）以解析 AD DS 域 FQDN。

-   **云见证**。 故障转移群集要求其节点的半数以上处于运行状态，这称为“建立仲裁”。 如果群集只有两个节点，则网络分区之后，每个节点都会认为自己是主节点。 在这种情况下，需要使用见证来打破“僵持”局面，建立仲裁。 见证是一种可以充当僵持局面打破者并建立仲裁的资源，例如共享磁盘。 云见证是一种使用 Azure Blob 存储的见证。 若要详细了解仲裁的概念，请参阅[了解群集和池仲裁](https://docs.microsoft.com/windows-server/storage/storage-spaces/understand-quorum)。 有关云见证的详细信息，请参阅[部署故障转移群集的云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。 在 Azure Stack 中心，云见证终结点不同于全局 Azure。 

它可能如下所示：

- 对于全球 Azure：  
  `https://mywitness.blob.core.windows.net/`

- 对于 Azure Stack 中心：  
  `https://mywitness.blob.<region>.<FQDN>`

-   **Jumpbox**。 也称为[守护主机](https://en.wikipedia.org/wiki/Bastion_host)。 网络上的一个安全 VM，管理员使用它来连接到其他 VM。 Jumpbox 中的某个 NSG 只允许来自安全列表中的公共 IP 地址的远程流量。 该 NSG 应允许远程桌面 (RDP) 流量。

## <a name="recommendations"></a>建议

你的要求可能不同于此处描述的体系结构。 请使用以下建议作为入手点。

### <a name="virtual-machines"></a>虚拟机

有关配置 Vm 的建议，请参阅[在 Azure Stack 集线器上运行 WINDOWS VM](iaas-architecture-vm-windows.md)。

### <a name="virtual-network"></a>虚拟网络

创建虚拟网络时，请确定每个子网中的资源所需的 IP 地址。 使用[CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法为所需的 IP 地址指定一个足够大的子网掩码和一个网络地址范围。 使用位于标准[专用 IP 地址块](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)（10.0.0.0/8、172.16.0.0/12 和 192.168.0.0/16）中的地址空间。

选择一个不与本地网络重叠的地址范围，以防需要在虚拟网络和本地网络之间设置网关，以备今后使用。 创建虚拟网络后，将无法更改地址范围。

在设计子网时一定要牢记功能和安全要求。 同一层或同一角色中的所有 VM 应当置于同一子网，这可能是一个安全边界。 有关设计虚拟网络和子网的详细信息，请参阅[规划和设计 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)。

### <a name="load-balancers"></a>负载均衡器

不要将 VM 直接向 Internet 公开，而是改为给每个 VM 提供专用 IP 地址。 客户端使用与第7层负载均衡器关联的公共 IP 地址进行连接。

定义用于将网络流量定向到 VM 的负载均衡器规则。 例如，若要启用 HTTP 流量，请将前端配置中的端口 80 映射到后端地址池上的端口 80。 当客户端向端口80发送 HTTP 请求时，负载均衡器将使用包含源 IP 地址的[哈希算法](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#load-balancer-concepts)选择后端 IP 地址。 客户端请求将在后端地址池中的所有 VM 之间分配。

### <a name="network-security-groups"></a>网络安全组

使用 NSG 规则限制各个层之间的流量。 在上面显示的三层体系结构中，Web 层不直接与数据库层进行通信。 若要强制实施此规则，数据库层应阻止来自 web 层子网的传入流量。

1.  拒绝来自虚拟网络的所有入站流量。 （使用规则中的 VIRTUAL_NETWORK 标记。）

2.  允许来自业务层子网的入站流量。

3.  允许来自数据库层子网本身的入站流量。 此规则允许在数据库 VM 之间通信，这是进行数据库复制和故障转移所必需的。

4.  允许来自 Jumpbox 子网的 RDP 流量（端口 3389）。 此规则允许管理员从 jumpbox 连接到数据库层。

创建优先级高于第一个规则的规则2–4，因此它们将重写。

## <a name="sql-server-always-on-availability-groups"></a>SQL Server Always On 可用性组

建议[Always On 可用性组](https://msdn.microsoft.com/library/hh510230.aspx)SQL Server 高可用性。 在 Windows Server 2016 之前，Always On 可用性组需要一个域控制器，并且可用性组中的所有节点必须在同一 AD 域中。

对于 VM 层高可用性，所有 SQL Vm 应位于可用性集中。

其他层通过[可用性组侦听器](https://msdn.microsoft.com/library/hh213417.aspx)连接到数据库。 该侦听程序使得 SQL 客户端能够在不知道 SQL Server 物理实例名称的情况下进行连接。 访问数据库的 VM 必须加入域。 客户端（在本例中为另一个层）使用 DNS 将该侦听程序的虚拟网络名称解析为 IP 地址。

如下所述配置 SQL Server Always On 可用性组：

1.  创建一个 Windows Server 故障转移群集 (WSFC) 群集、一个 SQL Server Always On 可用性组和一个主要副本。 有关详细信息，请参阅[Always On 可用性组的入门](https://msdn.microsoft.com/library/gg509118.aspx)。

2.  创建一个具有静态专用 IP 地址的内部负载均衡器。

3.  创建一个可用性组侦听程序，并将该侦听程序的 DNS 名称映射到一个内部负载均衡器的 IP 地址。

4.  为 SQL Server 侦听端口（默认情况下为 TCP 端口 1433）创建一个负载均衡器规则。 该负载均衡器规则必须启用*浮动 IP*，也称为“直接服务器返回”。 这将导致 VM 直接回复客户端，从而实现到主要副本的直接连接。

> [!Note]
> 当启用了浮动 IP 时，前端端口号必须与负载均衡器规则中的后端端口号相同。

当 SQL 客户端尝试连接时，负载均衡器会将连接请求路由到主要副本。 如果发生到其他副本的故障转移，则负载均衡器会自动将新请求路由到新的主要副本。 有关详细信息，请参阅[Configure a ILB 侦听器 for SQL Server Always On Availability Groups](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)。

在故障转移期间，现有的客户端连接将关闭。 在故障转移完成后，新连接将被路由到新的主要副本。

如果你的应用程序比写入更多，则可以将一些只读查询卸载到辅助副本。 请参阅[使用侦听器连接到只读辅助副本（只读路由）](https://technet.microsoft.com/library/hh213417.aspx#ConnectToSecondary)。

通过强制对可用性组进行[手动故障转移](https://msdn.microsoft.com/library/ff877957.aspx)来测试部署。

对于 SQL 性能优化，还可以参阅[sql server 最佳实践一文，以优化 Azure Stack 集线器中的性能](https://docs.microsoft.com/azure-stack/user/azure-stack-sql-server-vm-considerations)。

**Jumpbox**

不要允许通过公共 Internet 对运行应用程序工作负荷的 VM 进行 RDP 访问。 相反，对这些 Vm 的所有 RDP 访问都应经过 jumpbox。 管理员登录到 jumpbox，然后从 jumpbox 登录到其他 VM。 Jumpbox 允许来自 Internet 的 RDP 流量，但仅允许来自已知的安全 IP 地址的流量。

Jumpbox 的性能要求非常低，因此请选择一个较小的 VM 大小。 为 jumpbox 创建一个[公共 IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。 将 jumpbox 放在与其他 Vm 相同的虚拟网络中，但将其放在单独的管理子网中。

若要确保 Jumpbox 的安全，请添加一项 NSG 规则，仅允许来自一组安全的公共 IP 地址的 RDP 连接。 为其他子网配置 NSG 以允许来自管理子网的 RDP 流量。

## <a name="scalability-considerations"></a>可伸缩性注意事项

### <a name="scale-sets"></a>规模集

对于 web 和企业层，请考虑使用[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview)，而不是部署单独的 vm。 规模集可以轻松地部署和管理一组相同的 Vm。 如果需要快速横向扩展 Vm，请考虑规模集。

有两种基本方法可用来配置规模集中部署的 VM：

-   在部署 VM 后使用扩展对其进行配置。 使用此方法时，启动新 VM 实例的所需时间可能会长于启动不带扩展的 VM 的所需时间。

-   使用自定义磁盘映像部署[托管磁盘](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations)。 此选项的部署速度可能更快。 但是，它要求将映像保持最新。

有关详细信息，请参阅[规模集的设计注意事项](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview)。 对于 Azure Stack 中心，这一设计方面的考虑通常是如此，但有一些注意事项：

-   Azure Stack 集线器上的虚拟机规模集不支持过度预配或滚动升级。

-   无法在 Azure Stack 集线器上自动缩放虚拟机规模集。

-   强烈建议在 Azure Stack 集线器上使用托管磁盘，而不是虚拟机规模集的非托管磁盘

-   目前 Azure Stack 集线器上存在 700 VM 限制，这是所有 Azure Stack 中心基础结构 Vm、单个 Vm 和规模集实例的帐户。

## <a name="subscription-limits"></a>订阅限制

每个 Azure Stack 中心租户订阅都有默认限制，包括 Azure Stack 中心操作员配置的每个区域的最大 Vm 数。 有关详细信息，请参阅[Azure Stack 中心服务、计划、产品/服务、订阅概述](https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview)。 另请参阅[Azure Stack 集线器中的配额类型](https://docs.microsoft.com/azure-stack/operator/azure-stack-quota-types)。

## <a name="security-considerations"></a>安全注意事项

虚拟网络是 Azure 中的流量隔离边界。 默认情况下，一个虚拟网络中的 Vm 无法直接与其他虚拟网络中的 Vm 通信。

**NSG**。 使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)（nsg）限制进出 internet 的流量。 有关详细信息，请参阅[Microsoft 云服务和网络安全](https://docs.microsoft.com/azure/best-practices-network-security)。

**外围网络**。 请考虑添加一个网络虚拟设备 (NVA) 以在 Internet 与 Azure 虚拟网络之间创建一个外围网络。 NVA 是虚拟设备的一个通用术语，可以执行与网络相关的任务，例如防火墙、包检查、审核和自定义路由。

**加密**。 加密静态的敏感数据，并使用[Azure Stack 中心的 Key Vault](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal)来管理数据库加密密钥。 有关详细信息，请参阅 [为 Azure VM 上的 SQL Server 配置 Azure 密钥保管库集成](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-sql-keyvault)。 另外，建议将应用程序机密（例如数据库连接字符串）也存储在 Key Vault 中。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
