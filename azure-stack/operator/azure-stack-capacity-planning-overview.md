---
title: 容量规划概述
titleSuffix: Azure Stack Hub
description: 了解适用于 Azure Stack Hub 部署的容量规划。
author: PatAltimore
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 4b3a6b58b6738c4248eaeb5d76b48fa9d1fdb442
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871341"
---
# <a name="capacity-planning-for-azure-stack-hub-overview"></a>Azure Stack Hub 的容量规划概述

评估 Azure Stack Hub 解决方案时，应考虑硬件配置选择，因为它直接影响到 Azure Stack Hub 云的总体容量。

需要对 CPU、内存密度、存储配置和总体解决方案规模或服务器数目做出选择。 但是，确定可用容量将不同于传统的虚拟化解决方案，因为某些容量已在使用中。 构建 Azure Stack Hub 是为了在解决方案自身内部托管基础结构或管理组件。 另外，解决方案的某些容量保留用于支持复原。 复原的定义是：更新解决方案的软件时，必须将租户工作负载的中断降到最低程度。

> [!IMPORTANT]
> 可以从此容量规划信息和 [Azure Stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) 着手，进行 Azure Stack Hub 规划和配置决策。 该信息不能取代你自己的调查和分析。 对于此处提供的信息，Microsoft 不作任何明示或默示保证。

## <a name="hyperconvergence-and-the-scale-unit"></a>超融合性和缩放单元
Azure Stack Hub 解决方案是作为超融合群集构建的，包含计算和存储。 可以通过超融合性来共享群集中的硬件容量（称为“缩放单元”）。  在 Azure Stack Hub 中，缩放单元为资源提供可用性和可伸缩性。 缩放单元包含一组称为“主机”的 Azure Stack Hub 服务器。  基础结构软件托管在一组虚拟机 (VM) 中，与租户 VM 共享相同的物理服务器。 然后所有 Azure Stack Hub VM 可以通过缩放单元的 Windows Server 群集技术和独立的 Hyper-V 实例来管理。

缩放单元简化了 Azure Stack Hub 的获取和管理。 缩放单元还可以跨 Azure Stack Hub 移动和缩放所有服务（租户和体系结构）。

以下主题提供了有关每个组件的更多详细信息：

- [Azure Stack Hub 计算](azure-stack-capacity-planning-compute.md)
- [Azure Stack Hub 存储](azure-stack-capacity-planning-storage.md)
- [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md)
