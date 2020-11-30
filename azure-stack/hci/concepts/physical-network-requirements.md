---
title: Azure Stack HCI 的物理网络要求
description: 了解 Azure Stack HCI 的物理网络要求
author: v-dasis
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: a8eea75c58f597a1026b5075537d415c0694dc3c
ms.sourcegitcommit: 422c3f19d817037fa6ce73cca253742bbdec147e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321993"
---
# <a name="physical-network-requirements-for-azure-stack-hci"></a>Azure Stack HCI 的物理网络要求

> 适用于 Azure Stack HCI 版本 20H2

本主题讨论物理 (构造) 网络注意事项和 Azure Stack HCI 的要求，尤其是对于网络交换机。

> [!NOTE]
> 未来 Azure Stack HCI 版本的要求可能会改变。

## <a name="network-switches-for-azure-stack-hci"></a>Azure Stack HCI 的网络交换机

Microsoft 测试 Azure Stack HCI 到下面的 **网络交换机要求** 部分中标识的标准和协议。 虽然 Microsoft 不验证网络交换机，但我们确实会与供应商合作来识别支持 Azure Stack HCI 要求的设备。

这些要求也在 [Windows 硬件兼容性计划规范和策略](https://docs.microsoft.com/windows-hardware/design/compatibility/whcp-specifications-policies)中发布。  选择 " **下载规范和策略，版本 1809**"，打开 ZIP 文件，打开 **WHCP-Components-Peripherals-Specification-1809.pdf**，然后查看 " **设备** "。

> [!IMPORTANT]
> 尽管使用此处未列出的技术和协议的其他网络交换机可能有效，但 Microsoft 无法保证它们将与 Azure Stack HCI 一起使用，并且可能无法帮助排查发生的问题。

购买网络交换机时，请与交换机供应商联系，确保设备满足所有 Azure Stack HCI 要求。 以下供应商 (按字母顺序排序) 已确认其交换机支持 Azure Stack HCI 要求：

|Vendor|10 GbE|25 GbE|100 GbE|
|-----|-----|-----|-----|
|Dell|[S41xx 系列](https://www.dell.com/learn/us/en/45/shared-content~data-sheets~en/documents~dell-emc-networking-s4100-series-spec-sheet.pdf)|[S52xx 系列](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|[S52xx 系列](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|
|Lenovo|[G8272](https://lenovopress.com/tips1267-lenovo-rackswitch-g8272)、 [NE1032](https://lenovopress.com/lp0605-thinksystem-ne1032-rackswitch)|[NE2572](https://lenovopress.com/lp0608-lenovo-thinksystem-ne2572-rackswitch) |[NE10032](https://lenovopress.com/lp0609-lenovo-thinksystem-ne10032-rackswitch) |

> [!IMPORTANT]
> 由于网络交换机供应商的更改，Microsoft 将更新此列表。

如果未包含交换机，请联系交换机供应商，确保交换机型号和交换机操作系统版本支持下一部分中的要求。

## <a name="network-switch-requirements"></a>网络交换机要求

本部分列出了所有 Azure Stack HCI 部署中所使用的网络交换机必需的行业标准。 这些标准有助于确保 Azure Stack HCI 群集部署中的节点之间的可靠通信。

> [!NOTE]
> 用于计算、存储和管理流量的网络适配器需要以太网。

下面是必需的 IEEE 标准和规范：

### <a name="standard-ieee-8021q"></a>标准：IEEE 802.1Q

以太网交换机必须符合定义 VLAN 的 IEEE 802.1Q 规范。 Azure Stack HCI 的多个方面都需要 VLAN，并且所有场景下都需要 VLAN。

### <a name="standard-ieee-8021qbb"></a>标准：IEEE 802.1Qbb

以太网交换机必须符合定义优先级流控制 (PFC) 的 IEEE 802.1Qbb 规范。 使用数据中心桥接 (DCB) 的情况下需要 PFC。 由于 DCB 可以在 RoCE 和 iWARP RDMA 场景中使用，因此所有场景中都需要 802.1Qbb。 至少需要三类服务 (CoS) 优先级，而不会降低交换机功能或端口速度。 其中至少有一个通信类必须提供无损通信。

### <a name="standard-ieee-8021qaz"></a>标准：IEEE 802.1Qaz

以太网交换机必须符合定义增强传输选择 (ETS) 的 IEEE 802.1Qaz 规范。 使用 DCB 时需要 ETS。 由于 DCB 可以在 RoCE 和 iWARP RDMA 场景中使用，因此所有场景中都需要 802.1Qaz。 在不降低交换机功能或端口速度的情况下，至少需要三个 CoS 优先级。

> [!NOTE]
> 超聚合基础结构对同一机架内的 East-West 第2层通信具有很高的依赖性，因此需要 ETS。 Microsoft 不会差分服务码位 (DSCP) 测试 Azure Stack HCI。 

### <a name="standard-ieee-8021ab"></a>标准：IEEE 802.1AB

以太网交换机必须符合定义链接层发现协议 (LLDP) 的 IEEE 802.1AB 规范。 Azure Stack HCI 需要 LLDP，并支持组合以发现交换机配置。

必须动态启用 LLDP Type-Length-Value (TLV) 配置。 除了启用特定 TLV 以外，这些交换机不得要求额外配置。 例如，启用 802.1 子类型 3 应会自动播发交换机端口上所有可用的 VLAN。

### <a name="custom-tlv-requirements"></a>自定义 TLV 要求

LLDP 使组织可定义并编码自己的自定义 TLV。 这些称为组织特定的 TLV。 所有组织特定的 TLV 都以 LLDP TLV 类型值 127 开始。 下表显示了需要 (TLV 类型 127) 子类型的组织特定自定义 TLV：

|所需版本|组织|TLV 子类型|
|-----|-----|-----|-----|
|20H2 及更高版本|IEEE 802.1|VLAN 名称（子类型 = 3）|
|20H2 及更高版本|IEEE 802.3|最大帧大小（子类型 = 4）|

## <a name="network-traffic-and-architecture"></a>网络流量和体系结构

本部分主要面向网络管理员。

Azure Stack HCI 可在各种数据中心体系结构中运行，包括2层 (书脊) 和3层 (核心聚合访问) 。 本部分详细介绍了 Spine-Leaf 拓扑中的概念，这些概念通常用于超聚合基础结构中的工作负荷，如 Azure Stack HCI。

## <a name="network-models"></a>网络模型

网络流量可以按其方向进行分类。 传统的存储区域网络 (SAN) 环境在很大程度上是 North-South，其中流量从计算层流向第3层 (IP) 边界之间的存储层。 超聚合基础结构在很大程度上 East-West，其中巨大的流量部分位于第2层 (VLAN) 边界内。

> [!IMPORTANT]
>我们强烈建议站点中的所有群集节点都位于同一机架上，并连接到同一架顶 (ToR) 交换机。

### <a name="north-south-traffic-for-azure-stack-hci"></a>Azure Stack HCI North-South 流量

North-South 流量具有以下特征：

- 流量从 ToR 切换到书脊或从书脊流向 ToR 交换机
- 流量离开物理机架或跨越第3层边界 (IP) 
- 包括管理 (PowerShell、Windows 管理中心) 、计算 (VM) 和站点间延伸群集流量
- 使用以太网交换机连接到物理网络

### <a name="east-west-traffic-for-azure-stack-hci"></a>Azure Stack HCI East-West 流量

East-West 流量具有以下特征：

- 流量保留在 ToR 交换机和第2层边界 (VLAN) 
- 包括同一群集中节点之间的存储流量或实时迁移流量，并 (如果在同一站点中使用扩展群集) 
- 如接下来的两部分所述，可以使用 (交换) 或直接 (switchless) 连接的以太网交换机。

## <a name="using-switches"></a>使用开关

North-South 流量需要使用交换机。 除了使用支持 Azure Stack HCI 所需的协议的以太网交换机，最重要的方面是正确调整网络结构大小。

必须了解你的以太网交换机可支持的 "非阻止" 构造带宽，并最大程度地减少 (或最好地消除网络) 过度订阅。

可以通过正确使用子网和 Vlan 来消除常见的拥塞点和过度订阅，如用于路径冗余的 [多底盘链路聚合组](https://en.wikipedia.org/wiki/Multi-chassis_link_aggregation_group) 。

与您的网络供应商或网络支持团队合作，确保您的网络交换机为您要运行的工作负荷适当调整大小。

## <a name="using-switchless"></a>使用 switchless

由于群集中的每个节点都与群集中的每个节点建立冗余连接，因此 Azure Stack HCI 支持 switchless (直接) 连接以实现所有群集大小的 East-West 流量。 这称为 "全网格" 连接。

> [!NOTE]
>由于所需的网络适配器数，switchless 部署的优点会降低，因为群集大于三个节点。

### <a name="advantages-of-switchless-connections"></a>Switchless 连接的优点

- 对于 East-West 流量，无需购买交换机。 North-South 流量需要开关。 这可能会导致资本支出 (CAPEX) 开销，但取决于群集中的节点数。
- 由于没有交换机，因此配置仅限于主机，这可能会减少所需的配置步骤数。 当群集大小增加时，此值会减弱。

### <a name="disadvantages-of-switchless-connections"></a>Switchless 连接的缺点

- 由于群集中的节点数增长，网络适配器的成本可能会超过使用网络交换机的成本。
- IP 和子网寻址方案需要进行更多规划。
- 仅提供本地存储访问权限。 VM 流量、管理流量和需要 North-South 访问的其他流量不能使用这些适配器。
- 通常不能比三节点群集更好地扩展。

## <a name="next-steps"></a>后续步骤

- 了解网络适配器和主机要求。 请参阅 [主机网络要求](physical-network-requirements.md)。
- 温习故障转移群集基础知识。 请参阅 [故障转移群集网络基础知识](https://techcommunity.microsoft.com/t5/failover-clustering/.failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)。
- 温习如何使用 SET。 请参阅 [ (RDMA 的远程直接内存访问) 和交换机嵌入组合 (设置) ](https://docs.microsoft.com/windows-server/virtualization/.hyper-v-virtual-switch/rdma-and-switch-embedded-teaming)。
- 有关部署，请参阅 [使用 Windows 管理中心创建群集](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster)。
- 有关部署，请参阅 [使用 Windows PowerShell 创建群集](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell)。