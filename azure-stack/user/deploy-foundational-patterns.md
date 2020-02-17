---
title: 在 Azure Stack 集线器上部署基础模式
description: 了解如何通过 Azure Stack 中心部署基础模式。
author: mattbriggs
ms.topic: how-to
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: d35fd71b0f380c2594bc9aee8507162d5d83bda9
ms.sourcegitcommit: 7e55c8a4e4ef9c0a3494d42160c923b6585ed3bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77352106"
---
# <a name="deploy-foundational-patterns-overview"></a>部署基础模式概述


其中每个模式都包含指南、Azure 资源管理器模板和教程。 可以将这些模式与第三方应用一起使用，以创建 Azure Stack 尚不支持的产品/服务。 例如，运算符经常处理将虚拟专用网（VPN）设置为单个 Azure Stack 集线器实例所涉及的复杂性，这大大减少了创建跨越两个或多个环境的 VPN。 尝试在 Azure Stack 集线器前面创建负载均衡器来管理工作负荷时，操作员可能会遇到问题。 通过以下指南，你可以加快发布生产就绪工作负荷的部署时间。

## <a name="networking"></a>网络

使用网络模式查找有关使用 Azure Stack 集线器创建虚拟网络对等互连的说明。 通过虚拟网络对等互连，可以连接两个虚拟网络，使其显示为单个网络。 跨虚拟网络的站点到站点连接是通过远程和路由服务（RRAS）实现的。 RRAS 允许 Windows 虚拟机（VM）作为路由器使用。 利用这些脚本，你可以跨一个 Azure Stack 中心资源组、多个订阅和两个 Azure Stack 中心实例跨个资源组部署两个虚拟网络。 可以在 Azure Stack Hub 和全球 Azure 上部署脚本。 

每篇文章都解决了常见的考虑因素，例如： 
- 缩放
- 带宽
- 安全性
- 业务连续性

|  虚拟网络对等互连  |  VPN  |  负载均衡器  |
| --- | --- | --- |
| 与 Vm](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg) ![虚拟网络对等互连<br>[与 Vm 的虚拟网络对等互连](azure-stack-network-howto-vnet-peering.md) | ![将 VPN 设置为本地](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[设置 VPN 到本地](azure-stack-network-howto-vnet-to-onprem.md) | ![F5 负载均衡器](media/deploy-foundational-patterns/icon-networking-62-load-balancers.svg)<br>[F5 负载均衡器](network-howto-f5.md) |
| 与 FortiGate](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg) ![虚拟网络对等互连<br>[与 FortiGate 的虚拟网络对等互连](azure-stack-network-howto-vnet-to-vnet.md) | ![虚拟专用网络](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[虚拟网络到虚拟网络的连接](azure-stack-network-howto-vnet-to-vnet-stacks.md) |  |
|  | ![创建 VPN 隧道（GRE）](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[创建 VPN 隧道（GRE）](network-howto-vpn-tunnel-gre.md) | |
|  | ![设置多个站点到站点 VPN](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[设置多站点到站点 VPN](network-howto-vpn-tunnel.md) | |
|  | ![创建 VPN 隧道（IPSEC）](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[创建 VPN 隧道（IPSEC）](network-howto-vpn-tunnel-ipsec.md)| |


## <a name="storage"></a>存储

使用存储模式，通过 Azure Stack 集线器增加存储选项。 Azure Stack 中心存储是有限的。 连接到现有数据中心内的资源。 查找有关在 Azure Stack Hub 中创建 Windows VM 以连接到外部 iSCSI 目标的说明。 你可以了解如何启用关键功能，如多路径 i/o （MPIO），以优化 VM 与外部存储之间的性能和连接性。

| iSCSI 存储 | 扩展存储 |
| --- | --- | --- |
| ![连接到 iSCSI 存储](media/deploy-foundational-patterns/icon-storage-87-storage-accounts-classic.svg)<br>[连接到 iSCSI 存储](azure-stack-network-howto-iscsi-storage.md) | ![扩展数据中心](media/deploy-foundational-patterns/icon-storage-88-recovery-services-vaults.svg)<br>[扩展数据中心](azure-stack-network-howto-extend-datacenter.md) |

## <a name="backup"></a>备份

你可以使用备份和灾难恢复模式将订阅中的所有资源复制到 Azure 或另一个 Azure Stack 中心实例。 这些模式介绍了如何使用 Commvault 实时同步将存储在 Vm 内部的信息复制到另一个环境中。 你可以找到用于创建存储帐户的脚本，以及用于发送数据的备份存储帐户。 使用模块 Azure 订阅复制器可以协调资源复制，还可以自定义处理器来处理各种资源。 



|  备份  |  复制  |
| --- | --- | --- |
| ![通过 Commvault 在 Azure Stack 中心备份 VM](media/deploy-foundational-patterns/icon-storage-100-import-export-jobs.svg)<br>[通过 Commvault 在 Azure Stack 集线器上备份 VM](azure-stack-network-howto-backup-commvault.md) | ![复制订阅资源](media/deploy-foundational-patterns/icon-storage-94-data-box.svg)<br>[复制订阅资源](azure-stack-network-howto-backup-replicator.md) |
|  | ![在 Azure Stack 中心备份存储帐户](media/deploy-foundational-patterns/icon-storage-93-storage-sync-services.svg)<br>[在 Azure Stack 集线器上备份存储帐户](azure-stack-network-howto-backup-storage.md)  |

## <a name="github-samples"></a>GitHub 示例

可以在[Azure 智能边缘模式 GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)存储库中找到这些模板。

## <a name="next-steps"></a>后续步骤

[Azure 混合模式和解决方案文档](https://docs.microsoft.com/azure-stack/hybrid/)
