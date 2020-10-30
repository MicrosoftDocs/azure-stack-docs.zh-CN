---
title: 规划软件定义的网络基础结构
description: 本主题提供有关如何规划软件定义的网络 (SDN) 基础结构部署的信息。
manager: grcusanz
ms.topic: conceptual
ms.assetid: ea7e53c8-11ec-410b-b287-897c7aaafb13
ms.author: anpaul
author: AnirbanPaul
ms.date: 10/28/2020
ms.openlocfilehash: d75e22814afcb9610bdd1f9af3824d3e12e3199b
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064559"
---
# <a name="plan-a-software-defined-network-infrastructure"></a>规划软件定义的网络基础结构

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019、Windows Server（半年频道）、Windows Server 2016

了解针对软件定义的网络 (SDN) 基础结构的部署规划（包括硬件和软件先决条件）。 本主题包括针对物理和逻辑网络配置、路由、网关、网络硬件等的规划要求。 还包括有关扩展 SDN 基础结构和使用分阶段部署的注意事项。

   > [!NOTE]
   > 延伸 (多站点) 群集不支持 SDN。

## <a name="prerequisites"></a>先决条件
SDN 基础结构有几个硬件和软件先决条件，包括：
- 安全组和动态 DNS 注册。 必须为网络控制器部署准备好数据中心，这需要一组虚拟机 (VM)。 必须先配置安全组和动态 DNS 注册，然后才能部署网络控制器。

    若要了解有关数据中心的网络控制器部署的详细信息，请参阅[部署网络控制器的要求](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)。

- 物理网络。 需要访问物理网络设备以配置虚拟局域网 (VLAN)、路由和边界网关协议 (BGP)。 本主题提供有关手动交换机配置的说明，以及使用第 3 层交换机/路由器上的 BGP 对等互连或是路由和远程访问服务器 (RRAS) VM 的选项。

