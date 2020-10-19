---
title: Azure Stack 中心容量规划概述 |Microsoft Docs
description: 了解适用于 Azure Stack Hub 部署的容量规划。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: a608a64de28104513cb708ef350eb4db672f5f65
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182933"
---
# <a name="overview-of-azure-stack-hub-capacity-planning"></a>Azure Stack 中心容量规划概述

评估 Azure Stack Hub 解决方案时，应考虑硬件配置选择，因为它直接影响到 Azure Stack Hub 云的总体容量。 

例如，需要就 CPU、内存密度、存储配置和总体解决方案规模或服务器数目进行选择。 不同于传统的虚拟化解决方案，简单地评估这些组件并不能很好地确定可用的容量。 构建 Azure Stack Hub 是为了在解决方案自身内部托管基础结构或管理组件。 另外，解决方案的某些容量保留用于支持复原；更新解决方案的软件时，必须将租户工作负荷的中断降到最低程度。 

> [!IMPORTANT]
> 此容量计划信息和 [Azure Stack 中心 Capacity Planner](https://aka.ms/azstackcapacityplanner) 是 Azure Stack 中心规划和配置决策的起点。 该信息不能取代你自己的调查和分析。 对于此处提供的信息，Microsoft 不作任何明示或默示保证。
 
Azure Stack 中心解决方案构建为计算和存储的超聚合群集。 可以通过超融合性来共享群集中的硬件容量（称为“缩放单元”）。** 在 Azure Stack 中心，缩放单位提供了资源的可用性和可伸缩性。 缩放单位由一组称为 *主机*的 Azure Stack 集线器服务器组成。 基础结构软件托管在一组虚拟机 (VM) 中，与租户 VM 共享相同的物理服务器。 然后，所有 Azure Stack 中心 Vm 都由缩放单元的 Windows Server 群集技术和单个 Hyper-v 实例进行管理。 

缩放单位简化了获取和管理 Azure Stack 中心。 缩放单位还允许跨 Azure Stack 集线器 (租户和基础结构) 的所有服务的移动和可伸缩性。 

以下主题提供了有关每个组件的更多详细信息：

- [Azure Stack 中心计算](../operator/azure-stack-capacity-planning-compute.md)
- [Azure Stack 中心存储](../operator/azure-stack-capacity-planning-storage.md)
- [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md)

