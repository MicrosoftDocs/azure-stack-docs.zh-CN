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
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: e549413798ffc3c06c95bfbcf50ab4929ffeaf63
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461021"
---
# <a name="azure-stack-compute"></a>Azure Stack 计算

[VM 大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)Azure Stack 支持的是那些在 Azure 上受支持的子集。 Azure 在多方面施加资源限制，以避免资源（服务器本地和服务级别）的过度消耗。 如果未对租户使用资源施加一些限制，则当一些租户过度使用资源时，另一些租户的体验就会变差。 VM 的网络出口在 Azure Stack 上有与 Azure 限制一致的带宽上限。 对于存储资源，存储 IOPS 限制已经实现了在 Azure Stack，以避免资源过度的存储访问的租户。

>[!IMPORTANT]
>[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)不会考虑或保证 IOPS 性能。

## <a name="vm-placement"></a>VM 放置

在 Azure Stack 中，租户 VM 放置是通过自动放置引擎个可用主机。 放置 Vm 时的只有两个注意事项是该 VM 类型的主机上是否有足够的内存和虚拟机属于[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)或是[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。  

若要实现高可用性的 Azure Stack 中的多 VM 生产系统，需要将 Vm 分散到多个容错域的可用性集放置。 在可用性集中的容错域定义为缩放单位中的单个节点。 为了与 Azure 保持一致，Azure Stack 支持的可用性集最多有三个容错域。 放置在可用性集中的虚拟机将通过跨多个容错域，即，Azure Stack 主机尽可能均匀地分布它们物理上彼此隔离。 出现硬件故障时，发生故障的容错域中的 VM 会在其他容错域中重启，但在将其置于容错域中时，会尽可能让其与同一可用性集中的其他 VM 隔离。 当主机重新联机后时，Vm 将重新平衡，以维持高可用性。  

虚拟机规模集使用可用性集背面结束，并确保每个虚拟机规模集实例位于不同的容错域中。 这意味着它们使用单独的 Azure Stack 基础结构节点。 例如，在 4 节点 Azure Stack 系统，可能有其中的 3 个实例的虚拟机规模集将在 3 个单独的 Azure Stack 节点上放置 3 个虚拟机规模集实例的 4 节点容量所致的创建失败的情况。 此外，Azure Stack 节点可以在不同级别之前尝试放置, 填满中。 

Azure Stack 不超量使用内存。 但是，允许一个过度提交的物理内核数。 由于现有的虚拟与物理内核作为一个因素的过度预配之比不查看放置算法，每个主机可能具有不同的比。 作为 Microsoft，我们不提供指南上的物理到虚拟内核比由于工作负荷和服务级别要求。 

## <a name="azure-stack-memory"></a>Azure Stack 内存 

Azure Stack 方法旨在保持运行已成功预配的 Vm。 例如，如果主机处于脱机状态，由于硬件故障，Azure Stack 将尝试重新启动该 VM 在另一台主机上。 第二个示例是修补和更新 Azure Stack 软件。 如果需要重新启动物理主机，尝试继续执行到解决方案中的另一个可用主机的主机上的 Vm。   

此 VM 管理或移动可仅实现保留的内存容量，以允许重新启动或迁移发生时。 主机内存总量的部分是保留和租户 VM 放置虚拟机不可用。 

你可以查看显示在 Azure Stack 中的可用和已用内存在管理门户中的饼图。 下图显示了 Azure Stack 中 Azure Stack 缩放单位上的物理内存容量：

![物理内存容量](media/azure-stack-capacity-planning/physical-memory-capacity.png)

已用的内存由多个组件组成。 以下组件使用的内存的使用部分中的饼图。  

- 主机操作系统使用情况或保留 – 这在使用操作系统 (OS) 的内存主机、 虚拟内存页表、 主机 OS 和空间直通的内存缓存运行的进程。 
- 基础结构服务 – 这些是构成 Azure Stack Vm 的基础结构。 从 Azure Stack 1902年发布版本，这需要占用 242 GB 的 31 Vm + (4 GB x 节点数)。 这种内部服务结构允许在将来引入新开发的基础结构服务。
- 复原能力保留-Azure Stack 保留了一部分内存允许的租户可用性在单个主机发生故障期间以及修补和更新期间允许成功的实时迁移的 Vm。 
- 租户 Vm – 这些是租户的 Azure Stack 用户创建的 Vm。 除了运行虚拟机，使用的构造已经到达了任何虚拟机的内存。 这意味着，中的 Vm **Creating**或**失败**状态或关闭的情况下从来宾内 Vm 将会占用内存。 但是，Vm 已解除分配使用停止解除分配选项不会使用从 Azure Stack 的内存。 

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

> [!NOTE]
> 网络方面的容量规划要求很少，因为能够配置的只有公共 VIP 的大小。 有关如何将多个公共 IP 地址添加到 Azure Stack 的信息，请参阅[添加公共 IP 地址](azure-stack-add-ips.md)。

## <a name="next-steps"></a>后续步骤
了解有关[Azure Stack 存储](azure-stack-capacity-planning-storage.md)