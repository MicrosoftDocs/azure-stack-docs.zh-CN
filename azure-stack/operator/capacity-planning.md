---
title: Azure Stack 的容量规划 | Microsoft Docs
description: 了解适用于 Azure Stack 部署的容量规划。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: prchint
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: ad8fdc9181bb013735b8787658d1893e28c3799c
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618849"
---
# <a name="azure-stack-capacity-planning"></a>Azure Stack 容量规划
评估 Azure Stack 解决方案时，必须在硬件配置方面做出选择，因为它直接影响到 Azure Stack 云的总体容量。 这些是有关 CPU、内存密度、存储配置和总体解决方案规模或服务器数目的常规选择。 不同于传统的虚拟化解决方案，简单地评估这些组件并不能很好地确定可用的容量。 这方面的第一个原因是 Azure Stack 构建为在解决方案自身内部托管基础结构或管理组件。 第二个原因是容量的某些解决方案容量的保留用于支持复原;一种方法来最大程度减少中断的租户工作负荷解决方案的软件更新。

> [!IMPORTANT]
> 提供的容量规划信息和随附的电子表格只是为了指导你做出 Azure Stack 规划和配置方面的决策。 它们不是为了取代你自己的调查和分析。 

## <a name="compute-and-storage-capacity-planning"></a>计算和存储容量规划
Azure Stack 解决方案是作为超融合群集构建的，包含计算、网络和存储。 这样可以有效地使用或共享群集（称为 Azure Stack 的缩放单元）中的所有硬件容量，所采用的方式既提供可用性，又提供可伸缩性。 所有基础结构软件托管在一组虚拟机 (VM) 中，与租户 VM 共享相同的物理服务器。 然后由缩放单位的 Windows Server 群集技术和单独的 HYPER-V 实例管理的所有 Vm。 此方法简化了 Azure Stack 解决方案的获取和管理，可以跨整个缩放单元移动和缩放所有服务（租户和体系结构）。

在 Azure Stack 解决方案中，唯一不会预配过度的物理资源是服务器内存。 其他资源（CPU 核心、网络带宽和存储容量）会预配过度，以便充分利用可用资源。 在计算解决方案的可用容量时，物理服务器内存是容量的主要依据。 然后，其他资源的利用率就是了解预配过度的比率的可能值与预期工作负荷可接受的值。

大约 30 台 Vm 用于托管 Azure Stack 的基础结构，并且总共使用大约有 230 GB 的内存和 140 虚拟核心数。 使用这个数目的 VM 是为了进行必要的服务隔离，使之符合安全性、可伸缩性、服务和修补方面的要求。 这种内部服务结构允许在将来引入新开发的基础结构服务。

为了自动更新所有物理服务器和基础结构软件组件，或者为了进行修补和更新，在进行基础结构和用户 VM 的放置时，不会使用缩放单元的所有内存资源。 将支持解决方案的可恢复性要求而未分配的缩放单位的所有服务器之间的总内存量。 例如，当更新物理服务器的 Windows Server 映像时，在服务器上托管的 Vm 会移动到其他位置缩放单位内更新服务器的 Windows Server 映像时。 更新完成后，服务器会重启并继续处理工作负荷。 对 Azure Stack 解决方案进行修补和更新的目的是避免停止托管的 VM。 努力满足该目标，一台服务器的内存容量的最小配置未分配，以便允许缩放单位内的 Vm 的运动。 这样的放置和移动适用于基础结构 VM 以及代表 Azure Stack 解决方案的用户或租户创建的 VM。 最终实现结果可以这样量的内存保留以支持所需的 VM 移动可能会远不止是单个服务器的容量由于放置问题时虚拟机具有不同的内存要求。 此类内存使用的一种额外开销是 Windows Server 实例本身的开销。 每个服务器的基础操作系统实例会耗用操作系统及其虚拟页表的内存，以及 Hyper-V 在管理每个托管的 VM 时使用的内存。

VM 容量取决于可用内存。 虚拟核心与物理核心的比率例证了 VM 密度对可用 CPU 容量造成的影响，解决方案在构建时使用了较大数目的物理核心（选择了另一 CPU）的情况除外。 存储容量和存储缓存容量的情况也是这样。

上述 VM 密度示例仅用于说明。 在提供支持时，所做的计算更为复杂。 与 Microsoft 联系或解决方案合作伙伴时需要进一步了解容量规划选择和生成的、 可用容量。

此部分的剩余内容介绍针对计算和存储的 Azure Stack 部署要求。 可以根据此信息对所需组件及其最低配置值有一个基本的了解。 此外还提供其他信息，介绍如何根据可用容量来配置解决方案，以及系统在租户容量和性能方面的潜在限制。

> [!NOTE]
> 网络方面的容量规划要求很少，因为能够配置的只有公共 VIP 的大小。 若要了解如何向 Azure Stack 添加更多的公共 IP 地址，请参阅[添加公共 IP 地址](azure-stack-add-ips.md)。


## <a name="next-steps"></a>后续步骤
[计算容量规划](capacity-planning-compute.md)
