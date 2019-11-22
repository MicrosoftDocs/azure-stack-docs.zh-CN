---
title: Azure Stack 容量规划计算 | Microsoft Docs
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
ms.openlocfilehash: dac0360bba7c24c85d1f30efbfb7fad30eb97028
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74299161"
---
# <a name="azure-stack-compute"></a>Azure Stack 计算

Azure Stack 上支持的 [VM 大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)是在 Azure 上支持的 VM 大小的子集。 Azure 在多方面施加资源限制，以避免资源（服务器本地和服务级别）的过度消耗。 如果未对租户使用资源施加一些限制，则当一些租户过度使用资源时，另一些租户的体验就会变差。 VM 的网络出口在 Azure Stack 上有与 Azure 限制一致的带宽上限。 对于 Azure Stack 上的存储资源，存储 IOPS 限制可避免租户为了访问存储而造成资源过度消耗。

>[!IMPORTANT]
>[Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) 不考虑或保证 IOPS 性能。

## <a name="vm-placement"></a>VM 定位

Azure Stack 定位引擎跨可用的主机放置租户 VM。

放置 VM 时，Azure Stack 使用两个考虑因素。 第一个考虑因素是主机上要有足够的内存可供该 VM 类型使用。 第二个考虑因素是 VM 必须是[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)或[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)的一部分。

为了在 Azure Stack 中实现多 VM 生产系统的高可用性，可以将 VM 置于横跨多个容错域的可用性集中。 可用性集中的容错域定义为缩放单元中的单个节点。 为了与 Azure 保持一致，Azure Stack 支持的可用性集最多有三个容错域。 置于可用性集中的 VM 在物理上是彼此隔离的，换句话说，会尽可能均衡地让其分散到多个容错域（Azure Stack 主机）中。 出现硬件故障时，发生故障的容错域中的 VM 会在其他容错域中重启，但在将其置于容错域中时，会尽可能让其与同一可用性集中的其他 VM 隔离。 当主机重新联机时，会对 VM 重新进行均衡操作，以维持高可用性。  

虚拟机规模集在后端使用可用性集，并确保每个虚拟机规模集实例都位于不同的容错域。 这意味着规模集使用不同的 Azure Stack 基础结构节点。 例如，在四节点 Azure Stack 系统中，可能存在这样一种情况：由于缺少 4 节点容量，无法将三个虚拟机规模集实例放置在三个单独的 Azure Stack 节点上，因此由三个实例组成的虚拟机规模集在创建时将失败。 此外，Azure Stack 节点可能先在不同的级别填满，然后尝试放置。 

Azure Stack 不会过度提交内存。 但是，允许过度提交物理核心数。 

由于放置算法不将现在的虚拟核心与物理核心的预配过度比率视为一个因素，因此每个主机可以有不同的比率。 作为 Microsoft，我们不提供针对物理到虚拟核心比的指导，因为工作负荷和服务级别要求不同。 

## <a name="consideration-for-total-number-of-vms"></a>VM 总数注意事项 

准确规划 Azure Stack 容量时需要考虑一个新的因素。 使用 1901 更新（包括之后的所有更新）时，现在可创建的虚拟机总数有限制。 此限制是暂时性的，目的是避免解决方案不稳定。 我们已解决大量 VM 的稳定性问题根源，但尚未确定补救措施的具体时间表。 现在每台服务器存在 60 个 VM 的限制，解决方案总体限制为 700 个。 例如，8 个服务器的 Azure Stack VM 数目限制是 480 (8 * 60)。 对于包含 12 到 16 个服务器的 Azure Stack 解决方案，限制为 700 个 VM。 确定此限制时，我们考虑到了所有计算容量，例如复原能力，以及运营商想要在阵列上保持的虚拟与物理 CPU 之比。 有关详细信息，请参阅新版容量规划器。 

如果已达到 VM 缩放限制，则会返回以下错误代码： VMsPerScaleUnitLimitExceeded、VMsPerScaleUnitNodeLimitExceeded。

## <a name="considerations-for-deallocation"></a>解除分配的注意事项

当 VM 处于“解除分配”状态时，不会使用内存资源。 这允许将其他 VM 放置在系统中。 

如果随后再次启动已解除分配的 VM，则内存使用或分配将像放置在系统中的新 VM 一样处理，并占用可用内存。 

如果没有可用内存，则 VM 将不会启动。

## <a name="azure-stack-memory"></a>Azure Stack 内存 

Azure Stack 旨在让已成功预配的 VM 持续运行。 例如，如果某台主机由于硬件故障而脱机，Azure Stack 会尝试在另一台主机上重启该 VM。 第二个示例是 Azure Stack 软件的修补升级。 如果需要重新启动物理主机，系统会尝试将该主机上运行的 VM 移到解决方案中另一台可用的主机上。   

这种 VM 管理或移动能够实现的前提是存在预留内存容量，允许重启或迁移发生。 整个主机内存中有一部分进行预留，此部分不可用于放置租户 VM。 

可以在管理门户中查看饼图，其中显示了 Azure Stack 中可用和已用的内存。 下图显示了 Azure Stack 中 Azure Stack 缩放单元上的物理内存容量：

![物理内存容量](media/azure-stack-capacity-planning/physical-memory-capacity.png)

