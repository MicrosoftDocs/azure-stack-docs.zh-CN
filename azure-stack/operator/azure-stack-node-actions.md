---
title: Azure Stack Hub 中的缩放单元节点操作
description: 了解缩放单元节点操作，包括开机、关机、禁用、恢复以及如何在 Azure Stack Hub 集成系统中查看节点状态。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 11/19/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/19/2020
ms.openlocfilehash: ecca245124ce30597a535d8c2ca014821d471d67
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517678"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub"></a>Azure Stack Hub 中的缩放单元节点操作

本文介绍如何查看缩放单元的状态。 可以查看单元的节点。 可以运行开机、关机、关闭、清空、恢复和修复等节点操作。 通常，在现场更换组件期间或者在帮助恢复节点时，会使用这些节点操作。

> [!Important]  
> 本文中所述的所有节点操作每次应该针对一个节点。

## <a name="view-the-node-status"></a>查看节点状态

在管理员门户中，可以查看缩放单元及其关联节点的状态。

查看缩放单元的状态：

1. 在“区域管理”磁贴中选择区域。
2. 在左侧的“基础结构资源”下，选择“缩放单元”。 
3. 在结果中选择缩放单元。
4. 从左侧的“常规”下面，选择“节点”。 

   查看以下信息：

   - 各个节点的列表。
   - 操作状态（请参见以下列表）。
   - 电源状态（“正在运行”或“已停止”）。
   - 服务器模型。
   - 基板管理控制器 (BMC) 的 IP 地址。
   - 核心总数。
   - 总内存量。

![缩放单元的状态](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>节点操作状态

| 状态 | 说明 |
|----------------------|-------------------------------------------------------------------|
| 正在运行 | 节点都积极参与缩放单元。 |
| 已停止 | 节点不可用。 |
| 正在添加 | 正在主动将节点添加到缩放单元。 |
| 正在修复 | 正在主动修复节点。 |
| 维护 | 节点已暂停，没有处于运行状态的活动用户工作负荷。 |
| 需要修正 | 检测到错误，需要修复节点。 |

### <a name="azure-stack-hub-shows-adding-status-after-an-operation"></a>Azure Stack Hub 在操作后显示“正在添加”状态

Azure Stack Hub 在执行排出、恢复、修复、关闭或启动之类的操作后，可能会将操作节点状态显示为“正在添加”。
如果 Fabric 资源提供程序角色缓存在操作之后未刷新，可能会发生这种情况。 

在应用以下步骤之前，请确保当前没有正在进行的操作。 更新终结点，使之与环境匹配。



### <a name="az-modules"></a>[Az 模块](#tab/az1)

1. 打开 PowerShell 并添加 Azure Stack Hub 环境。 这需要在计算机上[安装 Azure Stack Hub PowerShell](./powershell-install-az-module.md)。

    ```powershell
    Add-AzEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Add-AzAccount -Environment AzureStack
    ```

2. 运行以下命令以重启 Fabric 资源提供程序角色。

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. 验证受影响的缩放单元节点的操作状态是否已更改为“正在运行”。 可以使用管理员门户或以下 PowerShell 命令：

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. 如果节点操作状态仍显示为“正在添加”，则继续创建支持事件。

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm1)

1. 打开 PowerShell 并添加 Azure Stack Hub 环境。 这需要在计算机上[安装 Azure Stack Hub PowerShell](./powershell-install-az-module.md)。

    ```powershell
    Add-AzureRMEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Add-AzureRMAccount -Environment AzureStack
    ```

2. 运行以下命令以重启 Fabric 资源提供程序角色。

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. 验证受影响的缩放单元节点的操作状态是否已更改为“正在运行”。 可以使用管理员门户或以下 PowerShell 命令：

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. 如果节点操作状态仍显示为“正在添加”，则继续创建支持事件。

---



## <a name="scale-unit-node-actions"></a>缩放单元节点操作

查看缩放单元节点的相关信息时，也可以执行节点操作，例如：

 - 启动和停止（取决于当前电源状态）。
 - 禁用和恢复（取决于操作状态）。
 - 修复。
 - 关闭。

节点的工作状态确定了哪些选项可用。

需要安装 Azure Stack Hub PowerShell 模块。 这些 cmdlet 位于 **Azs.Fabric.Admin** 模块中。 若要安装或验证适用于 Azure Stack Hub 的 PowerShell 的安装，请参阅[安装适用于 Azure Stack Hub 的 PowerShell](powershell-install-az-module.md)。

## <a name="stop"></a>停止

