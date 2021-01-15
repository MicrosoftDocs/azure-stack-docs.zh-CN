---
title: 虚拟机负载平衡
description: 使用本主题来了解如何在 Azure Stack HCI 和 Windows Server 中配置 VM 负载平衡功能。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 1/14/2021
ms.openlocfilehash: 9caf8efdd8db46479cce3c5c4299fb5588c7d37a
ms.sourcegitcommit: 51ce5ba6cf0a377378d25dac63f6f2925339c23d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224586"
---
# <a name="virtual-machine-load-balancing"></a>虚拟机负载平衡

> 适用于： Azure Stack HCI，版本 20H2;Windows Server 2019;Windows Server 2016

HCI 部署的一个重要考虑因素是 (CapEx) 投入生产所需的资本支出。 通常，在生产中的高峰流量内添加冗余以避免容量不足，但这会增加 CapEx。 通常需要此冗余，因为群集中的某些服务器承载的虚拟机 (Vm) ，而其他服务器却利用不足。

默认情况下，在 Azure Stack HCI、Windows Server 2019 和 Windows Server 2016 中启用，VM 负载平衡是一项功能，可用于优化群集中的服务器利用率。 它标识过多个服务器，并将这些服务器上的 Vm 实时迁移到未提交的服务器。 故障策略（如消除相关性、容错域 (站点) 和可能的所有者）受到的攻击。

VM 负载平衡基于以下试探法评估服务器的负载：

- **当前内存压力：** 内存是 Hyper-v 主机上最常见的资源约束。
- **五分钟时间范围内平均 CPU 使用率：** 减少群集中的任何服务器是否过度提交。

## <a name="how-does-vm-load-balancing-work"></a>VM 负载平衡如何工作？

将新服务器添加到群集时，会自动进行 VM 负载平衡，还可以将其配置为定期执行负载均衡。

### <a name="when-a-new-server-is-added-to-a-cluster"></a>将新服务器添加到群集时

将新服务器加入群集时，VM 负载平衡功能会按以下顺序自动将现有服务器的容量平衡到新添加的服务器：

1. 在群集中的现有服务器上评估内存压力和 CPU 使用率。
2. 确定超过阈值的所有服务器。
3. 内存压力和 CPU 使用率最高的服务器被标识为确定均衡的优先级。
4. Vm 是实时迁移的 (不会造成停机时间，而不会) 超出群集中新添加服务器的阈值的服务器。

:::image type="content" source="media/vm-load-balancing/server-added.png" alt-text="显示要添加到群集的新服务器的图像" border="false"::: 

### <a name="recurring-load-balancing"></a>周期性负载平衡

默认情况下，VM 负载平衡配置为定期平衡：群集中每个服务器上的内存压力和 CPU 使用率每30分钟评估一次。 下面是这些步骤的流：

1. 在群集中的所有服务器上评估内存压力和 CPU 使用率。
2. 所有超出阈值的服务器以及低于阈值的服务器都将被标识出来。
3. 内存压力和 CPU 使用率最高的服务器被标识为确定均衡的优先级。
4. Vm 是实时迁移的 (不会造成停机) 从超出阈值的服务器到低于最小阈值的另一台服务器。

:::image type="content" source="media/vm-load-balancing/periodic-balancing.png" alt-text="显示自动重新平衡的实时群集的图像" border="false"::: 

## <a name="configure-vm-load-balancing-using-windows-admin-center"></a>使用 Windows 管理中心配置 VM 负载均衡

配置 VM 负载平衡的最简单方法是使用 Windows 管理中心。 

:::image type="content" source="media/vm-load-balancing/vm-load-balancing.png" alt-text="配置 Windows 管理中心的 VM 负载均衡" lightbox="media/vm-load-balancing/vm-load-balancing.png":::

1. 连接到群集并中转到 " **工具" > 设置**"。

2. 在 " **设置**" 下，选择 " **虚拟机负载均衡**"。

3. 在 " **平衡虚拟机**" 下，选择 " **始终** 对服务器加入负载平衡" 和 "每隔30分钟"、"仅在服务器联接时将 **服务器加入** 负载平衡" 或 " **从不** 禁用 VM 负载平衡功能"。 默认设置为 " **始终**"。

4. 如果服务器的加载时间超过80%，则选择 "**低**"、"实时迁移 vm **"、"** 在服务器超过70% 时迁移时迁移" 或 "**高** **"，以** 使群集中的服务器数超过5% 时迁移。 默认设置为 " **低**"。

## <a name="configure-vm-load-balancing-using-windows-powershell"></a>使用 Windows PowerShell 配置 VM 负载均衡

你可以使用群集公用属性配置负载平衡的时间和时间 `AutoBalancerMode` 。 若要控制何时平衡群集，请在 PowerShell 中运行以下内容，并替换下表中的值：

```PowerShell
(Get-Cluster).AutoBalancerMode = <value>
```

|AutoBalancerMode |行为|
|-----------------|-----------|
| 0 | 已禁用 |
| 1 | 服务器联接时的负载平衡 |
| 2（默认值） | 服务器联接和每30分钟的负载平衡 |

你还可以使用群集公用属性配置平衡的入侵 `AutoBalancerLevel` 。 若要控制入侵阈值，请在 PowerShell 中运行以下内容，并替换下表中的值：

```PowerShell
(Get-Cluster).AutoBalancerLevel = <value>
```

| AutoBalancerLevel | 入侵 | 行为 |
|-------------------|----------------|----------|
| 1（默认值） | 低 | 当主机超过80% 时移动 |
| 2 | 中型 | 当主机超过70% 时移动 |
| 3 | 高 | 群集中的平均服务器数，当主机高于平均5% 时，移动 |

若要检查 `AutoBalancerLevel` 和 `AutoBalancerMode` 属性的设置方式，请在 PowerShell 中运行以下内容：

```PowerShell
Get-Cluster | fl AutoBalancer*
```

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [管理 VM](vm.md)
- [使用 PowerShell 管理 VM](vm-powershell.md)
- [创建 VM 相关性规则](vm-affinity.md)
