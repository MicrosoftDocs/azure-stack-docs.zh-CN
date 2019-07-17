---
title: Azure Stack 容量规划计算 |Microsoft Docs
description: 了解适用于 Azure Stack 部署的容量规划。
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 06/13/2019
ms.openlocfilehash: 224f5832af5d7fdc57f6b5fcb91d6308d479448b
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286709"
---
# <a name="azure-stack-compute"></a>Azure Stack 计算

[VM 大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)Azure Stack 支持的是那些在 Azure 上受支持的子集。 Azure 在多方面施加资源限制，以避免资源（服务器本地和服务级别）的过度消耗。 如果未对租户使用资源施加一些限制，则当一些租户过度使用资源时，另一些租户的体验就会变差。 VM 的网络出口在 Azure Stack 上有与 Azure 限制一致的带宽上限。 对于 Azure Stack 上的存储资源，存储 IOPS 限制，而避免基本租户为了访问存储资源的消耗。

>[!IMPORTANT]
>[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)不会考虑或保证 IOPS 性能。

## <a name="vm-placement"></a>VM 放置

Azure Stack 放置引擎会将所有可用主机的租户 Vm。

放置 Vm 时，azure Stack 使用两个注意事项。 之一，是足够的内存的 VM 类型的主机上。 两个，是的 Vm 的一部分[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)或是[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。

若要实现高可用性的 Azure Stack 中的多 VM 生产系统，需要将 Vm 分散到多个容错域的可用性集放置。 在可用性集中的容错域定义为缩放单位中的单个节点。 为了与 Azure 保持一致，Azure Stack 支持的可用性集最多有三个容错域。 放置在可用性集中的虚拟机将通过跨多个容错域，即，Azure Stack 主机尽可能均匀地分布它们物理上彼此隔离。 出现硬件故障时，发生故障的容错域中的 VM 会在其他容错域中重启，但在将其置于容错域中时，会尽可能让其与同一可用性集中的其他 VM 隔离。 当主机重新联机后时，Vm 将重新平衡，以维持高可用性。  

虚拟机规模集使用可用性集背面结束，并确保每个虚拟机规模集实例位于不同的容错域中。 这意味着它们使用单独的 Azure Stack 基础结构节点。 例如，在四个节点 Azure Stack 系统，可能有其中的三个实例的虚拟机规模集将在创建，由于缺少的 4 节点容量来将三个虚拟机规模集实例放在三个单独的 Azure Stack 节点上失败的情况. 此外，Azure Stack 节点可以在不同级别之前尝试放置, 填满中。 

Azure Stack 不超量使用内存。 但是，允许一个过度提交的物理内核数。 

由于现有的虚拟与物理内核作为一个因素的过度预配之比不查看放置算法，每个主机可能具有不同的比。 作为 Microsoft，我们不提供物理到虚拟核心之比的指导，由于工作负荷和服务级别要求。 

## <a name="consideration-for-total-number-of-vms"></a>考虑因素的 Vm 总数 

准确规划 Azure Stack 容量时需要考虑一个新的因素。 使用 1901年更新 （和今后的每个更新），是现在可以创建虚拟机的总数限制。 此限制是暂时性的，目的是避免解决方案不稳定。 进行寻址的源的稳定性问题，更多的 Vm，但尚未确定具体的时间表以提高补救。 现在有了每个服务器限制 60 台虚拟机的总体解决方案限制为 700。 例如，8 个服务器的 Azure Stack VM 数目限制是 480 (8 * 60)。 对于 Azure Stack 解决方案的 12 到 16 个服务器上，限制为 700。 已创建此限制，请记住所有计算资源容量注意事项，例如保留的复原能力和 CPU 虚拟与物理运算符想要在标记上维护的比率。 有关详细信息，请参阅新版容量规划器。 

如果达到了 VM 规模限制，将返回以下错误代码：VMsPerScaleUnitLimitExceeded、VMsPerScaleUnitNodeLimitExceeded。

## <a name="considerations-for-deallocation"></a>要释放的的注意事项

当 VM 处于_解除分配_状态，则不会使用内存资源。 这允许其他人要放置在系统中的 Vm。 

然后重新启动已解除分配的 VM，如果内存使用情况或分配将其视为新的 VM 放入系统，并使用可用内存。 

如果没有可用的内存，将无法启动 VM。

## <a name="azure-stack-memory"></a>Azure Stack 内存 

Azure Stack 方法旨在保持运行已成功预配的 Vm。 例如，如果主机处于脱机状态，由于硬件故障，Azure Stack 将尝试重新启动该 VM 在另一台主机上。 第二个示例是修补和更新 Azure Stack 软件。 如果需要重新启动物理主机，尝试继续执行到解决方案中的另一个可用主机的主机上的 Vm。   

此 VM 管理或移动可仅实现保留的内存容量，以允许重新启动或迁移发生时。 主机内存总量的部分是保留和租户 VM 放置虚拟机不可用。 

你可以查看显示在 Azure Stack 中的可用和已用内存在管理门户中的饼图。 下图显示了 Azure Stack 中 Azure Stack 缩放单位上的物理内存容量：

![物理内存容量](media/azure-stack-capacity-planning/physical-memory-capacity.png)

已用的内存由多个组件组成。 以下组件使用的内存的使用部分中的饼图：  

 -  主机操作系统使用情况或保留 – 这在使用操作系统 (OS) 的内存主机、 虚拟内存页表、 主机 OS 和空间直通的内存缓存运行的进程。 由于此值是依赖于不同主机上运行的 HYPER-V 过程使用的内存，它可以节省成本。
 - 基础结构服务 – 这些是构成 Azure Stack Vm 的基础结构。 从 Azure Stack 1904年发行版本，这涉及到 ~ 31 Vm 占用 242 GB + (4 GB x 的节点) 的内存。 改变基础结构服务组件的内存使用率，因为我们正在努力使我们的基础结构服务更具可缩放和复原。
 - 复原能力保留-Azure Stack 保留了一部分内存允许的租户可用性在单个主机发生故障期间以及修补和更新期间允许成功的实时迁移的 Vm。
 - 租户 Vm – 这些是租户的 Azure Stack 用户创建的 Vm。 除了运行虚拟机，使用的构造已经到达了任何虚拟机的内存。 这意味着在"创建"或"失败"状态下，Vm 或 Vm 关闭的情况下从来宾内将会占用内存。 但是，Vm 已解除分配使用停止解除分配选项从门户/powershell/cli 不会使用从 Azure Stack 的内存。
 - 外接程序 RPs-如 SQL、 MySQL、 应用服务等外接程序 RPs Vm 的部署。


了解在门户上的内存使用情况的最佳方式是使用[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)了解各种工作负荷的影响。 以下计算是由规划器使用的相同。

此计算会导致可用于租户 VM 放置的总数、 可用内存。 此内存容量是在整个 Azure Stack 缩放单位。 


  VM 放置虚拟机的可用内存 = 主机内存总量的复原能力保留的内存使用的运行租户的 Vm-Azure Stack 基础结构开销<sup>1</sup>

  复原保留 = H + R * ((N-1) * H) + V * (N-2)

> 其中：
> - H = 单服务器内存的大小
> - N = 缩放单元的大小（服务器数）
> - R = 的操作系统保留 OS 开销，这是在此公式.15<sup>2</sup>
> - V = 缩放单元中的最大 VM

  <sup>1</sup> azure Stack 基础结构开销 = 242 GB + (4 GB x 节点数)。 大约 31 Vm 用于托管 Azure Stack 的基础结构，并且总共使用大约 242 GB + (4 GB x 的节点) 的内存和 146 虚拟核心数。 使用这个数目的 VM 是为了进行必要的服务隔离，使之符合安全性、可伸缩性、服务和修补方面的要求。 这种内部服务结构允许在将来引入新开发的基础结构服务。 

  <sup>2</sup>操作系统保留的开销 = 15%(。 15) 的节点内存。 操作系统保留值是一个估计值，具体取决于服务器的物理内存容量和常规的操作系统开销。


值 V（缩放单元中的最大 VM）是动态变化的，具体取决于最大的租户 VM 内存大小。 例如，最大 VM 值可能是 7 GB 或 112 GB，或者是 Azure Stack 解决方案中任何其他受支持的 VM 内存大小。 更改 Azure Stack fabric 上的最大 VM 将导致保留除 VM 本身的内存中增加的复原能力的增加。 

## <a name="frequently-asked-questions"></a>常见问题

**问**：我的租户部署新的 VM 时，多长时间将需要在管理门户上功能图表来显示剩余的容量？

**答**：在容量边栏选项卡每 15 分钟刷新，因此请考虑此问题。

**问**：我在 Azure Stack 上部署的 Vm 数未发生更改，但我容量有波动。 为什么？

**答**：可用内存的 VM 放置具有多个依赖关系，其中之一是主机操作系统预留。 此值是依赖于使用不是常量的值的主机上运行的不同的 HYPER-V 进程的内存。

**问**：何种状态租户 Vm 是否处于占用的内存？

v 部分：除了运行虚拟机，使用的构造已经到达了任何虚拟机的内存。 这意味着，在"创建"、"失败"或 Vm 关闭从内 g 的 Vm

**问**：我有四个主机 Azure Stack。 我的租户具有使用 56 GB RAM (D5_v2) 每个的 3 个 Vm。 一个 Vm 的大小调整为 112 GB RAM (D14_v2)，仪表板上报告的可用内存的峰值容量边栏选项卡上的 168 GB 使用情况。 后续重设大小的其他两个 D5_v2 Vm 到 D14_v2，导致只有 56 GB 的 RAM 增加。 为什么这是这样吗？

**答**：可用内存是由 Azure Stack 维护的复原能力保留一个函数。 保留的复原能力是在 Azure Stack 标记上的最大 VM 大小的函数。 首先，在标记上的最大 VM 是 56 GB 内存。 VM 已重设大小时，在标记上的最大 VM 变得 112 GB 内存，这不仅能增加该租户 VM 使用的内存，但还增加了保留的复原能力。 这会导致 56 GB (112 GB 租户 VM 内存增加到 56 GB) 的增加 + 112 GB 复原保留内存增加。 后续 Vm 已重设大小时，最大 VM 大小保持为 112 GB 的 VM，因此未生成复原保留增加。 内存消耗提高的感觉租户 VM 内存增加 (56 GB)。 


> [!NOTE]
> 网络方面的容量规划要求很少，因为能够配置的只有公共 VIP 的大小。 有关如何将多个公共 IP 地址添加到 Azure Stack 的信息，请参阅[添加公共 IP 地址](azure-stack-add-ips.md)。

## <a name="next-steps"></a>后续步骤
了解有关[Azure Stack 存储](azure-stack-capacity-planning-storage.md)
