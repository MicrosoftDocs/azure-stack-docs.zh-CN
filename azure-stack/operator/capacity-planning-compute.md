---
title: Azure Stack 的计算容量规划 | Microsoft Docs
description: 了解适用于 Azure Stack 部署的计算容量规划。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 04/03/2019
ms.custom: ''
ms.openlocfilehash: f20dd8499abf4d8c7fd2097d47e655790c19d33d
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984005"
---
# <a name="azure-stack-compute-capacity-planning"></a>Azure Stack 计算容量规划
[Azure Stack 上支持的 VM 大小](../user/azure-stack-vm-sizes.md)是在 Azure 上支持的 VM 大小的子集。 Azure 在多方面施加资源限制，以避免资源（服务器本地和服务级别）的过度消耗。 如果未对租户使用资源施加一些限制，则当一些租户过度使用资源时，另一些租户的体验就会变差。 VM 的网络出口在 Azure Stack 上有与 Azure 限制一致的带宽上限。 就存储资源来说，在 Azure Stack 上实施存储 IOPS 限制可以避免租户因访问存储而造成资源过度使用。  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>VM 放置以及虚拟核心与物理核心的比率预配过度
在 Azure Stack 中，租户无法将特定的服务器指定用于 VM 放置。 放置 VM 时，唯一的考虑是主机上是否有足够的内存用于该 VM 类型。 Azure Stack 不允许过度使用内存，但允许过度使用核心数。 由于放置算法不将现在的虚拟核心与物理核心的预配过度比率视为一个因素，因此每个主机可以有不同的比率。 

在 Azure 中，为了实现多 VM 生产系统的高可用性，可以将 VM 置于横跨多个容错域的可用性集中。 在 Azure Stack 中，可用性集中的容错域定义为缩放单元中的单个节点。

在发生硬件故障时，虽然 Azure Stack 的基础结构具备故障还原能力，但基础技术（故障转移群集功能）的局限仍会导致受影响物理服务器上的 VM 出现停机。 目前，为了与 Azure 保持一致，Azure Stack 支持的可用性集最多有三个容错域。 置于可用性集中的 VM 在物理上是彼此隔离的，换句话说，会尽可能均衡地让其分散到多个容错域（Azure Stack 节点）中。 出现硬件故障时，发生故障的容错域中的 VM 会在其他节点中重启，但在将其置于容错域中时，会尽可能让其与同一可用性集中的其他 VM 隔离。 当硬件重新联机时，会对 VM 重新进行均衡操作，以维持高可用性。

另一个由 Azure 用于提供高可用性的概念采用可用性集中的更新域的形式。 更新域是可以同时维护或重新启动的基础硬件逻辑组。 在 Azure Stack 中，VM 会先跨群集中的其他联机主机进行实时迁移，然后其基础主机才会进行更新。 由于在主机更新期间不会造成租户停机，因此 Azure Stack 上存在更新域功能只是为了确保与 Azure 实现模板兼容。

## <a name="azure-stack-resiliency-resources"></a>Azure Stack 可恢复性资源
为了对 Azure Stack 集成系统进行修补和更新，以及为了确保物理硬件故障时的复原能力，系统需保留总服务器内存的一部分，该部分不可用于租户虚拟机 (VM) 放置。

如果某个服务器故障，托管在故障服务器上的 VM 会在其余的可用服务器上重启，以确保 VM 可用性。 类似地，在修补和更新过程中，在某个服务器上运行的 VM 会实时迁移到其他可用服务器。 这种 VM 管理或移动能够实现的前提是存在保留容量，允许重启或迁移发生。

以下计算会生成一个可用于租户 VM 放置的总可用内存。 该内存容量适用于整个 Azure Stack 缩放单元。

  VM 放置的可用内存 = 服务器总内存 - 复原保留 - 运行 VM 所使用的内存 - Azure Stack 基础结构开销<sup>1</sup>

  复原能力保留 = H + R * ((N-1) * H) + V * (N-2)

> 其中：
> - H = 单服务器内存的大小
> - N = 缩放单元的大小（服务器数）
> - R = 操作系统针对 OS 开销的保留<sup>2</sup>
> - V = 缩放单元中的最大 VM

  <sup>1</sup> azure Stack 基础结构开销 = 230 GB

  <sup>2</sup> 操作系统针对开销的保留 = 15% 的节点内存。 操作系统保留值是一个估计值，具体取决于服务器的物理内存容量和常规的操作系统开销。

值 V（缩放单元中的最大 VM）是动态变化的，具体取决于最大的租户 VM 内存大小。 例如，最大 VM 值可能是 7 GB 或 112 GB，或者是 Azure Stack 解决方案中任何其他受支持的 VM 内存大小。

上述计算是一个估计值，可能因 Azure Stack 的当前版本而异。 能否部署租户 VM 和服务取决于已部署解决方案的具体情况。 此示例计算仅作参考，不是能否部署 VM 的绝对标准。



## <a name="next-steps"></a>后续步骤
[存储容量规划](capacity-planning-storage.md)
