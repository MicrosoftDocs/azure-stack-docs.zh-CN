---
title: 概述 Azure Stack 的容量规划 |Microsoft Docs
description: 了解适用于 Azure Stack 部署的容量规划。
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 335e92fcdb2eadd8bebfbfd3fb2e3b31edd00734
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131338"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Azure Stack 容量规划的概述

在要评估 Azure Stack 解决方案，请考虑 Azure Stack 云的总体容量有直接影响的硬件配置选择。 

例如，您需要进行选择的 CPU、 内存密度、 存储配置和总体解决方案规模或的服务器数量。 不同于传统的虚拟化解决方案，不适用于这些组件才能确定可用的容量的简单算术。 Azure Stack 构建托管在解决方案自身内部的基础结构或管理组件。 此外，某些解决方案的容量保留以支持复原，以尽量减少的租户工作负荷造成中断的方式的解决方案的软件更新。 

> [!IMPORTANT]
> 此容量规划信息和[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)是 Azure Stack 规划和配置决策的起点。 此信息并不旨在取代你自己的调查和分析。 Microsoft 不做任何陈述或者任何明示或暗示的担保，此处提供的信息。
 
Azure Stack 解决方案旨在作为超聚合群集的计算和存储。 聚合允许在群集中，称为共享祑砰甧秖*缩放单位*。 在 Azure Stack 缩放单位提供的可用性和可伸缩性的资源。 扩展单元包含的一组 Azure Stack 服务器，称为*主机*。 基础结构软件集中的虚拟机 (Vm) 托管，并共享为租户 Vm 相同的物理服务器。 所有 Azure Stack Vm 然后进行都管理的缩放单位的 Windows Server 群集技术和单独的 HYPER-V 实例。 

获取和管理 Azure Stack，简化了缩放单位。 缩放单位还允许用于移动和所有服务 （租户和基础结构） 的可伸缩性的跨 Azure Stack。 

以下主题提供有关每个组件的更多详细信息：

- [Azure Stack 计算](azure-stack-capacity-planning-compute.md)
- [Azure Stack 存储](azure-stack-capacity-planning-storage.md)
- [Azure Stack 容量计划程序](azure-stack-capacity-planner.md)
