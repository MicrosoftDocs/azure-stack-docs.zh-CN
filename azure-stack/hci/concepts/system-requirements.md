---
title: Azure Stack HCI 的系统需求
description: 如何为 Azure Stack HCI 选择服务器、存储和网络组件。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/14/2021
ms.openlocfilehash: 3b29dfb35bad91ef02feebc22255b89116da1505
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243453"
---
# <a name="system-requirements-for-azure-stack-hci"></a>Azure Stack HCI 的系统需求

> 适用于：Azure Stack HCI 版本 20H2

本主题可用于评估 Azure Stack HCI 的服务器、存储和网络的系统需求。

## <a name="server-requirements"></a>服务器要求

标准 Azure Stack HCI 群集至少需要 2 台服务器，最多 16 台服务器；但是，可以使用群集组来组合群集，以创建包含数百个节点的 HCI 平台。

对于各种类型的 Azure Stack HCI 部署，请记住以下几点：

- 延伸群集要求将服务器部署在两个不同的站点上。 这两个站点可位于不同的国家/地区、不同的城市、不同的楼层或不同的房间。 延伸群集需要最少 4 台服务器（每个站点 2 台），最多 16 台服务器（每个站点 8 台）。

- 建议所有服务器都具有相同的制造商和型号，使用与 64 位 Intel Nehalem 级别、AMD EPYC 或更高级别兼容的处理器和二级地址转换 (SLAT)。 需要第二代 Intel Xeon Scalable 处理器来支持 Intel Optane DC 永久性内存。 处理器必须至少为 1.4 GHz 并且与 x64 指令集兼容。

- 确保在每个节点上为服务器配备至少 32 GB 的 RAM，以容纳服务器操作系统、VM 和其他应用或工作负荷。 此外，每台服务器上每 TB 缓存驱动器容量应留出 4 GB 的 RAM 用于存储空间直通元数据。

- 验证是否已在 BIOS 或 UEFI 中启用虚拟化支持：
    - 硬件协助的虚拟化。 这适用于包含虚拟化选项的处理器，特别是具有 Intel 虚拟化技术 (Intel VT) 或 AMD 虚拟化 (AMD-V) 技术的处理器。
    - 硬件强制实施的数据执行保护 (DEP) 必须可用且已启用。 对于 Intel 系统，这是 XD 位（执行禁用位）。 对于 AMD 系统，这是 NX 位（无执行位）。

- 可以使用 Windows Server 支持的任何启动设备（[现在包括 SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/)）。 RAID 1 镜像不是必需的，但可用于启动。 建议的最小大小为 200 GB。

- 有关 Hyper-V 的其他特定于功能的要求，请参阅 [Windows Server 上的 Hyper-V 的系统要求](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows)。

## <a name="storage-requirements"></a>存储要求

Azure Stack HCI 可使用直接连接的 SATA、SAS、NVMe 或永久性内存驱动器，每个驱动器使用物理方式仅连接到一个服务器。

为获得最佳结果，请遵循以下内容：

- 群集中每个服务器都应具有相同类型的驱动器，各类型驱动器的数量也应该相同。 另外，建议（但不要求）驱动器的大小和型号也相同。 驱动器可以是服务器的内部驱动器，也可以是仅连接到一台服务器的外接盒。 若要了解详细信息，请参阅[驱动器对称性注意事项](drive-symmetry-considerations.md)。

- 群集中的每台服务器都应有日志专用卷，日志存储至少与数据存储一样快。 延伸群集至少需要两个卷：一个用于复制的数据，一个用于日志数据。

- 槽映射和标识需要 SCSI 机箱服务 (SES)。 每个外接盒都必须提供唯一标识符（唯一 ID）。 

   > [!IMPORTANT]
   > **不支持：** RAID 控制器卡或 SAN（光纤通道、iSCSI、FCoE）存储、连接到多台服务器的共享 SAS 机箱，或可以通过多个路径来访问驱动器的任何形式的多路径 IO (MPIO)。 主机总线适配器 (HBA) 卡必须实现简单的直通模式。

## <a name="networking-requirements"></a>网络要求

Azure Stack HCI 群集要求在各个服务器节点之间具有可靠的高带宽、低延迟网络连接。

- 验证是否至少有一个网络适配器可用且专用于群集管理。
- 验证网络中的物理交换机是否已配置为允许你要使用的任何 VLAN 上的流量。

有关主机网络注意事项和要求，请参阅 [主机网络要求](host-network-requirements.md)。

## <a name="software-defined-networking-sdn-requirements"></a>软件定义的网络 (SDN) 要求

