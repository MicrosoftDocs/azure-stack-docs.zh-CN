---
title: 对群集验证报告进行故障排除
description: 对群集验证报表进行故障排除，并验证 Azure Stack HCI 群集的 QoS 设置配置
author: khdownie
ms.topic: troubleshooting
ms.date: 07/21/2020
ms.author: v-kedow
ms.reviewer: JasonGerend
ms.openlocfilehash: c4da92a6d88a3d2046ee6136f2481ac23e5bd476
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867136"
---
# <a name="troubleshoot-cluster-validation-reporting"></a>对群集验证报告进行故障排除

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

本主题可帮助你对 Azure Stack HCI 群集中所有服务器上的网络和存储 QoS（服务质量）设置的群集验证报表进行故障排除，并验证是否已定义重要规则。 为了获得最佳连接性和性能，群集验证过程会验证数据中心桥接 (DCB) QoS 配置是否一致，并包含适当的用于“故障转移群集”和 SMB/SMB Direct 流量类的规则（如果已定义）。

## <a name="install-data-center-bridging"></a>安装数据中心桥接

必须安装数据中心桥接才能使用特定于 QoS 的 cmdlet。 若要检查服务器上是否已安装数据中心桥接功能，请在 PowerShell 中运行以下 cmdlet：

```PowerShell
Get-WindowsFeature -Name Data-Center-Bridging -ComputerName Server1
```

如果未安装数据中心桥接，请通过在群集中的每个服务器上运行以下 cmdlet 来安装它：

```PowerShell
Install-WindowsFeature –Name Data-Center-Bridging -ComputerName Server1
```

## <a name="run-a-cluster-validation-test"></a>运行群集验证检查

通过选择“工具”>“服务器”>“库存”>“验证群集”来使用 Windows Admin Center 中的“验证”功能，或者运行以下 PowerShell 命令：

```PowerShell
Test-Cluster –Node Server1, Server2
```

该测试会执行多项操作，其中包括验证 DCB QoS 配置是否一致，以及集群中的所有服务器是否具有相同数量的流量类和 QoS 规则。 它还验证所有服务器是否为“故障转移群集”和 SMB/SMB Direct 流量类定义了 QoS 规则。

你可以在 Windows Admin Center 中，或通过访问当前工作目录中的日志文件来查看验证报告。 例如：C:\Users\<username>\AppData\Local\Temp\

在报表底部，你将看到“验证 QoS 设置配置”和集群中每个服务器的相应报表。

要了解服务器上已经设置了哪些流量类别，请使用 `Get-NetQosTrafficClass` cmdlet。

若要了解详细信息，请参阅[验证 Azure Stack HCI 群集](../deploy/validate.md)。

## <a name="validate-networking-qos-rules"></a>验证网络 QoS 规则

验证群集中不同服务器上的 DCB 就绪状态和优先级流控制状态设置的一致性。

### <a name="dcb-willing-status"></a>DCB 就绪状态

支持数据中心桥接功能交换协议 (DCBX) 的网络适配器可以接受来自远程设备的配置。 要启用此功能，网络适配器上的 DCB willing 比特必须设置为 True。 如果“就绪比特”设置为 False，则设备将拒绝来自远程设备的所有配置尝试，并仅强制执行本地配置。 如果你使用的是 RDMA over Converged Ethernet (RoCE) 适配器，那么所有服务器上的就绪比特都应设置为 False。

Azure Stack HCI 集群中的所有服务器都应该以相同的方式设置 DCB 就绪比特。

使用 `Set-NetQosDcbxSetting` cmdlet 将 DCB 就绪比特设置为 True 或 False，如下面的示例所示：

```PowerShell
Set-NetQosDcbxSetting –Willing $false
```

### <a name="dcb-flow-control-status"></a>DCB 流控制状态

如果上层协议（例如光纤通道）假定无损基础传输，基于优先级的流控制就至关重要。 可以全局或针对单个网络适配器启用或禁用 DCB 流控制。 如果启用，可创建优先考虑某些应用程序流量的 QoS 策略。

为了使 QoS 策略在故障转移期间无缝工作，Azure Stack HCI 集群中的所有服务器都应该具有相同的流控制状态设置。 如果使用的是 RoCE 适配器，必须在所有服务器上启用优先级流控制。

使用 `Get-NetQosFlowControl` cmdlet 获取当前的流控制配置。 所有优先级默认已禁用。

使用带有 -priority 参数的 `Enable-NetQosFlowControl` 和 `Disable-NetQosFlowControl` cmdlets 来打开或关闭优先级流量控制。 例如，以下命令对带有优先级 3 标记的流量启用流控制：

```PowerShell
Enable-NetQosFlowControl –Priority 3
```

## <a name="validate-storage-qos-rules"></a>验证存储 QoS 规则

验证是否所有节点都有用于“故障转移群集”以及 SMB 或 SMB Direct 的 QoS 规则。 否则，可能会出现连接问题和性能问题。

### <a name="qos-rule-for-failover-clustering"></a>故障转移群集的 QoS 规则

如果在群集中定义了“任何”存储 QoS 规则，则应设置用于故障转移群集的 QoS 规则，否则可能会出现连接问题。 若要添加新的用于故障转移群集的 QoS 规则，请使用 `New-NetQosPolicy` cmdlet，如下面的示例所示：

```PowerShell
New-NetQosPolicy "Cluster" -IPDstPort 3343 -Priority 6
```

### <a name="qos-rule-for-smb"></a>SMB 的 QoS 规则

如果某些或所有节点定义了 QOS 规则，但 SMB 没有 QoS 规则，这可能会导致 SMB 的连接和性能问题。 若要为 SMB 添加新的网络 QoS 规则，请使用 `New-NetQosPolicy` cmdlet，如下面的示例所示：

```PowerShell
New-NetQosPolicy -Name "SMB" -SMB -PriorityValue8021Action 3
```

### <a name="qos-rule-for-smb-direct"></a>SMB Direct 的 QoS 规则

SMB Direct 会绕过网络堆栈，使用 RDMA 方法传输数据。 如果某些或所有节点定义了 QOS 规则，但 SMB Direct 没有 QoS 规则，这可能会导致 SMB Direct 的连接和性能问题。 若要为 SMB Direct 新建 QoS 策略，请发出以下命令：

```PowerShell
New-NetQosPolicy "SMB Direct" –NetDirectPort 445 –Priority 3
```

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [数据中心桥接](/windows-server/networking/technologies/dcb/dcb-top)
- [管理数据中心桥接](/windows-server/networking/technologies/dcb/dcb-manage)
- [QoS 常见配置](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj735302(v=ws.11))