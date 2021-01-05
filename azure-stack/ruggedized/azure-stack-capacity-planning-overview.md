---
title: Azure Stack Hub 容量规划概述 | Microsoft Docs
description: 了解适用于 Azure Stack Hub 部署的容量规划。 请参阅 Azure Stack 中心耐用的高和低型号的规格。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 1231b69574466cadcebff56f23cbb85d4dbaa9fd
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867972"
---
# <a name="overview-of-azure-stack-hub-capacity-planning"></a>Azure Stack Hub 容量规划概述

评估 Azure Stack Hub 解决方案时，应考虑硬件配置选择，因为它直接影响到 Azure Stack Hub 云的总体容量。 

例如，需要就 CPU、内存密度、存储配置和总体解决方案规模或服务器数目进行选择。 不同于传统的虚拟化解决方案，简单地评估这些组件并不能很好地确定可用的容量。 构建 Azure Stack Hub 是为了在解决方案自身内部托管基础结构或管理组件。 另外，解决方案的某些容量保留用于支持复原；更新解决方案的软件时，必须将租户工作负荷的中断降到最低程度。 

> [!IMPORTANT]
> 可以从此容量规划信息和 [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) 着手，进行 Azure Stack Hub 规划和配置决策。 该信息不能取代你自己的调查和分析。 对于此处提供的信息，Microsoft 不作任何明示或默示保证。
 
Azure Stack Hub 解决方案是作为超融合群集构建的，包含计算和存储。 可以通过超融合性来共享群集中的硬件容量（称为“缩放单元”）。 在 Azure Stack Hub 中，缩放单元为资源提供可用性和可伸缩性。 缩放单元包含一组称为“主机”的 Azure Stack Hub 服务器。 基础结构软件托管在一组虚拟机 (VM) 中，与租户 VM 共享相同的物理服务器。 然后所有 Azure Stack Hub VM 可以通过缩放单元的 Windows Server 群集技术和独立的 Hyper-V 实例来管理。 

缩放单元简化了 Azure Stack Hub 的获取和管理。 缩放单元还可以跨 Azure Stack Hub 移动和缩放所有服务（租户和体系结构）。 

以下主题提供了有关每个组件的更多详细信息：

- [Azure Stack Hub 计算](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Azure Stack Hub 存储](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Azure Stack Hub Capacity Planner](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)

## <a name="azure-stack-hub-ruggedized-high-and-low-models"></a>Azure Stack 集线器耐用高和低模型

下表列出了 Azure Stack 中心耐用的高和低型号的规格。

| 组件               | 规范 |
|-------------------------|---------------|
| CPU                     |高： 284 vCPU 核心<br>低： 184 vCPU 核心  |
| 内存                  |高： 1037 GB<br>低： 547 GB                |
| 存储                 |高： 34.2 TB<br>低： 15.4 TB                |
| Acceleration            |空值                                          |
| 大小/权重             |计算： 31.5 "L x 23.8" W x 15.33 "H<br>重量120磅。  (数量 2) <br>网络： 31.5 "L x 23.8" W x 17.12 "H<br>重量145磅。  (数量 1)               |
| 可选配置 |预热水<br>高或低配置     |

## <a name="next-steps"></a>后续步骤

[Azure Stack 中心计算](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json) 
[Azure Stack 中心存储](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json) 
[Azure Stack 中心 Capacity Planner](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