已用内存由多个部分组成。 以下组件消耗饼了图中的已用内存部分：  

 -  主机 OS 的用量或预留量 – 这是主机上操作系统 (OS)、虚拟内存页面表、主机 OS 上所运行程序和空间直通内存缓存使用的内存。 此值依赖于主机上运行中的不同 Hyper-V 进程使用的内存，因此可能有浮动。
 - 基础结构服务 – 这些是构成 Azure Stack 的基础结构 VM。 自 Azure Stack 发行版 1904 开始，这需要将近 31 个最多使用 242 GB + (4 GB x 节点数) 的 VM。 我们一直在努力让基础结构服务变得更具可伸缩性和弹性，因此基础结构服务组件的内存用量可能会有变化。
 - 复原功能的预留量 – Azure Stack 预留一部分内存，用于在单个主机故障期间保持租户可用性，以及在修补和更新期间让 VM 成功进行实时迁移。
 - 租户 VM – 这些是 Azure Stack 用户创建的租户 VM。 除了运行 VM 以外，任何在结构上登陆的 VM 也消耗内存。 这些 VM 指处于“正在创建”或“失败”状态的 VM，或者从来宾关闭的 VM。 但是，已从门户/PowerShell/CLI 使用“停止解除分配”选项来解除分配的 VM 不会消耗 Azure Stack 中的内存。
 - 加载项 RP – 为加载项 RP 部署的 VM，例如 SQL、MySQL、应用服务等。


在门户上了解内存消耗量的最佳方式是使用 [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) 来查看各种工作负荷的影响。 以下计算方式与规划器使用的方式相同。

此计算会生成一个可用于租户 VM 放置的总可用内存。 该内存容量适用于整个 Azure Stack 缩放单元。 


  VM 放置的可用内存 = 主机总内存 - 复原保留 - 运行租户 VM 所使用的内存 - Azure Stack 基础结构开销<sup>1</sup>

  复原保留 = H + R * ((N-1) * H) + V * (N-2)

> 其中：
> - H = 单服务器内存的大小
> - N = 缩放单元的大小（服务器数）
> - R = 用于 OS 开销的操作系统预留量，在此公式中为 .15<sup>2</sup>
> - V = 缩放单元中的最大 VM

  <sup>1</sup> Azure Stack 基础结构开销 = 242 GB + (4 GB x 节点数)。 大约使用 31 个 VM 来托管 Azure Stack 基础结构，总共消耗约 242 GB + (4 GB x 节点数) 的内存和 146 个虚拟核心。 使用这个数目的 VM 是为了进行必要的服务隔离，使之符合安全性、可伸缩性、服务和修补方面的要求。 这种内部服务结构允许在将来引入新开发的基础结构服务。 

  <sup>2</sup> 操作系统针对开销的保留 = 15% (.15) 的节点内存。 操作系统保留值是一个估计值，具体取决于服务器的物理内存容量和常规的操作系统开销。


值 V（缩放单元中的最大 VM）是动态变化的，具体取决于最大的租户 VM 内存大小。 例如，最大 VM 值可能是 7 GB 或 112 GB，或者是 Azure Stack 解决方案中任何其他受支持的 VM 内存大小。 更改 Azure Stack 结构上最大的 VM 除了导致 VM 本身的内存增加外，还会增大复原预留量。 

## <a name="frequently-asked-questions"></a>常见问题

**问**：我的租户部署了新的 VM，管理门户上的功能图表需要多长时间才能显示剩余容量？

**答**：容量边栏选项卡每15分钟刷新一次，因此请考虑到这一点。

**问**：我 Azure Stack 上部署的虚拟机的数量没有变化，但容量正在波动。 为什么？

**答**： VM 放置的可用内存具有多个依赖项，其中一个是主机操作系统保留项。 此值取决于主机上运行的不同 Hyper-V 进程所使用的内存，它不是常量值。

**问**：租户 vm 需要处于哪些状态才能消耗内存？

**答**：除了运行 vm 之外，在构造上具有着陆的任何 vm 均使用内存。 这意味着，在 "正在创建"、"失败" 或 Vm 中关闭的 Vm，而不是从门户/powershell/cli 中停止分配的虚拟机将消耗内存。

**问**：我有一个 Azure Stack 的主机。 我的租户中有 3 个 VM，它们各自消耗 56 GB RAM (D5_v2)。 其中一个 VM 的大小调整为 112 GB RAM (D14_v2)，仪表板上的可用内存报告导致容量边栏选项卡上出现 168 GB 的用量高峰。 后续将另外两个 D5_v2 VM 的大小调整为 D14_v2 时，各自只导致增加 56 GB 的 RAM。 为什么会这样？

**答**：可用内存是由 Azure Stack 维护的复原预留的函数。 复原预留量受 Azure Stack 阵列上最大 VM 大小的影响。 最初，阵列上的最大 VM 是 56 GB 内存。 调整 VM 大小后，阵列上的最大 VM 将变成 112 GB 内存，这不只会增大该租户 VM 使用的内存，也会增大复原预留量。 结果便是增加 56 GB（租户 VM 内存从 56 GB 增加到 112 GB）+ 增加 112 GB 复原预留内存。 后续调整 VM 大小时，最大 VM 的大小仍保持为 112 GB VM，因此没有增加任何复原预留量。 内存消耗量的增量只是租户 VM 内存增量 (56 GB)。 


> [!NOTE]
> 网络方面的容量规划要求很少，因为能够配置的只有公共 VIP 的大小。 若要了解如何向 Azure Stack 添加更多的公共 IP 地址，请参阅[添加公共 IP 地址](azure-stack-add-ips.md)。

## <a name="next-steps"></a>后续步骤
了解 [Azure Stack 存储](azure-stack-capacity-planning-storage.md)
