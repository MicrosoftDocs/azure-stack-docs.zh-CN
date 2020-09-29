---
title: 部署 Azure Stack Hub 上的基础模式
description: 了解如何部署 Azure Stack Hub 的基础模式。
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: df603935dc192894dcb96837dc92fa4bc88f239e
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88818396"
---
# <a name="deploy-foundational-patterns-overview"></a>部署基础模式的概述


本文所述的每种模式都包含指导、Azure 资源管理器模板和教程。 可将这些模式与第三方应用配合使用，以创建 Azure Stack 尚不支持的套餐。 例如，操作员通常需要解决将虚拟专用网 (VPN) 设置为单一 Azure Stack Hub 实例所存在的复杂性，而不经常创建跨两个或更多个环境的 VPN。 尝试在 Azure Stack Hub 的前面创建负载均衡器来管理工作负荷时，操作员可能会遇到问题。 遵循以下指导可以加快生产就绪型工作负荷的部署和发行速度。

## <a name="networking"></a>网络

使用网络模式可以查找有关创建虚拟网络与 Azure Stack Hub 的对等互连的说明。 使用虚拟网络对等互连可以连接两个虚拟网络，使其显示为单个网络。 通过远程和路由服务 (RRAS) 完成跨虚拟网络的站点到站点连接。 RRAS 可让 Windows 虚拟机 (VM) 作为路由器运行。 使用这些脚本可以跨一个 Azure Stack Hub 资源组中的资源组、跨订阅以及跨两个 Azure Stack Hub 实例部署两个虚拟网络。 可以在 Azure Stack Hub 和全球 Azure 上部署脚本。 

每篇文章都会提出常见的注意事项，例如： 
- 缩放
- 带宽
- 安全性
- 业务连续性

|  虚拟网络对等互连  |  VPN  |  负载均衡器  |
| --- | --- | --- |
| ![虚拟网络与 VM 的对等互连](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[虚拟网络与 VM 的对等互连](azure-stack-network-howto-vnet-peering.md) | ![将 VPN 设置到本地](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[将 VPN 设置到本地](azure-stack-network-howto-vnet-to-onprem.md) | ![F5 负载均衡器](media/deploy-foundational-patterns/icon-networking-62-load-balancers.svg)<br>[F5 负载均衡器](network-howto-f5.md) |
| ![虚拟网络与 FortiGate 的对等互连](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[虚拟网络与 FortiGate 的对等互连](azure-stack-network-howto-vnet-to-vnet.md) | ![虚拟专用网络](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[虚拟网络到虚拟网络的连接](azure-stack-network-howto-vnet-to-vnet-stacks.md) |  |
|  | ![创建 VPN 隧道 (GRE)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[创建 VPN 隧道 (GRE)](network-howto-vpn-tunnel-gre.md) | |
|  | ![设置多站点到站点 VPN](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[设置多站点到站点 VPN](network-howto-vpn-tunnel.md) | |
|  | ![创建 VPN 隧道 (IPSEC)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[创建 VPN 隧道 (IPSEC)](network-howto-vpn-tunnel-ipsec.md)| |


## <a name="storage"></a>存储

使用存储模式可以增加 Azure Stack Hub 的存储选项。 Azure Stack Hub 中的存储是有限的。 连接到现有数据中心内的资源。 查找有关在 Azure Stack Hub 中创建 Windows VM 以连接到外部 iSCSI 目标的说明。 可以了解如何启用多路径 I/O (MPIO) 等重要功能，以优化性能以及 VM 与外部存储之间的连接。

| iSCSI 存储 | 扩展存储 |
| --- | --- |
| ![连接到 iSCSI 存储](media/deploy-foundational-patterns/icon-storage-87-storage-accounts-classic.svg)<br>[连接到 iSCSI 存储](azure-stack-network-howto-iscsi-storage.md) | ![扩展数据中心](media/deploy-foundational-patterns/icon-storage-88-recovery-services-vaults.svg)<br>[扩展数据中心](azure-stack-network-howto-extend-datacenter.md) |

## <a name="backup"></a>备份

可以使用备份和灾难恢复模式，将订阅中的所有资源复制到 Azure 或另一个 Azure Stack Hub 实例。 这些模式探讨如何使用 Commvault 实时同步将 VM 内部存储的信息复制到另一个环境。 可以查找用于创建存储帐户的脚本，以及用于发送数据的备份存储帐户。 使用 Azure 订阅复制器模块可以协调资源复制，并可以自定义处理器来处理各种资源。 



|  备份  |  复制  |
| --- | --- |
| ![使用 Commvault 在 Azure Stack Hub 上备份 VM](media/deploy-foundational-patterns/icon-storage-100-import-export-jobs.svg)<br>[使用 Commvault 在 Azure Stack Hub 上备份 VM](azure-stack-network-howto-backup-commvault.md) | ![复制订阅资源](media/deploy-foundational-patterns/icon-storage-94-data-box.svg)<br>[复制订阅资源](azure-stack-network-howto-backup-replicator.md) |
|  ![在 Azure Stack Hub 上备份存储帐户](media/deploy-foundational-patterns/icon-storage-93-storage-sync-services.svg)<br>[在 Azure Stack Hub 上备份存储帐户](azure-stack-network-howto-backup-storage.md)  | |

## <a name="github-samples"></a>GitHub 示例

可以在 [Azure 智能边缘模式](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) GitHub 存储库中找到这些模板。

## <a name="next-steps"></a>后续步骤

[Azure 混合模式和解决方案文档](/hybrid/app-solutions)
