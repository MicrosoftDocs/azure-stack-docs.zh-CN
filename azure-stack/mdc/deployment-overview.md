---
title: 模块化数据中心 (MDC) 部署概述，并为 Azure Stack 集线器硬件生命周期 (主机安装) 管理服务器 |Microsoft Docs
description: 从规划到后期部署，了解如何成功地现场部署模块化数据中心 (MDC) 。
services: azure-stack
documentationcenter: ''
author: asganesh
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 60361a3e44b5ad54c63e6a94223d75a7b5106b9e
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253921"
---
# <a name="mdc-deployment-overview"></a>MDC 部署概述

本部署指南介绍了安装和配置模块化数据中心 (MDC) 的步骤。 本指南还介绍了为 Azure Stack 中心部署 (HLH) 管理服务器设置 Azure Stack 集线器硬件生命周期主机的自动化过程。

本指南的目标包括：

- 提供预先部署清单，验证在安装组件之前是否已满足所有先决条件。
- 介绍 MDC 的关键组件。
- 描述如何安装和配置关键组件。
- 验证客户部署。

若要充分了解本指南的内容，需要具备虚拟化、服务器、操作系统、网络和存储解决方案的技术经验。 部署工程师必须了解 Microsoft Windows Server 2019 with Hyper-v、Azure Stack Hub、Azure 和 Microsoft PowerShell。

本指南重点介绍 Microsoft Azure Stack 集线器的核心组件的部署，以及 MDC 解决方案的具体信息。 本指南未介绍 Azure Stack 集线器的操作过程，并且不涵盖 Azure Stack 中心提供的所有功能。 有关详细信息，请参阅 [Azure Stack 中心操作员指南](https://docs.microsoft.com/azure-stack/operator/)。

## <a name="introduction"></a>简介

MDC 是适用于在标准40英尺金属发货容器中打包的 Azure Stack 集线器的集成产品。 容器包括气候控制单元、照明和报警系统。 核心 Azure Stack 集线器组件（如服务器和交换机）安装在以逻辑方式组织在三个独立 pod 中的六个物理机架中。

每个 pod 都包含两个42U 架。 Pod 包含机顶盒 (ToR) 交换机、边缘交换机和基板管理控制器 (BMC) 开关。 此外，每个 pod 包含硬件生命周期主机 (HLH) 和串行端口集中器。 核心计算和存储容量由 Azure Stack 集线器缩放单位提供， (SU) 包含8个坚固的边缘设备 (REA) R840 服务器。 额外的存储容量由 48 Isilon 存储节点提供。 所有盒的物理配置都是相同的。

## <a name="terminology"></a>术语

下表列出了本指南中使用的一些术语。

|术语    |定义 |
|-------|-----------|
|硬件生命周期主机 (HLH) |    HLH 是用于初始部署启动以及日常硬件管理、支持和备份 Azure Stack 中心基础结构的物理服务器。 HLH 运行具有桌面体验和 Hyper-v 角色的 Windows Server 2019。 服务器用于承载硬件管理工具、交换机管理工具、Azure Stack 集线器合作伙伴工具包和部署虚拟机。 |
|部署虚拟机 (DVM) |    DVM 是在 Azure Stack 集线器软件部署期间在 HLH 上创建的虚拟机。 DVM 运行 Azure Stack 集线器软件业务流程引擎（称为企业云引擎） (ECE) ，通过网络在所有 Azure Stack 中心缩放单元服务器上安装和配置 Azure Stack 集线器构造基础结构软件。|
|Azure Stack 集线器合作伙伴工具包|    用于捕获客户特定输入参数并启动 Azure Stack 集线器安装和配置的软件工具的集合。 它包括部署工作表，该工作表是一个图形用户界面， () GUI 用于捕获和存储用于 Azure Stack 集线器安装的可配置参数。 它还包括网络配置生成器工具，该工具使用部署工作表输入为解决方案中的所有物理网络设备生成网络配置文件。|
|OEM 扩展包    |在初始部署和更新过程中由 Azure Stack 中心使用的专用格式的固件包、设备驱动程序和硬件管理工具。|
|串行端口集中器    |安装在每个 pod 中的物理设备，可提供网络交换机串行端口的网络访问权限，以便进行部署和管理。|
|缩放单元    |Azure Stack 集线器的核心组件，可提供计算和存储资源，以 Azure Stack 集线器结构基础结构和工作负载。 每个 pod 都包含八个 MDC R840 服务器（也称为节点）。|
|Isilon 存储 |    特定于 MDC 解决方案的 Azure Stack 集线器组件。 Isilon 为 Azure Stack 集线器工作负荷提供额外的 blob 和文件存储。 每个 pod 包含 48 Isilon 存储节点。|
|Pod    |在 MDC 的上下文中，pod 是一个独立的逻辑单元，它由两个互连的物理机架组成。 完整的解决方案包括在单个容器中安装的三个 pod。|





## <a name="deployment-workflow"></a>部署工作流

在高级别上，MDC 部署过程包括以下步骤：

1. 规划阶段：
   1. 规划数据中心电源。
   1. 规划 Azure Stack 集线器的逻辑网络配置。
   1. 规划数据中心网络集成。
   1. 规划标识和安全集成。
   1. 规划 PKI 证书。
1. 准备阶段：
   1. 正在收集清单。
   1. 接通电源并打开解决方案。
   1. 正在验证 HVAC 系统运行状况。
   1. 验证火灾监视和警报系统运行状况。
   1. 正在验证物理硬件运行状况。
1. 执行阶段–为三个盒中的每个 pod 单独执行：
   1. 配置硬件生命周期主机。
   1. 正在配置网络交换机。
   1. 数据中心网络集成。
   1. 配置物理硬件设置。
   1. 配置 Isilon 存储。
   1. 部署 Azure Stack 集线器结构基础结构。
   1. 数据中心标识集成。
   1. 安装扩展功能的加载项。
1. 验证阶段–为三个盒中的每个盒单独提供：
   1. 部署后运行状况验证。
   1. 向 Microsoft 注册 Azure Stack 中心。
   1. Azure Stack 中心运营商。
  
本指南将详细介绍上述每个主题。