- 物理计算主机。 这些主机运行 Hyper-V，是托管 SDN 基础结构和租户 VM 所必需的。 为了获得最佳性能，在这些主机中需要特定网络硬件，如 [SDN 硬件要求](system-requirements.md#sdn-hardware-requirements)中所述。

## <a name="physical-and-logical-network-configuration"></a>物理和逻辑网络配置
每个物理计算主机都需要通过一个或多个连接到物理交换机端口的网络适配器建立网络连接。 第 2 层 [VLAN](https://en.wikipedia.org/wiki/Virtual_LAN) 支持划分为多个逻辑网段的网络。

>[!TIP]
>在访问模式或未标记的情况下，将 VLAN 0 用于逻辑网络。

>[!IMPORTANT]
>Windows Server 2016 软件定义的网络支持将 IPv4 寻址用于基础和覆盖。 不支持 IPv6。 Windows Server 2019 支持 IPv4 和 IPv6 寻址。

### <a name="logical-networks"></a>Logical networks
此部分介绍管理逻辑网络和 Hyper-V 网络虚拟化 (HNV) 提供程序逻辑网络的 SDN 基础结构规划要求。 它包含有关如何预配其他逻辑网络以使用网关和软件负载均衡器 (SLB) 的详细信息，以及一个示例网络拓扑。

#### <a name="management-and-hnv-provider"></a>管理和 HNV 提供程序
所有物理计算主机都必须访问管理逻辑网络和 HNV 提供程序逻辑网络。 出于 IP 地址规划目的，每个物理计算主机都必须至少有一个从管理逻辑网络分配的 IP 地址。 网络控制器需要来自此网络的保留 IP 地址作为表述性状态转移 (REST) IP 地址。

HNV 提供商网络充当东部/西部（内部-内部）租户流量、北部/南部（外部-内部）租户流量的基础物理网络，以及用于与物理网络交换 BGP 对等互连信息。

DHCP 服务器可以为管理网络自动分配 IP 地址，你也可以手动分配静态 IP 地址。 对于来自 IP 地址池的各个 Hyper-V 主机，SDN 堆栈会自动为 HNV 提供程序逻辑网络分配 IP 地址。 网络控制器指定并管理 IP 地址池。

>[!NOTE]
>仅在网络控制器主机代理收到特定租户 VM 的网络策略之后，网络控制器才将 HNV 提供程序 IP 地址分配给物理计算主机。

| 如果...                                                    | 则...                                               |
| :------------------------------------------------------- | :---------------------------------------------------- |
| 逻辑网络使用 VLAN，                          | 物理计算主机必须连接到有权访问 VLAN 的中继交换机端口。 请务必注意，计算机主机上的物理网络适配器不得激活任何 VLAN 筛选。|
| 你在使用交换嵌入式组合 (SET) 并具有多个网络接口卡 (NIC) 组成员（例如网络适配器），| 必须将该特定主机的所有 NIC 组成员连接到相同的第 2 层广播域。|
| 物理计算主机在运行其他基础结构 VM，如网络控制器、SLB/多路复用器 (MUX) 或网关， | 确保管理逻辑网络为每个托管 VM 提供足够的 IP 地址。 此外，确保 HNV 提供程序逻辑网络具有足够的 IP 地址可分配给每个 SLB/MUX 和网关基础结构 VM。 尽管 IP 保留由网络控制器管理，但由于不可用而无法保留新 IP 地址可能会导致网络上存在重复的 IP 地址。|

有关可用于在 Microsoft SDN 部署中虚拟化网络的 Hyper-V 网络虚拟化 (HNV) 的信息，请参阅 [Hyper-V 网络虚拟化](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization)。

#### <a name="gateways-and-the-software-load-balancer-slb"></a>网关和软件负载均衡器 (SLB)
需要创建和预配其他逻辑网络，才能使用网关和 SLB。 确保为这些网络获取正确的 IP 前缀、VLAN ID 和网关 IP 地址。

|                                |                     |
| :----------------------------- | :------------------ |
| 公共 VIP 逻辑网络 | 公共虚拟 IP (VIP) 逻辑网络必须使用可在云环境外部路由的 IP 子网前缀（通常可通过 Internet 路由）。 这些是外部客户端用于访问虚拟网络中的资源的前端 IP 地址，包括站点到站点网关的前端 VIP。 不需要为此网络分配 VLAN。 |
| **专用 VIP 逻辑网络** | 不需要专用 VIP 逻辑网络即可在云外部进行路由。 这是因为只有可以从内部云客户端访问的 VIP 才会使用它，如专用服务。 不需要为此网络分配 VLAN。 |
| GRE VIP 逻辑网络 | 通用路由封装 (GRE) VIP 网络是仅为定义 VIP 才存在的子网。 VIP 会分配给在 SDN 结构上运行的、用于站点到站点 (S2S) GRE 连接类型的网关 VM。 不需要在物理交换机或路由器中预配置此网络，或向它分配 VLAN。 |

#### <a name="sample-network-topology"></a>示例网络拓扑
更改环境的示例 IP 子网前缀和 VLAN ID。

| 网络名称 | 子网 | Mask | Trunk 上的 VLAN ID | 网关 | 保留（示例） |
| :----------------------- | :------------ | :------- | :---------------------------- | :-------------- | :------------------------------------------- |
| 管理              | 10.184.108.0 |    24   |          7                   | 10.184.108.1   | 10.184.108.1 - 路由器<br> 10.184.108.4 - 网络控制器<br> 10.184.108.10 - 计算主机 1<br> 10.184.108.11 - 计算主机 2<br> 10.184.108.X - 计算主机 X |
| HNV 提供程序             |  10.10.56.0  |    23    |          11                |  10.10.56.1    | 10.10.56.1 - 路由器<br> 10.10.56.2 - SLB/MUX1<br> 10.10.56.5 - 网关 1 |
| 公共 VIP               |  41.40.40.0  |    27    |          NA                |  41.40.40.1    | 41.40.40.1 - 路由器<br> 41.40.40.3 - IPSec S2S VPN VIP |
| 专用 VIP              |  20.20.20.0  |    27    |          NA                |  20.20.20.1    | 20.20.20.1 - 默认 GW（路由器） |
| GRE VIP                  |  31.30.30.0  |    24    |          NA                |  31.30.30.1    | 31.30.30.1 - 默认 GW |

## <a name="routing-infrastructure"></a>路由基础结构
VIP 子网的路由信息（如下一个跃点）由 SLB/MUX 和远程访问服务器 (RAS) 网关使用内部 BGP 对等互连播发到物理网络。 不会为 VIP 逻辑网络分配 VLAN，也不会在第 2 层交换机（如架顶式交换机）中对它们进行预配置。

需要在 SDN 基础结构用于接收由 SLB/MUX 和 RAS 网关播发的 VIP 逻辑网络路由的路由器上创建 BGP 对等机。 BGP 对等互连只需单向进行（从 SLB/MUX 或 RAS 网关到外部 BGP 对等机）。 在路由的第一层上，可以使用静态路由或其他动态路由协议，例如开放最短路径优先 (OSPF)。 但是如前所述，VIP 逻辑网络的 IP 子网前缀确实需要可从物理网络路由到外部 BGP 对等机。

BGP 对等互连通常在作为网络基础结构一部分的托管交换机或路由器中进行配置。 还可以在采用“仅路由”模式安装了 RAS 角色的 Windows Server 上配置 BGP 对等机。 网络基础结构中的 BGP 路由器对等机必须配置为使用其自己的自治系统编号 (ASN)，并允许从分配给 SDN 组件（SLB/MUX 和 RAS 网关）的 ASN 进行对等互连。

必须从物理路由器或是控制该路由器的网络管理员处获取以下信息：
- 路由器 ASN
- 路由器 IP 地址

>[!NOTE]
>SLB/MUX 不支持四字节 ASN。 必须向 SLB/MUX 及其连接到的路由器分配两字节 ASN。 可以在环境中的其他位置使用四字节 ASN。

你或网络管理员必须配置 BGP 路由器对等机，以接受来自 RAS 网关和 SLB MUX 所使用的 HNV 提供程序逻辑网络的 ASN 和 IP 地址或子网地址的连接。

有关详细信息，请参阅[边界网关协议 (BGP)](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp)。

## <a name="default-gateways"></a>默认网关
配置为连接到多个网络的计算机（例如物理主机、SLB/MUX 和网关 VM）必须仅配置一个默认网关。 将以下默认网关用于主机和基础结构 VM：
- 对于 Hyper-V 主机，使用管理网络作为默认网关。
- 对于网络控制器 VM，使用管理网络作为默认网关。
- 对于 SLB/MUX VM，使用管理网络作为默认网关。
- 对于网关 VM，使用 HNV 提供程序网络作为默认网关。 这应在网关 VM 的前端 NIC 上进行设置。

## <a name="switches-and-routers"></a>交换机和路由器
为了帮助配置物理交换机或路由器，在 [Microsoft SDN GitHub 存储库](https://github.com/microsoft/SDN/tree/master/SwitchConfigExamples)中提供了适用于各种交换机型号和供应商的一组示例配置文件。 提供了一个 readme 文件和经过测试的命令行界面 (CLI) 为特定交换机提供命令。

有关详细的交换机和路由器要求，请参阅 [SDN 硬件要求](system-requirements.md#sdn-hardware-requirements)。

## <a name="compute"></a>计算
所有 Hyper-V 主机都必须安装适当的操作系统，针对 Hyper-V 进行启用，以及使用至少有一个连接到管理逻辑网络的物理适配器的外部 Hyper-V 虚拟交换机。 主机必须可通过分配给管理主机 vNIC 的管理 IP 地址来访问。

可以使用与 Hyper-V 兼容的任何存储类型（共享或本地）。

> [!TIP]
> 可以方便地对所有虚拟交换机使用相同名称，但这并不是必需的。 如果规划使用脚本进行部署，请参阅 config.psd1 文件中与 `vSwitchName` 变量关联的注释。

### <a name="host-compute-requirements"></a>主机计算要求
下面显示在示例部署中使用的四个物理主机的最低硬件和软件要求。

主机|硬件要求|软件要求|
--------|-------------------------|-------------------------
|物理 Hyper-V 主机|四核 2.66 GHz CPU<br> 32 GB RAM<br> 300 GB 磁盘空间<br> 1 Gb/s（或更快）的物理网络适配器|操作系统：在<br> 本主题开头的 "适用于"。<br> 安装 Hyper-V 角色|

### <a name="sdn-infrastructure-vm-role-requirements"></a>SDN 基础结构 VM 角色要求
下面显示 VM 角色的要求。

角色|vCPU 要求|内存需求|磁盘要求|
--------|-----------------------------|-----------------------|--------------------------
|网络控制器（三个节点）|4 个 vCPU|最少 4 GB<br> （建议 8 GB）|75 GB 用于操作系统驱动器
|SLB/MUX（三个节点）|8 个 vCPU|建议 8 GB|75 GB 用于操作系统驱动器
|RDS 网关<br> （三个节点网关组成的<br> 单个池，两个活动，一个被动）|8 个 vCPU|建议 8 GB|75 GB 用于操作系统驱动器
|用于 SLB/MUX 对等互连的<br> RAS 网关 BGP 路由器<br> （或者使用 ToR 交换机<br> 作为 BGP 路由器）|2 个 vCPU|2 GB|75 GB 用于操作系统驱动器|

如果使用 System Center - Virtual Machine Manager (VMM) 进行部署，则 VMM 和其他非 SDN 基础结构需要附加基础结构 VM 资源。 若要了解详细信息，请参阅 [System Center Virtual Machine Manager 的系统要求](/system-center/vmm/system-requirements?preserve-view=true&view=sc-vmm-2019)。

## <a name="extending-your-infrastructure"></a>扩展基础结构
基础结构的大小调整和资源要求取决于规划托管的租户工作负载 VM。 基础结构 VM（例如：网络控制器、SLB、网关等）的 CPU、内存和磁盘要求在上表中定义。 可以添加更多基础结构 VM 以根据需要进行缩放。 但是，在 Hyper-V 主机上运行的任何租户 VM 都有自己的 CPU、内存和磁盘要求，你必须考虑这些要求。

当租户工作负载 VM 开始在物理 Hyper-V 主机上消耗过多资源时，可以通过添加其他物理主机来扩展基础结构。 可以使用 Windows Admin Center、VMM 或 PowerShell 脚本，通过网络控制器创建新的服务器资源。 使用的方法取决于最初部署基础结构的方式。 如果需要为 HNV 提供程序网络添加其他 IP 地址，则可以创建主机可以使用的新逻辑子网（具有对应的 IP 池）。

## <a name="phased-deployment"></a>分阶段部署
根据你的要求，你可能需要部署 SDN 基础结构的子集。 例如，如果你只想在数据中心托管客户工作负载，不需要外部通信，则可以部署网络控制器，而跳过部署 SLB/MUX 和网关 VM。 下面介绍 SDN 基础结构分阶段部署的网络功能基础结构要求。

功能|部署要求|网络要求|
--------|-------------------------|-------------------------
|逻辑网络管理<br> 访问控制列表 (ACL)（适用于基于 VLAN 的网络）<br> 服务质量 (QoS)（适用于基于 VLAN 的网络）<br>|网络控制器|无|
|虚拟网络<br> 用户定义的路由<br> ACL（适用于虚拟网络）<br> 加密子网<br> QoS（适用于虚拟网络）<br> 虚拟网络对等互连|网络控制器|HNV PA VLAN、子网、路由器|
|入站/出站 NAT<br> 负载平衡|网络控制器<br> SLB/MUX|HNV PA 网络上的 BGP<br> 专用和公共 VIP 子网|
|GRE 网关连接|网络控制器<br> 网关|HNV PA 网络上的 BGP<br> GRE VIP 子网|
|IPSec 网关连接|网络控制器<br> SLB/MUX<br> 网关|HNV PA 网络上的 BGP<br> 公共 VIP 子网|
|L3 网关连接|网络控制器<br> 网关|租户 VLAN、子网、路由器<br> 租户 VLAN 上的 BGP（可选）。|

## <a name="next-steps"></a>后续步骤
如需相关信息，另请参阅：
- [部署网络控制器的要求](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)
- [Azure Stack HCI 中的 SDN](./software-defined-networking.md)