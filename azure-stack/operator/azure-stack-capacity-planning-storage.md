---
title: Azure Stack 中心存储容量规划
description: 了解 Azure Stack 中心部署的存储容量规划。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: beccdd382ad95229e136442651e8def37a3a9552
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695370"
---
# <a name="azure-stack-hub-storage-capacity-planning"></a>Azure Stack 中心存储容量规划

以下各节提供 Azure Stack 集线器存储容量规划信息，以帮助规划解决方案的存储需求。

## <a name="uses-and-organization-of-storage-capacity"></a>存储容量的使用和组织

Azure Stack 集线器的超聚合配置允许共享物理存储设备。 可用存储有三个主要划分：基础结构、租户虚拟机（Vm）的临时存储区，以及支持 Azure 一致性存储（ACS）服务的 blob、表和队列的存储。

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>存储空间直通缓存和容量层

用于操作系统、本地日志记录、转储和其他临时基础结构存储需求的存储容量。 此本地存储容量与存储空间直通配置的管理下的存储设备不同（设备和容量）。 其余存储设备将放置在一个存储容量池中，而不考虑缩放单位中的服务器数量。

这些设备分为两种类型：缓存和容量。 存储空间直通使用缓存设备进行写回和读缓存。 这些缓存设备的容量在使用时不会提交到格式化虚拟磁盘的格式化和 "可见" 容量。 与此相反，存储空间直通会使用容量设备来实现托管数据的 "主位置"。

Azure Stack 中心基础结构直接分配和管理所有存储容量。 操作员无需做出有关配置、分配、容量扩展的选项。 在初始安装和部署或容量扩展期间，Azure Stack 集线器会自动执行这些设计决策，以符合解决方案要求。 在设计过程中，Azure Stack 集线器会考虑复原能力、重建的保留容量以及其他详细信息。

操作员可以在 "*所有闪存*" 或 "*混合*" 存储配置之间进行选择：

![Azure 存储容量规划示意图](media/azure-stack-capacity-planning/storage.png)

在 "所有闪存" 配置中，配置可以是两层或单层配置。 如果配置为单层，则所有容量设备的类型相同（例如， *NVMe*或*SATA SSD*或*SAS ssd*），不使用缓存设备。 在双层的所有闪存配置中，典型配置是 NVMe 作为缓存设备，然后是 SATA 或 SAS Ssd 作为容量设备。

在混合两层配置中，缓存是在 NVMe、SATA 或 SAS SSD 之间选择的，容量为 HDD。

存储空间直通和 Azure Stack 中心存储配置的简短摘要如下：
- 每个缩放单位一个存储空间直通池（所有存储设备都在单个池中配置）。
- 虚拟磁盘创建为三个复制镜像，以获得最佳性能和复原能力。
- 每个虚拟磁盘的格式设置为 ReFS 文件系统。
- 虚拟磁盘容量的计算和分配方式为：保留池中的一个容量设备的数据容量未分配。 这相当于每台服务器一个容量驱动器。
- 每个 ReFS 文件系统都为静态数据加密启用了 BitLocker。 

自动创建的虚拟磁盘及其容量如下：

|名称|容量计算|说明|
|-----|-----|-----|
|本地/引导设备|最小 340 GB<sup>1</sup>|用于操作系统映像和 "本地" 基础结构 Vm 的单独服务器存储。|
|基础结构|3.5 TB|所有 Azure Stack 集线器基础结构。|
|VmTemp|请参阅下面<sup>2</sup>|租户 Vm 已附加临时磁盘，并且该数据存储在这些虚拟磁盘中。|
|ACS|请参阅下<sup>3</sup>|用于维护 blob、表和队列的 Azure 一致存储容量。|

<sup>1</sup> Azure Stack 中心解决方案伙伴所需的最小存储容量。

<sup>2</sup>用于租户 VM 临时磁盘的虚拟磁盘大小计算为服务器物理内存的比率。 临时磁盘是分配给 VM 的物理内存的比率。 为 Azure Stack 中心中的 "临时磁盘" 存储完成的分配将捕获大多数用例，但可能无法满足所有临时磁盘存储需求。 该比率是提供临时存储空间与仅使用临时磁盘容量的大部分解决方案的存储容量之间的权衡。 一个临时存储磁盘是在缩放单位的每个服务器上创建的。 临时存储的容量不会增长到缩放单位的存储池中总体可用存储容量的10% 以上。 计算类似于以下示例：

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup>创建以供 ACS 使用的虚拟磁盘是剩余容量的简单部分。 如上所述，所有虚拟磁盘都是一个三向镜像，而每个服务器的容量驱动器容量尚未分配。 首先分配之前枚举的各种虚拟磁盘，然后将剩余容量用于 ACS 虚拟磁盘。

## <a name="next-steps"></a>后续步骤

了解[Azure Stack 中心 Capacity Planner](azure-stack-capacity-planner.md)。
