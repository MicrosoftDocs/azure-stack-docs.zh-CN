---
title: 部署 Azure Stack HCI 之前的准备工作
description: 如何为部署 Azure Stack HCI 做准备。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/30/2020
ms.openlocfilehash: a5406ef1098750248d516416f55902d5ae6909cd
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623093"
---
# <a name="before-you-deploy-azure-stack-hci"></a>部署 Azure Stack HCI 之前的准备工作

> 适用于：Azure Stack HCI 版本 20H2

本操作指南的内容：

- 确定你的硬件是否符合标准（单站点）或延伸（双站点）Azure Stack HCI 群集的基本要求
- 确保未超出支持的最大硬件规格
- 收集成功部署所需的信息
- 在管理 PC 或服务器上安装 Windows Admin Center·

有关 Azure Stack HCI 要求的 Azure Kubernetes 服务，请参阅 [AZURE STACK HCI 上的 AKS 要求](../../aks-hci/overview.md#what-you-need-to-get-started)。

## <a name="determine-hardware-requirements"></a>确定硬件要求

Microsoft 建议从我们的合作伙伴购买经验证的 Azure Stack HCI 硬件/软件解决方案。 这些解决方案是依据我们的参考体系结构设计和汇编的，并且经过了验证，能够确保兼容性和可靠性，因此你可以快速起步和运行。 检查所用的系统、组件、设备和驱动程序是否是通过 Windows Server 目录认证的 Windows Server 2019。 请访问 [AZURE STACK HCI 解决方案](https://azure.microsoft.com/overview/azure-stack/hci) 网站获取经验证的解决方案。

### <a name="server-requirements"></a>服务器要求

- 标准 Azure Stack HCI 群集至少需要 2 台服务器，最多 16 台服务器；但是，可以使用群集组来组合群集，以创建包含数百个节点的 HCI 平台。
- 延伸群集要求将服务器部署在两个不同的站点上。 这两个站点可位于不同的国家/地区、不同的城市、不同的楼层或不同的房间。 延伸群集需要最少 4 台服务器（每个站点 2 台），最多 16 台服务器（每个站点 8 台）。
- 建议所有服务器都具有相同的制造商和型号，使用与 64 位 Intel Nehalem 级别、AMD EPYC 或更高级别兼容的处理器和二级地址转换 (SLAT)。 需要第二代 Intel 至强可扩展处理器来支持 Intel Optane DC 永久性内存。 处理器必须至少为 1.4 GHz 并且与 x64 指令集兼容。
- 确保在每个节点上为服务器配备至少 32 GB 的 RAM，以容纳服务器操作系统、VM 和其他应用或工作负荷。 此外，每台服务器上每 TB 缓存驱动器容量应留出 4 GB 的 RAM 用于存储空间直通元数据。
- 验证是否已在 BIOS 或 UEFI 中启用虚拟化支持：
    - 硬件协助的虚拟化。 这适用于包含虚拟化选项的处理器，特别是具有 Intel 虚拟化技术 (Intel VT) 或 AMD 虚拟化 (AMD-V) 技术的处理器。
    - 硬件强制实施的数据执行保护 (DEP) 必须可用且已启用。 对于 Intel 系统，这是 XD 位（执行禁用位）。 对于 AMD 系统，这是 NX 位（无执行位）。
- 可以使用 Windows Server 支持的任何启动设备（[现在包括 SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/)）。 RAID 1 镜像不是必需的，但可用于启动。 建议的最小大小为 200 GB。
- 有关 Hyper-V 的其他特定于功能的要求，请参阅 [Windows Server 上的 Hyper-V 的系统要求](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows)。

### <a name="networking-requirements"></a>网络要求

Azure Stack HCI 群集要求在各个服务器节点之间具有可靠的高带宽、低延迟网络连接。 应该验证以下各项：

- 验证是否至少有一个网络适配器可用且专用于群集管理。
- 验证网络中的物理交换机是否已配置为允许你要使用的任何 VLAN 上的流量。

服务器节点之间存在多种类型的通信：

- 群集通信（节点加入、群集更新、注册表更新）
- 群集检测信号
- 群集共享卷 (CSV) 重定向流量
- 虚拟机的实时迁移流量

使用存储空间直通时，需要考虑额外的网络流量：

- 存储总线层 (SBL) – 在节点间分散的盘区或数据
- 运行状况 – 监视和管理对象（节点、驱动器、网卡、群集服务）

对于延伸群集，还会有在站点之间流动的额外存储副本流量。 存储总线层 (SBL) 和群集共享卷 (CSV) 流量不会在站点之间传输，只能在每个站点中的服务器节点之间传输。

### <a name="software-defined-networking-requirements"></a>软件定义的网络要求

使用 Windows 管理中心创建 Azure Stack HCI 群集时，可以选择部署网络控制器，以启用软件定义的网络 (SDN) 。 如果要在 Azure Stack HCI 上使用 SDN：

- 请确保主机服务器至少有 50-100 GB 的可用空间来创建网络控制器 Vm。

- 必须将 Azure Stack HCI 操作系统 (VHD) 的虚拟硬盘复制到群集中的第一个节点，才能创建网络控制器 Vm。 可以使用 [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) 或通过运行 [add-windowsimage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) 脚本将 .ISO 文件转换为 vhd 来准备 vhd。

有关在 Azure Stack HCI 中准备使用 SDN 的详细信息，请参阅 [规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md) 和 [计划部署网络控制器](../concepts/network-controller.md)。

### <a name="domain-requirements"></a>域要求

Azure Stack HCI 没有特殊的域功能级别要求，只是一个适用于你的域控制器且仍受支持的操作系统版本。 建议启用 Active Directory 回收站功能（如果尚未这样做），这是一种常规的最佳做法。

### <a name="interconnect-requirements-between-nodes"></a>节点之间的互连要求

本部分讨论了站点中服务器节点之间的特定网络要求，即互连。 可以使用和支持有交换机或无交换机节点互连：

- **有交换机：** 最常用的方案是，服务器节点通过那些使用网络交换机的以太网彼此连接。 必须正确配置交换机以处理带宽和网络类型。 如果使用的 RDMA 实现了 RoCE 协议，则网络设备和交换机配置很重要。
- **无交换机：** 服务器节点还可以使用直接以太网连接进行互连，不使用交换机。 在这种情况下，每个服务器节点必须与同一站点中的所有其他群集节点直接连接。

#### <a name="interconnects-for-2-3-node-clusters"></a>具有 2-3 个节点的群集的互连

下面是具有两个或三个节点的单站点群集的最低互连要求。 这些要求适用于每个服务器节点：

- 一个或多个用于管理功能的 1 Gb 网络适配器卡
- 一个或多个 10 Gb（或更快）的网络接口卡，用于存储和工作负荷流量
- 建议在各个节点之间使用两个或更多个网络连接以确保冗余和性能

#### <a name="interconnects-for-4-node-and-greater-clusters"></a>具有 4 个节点的群集和更大群集的互连

下面是具有四个或更多个节点的群集和高性能群集的最低互连要求。 这些要求适用于每个服务器节点：

- 一个或多个用于管理功能的 1 Gb 网络适配器卡。
- 一个或多个 25 Gb（或更快）的网络接口卡，用于存储和工作负荷流量。 建议使用两个或更多个网络连接以确保冗余和性能。
- 支持远程直接内存访问 (RDMA) 的网卡：iWARP（推荐）或 RoCE。

### <a name="site-to-site-requirements-stretched-cluster"></a>站点到站点要求（延伸群集）

在延伸群集的站点之间连接时，每个站点中的互连要求仍适用，此外还有其他必须考虑的存储副本和 Hyper-V 实时迁移流量要求：

- 在进行同步复制的站点之间至少有一个 1 Gb RDMA 或以太网/TCP 连接。 首选 25 Gb RDMA 连接。
- 对于同步复制，站点之间的网络需要有足够的带宽来包含 I/O 写入工作负荷，往返延迟平均为 5 毫秒或更低。 异步复制没有延迟建议。
- 如果在站点之间使用单个连接，请使用 PowerShell 设置存储副本的 SMB 带宽限制。 有关详细信息，请参阅 [Set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit)。
- 如果在站点之间使用多个连接，请在连接之间分隔流量。 例如，将存储副本流量与使用 PowerShell 的 Hyper-V 实时迁移流量分别置于不同的网络上。 有关详细信息，请参阅 [Set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint)。

### <a name="network-port-requirements"></a>网络端口要求

请确保站点内和站点间的所有服务器节点之间的相应网络端口处于打开状态（对于延伸群集）。 你需要适当的防火墙和路由器规则，以允许在群集中的所有服务器之间进行 ICMP、SMB（端口 445，以及适用于 SMB Direct 的端口 5445）和 WS-MAN（端口 5985）双向通信。

使用 Windows Admin Center 中的群集创建向导来创建群集时，向导会针对故障转移群集、Hyper-V 和存储副本自动打开群集中每台服务器上的相应防火墙端口。 如果在每台服务器上使用不同的软件防火墙，请打开以下端口：

#### <a name="failover-clustering-ports"></a>故障转移群集端口

- ICMPv4 和 ICMPv6
- TCP 端口 445
- RPC 动态端口
- TCP 端口 135
- TCP 端口 137
- TCP 端口 3343
- UDP 端口 3343

#### <a name="hyper-v-ports"></a>Hyper-V 端口

- TCP 端口 135
- TCP 端口 80（HTTP 连接）
- TCP 端口 443（HTTPS 连接）
- TCP 端口 6600
- TCP 端口 2179
- RPC 动态端口
- RPC 终结点映射程序
- TCP 端口 445

#### <a name="storage-replica-ports-stretched-cluster"></a>存储副本端口（延伸群集）

- TCP 端口 445
- TCP 5445（如果使用 iWarp RDMA）
- TCP 端口 5985
- ICMPv4 和 ICMPv6（如果使用 Test-SRTopology）

可能需要其他端口，但上面未列出。 这些是用于基本的 Azure Stack HCI 功能的端口。

### <a name="network-switch-requirements"></a>网络交换机要求

本部分定义与 Azure Stack HCI 一起使用的物理交换机的要求。 这些要求列出了所有 Azure Stack HCI 部署所必需的行业规范、组织标准和协议。 除非注明，否则不需要为标准的最新活动 (非取代) 版本。

这些要求有助于确保 Azure Stack HCI 群集部署中的节点之间的可靠通信。 节点之间的可靠通信至关重要。 若要为 Azure Stack HCI 提供所需的可靠性级别，需要交换机：

- 遵守适用的行业规范、标准和协议
- 提供交换机支持的规范、标准和协议的可见性
- 提供有关启用了哪些功能的信息

#### <a name="standard-ieee-8021q"></a>标准： IEEE 802.1 Q

以太网交换机必须符合定义 Vlan 的 IEEE 802.1 Q 规范。 Vlan 是 Azure Stack HCI 的几个方面所必需的，并且在所有方案中都是必需的。

#### <a name="standard-ieee-8021-qbb"></a>标准： IEEE 802.1 Q b b

以太网交换机必须符合定义 (PFC) 优先级流控制的 IEEE 802.1 Q b b 规范。 使用数据中心桥接 (DCB) 时，需要 PFC。 由于 DCB 可以在 RoCE 和 iWARP RDMA 方案中使用，因此在所有方案中都需要 802.1 Q b b。 至少需要三类服务 (CoS) 优先级，而不会降级交换机功能或端口速度。

#### <a name="standard-ieee-8021qaz"></a>标准： IEEE 802.1 Qaz

以太网交换机必须符合定义增强传输的 IEEE 802.1 Qaz 规范 (ETS) 。 使用 DCB 时需要 ETS。 由于 DCB 可以在 RoCE 和 iWARP RDMA 方案中使用，因此在所有方案中都需要 802.1 Qaz。 至少需要三个 CoS 优先级，而不会降级交换机功能或端口速度。

#### <a name="standard-ieee-8021ab"></a>标准： IEEE 802.1 AB

以太网交换机必须符合定义 (LLDP) 的链接层发现协议的 IEEE 802.1 AB 规范。 Windows 需要 LLDP 才能发现交换机配置。 必须动态启用 LLDP 类型-长度-值 (TLVs) 的配置。 这些开关不能需要其他配置。

例如，启用802.1 子类型3应自动播发交换机端口上的所有可用 Vlan。

#### <a name="tlv-requirements"></a>TLV 要求

LLDP 允许组织定义并编码自己的自定义 TLVs。 这些称为组织特定的 TLVs。 所有组织特定的 TLVs 从 LLDP TLV 类型值127开始。 下表显示了需要 (TLV 类型 127) 子类型的特定于组织的自定义 TLV，它们是可选的：

|条件|组织|TLV 子类型|
|-|-|-|
|可选|IEEE 802。1|端口 VLAN ID (子类型 = 1) |
|可选|IEEE 802。1|端口和协议 VLAN ID (子类型 = 2) |
|必需|IEEE 802。1|VLAN 名称 (子类型 = 3) |
|可选|IEEE 802。1|链接聚合 (子类型 = 7) |
|可选|IEEE 802。1|拥塞通知 (子类型 = 8) |
|可选|IEEE 802。1|ETS 配置 (子类型 = 9) |
|可选|IEEE 802。1|ETS 建议 (子类型 =) |
|可选|IEEE 802。1|PFC 配置 (子类型 = B) |
|可选|IEEE 802。1|EVB (子类型 = D) |
|可选|IEEE 802。3|链接聚合 (子类型 = 3) |
|必需|IEEE 802。3|最大帧大小 (子类型 = 4) |

### <a name="storage-requirements"></a>存储要求

- Azure Stack HCI 可使用直接连接的 SATA、SAS、NVMe 或永久性内存驱动器，每个驱动器使用物理方式仅连接到一个服务器。
- 群集中的每个服务器都应具有相同类型的驱动器和每种类型的相同编号。 它也建议 (但不是必需的) 驱动器大小和型号相同。 驱动器可以是服务器的内部驱动器，也可以是仅连接到一台服务器的外接盒。 若要了解详细信息，请参阅 [驱动对称注意事项](../concepts/drive-symmetry-considerations.md)。
- 群集中的每台服务器都应有日志专用卷，日志存储至少与数据存储一样快。 延伸群集至少需要两个卷：一个用于复制的数据，一个用于日志数据。
- 槽映射和标识需要 SCSI 机箱服务 (SES)。 每个外接盒都必须提供唯一标识符（唯一 ID）。 **不支持：** RAID 控制器卡或 SAN（光纤通道、iSCSI、FCoE）存储、连接到多台服务器的共享 SAS 机箱，或可以通过多个路径来访问驱动器的任何形式的多路径 IO (MPIO)。 主机总线适配器 (HBA) 卡必须实现简单的直通模式。
- 有关更多帮助，请参阅[选择驱动器](../concepts/choose-drives.md)主题或[存储空间直通硬件要求](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)。
- 有关卷和复原能力的可用选项，请参阅[在存储空间直通中规划卷](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type)主题。
- 如果还部署虚拟机和虚拟化存储，请参阅[在来宾虚拟机群集中使用存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm)。

## <a name="review-maximum-supported-hardware-specifications"></a>查看支持的最大硬件规格

超出以下规格的 Azure Stack HCI 部署不受支持：

| 资源                     | 最大值 |
| ---------------------------- | --------|
| 每个群集的物理服务器数 | 16      |
| 每台主机的 VM 数                 | 1,024   |
| 每个 VM 的磁盘数 (SCSI)          | 256     |
| 每个群集的存储          | 4 PB    |
| 每台服务器的存储           | 400 TB  |
| 每台主机的逻辑处理器数  | 512     |
| 每台主机的 RAM                 | 24 TB   |
| 每个 VM 的 RAM                   | 12 TB（第 2 代 VM）或 1 TB（第 1 代 VM）|
| 每台主机的虚拟处理器  | 2,048   |
| 每个 VM 的虚拟处理器数    | 240（第 2 代 VM）或 64（第 1 代 VM）|

## <a name="gather-information"></a>收集信息

若要为部署做准备，请收集有关你的环境的以下详细信息：

- **服务器名称：** 熟悉你的组织的计算机、文件、路径和其他资源的命名策略。 你需要预配多台服务器，每台都有唯一的名称。
- **域名：** 熟悉你的组织用于域命名和域加入的策略。 你需要将服务器加入域，且需指定域名。
- **静态 IP 地址：** Azure Stack HCI 要求为存储和工作负荷 (VM) 流量使用静态 IP 地址，不支持通过 DHCP 为此高速网络分配动态 IP 地址。 你可以为管理网络适配器使用 DHCP，除非你在一个团队中使用两个适配器，在这种情况下，你仍需使用静态 IP。 有关群集中每台服务器应该使用的 IP 地址，请询问网络管理员。
- **RDMA 网络：** 有两种 RDMA 协议：iWarp 和 RoCE。 请记下你的网络适配器使用哪一种。如果是 RoCE，还请记下版本（v1 或 v2）。 对于 RoCE，还请记下架顶式交换机的型号。
- **VLAN ID：** 请记下用于服务器上的网络适配器的 VLAN ID（如果有）。 应该能够从网络管理员处获取此信息。
- **站点名称：** 对于延伸群集，将使用两个站点以便进行灾难恢复。 你可以使用 Active Directory 域服务设置站点，也可以让创建群集向导自动为你设置站点。 有关站点设置的问题，可以咨询域管理员。 也可参阅 [Active Directory 域服务概述](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)来了解详细信息。

## <a name="install-windows-admin-center"></a>安装 Windows Admin Center

Windows Admin Center 是一个本地部署的基于浏览器的应用，用于管理 Azure Stack HCI。 若要[安装 Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install)，最简单的方法是在本地管理 PC 上安装（桌面模式），但也可以在服务器上安装（服务模式）。

如果在服务器上安装 Windows Admin Center，则那些需要 CredSSP 的任务（例如创建群集以及安装更新和扩展）使用的帐户必须是 Windows Admin Center 服务器上的“网关管理员”组的成员。 有关详细信息，请参阅[配置用户访问控制和权限](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)中的前两部分。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何部署 Azure Stack HCI 操作系统。
> [!div class="nextstepaction"]
> [部署 Azure Stack HCI 操作系统](operating-system.md)
