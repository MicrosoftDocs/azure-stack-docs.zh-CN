---
title: Azure Stack 的存储容量规划 | Microsoft Docs
description: 了解适用于 Azure Stack 部署的存储容量规划。
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
ms.date: 02/20/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 02/20/2019
ms.openlocfilehash: 1d111d56d58869121f6833fe5c7ea15664883aaa
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984477"
---
# <a name="azure-stack-storage-capacity-planning"></a>Azure Stack 存储容量规划
以下部分提供 Azure Stack 存储容量规划信息，帮助你针对解决方案的存储需求进行规划。

## <a name="uses-and-organization-of-storage-capacity"></a>存储容量的使用和组织
Azure Stack 的超聚合配置允许共享物理存储设备。 可用存储的三个主要部分包括：基础结构存储、租户虚拟机的临时存储，以及为 Azure 一致存储 (ACS) 服务的 Blob、表和队列提供支持的存储。

## <a name="spaces-direct-cache-and-capacity-tiers"></a>空间直通缓存和容量层
存储容量可以用于操作系统、本地日志记录、转储和其他临时基础结构存储需求。 此本地存储容量独立于受存储空间直通配置管理的存储设备（设备和容量独立）。 其余存储设备置于单个存储容量池中，不管在缩放单元中的服务器数量为多少。 这些设备分为两种类型：缓存设备和容量设备。  缓存设备都只是该-缓存。 空间直通将使用这些设备来写回和读取缓存。 这些缓存设备的容量使用，而不是致力于到带格式的虚拟磁盘的带格式的、"可见"容量。 容量设备用于此目的，并提供"家庭"的位置管理存储空间的数据。

所有存储容量都由 Azure Stack 基础结构进行直接分配和管理。 进行容量扩展时，操作员需选择配置或分配，或者对选择进行处理。 进行这些设计决策需遵循解决方案要求，是在初始安装/部署过程中或容量扩展过程中自动完成的。 有关复原的详细信息、为重新生成保留的容量以及其他详细信息视为设计的一部分。 

操作员可以选择进行全闪存或混合存储配置：

![Azure 存储容量规划](media/azure-stack-capacity-planning/storage.png)

在全闪存配置中，配置可以是双层配置，也可以是单层配置。  如果配置是单层配置，则所有容量设备将是相同类型（例如 NVMe 或 SATA SSD 或 SAS SSD），并且不使用缓存设备。 在双层全闪存配置中，典型配置是 NVMe 作为缓存设备，然后 SATA 或 SAS SSD 作为容量设备。

在混合双层配置中，缓存可在 NVMe、SATA 或 SAS SSD 之间进行选择，而容量则为 HDD。 

存储空间直通和 Azure Stack 存储配置概述如下：
- 一个缩放单元一个存储空间池（所有存储设备在单个池中配置）
- 虚拟磁盘创建为三副本镜像，以确保最佳性能和复原能力
- 每个虚拟磁盘格式化为一个 ReFS 文件系统
- 虚拟磁盘容量计算，并在一种方式将保留一个容量设备的数量的未分配的池中的数据容量中分配。 这相当于一个服务器一个容量驱动器。
- 每个 ReFS 文件系统都会启用 BitLocker 进行静态数据加密。 

虚拟磁盘自动创建，其容量如下所示：

|名称|容量计算|描述|
|-----|-----|-----|
|本地/启动设备|至少 340 GB<sup>1</sup>|用于操作系统映像和“本地”基础结构 VM 的单个服务器存储|
|基础结构|3.5 TB|所有 Azure Stack 基础结构使用量|
|VmTemp|见下<sup>2</sup>|租户虚拟机附加了一个临时磁盘，该数据存储在这些虚拟磁盘中|
|ACS|见下<sup>3</sup>|Azure 一致存储容量，为 Blob、表和队列提供服务|

<sup>1</sup> 对 Azure Stack 解决方案合作伙伴有最低存储容量要求。

<sup>2</sup> 用于租户虚拟机临时磁盘的虚拟磁盘大小按服务器中物理内存的比率进行计算。 如下面的表（针对 Azure IaaS VM 大小）中所述，临时磁盘在分配给虚拟机的物理内存中占一定的比率。 完成 Azure Stack 中的"临时磁盘"存储的分配将在捕获大多数使用情况下完成，但可能无法满足所有临时磁盘存储需求。 选择比率时，既要让临时存储可用，又要确保不将解决方案的大多数存储容量仅用于临时磁盘容量。 在缩放单元中，将为一个服务器创建一个临时存储磁盘。 在缩放单元的存储池中，临时存储的容量不会超出总体可用存储容量的 10%。 计算如以下示例所示：

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> 创建的供 ACS 使用的虚拟磁盘对剩余容量进行了简单的划分。 如前所述，所有虚拟磁盘是三向镜像和未分配的容量来用于每个服务器的一个容量驱动器的价值。 上面枚举的各种虚拟磁盘会先进行分配，剩余容量随后用于 ACS 虚拟磁盘。

## <a name="next-steps"></a>后续步骤
[了解 Azure Stack 容量规划电子表格](capacity-planning-spreadsheet.md)
