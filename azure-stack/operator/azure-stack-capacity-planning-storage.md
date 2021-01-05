---
title: Azure Stack Hub 存储容量规划
description: 了解适用于 Azure Stack Hub 部署的存储容量规划。
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 69248d5a81cf3d1017f221a57549d75b205f0fc3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871253"
---
# <a name="azure-stack-hub-storage-capacity-planning"></a>Azure Stack Hub 存储容量规划

以下部分提供 Azure Stack Hub 存储容量规划信息，帮助你针对解决方案的存储需求进行规划。

## <a name="uses-and-organization-of-storage-capacity"></a>存储容量的使用和组织

Azure Stack Hub 的超融合配置允许共享物理存储设备。 可共享的可用存储的三个主要部分包括：基础结构存储、租户虚拟机 (VM) 的临时存储，以及为 Azure 一致存储 (ACS) 服务的 Blob、表和队列提供支持的存储。

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>存储空间直通缓存和容量层

存储容量可以用于操作系统、本地日志记录、转储和其他临时基础结构存储需求。 此本地存储容量独立于受存储空间直通配置管理的存储设备（设备和容量独立）。 其余存储设备置于单个存储容量池中，不管在缩放单元中的服务器数量为多少。

这些设备分为两种类型：缓存设备和容量设备。 存储空间直通使用缓存设备来写回和读取缓存。 这些缓存设备的容量在使用时，并不局限于已格式化虚拟磁盘的格式化“可见”容量。 相比之下，存储空间直通使用容量设备来实现此目的，并提供托管数据的“主位置”。

Azure Stack Hub 基础结构直接分配和管理所有存储容量。 操作人员无需在配置、分配和容量扩展方面做出选择。 Azure Stack Hub 在初始安装和部署期间或容量扩展期间会自动完成这些设计决策，以符合解决方案要求。 在设计过程中，Azure Stack Hub 会考虑到复原能力、用于重新生成的预留容量和其他详细信息。

操作员可以选择进行全闪存或混合存储配置   ：

![Azure 存储容量规划示意图](media/azure-stack-capacity-planning/storage.png)

在全闪存配置中，配置可以是双层配置，也可以是单层配置。 如果配置是单层配置，则所有容量设备将是相同类型（例如 *NVMe* 或 *SATA SSD* 或 *SAS SSD*），并且不使用缓存设备。 在双层全闪存配置中，典型配置是 NVMe 作为缓存设备，然后 SATA 或 SAS SSD 作为容量设备。

在混合双层配置中，缓存可在 NVMe、SATA 或 SAS SSD 之间进行选择，而容量则为 HDD。

存储空间直通和 Azure Stack Hub 存储配置概述如下：
- 每个缩放单元有一个存储存储空间池（所有存储设备在单个池中配置）。
- 虚拟磁盘创建为三副本镜像，以确保最佳性能和复原能力。
- 每个虚拟磁盘格式化为一个 ReFS 文件系统。
- 在对虚拟磁盘容量进行计算和分配时，会将池中一个容量设备的数据容量保持取消分配状态。 这相当于一个服务器一个容量驱动器。
- 每个 ReFS 文件系统都会启用 BitLocker 进行静态数据加密。 

虚拟磁盘自动创建，其容量如下所示：

|名称|容量计算|说明|
|-----|-----|-----|
|本地/启动设备|至少 340 GB<sup>1</sup>|用于操作系统映像和“本地”基础结构 VM 的单个服务器存储。|
|基础结构|3.5 TB|所有 Azure Stack Hub 基础结构使用量。|
|VmTemp|见下<sup>2</sup>|租户 VM 附加了一个临时磁盘，该数据存储在这些虚拟磁盘中。|
|ACS|见下<sup>3</sup>|Azure 一致存储容量，为 Blob、表和队列提供服务。|

<sup>1</sup> 对 Azure Stack Hub 解决方案合作伙伴有最低存储容量要求。

<sup>2</sup> 用于租户 VM 临时磁盘的虚拟磁盘大小按服务器中物理内存的比率进行计算。 临时磁盘是分配给 VM 的物理内存比率。 在 Azure Stack Hub 中为“临时磁盘”存储进行分配时，会采用能够捕获大多数用例的方式，但这种方式可能无法满足所有临时磁盘存储需求。 该比率既要让临时存储可用，又要确保不将解决方案的大多数存储容量仅用于临时磁盘容量。 在缩放单元中，将为一个服务器创建一个临时存储磁盘。 在缩放单元的存储池中，临时存储的容量不会超出总体可用存储容量的 10%。 计算如以下示例所示：

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> 创建的供 ACS 使用的虚拟磁盘对剩余容量进行了简单的划分。 如前所述，所有虚拟磁盘都是一个三向镜像，每个服务器的一个容量驱动器的容量值是未分配的。 上面列举的各种虚拟磁盘会先进行分配，剩余容量随后用于 ACS 虚拟磁盘。

## <a name="next-steps"></a>后续步骤

了解 [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md)。
