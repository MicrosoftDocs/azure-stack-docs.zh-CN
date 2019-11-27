---
title: 更换 Azure Stack 缩放单元节点上的硬件组件
titleSuffix: Azure Stack
description: 了解如何更换 Azure Stack 集成系统上的硬件组件。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2019
ms.author: thoroet
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: ff78409ecdbdec8b7a6860db18244a4835351ed8
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465292"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>更换 Azure Stack 缩放单元节点上的硬件组件

*适用于：Azure Stack 集成系统*

本文介绍更换非热插拔硬件组件的一般过程。 实际的更换步骤将因原始设备制造商 (OEM) 硬件供应商而异。 有关 Azure Stack 集成系统特有的详细步骤，请参阅供应商的现场可更换部件 (FRU) 文档。

> [!CAUTION]  
> 固件分级对于本文中所述的操作的成功至关重要。 缺少此步骤可能会导致系统不稳定、性能降低、安全线程，或阻止 Azure Stack 自动部署操作系统。 在更换硬件时，请始终咨询硬件合作伙伴的文档，以确保应用的固件与[Azure Stack 管理员门户](azure-stack-updates.md)中显示的 OEM 版本匹配。

| 硬件合作伙伴 | 区域 | 代码 |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [适用于 Microsoft Azure Stack 操作指南的 Cisco 集成系统](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[适用于 Microsoft Azure Stack 的 Cisco 集成系统的发行说明](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | All | [适用于 Microsoft Azure Stack 14G 的云（需要帐户和登录）](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[适用于 Microsoft Azure Stack 13G 的云（需要帐户和登录）](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | 日本 | [Fujitsu 托管服务支持人员（需要帐户和登录）](https://eservice.fujitsu.com/supportdesk-web/) |
|  | 欧洲、中东和非洲 | [Fujitsu 支持 IT 产品和系统](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Fujitsu MySupport （需要帐户和登录）](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | All | [HPE ProLiant for Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM 最佳食谱](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/固件包](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[terra Azure Stack 文档（包括 FRU）](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

非热插拔组件包括以下各项：

- CPU*
- 内存*
- 母板/基板管理控制器 (BMC)/视频卡
- 磁盘控制器/主机总线适配器 (HBA)/底板
- 网络适配器 (NIC)
- 操作系统磁盘*
- 数据驱动器（不支持热插拔的驱动器，例如 PCI-e 外接卡）*

*这些组件可能支持热插拔，但因供应商实施情况而有所不同。 有关详细步骤，请参阅 OEM 供应商的 FRU 文档。

以下流程图显示更换非热插拔硬件组件的一般 FRU 过程。

![显示组件更换流程的流程图](media/azure-stack-replace-component/replacecomponentflow.PNG)

* 根据硬件的物理条件，可能不需要此操作。

\* * OEM 硬件供应商是否执行组件更换和更新固件会根据支持合同而有所不同。

## <a name="review-alert-information"></a>查看警报信息

Azure Stack 运行状况和监视系统跟踪存储空间直通控制的网络适配器和数据驱动器的运行状况。 它不跟踪其他硬件组件。 针对所有其他硬件组件，在硬件生命周期主机上运行的供应商特定硬件监视解决方案中引发警报。  

## <a name="component-replacement-process"></a>组件更换过程

以下步骤提供组件更换过程的高级概述。 请勿在未参考 OEM 提供的 FRU 文档的情况下执行这些步骤。

1. 使用关闭操作正常关闭缩放单元节点。 根据硬件的物理条件，可能不需要此操作。

2. 万一关闭操作失败，请使用[清空](azure-stack-node-actions.md#drain)操作使缩放单元节点进入维护模式。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 在任何情况下，只能同时禁用一个节点并关机，而不中断 S2D（存储空间直通）。

3. 缩放单元节点处于维护模式后，请使用[关闭电源](azure-stack-node-actions.md#scale-unit-node-actions)操作。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 在关闭电源操作不起作用的罕见情况下，请改用基板管理控制器 (BMC) Web 界面。

4. 更换损坏的硬件组件。 您的 OEM 硬件供应商是否根据您的支持合同而有所不同。  
5. 更新固件。 请使用硬件生命周期主机按照供应商特定的固件更新过程进行操作，以确保替换的硬件组件已应用批准的固件级别。 你的 OEM 硬件供应商是否执行此步骤取决于你的支持合同。  
6. 使用[修复](azure-stack-node-actions.md#scale-unit-node-actions)操作将缩放单元节点恢复到缩放单元。
7. 使用到特权终结点[检查虚拟磁盘修复状态](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint)。 利用新的数据驱动器，完整的存储修复作业可能需要数小时的时间，具体取决于系统负载和已使用的空间。
8. 修复操作完成后，验证是否已自动关闭所有活动警报。

## <a name="next-steps"></a>后续步骤

- 有关更换热插拔物理磁盘的信息，请参阅[更换磁盘](azure-stack-replace-disk.md)。
- 有关更换物理节点的信息，请参阅[更换缩放单元节点](azure-stack-replace-node.md)。