在使用 Windows Admin Center 创建 Azure Stack HCI 群集时，可以选择部署网络控制器，以启用软件定义的网络 (SDN)。 如果要在 Azure Stack HCI 上使用 SDN，请注意以下事项：

- 请确保主机服务器至少有 50-100 GB 的可用空间，以用于创建网络控制器 VM。

- 为了创建网络控制器 VM，必须将 Azure Stack HCI 操作系统的虚拟硬盘 (VHD) 复制到群集中的第一个节点。 准备 VHD 的方式可以是使用 [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview)，或者也可以是通过运行 [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) 脚本将 .iso 文件转换为 VHD。

关于在 Azure Stack HCI 中使用 SDN，若要详细了解如何为此进行准备，请参阅[规划软件定义的网络基础结构](plan-software-defined-networking-infrastructure.md)和[规划部署网络控制器](../concepts/network-controller.md)。

   > [!NOTE]
   > 延伸（多站点）群集不支持 SDN。

### <a name="sdn-hardware-requirements"></a>SDN 硬件要求

此部分提供规划 SDN 环境时 NIC 和物理交换机的网络硬件要求。

#### <a name="network-interface-cards-nics"></a>网络接口卡 (NIC)

在 Hyper-V 主机和存储主机中使用的 NIC 需要特定功能才能实现最佳性能。

远程直接内存访问 (RDMA) 是一种内核旁路技术，可用于在不使用主机 CPU 的情况下传输大量数据，这样可释放 CPU 来执行其他工作。 交换嵌入式组合 (SET) 是一种备用 NIC 组合解决方案，可在包含 Hyper-V 和 SDN 堆栈的环境中使用。 SET 将一些 NIC 组合功能集成到 Hyper-V 虚拟交换机中。

有关详细信息，请参阅 [远程直接内存访问 (RDMA) 和交换嵌入式组合 (SET)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming)。

若要考虑由 VXLAN 或 NVGRE 封装标头导致的租户虚拟网络流量中的开销，第 2 层结构网络（交换机和主机）的最大传输单位 (MTU) 必须设置为大于或等于 1674 字节（包括第 2 层以太网标头）。

支持新 EncapOverhead 高级适配器关键字的 NIC 通过网络控制器主机代理自动设置 MTU。 不支持新 EncapOverhead 关键字的 NIC 需要使用 JumboPacket（或等效关键字）在每个物理主机上手动设置 MTU 大小。

#### <a name="switches-and-routers"></a>交换机和路由器

为 SDN 环境选择物理交换机和路由器时，请确保它支持以下功能集：
- 交换机端口 MTU 设置（必需）
- MTU 设置为 >= 1674 字节（包括 L2 以太网标头）
- L3 协议（必需）
- 相等成本多路径 (ECMP) 路由
- 基于 BGP \(IETF RFC 4271\) 的 ECMP

实现应支持以下 IETF 标准中的“必须”陈述：
- RFC 2545：[用于 IPv6 域际路由的 BGP-4 多协议扩展](https://tools.ietf.org/html/rfc2545)
- RFC 4760：[用于 BGP-4 的多协议扩展](https://tools.ietf.org/html/rfc4760)
- RFC 4893：[针对四个八进制 AS 编号空间的 BGP 支持](https://tools.ietf.org/html/rfc4893)
- RFC 4456：[BGP 路由反射：完整网格内部 BGP (IBGP) 的替代方法](https://tools.ietf.org/html/rfc4456)
- RFC 4724：[用于 BGP 的正常重新启动机制](https://tools.ietf.org/html/rfc4724)

需要以下标记协议：
- VLAN - 各种类型流量的隔离
- 802.1q trunk

以下各项提供了链路控制：
- 服务质量 \(QoS\)（仅当使用 RoCE 时才需要 PFC）
- 增强型流量选择 \(802.1Qaz\)
- 基于优先级的流控制 (PFC)（802.1p/Q 和 802.1Qbb）

以下各项提供可用性和冗余：
- 交换机可用性（必需）
- 执行网关功能需要高可用路由器。 可以使用多机箱交换机\路由器或技术（例如虚拟路由器冗余协议 (VRRP)）提供此功能。

### <a name="domain-requirements"></a>域要求

Azure Stack HCI 没有特殊的域功能级别要求，只是一个适用于你的域控制器且仍受支持的操作系统版本。 建议启用 Active Directory 回收站功能（如果尚未这样做），这是一种常规的最佳做法。 若要了解详细信息，请参阅 [Active Directory 域服务概述](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

## <a name="maximum-supported-hardware-specifications"></a>支持的最大硬件规格

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

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [选择驱动器](choose-drives.md)
- [存储空间直通硬件要求](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
