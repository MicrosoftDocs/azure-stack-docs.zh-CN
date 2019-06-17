---
title: Azure Stack 容量规划存储 |Microsoft Docs
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
ms.openlocfilehash: 2845a90f97c1b859269f73333448bf42ff699da9
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131091"
---
# <a name="azure-stack-storage"></a>Azure Stack 存储

以下部分提供 Azure Stack 的存储容量规划信息以帮助您完成规划为解决方案的存储需求。

## <a name="uses-and-organization-of-storage-capacity"></a>使用和组织使用的存储容量
Azure Stack 的超聚合配置允许的物理存储设备共享。 有三个主要区域的可共享的存储可用： 基础结构、 租户虚拟机的临时存储和存储区支持 blob、 表和队列的 Azure 一致的存储 (ACS) 服务。

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>存储空间直通缓存和容量层
存储容量可以用于操作系统、本地日志记录、转储和其他临时基础结构存储需求。 此本地存储容量独立于受存储空间直通配置管理的存储设备（设备和容量独立）。 存储设备的其余部分被放置在单个池中的存储容量，无论缩放单位中的服务器数量是多少。

这些设备有两种类型： 缓存和容量。 存储空间直通使用缓存设备写回和读取缓存。 这些缓存设备的容量使用，而不是提交到带格式的虚拟磁盘的带格式的、"可见"容量。 与此相反，存储空间直通 does 使用容量设备实现此目的，提供"家庭"的位置的托管数据。

在 Azure Stack 基础结构直接分配和管理所有存储容量。 运算符不需要进行配置、 分配、 扩展容量的选择。 Azure Stack 会自动将这些设计决策，以便符合解决方案要求，在初始安装和部署或容量扩展过程。 Azure Stack 将不考虑复原能力、 保留的容量以重新生成和其他详细信息，作为设计的一部分。 

运算符可以选择任一*所有闪存*或*混合*存储配置：

![Azure 存储的容量规划的关系图](media/azure-stack-capacity-planning/storage.png)

在所有闪存配置中，配置可以是两层或单个层配置。 如果配置是单个层，所有容量设备都属于相同类型 （例如，NVMe 或 SATA SSD 或 SAS SSD），并不使用缓存设备。 两层所有闪存配置，典型的配置是 NVMe 作为缓存设备以及然后任一 SATA 或 SAS Ssd 为容量设备。

在混合环境中，两层配置中，缓存是一个 NVMe、 SATA 或 SAS SSD 之间的选择和容量 HDD。 

存储空间直通和 Azure Stack 存储配置概述如下：
- 每个缩放单位 （所有存储设备都配置单个池内） 的一个存储空间直通池。
- 为了优化性能和复原能力的三个副本镜像作为创建虚拟磁盘。
- 每个虚拟磁盘将格式化为 ReFS 文件系统。
- 虚拟磁盘容量计算，并在一种方式将保留一个容量设备数量的未分配的池中的数据容量中分配。 这是每个服务器的一个容量驱动器的等效项。
- 每个 ReFS 文件系统已启用静态数据加密的 BitLocker。 

自动创建的虚拟磁盘和其容量，如下所示：

|名称|容量计算|描述|
|-----|-----|-----|
|本地/启动设备|至少 340 GB<sup>1</sup>|适用于操作系统映像和"本地"基础结构 Vm 的单个服务器存储。|
|基础结构|3.5 TB|所有 Azure Stack 基础结构使用情况。|
|VmTemp|见下<sup>2</sup>|租户虚拟机具有临时磁盘附加，并且该数据存储在这些虚拟磁盘。|
|ACS|见下<sup>3</sup>|有关 blob、 表和队列服务的 azure 一致的存储容量。|

<sup>1</sup> 对 Azure Stack 解决方案合作伙伴有最低存储容量要求。

<sup>2</sup>用于租户虚拟机的临时磁盘的虚拟磁盘大小计算为服务器的物理内存的比率。 临时磁盘将分配给虚拟机的物理内存的比率。 完成 Azure Stack 中使用"临时磁盘"存储的分配捕获大多数用例，但可能不能满足所有临时磁盘存储需求。 为实现临时存储，并且不会占用大部分仅临时磁盘容量解决方案的存储容量的利弊。 针对每个缩放单位中的服务器创建一个临时存储磁盘。 临时存储的容量不超过 10%的缩放单位的存储池中的总体的可用存储容量。 计算如以下示例所示：

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup>使用 acs 创建的虚拟磁盘是简单部门的剩余容量。 如前所述，所有虚拟磁盘是三向镜像和未分配的容量来用于每个服务器的一个容量驱动器的价值。 以前枚举的各种虚拟磁盘将分配第一个，并且剩余容量然后用于 ACS 虚拟磁盘。


## <a name="next-steps"></a>后续步骤
了解如何[Azure Stack 容量计划程序](azure-stack-capacity-planner.md)。
