---
title: Azure Stack 中心中的缩放单位节点操作
description: 了解缩放单位节点操作，包括开机、关机、禁用、恢复，以及如何查看 Azure Stack 集线器集成系统中的节点状态。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 4874b93acf9e869a3b8e66f42191d5419e48fece
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367807"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub"></a>Azure Stack 中心中的缩放单位节点操作

本文介绍如何查看缩放单位的状态。 您可以查看该单元的节点。 您可以运行 "电源打开"、"关闭"、"关闭"、"排出"、"恢复" 和 "修复" 等节点操作。 通常，在对部件进行现场替换时使用这些节点操作，或帮助恢复节点。

> [!Important]  
> 本文中所述的所有节点操作都应该一次针对一个节点。

## <a name="view-the-node-status"></a>查看节点状态

在管理员门户中，可以查看缩放单位及其关联节点的状态。

若要查看缩放单元的状态，请执行以下操作：

1. 在 "**区域管理**" 磁贴上，选择区域。
2. 在左侧的 "**基础结构资源**" 下，选择 "**缩放单位**"。
3. 在结果中，选择缩放单位。
4. 在左侧的 "**常规**" 下，选择 "**节点**"。

   查看以下信息：

   - 各个节点的列表。
   - 操作状态（请参阅下面的列表）。
   - 电源状态（正在运行或已停止）。
   - 服务器模型。
   - 基板管理控制器（BMC）的 IP 地址。
   - 核心总数。
   - 内存总量。

![缩放单位的状态](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>节点操作状态

| 状态 | 说明 |
|----------------------|-------------------------------------------------------------------|
| 正在运行 | 节点正在积极地参与缩放单位。 |
| 已停止 | 节点不可用。 |
| 添加 | 正在将节点主动添加到缩放单位。 |
| 修复 | 当前正在修复节点。 |
| 维护 | 节点已暂停，并且没有正在运行的活动用户工作负荷。 |
| 需要更正 | 检测到需要修复节点的错误。 |

## <a name="scale-unit-node-actions"></a>缩放单位节点操作

查看缩放单位节点的相关信息时，还可以执行如下节点操作：

 - 启动和停止（取决于当前电源状态）。
 - 禁用和恢复（取决于操作状态）。
 - 修正.
 - 立即.

节点的操作状态决定了哪些选项可用。

需要安装 Azure Stack 集线器 PowerShell 模块。 这些 cmdlet 位于**Azs**模块中。 若要安装或验证 Azure Stack 集线器的 PowerShell 安装，请参阅[安装适用于 Azure Stack 中心的 powershell](azure-stack-powershell-install.md)。

## <a name="stop"></a>停止

**停止**操作将关闭节点。 它与按下 "电源" 按钮相同。 它不会将关闭信号发送到操作系统。 对于计划的停止操作，请始终首先尝试关闭操作。

此操作通常在节点处于挂起状态且不再响应请求时使用。

若要运行停止操作，请打开提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

如果停止操作不起作用，请重试该操作，如果第二次失败，请改用 BMC web 界面。

有关详细信息，请参阅[AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode)。

## <a name="start"></a>开始

**开始**操作将打开节点。 它与按下电源按钮时相同。

若要运行启动操作，请打开提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

如果启动操作不起作用，则重试该操作。 如果第二次失败，请改用 BMC web 界面。

有关详细信息，请参阅[AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode)。

## <a name="drain"></a>管

"**排出**" 操作会将所有活动工作负荷移动到该特定缩放单位的剩余节点。

此操作通常在对部件进行现场替换时使用，如替换整个节点。

> [!Important]
> 请确保在计划内维护时段内对节点使用排出操作，用户已收到通知。 在某些情况下，活动工作负荷会出现中断。

若要运行排出操作，请打开提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

有关详细信息，请参阅[AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode)。

## <a name="resume"></a>恢复

"**恢复**" 操作会恢复已禁用的节点并将其标记为活动状态以进行工作负荷放置。 之前在节点上运行的工作负荷不会故障回复。 （如果在节点上使用排出操作，请确保关闭电源。 重新打开节点时，该节点不会标记为活动状态，因此无法放置工作负荷。 准备就绪后，必须使用 "恢复" 操作将节点标记为活动状态。）

若要运行恢复操作，请打开提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

有关详细信息，请参阅[AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode)。

## <a name="repair"></a>Repair

> [!CAUTION]  
> 固件分级对于本文中所述的操作的成功至关重要。 缺少此步骤可能会导致系统不稳定、性能降低、安全线程或 Azure Stack 集线器自动化部署操作系统时出现故障。 在更换硬件时，请始终咨询硬件合作伙伴的文档，以确保应用的固件与[Azure Stack 中心管理员门户](azure-stack-updates.md)中显示的 OEM 版本相匹配。<br><br>
有关详细信息和合作伙伴文档的链接，请参阅[替换硬件组件](azure-stack-replace-component.md)。

| 硬件伙伴 | 区域 | 代码 |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [适用于 Microsoft Azure Stack 集线器操作指南的 Cisco 集成系统](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[适用于 Microsoft Azure Stack 集线器的 Cisco 集成系统的发行说明](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | All | [适用于 Microsoft Azure Stack 中心14G 的云（需要帐户和登录）](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[适用于 Microsoft Azure Stack 中心13G 的云（需要帐户和登录）](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | 日本 | [Fujitsu 托管服务支持人员（要求提供帐户和登录名）](https://eservice.fujitsu.com/supportdesk-web/) |
|  | 欧洲、非洲和中东 | [Fujitsu 支持 IT 产品和系统](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [Fujitsu MySupport （需要帐户和登录名）](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | All | [Microsoft Azure Stack 中心的 HPE ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM 最佳食谱](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

**修复**操作修复节点。 仅适用于以下任一方案：

- 完全节点替换（包含或不包含新数据磁盘）。
- 硬件组件故障和更换后（如现场可更换部件 [FRU] 文档中所述）。

> [!Important]  
> 如果需要更换节点或单个硬件组件，请参阅 OEM 硬件供应商的 FRU 文档。 FRU 文档将指定在替换硬件组件后是否需要运行修复操作。

在运行 "修复" 操作时，需要指定 BMC IP 地址。

若要运行修复操作，请打开提升的 PowerShell 提示符，并运行以下 cmdlet：

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>关机

**Shutdown**操作首先将所有活动工作负荷移动到相同缩放单位中的其余节点。 然后，该操作会正常关闭缩放单位节点。

启动已关闭的节点后，需要运行[恢复](#resume)操作。 之前在节点上运行的工作负荷不会故障回复。

如果关闭操作失败，则尝试执行[排出](#drain)操作，然后执行关闭操作。

若要运行关机操作，请打开提升的 PowerShell 提示符，并运行以下 cmdlet：

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```

## <a name="next-steps"></a>后续步骤

[了解 Azure Stack 集线器 Fabric 操作员模块](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0)。