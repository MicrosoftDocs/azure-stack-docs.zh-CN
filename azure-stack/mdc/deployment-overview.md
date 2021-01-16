---
title: Modular Data Center (MDC) 部署概述和 Azure Stack Hub 硬件生命周期主机 (HLH) 管理服务器的设置 | Microsoft Docs
description: 了解在现场成功部署 Modular Data Center (MDC) 后预期产生的效果（从规划到部署后）。
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
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 5d59532308239f9eda95ba9f81599235a8c8f61c
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255548"
---
# <a name="mdc-requirements-overview"></a>MDC 要求概述

本指南描述了安装和配置模块化数据中心 (MDC) 所需的要求。 

本指南的目标包括：

- 提供部署前清单，以便在安装组件之前验证是否已满足所有先决条件。
- 介绍 MDC 的关键组件。
- 验证客户部署。

若要充分理解本指南的内容，需要具备虚拟化、服务器、操作系统、网络和存储解决方案方面的技术经验。 

本指南重点介绍 Microsoft Azure Stack 集线器的核心组件的部署，以及 MDC 解决方案的具体信息。 本指南未介绍 Azure Stack Hub 的操作过程，并且未涵盖 Azure Stack Hub 提供的所有功能。 

## <a name="introduction"></a>介绍

MDC 是用于 Azure Stack Hub 的集成产品，包装在标准的 40 英尺金属运输集装箱中。 集装箱包括气候控制单元、照明和报警系统。 核心 Azure Stack 集线器组件安装为三个独立的 Pod： Pod 1、机架1和机架2、Pod 2、机架1和机架2以及 Pod 3、机架1和机架2。

每个 Pod 都包含两个 42U 机架。 Pod 包括架顶 (ToR) 交换机、边缘交换机和基板管理控制器 (BMC) 交换机。 此外，每个 Pod 都包括硬件生命周期主机 (HLH) 和串行端口集中器。 核心计算和存储容量由 Azure Stack Hub 缩放单元 (SU) 提供，包含 8 台 Rugged Edge Appliance (REA) R840 服务器。 48 个 Isilon 存储节点提供额外的存储容量。 所有 Pod 的物理配置都是相同的。

## <a name="terminology"></a>术语

下表列出了本指南中使用的一些术语。

|术语    |定义 |
|-------|-----------|
|硬件生命周期主机 (HLH)|    HLH 是用于 Azure Stack Hub 基础结构的初始部署启动以及日常硬件管理、支持和备份的物理服务器。 HLH 运行具有桌面体验和 Hyper-V 角色的 Windows Server 2019。 服务器用于承载硬件管理工具、交换机管理工具、Azure Stack Hub 合作伙伴工具包和部署虚拟机。 |
|部署虚拟机 (DVM)|    DVM 是在部署 Azure Stack Hub 软件期间在 HLH 上创建的虚拟机。 DVM 运行 Azure Stack Hub 软件业务流程引擎（称为企业云引擎 (ECE)），可以通过网络在所有 Azure Stack Hub 缩放单元服务器上安装和配置 Azure Stack Hub 构造基础结构软件。|
|Azure Stack Hub 合作伙伴工具包|    一个软件工具集合，用来捕获 Azure Stack Hub 的特定于客户的输入参数并启动其安装和配置。 它包括部署工作表，该工作表是一个图形用户界面 (GUI) 工具，用于捕获和存储 Azure Stack Hub 安装的可配置参数。 它还包括网络配置生成器工具，该工具使用部署工作表输入为解决方案中的所有物理网络设备生成网络配置文件。|
|OEM 扩展包    |一个包含固件、设备驱动程序和硬件管理工具的包，它采用专用格式，在初始部署和更新过程中由 Azure Stack Hub 使用。|
|串行端口集中器    |一个安装在每个 Pod 中的物理设备，它提供对网络交换机串行端口的网络访问权限，方便用户进行部署和管理。|
|缩放单元    |Azure Stack Hub 的一个核心组件，为 Azure Stack Hub 构造基础结构和工作负荷提供计算和存储资源。 每个 Pod 都包含八台 MDC R840 服务器（也称为节点）。|
|Isilon 存储 |    一个特定于 MDC 解决方案的 Azure Stack Hub 组件。 Isilon 为 Azure Stack Hub 工作负荷提供额外的 blob 和文件存储。 每个 Pod 都包括 48 个 Isilon 存储节点。|
|Pod    |在 MDC 的上下文中，Pod 是一个独立的逻辑单元，它由两个互连的物理机架组成。 完整的解决方案包括安装在单个容器中的三个 Pod。|

## <a name="deployment-workflow"></a>部署工作流

在高级别上，MDC 部署过程包括以下阶段：

### <a name="planning-phase"></a>规划阶段
1. 规划数据中心电源。
1. 规划 Azure Stack Hub 的逻辑网络配置。
1. 规划 [数据中心网络集成](../operator/azure-stack-network.md)。
1. 规划 [标识和安全集成](/azure/security/fundamentals/identity-management-best-practices)。
1. 规划 [PKI 证书](../operator/azure-stack-pki-certs.md)。

### <a name="preparation-phase"></a>准备阶段
1. 收集清单。
1. 连接电源并为解决方案接通电源。
1. 验证 HVAC 系统运行状况。
1. 验证火灾监视和警报系统运行状况。
1. 验证物理硬件运行状况。

### <a name="execution-phase--separately-for-each-of-the-three-pods"></a>执行阶段–为三个盒中的每个 pod 单独执行
1. 配置硬件生命周期主机。
1. 配置网络交换机。
1. 数据中心网络集成。
1. 配置物理硬件设置。
1. 配置 Isilon 存储。
1. 部署 Azure Stack Hub 构造基础结构。
1. 数据中心标识集成。
1. 安装附加产品以实现扩展功能。

### <a name="validation-phase--separately-for-each-of-the-three-pods"></a>验证阶段–为三个盒中的每个 pod 单独提供
1. 部署后运行状况验证。
1. 将 Azure Stack Hub 注册到 Microsoft。
1. Azure Stack 中心客户 "。