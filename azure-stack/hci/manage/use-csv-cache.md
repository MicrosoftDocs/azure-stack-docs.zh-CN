---
title: 通过 Azure Stack HCI 使用 CSV 内存中读取缓存
description: 本主题介绍如何使用系统内存来提高性能。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/04/2020
ms.openlocfilehash: 84d5292c3f812402027b310954a021752276a799
ms.sourcegitcommit: 01dcda15d88c8d44b4918e2f599daca462a8e3d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2020
ms.locfileid: "89493795"
---
# <a name="use-the-csv-in-memory-read-cache-with-azure-stack-hci"></a>通过 Azure Stack HCI 使用 CSV 内存中读取缓存

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

本主题介绍如何使用系统内存提高 Azure Stack HCI 的性能。

Azure Stack HCI 与群集共享卷 (CSV) 内存中读取缓存兼容。 使用系统内存来缓存读取可以提高应用程序的性能，例如 Hyper-v，它使用无缓冲 i/o 来访问 VHD 或 VHDX 文件。  (无缓冲 i/o 是指不是由 Windows 缓存管理器缓存的任何操作。 ) 

由于内存中缓存是服务器本地缓存，因此它可改善数据区域：最新的读取将缓存在运行虚拟机的同一主机上的内存中，从而减少读取网络的频率。 这会降低延迟和更好的存储性能。

请注意，CSV 内存中读取缓存不同于 Azure Stack HCI 中的 [存储池缓存](../concepts/cache.md) 。

## <a name="planning-considerations"></a>规划注意事项

内存中读取缓存最适用于读取密集型工作负荷，如虚拟桌面基础结构 (VDI) 。 相反，如果工作负荷非常耗费写入，则缓存可能会引入比值更多的开销，因此应将其禁用。

对于 CSV 内存中读取缓存，最多可以使用80% 的总物理内存。 请注意为虚拟机留出足够的内存！

  > [!NOTE]
  > 某些 microbenchmarking 工具（如 DISKSPD 和 [VM 汽油](https://github.com/Microsoft/diskspd/tree/master/Frameworks/VMFleet) ）可能会产生更糟糕的结果，其中启用了 CSV 内存中读取缓存。 默认情况下，VM 汽油每个虚拟机创建 1 10 GiB VHDX – 100 Vm 约有 1 TiB 的总数，然后对它们执行 *统一的随机* 读取和写入。 与实际工作负载不同，读取不遵循任何可预测或重复的模式，因此内存中缓存不起作用，且只会造成开销。

## <a name="configuring-the-in-memory-read-cache"></a>配置内存中读取缓存

CSV 内存中读取缓存可用 Azure Stack HCI、Windows Server 2019 和 Windows Server 2016，具有相同的功能。 在 Azure Stack HCI 和 Windows Server 2019 中，默认情况下，它默认为1个 gibibyte (GiB) 分配。 在 Windows Server 2016 中，它在默认情况下处于关闭状态。

| OS 版本          | 默认 CSV 缓存大小 |
|---------------------|------------------------|
| Azure Stack HCI     | 1 GiB                  |
| Windows Server 2019 | 1 GiB                  |
| Windows Server 2016 | 0（禁用）           |

若要查看使用 PowerShell 分配的内存量，请运行：

```PowerShell
(Get-Cluster).BlockCacheSize
```

返回的值在 mebibytes (MiB) 每个服务器。 例如， `1024` 表示1个 GiB。

若要更改分配的内存量，请使用 PowerShell 修改此值。 例如，若要为每台服务器分配2个 GiB，请运行：

```PowerShell
(Get-Cluster).BlockCacheSize = 2048
```

要使更改立即生效，请暂停并恢复 CSV 卷，或在服务器之间移动它们。 例如，使用此 PowerShell 片段将每个 CSV 移到另一个服务器节点，并再次返回：

```PowerShell
Get-ClusterSharedVolume | ForEach {
    $Owner = $_.OwnerNode
    $_ | Move-ClusterSharedVolume
    $_ | Move-ClusterSharedVolume -Node $Owner
}
```

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [了解存储池缓存](../concepts/cache.md)
- [在故障转移群集中使用群集共享卷](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional)
