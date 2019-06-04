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
ms.openlocfilehash: 0d2610d2082973f4ab255027cc299b5858660c58
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2019
ms.locfileid: "66460988"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Azure Stack 容量规划的概述

评估 Azure Stack 解决方案时，必须在硬件配置方面做出选择，因为它直接影响到 Azure Stack 云的总体容量。 这些是有关 CPU、内存密度、存储配置和总体解决方案规模或服务器数目的常规选择。 不同于传统的虚拟化解决方案，简单地评估这些组件并不能很好地确定可用的容量。 这方面的第一个原因是 Azure Stack 构建为在解决方案自身内部托管基础结构或管理组件。 第二个原因是软件的容量的某些解决方案容量的保留用于支持复原，以尽量减少的租户工作负荷造成中断的方式的解决方案更新。 

> [!IMPORTANT]
> 此容量规划信息和[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)是 Azure Stack 规划和配置决策的起点。 它不是作为你自己的调查和分析的替代。 Microsoft 不做任何陈述或者任何明示或暗示的担保，此处提供的信息。
 
Azure Stack 解决方案旨在作为超聚合群集的计算和存储。 聚合允许在群集中，称为共享祑砰甧秖*缩放单位*。 在 Azure Stack 缩放单位提供的可用性和可伸缩性的资源。 扩展单元包含的一组 Azure Stack 服务器，称为*主机*。 基础结构软件驻留在一组 Vm，并共享为租户 Vm 相同的物理服务器。 所有 Azure Stack Vm 然后进行都管理的缩放单位的 Windows Server 群集技术和单独的 HYPER-V 实例。 获取和管理 Azure Stack，简化了缩放单位。 缩放单位还允许用于移动和所有服务 （租户和基础结构） 的可伸缩性的跨 Azure Stack。 

以下主题提供有关每个组件的更多详细信息：

- [Azure Stack 计算](azure-stack-capacity-planning-compute.md)
- [Azure Stack 存储](azure-stack-capacity-planning-storage.md)
- [Azure Stack 容量计划程序](azure-stack-capacity-planner.md)
