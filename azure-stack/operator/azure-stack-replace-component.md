---
title: 替换 Azure Stack 集线器缩放单位节点上的硬件组件
titleSuffix: Azure Stack Hub
description: 了解如何更换 Azure Stack 集线器集成系统上的硬件组件。
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
ms.openlocfilehash: 17155ab45678158fc669a77d63074748c867e7f3
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882005"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-hub-scale-unit-node"></a>替换 Azure Stack 集线器缩放单位节点上的硬件组件

本文介绍替换非热插拔硬件组件的一般过程。 实际替换步骤因原始设备制造商（OEM）硬件供应商而异。 请参阅供应商的现场可更换部件（FRU）文档，以了解特定于 Azure Stack 集线器集成系统的详细步骤。

> [!CAUTION]  
> 固件分级对于本文中所述的操作的成功至关重要。 缺少此步骤可能会导致系统不稳定、性能降低、安全线程，或阻止 Azure Stack 集线器自动化部署操作系统。 在更换硬件时，请始终咨询硬件合作伙伴的文档，以确保应用的固件与[Azure Stack 中心管理员门户](azure-stack-updates.md)中显示的 OEM 版本相匹配。

| 硬件伙伴 | 地区 | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | 所有 | [适用于 Microsoft Azure Stack 集线器操作指南的 Cisco 集成系统](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[适用于 Microsoft Azure Stack 集线器的 Cisco 集成系统的发行说明](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | 所有 | [适用于 Microsoft Azure Stack 中心14G 的云（需要帐户和登录）](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[适用于 Microsoft Azure Stack 中心13G 的云（需要帐户和登录）](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | 日本 | [Fujitsu 托管服务支持人员（需要帐户和登录）](https://eservice.fujitsu.com/supportdesk-web/) |
|  | 欧洲、中东和非洲 | [Fujitsu 支持 IT 产品和系统](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | 欧洲 | [Fujitsu MySupport （需要帐户和登录）](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | 所有 | [Microsoft Azure Stack 中心的 HPE ProLiant](http://www.hpe.com/info/MASupdates) |
| Lenovo | 所有 | [ThinkAgile SXM 最佳食谱](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [OEM/固件包](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[terra Azure Stack 中心文档（包括 FRU）](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

非热插拔组件包括以下各项：

- CPU
- 内存*
- 母板/基板管理控制器（BMC）/video 卡
- 磁盘控制器/主机总线适配器（HBA）/backplane
- 网络适配器（NIC）
- 操作系统磁盘 *
- 数据驱动器（不支持热交换的驱动器，例如，PCI-e 外接卡） *

\* 这些组件可能支持热交换，但可能因供应商实现而异。 有关详细步骤，请参阅 OEM 供应商的 FRU 文档。

以下流程图显示了更换非热插拔硬件组件的一般 FRU 过程。

![显示组件替换流的流程图](media/azure-stack-replace-component/replacecomponentflow.PNG)

* 根据硬件的物理条件，可能不需要此操作。

\* * OEM 硬件供应商是否执行组件更换和更新固件会根据支持合同而有所不同。

## <a name="review-alert-information"></a>查看警报信息

Azure Stack 中心运行状况和监视系统跟踪存储空间直通控制的网络适配器和数据驱动器的运行状况。 它不跟踪其他硬件组件。 对于所有其他硬件组件，会在硬件生命周期主机上运行的供应商特定硬件监视解决方案中引发警报。  

## <a name="component-replacement-process"></a>组件更换过程

以下步骤提供了组件更换过程的高级概述。 请勿在未参考 OEM 提供的 FRU 文档的情况下执行这些步骤。

1. 使用 "关闭" 操作以正常关闭缩放单位节点。 根据硬件的物理条件，可能不需要此操作。

2. 在不太可能出现关机操作失败的情况下，请使用[排出](azure-stack-node-actions.md#drain)操作将缩放单位节点置于维护模式。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 在任何情况下，一次只能禁用和关闭一个节点，而不会破坏 S2D （存储空间直通）。

3. 缩放单位节点处于维护模式后，请使用[关闭电源](azure-stack-node-actions.md#scale-unit-node-actions)操作。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 如果关机操作不起作用，请改用基板管理控制器（BMC） web 界面。

4. 更换损坏的硬件组件。 您的 OEM 硬件供应商是否根据您的支持合同而有所不同。  
5. 更新固件。 使用硬件生命周期主机遵循供应商特定的固件更新过程，以确保替换的硬件组件应用了批准的固件级别。 你的 OEM 硬件供应商是否执行此步骤取决于你的支持合同。  
6. 使用 "[修复](azure-stack-node-actions.md#scale-unit-node-actions)" 操作将缩放单位节点返回到缩放单位。
7. 使用特权终结点[检查虚拟磁盘修复的状态](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint)。 使用新的数据驱动器，完全存储修复作业可能需要几个小时，具体取决于系统负载和使用的空间。
8. 修复操作完成后，验证是否已自动关闭所有活动警报。

## <a name="next-steps"></a>后续步骤

- 有关更换热插拔的物理磁盘的信息，请参阅[更换磁盘](azure-stack-replace-disk.md)。
- 有关替换物理节点的信息，请参阅[替换缩放单位节点](azure-stack-replace-node.md)。
