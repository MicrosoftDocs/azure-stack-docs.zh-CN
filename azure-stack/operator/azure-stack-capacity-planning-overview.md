---
title: 容量规划概述
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack 中心部署的容量规划。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 0ef537db34ef882257ec5d35eb7d6c91ac1b03d1
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625231"
---
# <a name="capacity-planning-for-azure-stack-hub-overview"></a>Azure Stack 集线器的容量规划概述

评估 Azure Stack 中心解决方案时，请考虑对 Azure Stack 中心云的总体容量有直接影响的硬件配置选择。

你将需要对 CPU、内存密度、存储配置和整体解决方案规模或服务器数量做出选择。 不过，确定可用容量将不同于传统的虚拟化解决方案，因为某些容量已在使用中。 构建 Azure Stack 集线器是为了在解决方案中托管基础结构或管理组件。 此外，还保留了某些解决方案的容量以支持复原。 弹性定义为以最大程度减少租户工作负荷中断的方式更新解决方案的软件。

> [!IMPORTANT]
> 此容量计划信息和[Azure Stack 中心 Capacity Planner](https://aka.ms/azstackcapacityplanner)是 Azure Stack 中心规划和配置决策的起点。 此信息不是为了替代你自己的调查和分析。 对于此处提供的信息，Microsoft 不作任何明示或默示保证。

## <a name="hyperconvergence-and-the-scale-unit"></a>Hyperconvergence 和缩放单位
Azure Stack 中心解决方案构建为计算和存储的超聚合群集。 收敛允许共享群集中的硬件容量，称为*缩放单位*。 在 Azure Stack 中心，缩放单位提供了资源的可用性和可伸缩性。 缩放单位由一组称为*主机*的 Azure Stack 集线器服务器组成。 基础结构软件托管在一组虚拟机（Vm）中，并与租户 Vm 共享相同的物理服务器。 然后，所有 Azure Stack 中心 Vm 都由缩放单元的 Windows Server 群集技术和单个 Hyper-v 实例进行管理。

缩放单位简化了 Azure Stack 集线器的获取和管理。 缩放单位还允许跨 Azure Stack 集线器的所有服务（租户和基础结构）的移动和可伸缩性。

以下主题提供了有关每个组件的更多详细信息：

- [Azure Stack 中心计算](azure-stack-capacity-planning-compute.md)
- [Azure Stack 中心存储](azure-stack-capacity-planning-storage.md)
- [Azure Stack 中心 Capacity Planner](azure-stack-capacity-planner.md)
