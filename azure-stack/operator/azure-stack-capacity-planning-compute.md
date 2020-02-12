---
title: Azure Stack 中心计算容量
description: 了解 Azure Stack 中心部署的计算容量规划。
author: ihenkel
ms.topic: article
ms.date: 07/16/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 06/13/2019
ms.openlocfilehash: c2745aba7d58ec6afda404307e8d3dbd47b62846
ms.sourcegitcommit: 0a3c8b0bf9c116a5caaeca453a2bbc6e7f7cbfb9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77147856"
---
# <a name="azure-stack-hub-compute-capacity"></a>Azure Stack 中心计算容量

Azure Stack 中心支持的[虚拟机（VM）大小是 Azure 上支持的虚拟机（VM）大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)的子集。 Azure 会对许多媒介施加资源限制，以避免过度资源（服务器本地和服务级别）。 如果未对租户消耗施加某些限制，则当其他租户 overconsume 资源时，租户体验将会受到影响。 对于 VM 传出的网络流量，与 Azure 限制匹配的 Azure Stack 集线器上有带宽上限。 对于 Azure Stack 集线器上的存储资源，存储 IOPS 限制通过租户实现存储访问限制，从而避免对资源的基本消耗。

>[!IMPORTANT]
>[Azure Stack 中心 Capacity Planner](https://aka.ms/azstackcapacityplanner)不考虑或保证 IOPS 性能。

## <a name="vm-placement"></a>VM 位置

Azure Stack 集线器位置引擎将租户 Vm 放置在可用的主机上。

放置 Vm 时，Azure Stack 集线器使用两个注意事项。 一，主机上是否有足够的内存用于该 VM 类型？ 两个虚拟机是[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)的一部分还是[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)？

若要在 Azure Stack 中心实现多 VM 生产系统的高可用性，请将虚拟机（Vm）置于可跨多个容错域传播这些虚拟机的可用性集中。 可用性集中的容错域定义为缩放单位中的单个节点。 Azure Stack 中心支持拥有最多三个容错域的可用性集，以便与 Azure 保持一致。 放置在可用性集中的 Vm 将以物理方式彼此隔离，方法是在多个容错域（Azure Stack 中心主机）上均匀地分布它们。 如果发生硬件故障，故障容错域中的 Vm 将在其他容错域中重新启动。 如果可能，它们将保存在同一可用性集中其他 Vm 的单独容错域中。 当主机恢复联机时，将重新平衡 Vm 以保持高可用性。  

虚拟机规模集在后端使用可用性集，并确保将每个虚拟机规模集实例放置在不同的容错域中。 这意味着它们使用不同的 Azure Stack 中心基础结构节点。 例如，在具有四个节点的 Azure Stack 集线器系统中，可能会出现以下情况：由于缺乏四节点容量而导致在三个单独的 Azure 上放置三个虚拟机规模集实例，导致创建三个实例的虚拟机规模集失败堆栈中心节点。 此外，在尝试放置之前，可以在不同级别上填充 Azure Stack 中心节点。

Azure Stack 集线器不过载内存。 但允许过载的物理内核数。

由于放置算法不会考虑现有的虚拟到物理内核的过度预配比率，因此每个主机都有不同的比率。 作为 Microsoft，我们不提供针对物理到虚拟核心比的指导，因为工作负荷和服务级别要求不同。

## <a name="consideration-for-total-number-of-vms"></a>Vm 总数注意事项

准确规划 Azure Stack 集线器容量有一个新的注意事项。 使用1901更新（以及今后的每个更新），现在可以创建的 Vm 总数有限制。 此限制旨在临时避免解决方案不稳定。 正在处理更多 Vm 上的稳定性问题源，但尚未确定用于修正的特定时间线。 现在，每个服务器的最大 Vm 数限制为60，总解决方案限制为700。 例如，8服务器 Azure Stack 中心 VM 限制为480（8 * 60）。 对于12到16台服务器 Azure Stack 集线器解决方案，此限制为700。 创建此限制后，请记住所有计算容量注意事项，如复原预留和运算符要在戳记上维护的 CPU 虚拟与物理的比率。 有关详细信息，请参阅容量规划器的新版本。

如果达到了 VM 规模限制，将返回以下错误代码，结果为： `VMsPerScaleUnitLimitExceeded`，`VMsPerScaleUnitNodeLimitExceeded`。

## <a name="considerations-for-deallocation"></a>解除分配的注意事项

当 VM 处于 "已_解除分配_" 状态时，不会使用内存资源。 这允许将其他 Vm 放置在系统中。

如果重新启动解除分配的 VM，则会将内存使用量或分配视为系统中的新 VM，并使用可用内存。

如果没有可用内存，VM 将无法启动。

## <a name="azure-stack-hub-memory"></a>Azure Stack 集线器内存

Azure Stack 中心用于保持已成功预配的运行中的 Vm。 例如，如果某个主机因硬件故障而处于脱机状态，Azure Stack 集线器将尝试在另一个主机上重新启动该 VM。 Azure Stack 中心软件的修补和更新过程中的第二个示例。 如果需要重新启动物理主机，则会尝试将在该主机上执行的 Vm 移到解决方案中的其他可用主机上。

仅当有保留的内存容量以便重新启动或迁移时，才能实现此 VM 管理或移动。 主机内存的一部分已保留，不能用于租户 VM 位置。

你可以在管理员门户中查看显示 Azure Stack 集线器中的可用内存和已用内存的饼图。 下图显示了 Azure Stack 中心的 Azure Stack 集线器缩放单位上的物理内存容量：

![Azure Stack 中心缩放单位上的物理内存容量](media/azure-stack-capacity-planning/physical-memory-capacity.png)

使用的内存由多个组件组成。 以下组件使用饼图的 "使用" 部分中的内存：  

- **主机操作系统使用或保留：** 主机上的操作系统（OS）、虚拟内存页表、在主机操作系统上运行的进程和空格直接内存缓存上的操作系统所使用的内存。 由于此值依赖于主机上运行的不同 Hyper-v 进程使用的内存，因此它可能会波动。
- **基础结构服务：** 构成 Azure Stack 中心的基础结构 Vm。 从 Azure Stack 集线器的1904版发行版起，这需要大约 31 31 个 Vm，占用 242 GB 的内存（4 GB 的节点）。 基础结构服务组件的内存使用率可能会改变，因为我们正在努力提高基础结构服务的可伸缩性和复原能力。
- **复原预留：** Azure Stack 中心保留部分内存，以便在单个主机故障期间以及在修补和更新期间允许 Vm 的实时迁移成功。
- **租户 vm：** Azure Stack 集线器用户创建的租户 Vm。 除了运行 Vm 之外，在构造上具有着陆的任何 Vm 均使用内存。 这意味着，"创建" 或 "失败" 状态的 Vm 或从来宾内部关闭的 Vm 会消耗内存。 但是，使用门户/powershell/cli 中的 "停止解除分配" 选项解除分配的 Vm 不会消耗 Azure Stack 集线器中的内存。
- **增值资源提供程序（RPs）：** 部署的 Vm 的值-添加 RPs，如 SQL、MySQL、应用服务等。

了解门户上的内存消耗的最佳方式是使用[Azure Stack 中心 Capacity Planner](https://aka.ms/azstackcapacityplanner)来了解各种工作负荷的影响。 以下计算与 planner 使用的相同。

此计算将产生可用于租户 VM 位置的可用内存总量。 此内存容量适用于整个 Azure Stack 集线器缩放单位。

VM 位置的可用内存 = 总主机内存-复原保留-运行租户 Vm 所使用的内存-Azure Stack 集线器基础结构开销<sup>1</sup>

复原预留 = H + R * （（N-1） * H） + V * （N-2）

> 其中：
> - H = 单一服务器内存的大小
> - N = 缩放单位的大小（服务器数）
> - R = 操作系统的系统开销保留，在此公式中为 .15<sup>2</sup>
> - V = 缩放单位中的最大 VM

<sup>1</sup> Azure Stack 集线器基础结构开销 = 242 gb + （4 GB x # 节点）。 大约31个 Vm 用于承载 Azure Stack 集线器的基础结构，并且总消耗大约 242 GB + （4 GB x # 的节点）的内存和146虚拟核心。 这种 Vm 的基本原理是满足所需的服务分离，以满足安全性、可伸缩性、服务和修补要求。 此内部服务结构允许将来引入新的基础结构服务。

<sup>2</sup>用于开销的操作系统预留 = 15% （15）节点内存。 操作系统保留值是一个估计值，将根据服务器的物理内存容量和一般操作系统开销而有所不同。

缩放单位中的最大 VM 值为 V，这是基于最大租户 VM 内存大小动态实现的。 例如，在 Azure Stack 中心解决方案中，最大 VM 值可以是 7 GB 或 112 GB 或任何其他受支持的 VM 内存大小。 更改 Azure Stack 集线器构造上的最大 VM 将导致复原预留增加，同时还会增加 VM 自身的内存量。

## <a name="frequently-asked-questions"></a>常见问题

**问**：我的租户部署了新的 VM，管理员门户上的功能图表需要多长时间才能显示剩余容量？

**答**：容量边栏选项卡每15分钟刷新一次，因此请考虑到这一点。

**问**：我的 Azure Stack 集线器上部署的虚拟机的数量没有变化，但容量正在波动。 为什么?

**答**： VM 放置的可用内存具有多个依赖项，其中一个是主机操作系统保留项。 此值取决于主机上运行的不同 Hyper-v 进程使用的内存，这不是一个常数值。

**问**：租户 vm 需要处于哪些状态才能消耗内存？

**答**：除了运行 vm 之外，在构造上具有着陆的任何 vm 均使用内存。 这意味着处于 "正在创建" 或 "失败" 状态的 Vm 将消耗内存。 Vm 在来宾内关闭，而不是从门户/powershell/cli 停止分配的虚拟机也会消耗内存。

**问**：我有一个四宿主 Azure Stack 集线器。 我的租户有3个虚拟机，每个虚拟机使用 56 GB RAM （D5_v2）。 其中一个 Vm 调整大小为 112 GB RAM （D14_v2），在 "容量" 边栏选项卡上提供的内存报告峰值为 168 GB。 将其他两个 D5_v2 Vm 的后续调整到 D14_v2 会导致每个虚拟机仅增加 56 GB 的 RAM。 为什么要这样做呢？

**答**：可用内存是 Azure Stack 集线器维护的复原预留的函数。 复原预留是 Azure Stack 集线器戳记上的最大 VM 大小的函数。 首先，戳记上的最大 VM 为 56 GB 内存。 调整 VM 的大小时，戳记上的最大 VM 成为 112 GB 内存，不仅增加了该租户 VM 使用的内存，还增加了复原预留。 此更改导致增加了 56 GB （56 GB 到 112 GB 租户 VM 内存增加） + 112 GB 复原预留内存增加。 当调整了后续 Vm 的大小时，最大的 VM 大小将保留为 112 GB VM，因此，不会提高复原预留量。 内存占用增加仅限租户 VM 内存增加（56 GB）。

> [!NOTE]
> 网络的容量规划要求非常小，因为只有公共 VIP 的大小是可配置的。 有关如何向 Azure Stack 中心添加更多公共 IP 地址的信息，请参阅[添加公共 ip 地址](azure-stack-add-ips.md)。

## <a name="next-steps"></a>后续步骤
了解[Azure Stack 中心存储](azure-stack-capacity-planning-storage.md)