“停止”操作会关闭节点。 它的作用如同按下电源按钮。 它不会向操作系统发送关闭信号。 对于计划的停止操作，请始终先尝试关闭操作。

当节点不再响应请求时，通常使用此操作。

若要运行停止操作，请打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

在停止操作不起作用的情况下（这种情况很少见），请重试操作，如果仍然失败，请改用 BMC Web 界面。

有关详细信息，请参阅 [Stop-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/stop-azsscaleunitnode)。

## <a name="start"></a>开始

“启动”操作会打开节点。 它的作用如同按下电源按钮。

若要运行启动操作，请打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

万一启动操作不起作用，则重试该操作。 如果它再次失败，请改用 BMC Web 界面。

有关详细信息，请参阅 [Start-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/start-azsscaleunitnode)。

## <a name="drain"></a>清空

“清空”操作将所有活动工作负荷移到该特定缩放单元中的剩余节点。

在现场更换组件期间（例如，更换整个节点），通常使用此操作。

> [!Important]
> 在计划内维护时段内，确保只在已通知用户后才对节点进行清空操作。 在某些情况下，活动的工作负荷可能遇到中断。

若要运行清空操作，请打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

有关详细信息，请参阅 [Disable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/disable-azsscaleunitnode)。

## <a name="resume"></a>恢复

“恢复”操作恢复已禁用的节点，并将其标记为活动，可用于放置工作负荷。 之前在节点上运行的工作负荷不会故障回复。 （如果在节点上使用清空操作，请务必关机。 将节点重新开机时，系统不会将它标记为可放置工作负荷的活动状态。 准备就绪后，必须使用恢复操作将节点标记为活动。）

若要运行恢复操作，请打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

有关详细信息，请参阅 [Enable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/enable-azsscaleunitnode)。

## <a name="repair"></a>修复

> [!CAUTION]  
> 固件分级对于本文中所述的操作的成功至关重要。 当 Azure Stack Hub 自动化部署操作系统时，缺少此步骤可能会导致系统不稳定、性能降低、安全威胁或失败。 更换硬件时，请始终参阅硬件合作伙伴的文档，以确保应用的固件与 [Azure Stack Hub 管理员门户](azure-stack-updates.md)中显示的 OEM 版本匹配。<br><br>
有关详细信息和合作伙伴文档的链接，请参阅[更换硬件组件](azure-stack-replace-component.md)。

| 硬件合作伙伴 | 区域 | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | 全部 | [适用于 Microsoft Azure Stack Hub 的 Cisco 集成系统操作指南](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[适用于 Microsoft Azure Stack 集线器的 Cisco 集成系统的发行说明](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | 全部 | [Microsoft Azure Stack 中心的云 14G (帐户和需要登录) ](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Microsoft Azure Stack 中心的云 13G (帐户和需要登录) ](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | 日本 | [Fujitsu 托管服务支持人员 (需要帐户和登录) ](https://eservice.fujitsu.com/supportdesk-web/) |
|  | 欧洲、中东和非洲 | [Fujitsu 支持 IT 产品和系统](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [需要 Fujitsu MySupport (帐户和登录名) ](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | 全部 | [Microsoft Azure Stack 中心的 HPE ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | 全部 | [ThinkAgile SXM 最佳食谱](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

“修复”操作可修复节点。 请只在出现以下情况时才使用此操作：

- 更换整个节点（不管是否包含新数据磁盘）时。
- 硬件组件发生故障并予以更换之后（如果现场可更换单元 [FRU] 文档中建议更换）。

> [!Important]  
> 需要更换节点或单个硬件组件时，请参阅 OEM 硬件供应商的 FRU 文档，以了解具体步骤。 FRU 文档将指定在更换硬件组件之后是否需要运行修复操作。

运行修复操作时，需要指定 BMC IP 地址。

若要运行修复操作，请打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

“关闭”操作会先将所有活动工作负荷移到同一缩放单元中的其余节点。 然后该操作会正常关闭缩放单元节点。

启动已关闭的节点后，需要运行 [恢复](#resume)操作。 之前在节点上运行的工作负荷不会故障回复。

如果关闭操作失败，请尝试“[清空](#drain)”操作，然后执行关闭操作。

若要运行关闭操作，请打开权限提升的 PowerShell 提示符，并运行以下 cmdlet：

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```

## <a name="next-steps"></a>后续步骤

- [安装 Azure Stack PowerShell](./powershell-install-az-module.md)
- [了解 Azure Stack 集线器 Fabric 操作员模块](/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0&preserve-view=true)
- [监视“添加节点”操作](./azure-stack-add-scale-node.md#monitor-add-node-operations)
