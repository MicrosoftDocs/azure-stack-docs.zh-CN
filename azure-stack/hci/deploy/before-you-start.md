---
title: 部署 Azure Stack HCI 之前的准备工作
description: 如何为部署 Azure Stack HCI 做准备。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/14/2020
ms.openlocfilehash: 4ff495aba1f46824a6ab47c95601687402d24edb
ms.sourcegitcommit: 8122672409954815e472a5b251bb7319fab8f951
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92060100"
---
# <a name="before-you-deploy-azure-stack-hci"></a>部署 Azure Stack HCI 之前的准备工作

> 适用于：Azure Stack HCI 版本 20H2

本操作指南的内容：

- 确定你的硬件是否符合标准（单站点）或延伸（双站点）Azure Stack HCI 群集的基本要求
- 确保未超出支持的最大硬件规格
- 收集成功部署所需的信息
- 在管理 PC 或服务器上安装 Windows Admin Center·

关于 Azure Stack HCI 上 Azure Kubernetes 服务的要求，请参阅 [Azure Stack HCI 上的 AKS 要求](../../aks-hci/overview.md#what-you-need-to-get-started)。

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

有关主机网络规划注意事项和要求，请参阅为 [AZURE STACK HCI 规划主机网络](../concepts/plan-host-networking.md)。

### <a name="software-defined-networking-requirements"></a>软件定义的网络要求

在使用 Windows Admin Center 创建 Azure Stack HCI 群集时，可以选择部署网络控制器，以启用软件定义的网络 (SDN)。 如果要在 Azure Stack HCI 上使用 SDN，请注意以下事项：

- 请确保主机服务器至少有 50-100 GB 的可用空间，以用于创建网络控制器 VM。

- 为了创建网络控制器 VM，必须将 Azure Stack HCI 操作系统的虚拟硬盘 (VHD) 复制到群集中的第一个节点。 准备 VHD 的方式可以是使用 [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview)，或者也可以是通过运行 [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) 脚本将 .iso 文件转换为 VHD。

关于在 Azure Stack HCI 中使用 SDN，若要详细了解如何为此进行准备，请参阅[规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md)和[规划部署网络控制器](../concepts/network-controller.md)。

### <a name="domain-requirements"></a>域要求

Azure Stack HCI 没有特殊的域功能级别要求，只是一个适用于你的域控制器且仍受支持的操作系统版本。 建议启用 Active Directory 回收站功能（如果尚未这样做），这是一种常规的最佳做法。

### <a name="storage-requirements"></a>存储要求

- Azure Stack HCI 可使用直接连接的 SATA、SAS、NVMe 或永久性内存驱动器，每个驱动器使用物理方式仅连接到一个服务器。
- 群集中每个服务器都应具有相同类型的驱动器，各类型驱动器的数量也应该相同。 另外，建议（但不要求）驱动器的大小和型号也相同。 驱动器可以是服务器的内部驱动器，也可以是仅连接到一台服务器的外接盒。 若要了解详细信息，请参阅[驱动器对称性注意事项](../concepts/drive-symmetry-considerations.md)。
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

如果在服务器上安装 Windows Admin Center，则那些需要 CredSSP 的任务（例如创建群集以及安装更新和扩展）使用的帐户必须是 Windows Admin Center 服务器上的“网关管理员”组的成员。 有关详细信息，请参阅[配置用户访问控制和权限](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)的前两个部分。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何部署 Azure Stack HCI 操作系统。
> [!div class="nextstepaction"]
> [部署 Azure Stack HCI 操作系统](operating-system.md)
