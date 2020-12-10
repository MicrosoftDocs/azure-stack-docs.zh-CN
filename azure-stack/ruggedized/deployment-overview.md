---
title: Azure Stack 中心耐用部署，并为 Azure Stack 中心硬件生命周期主机 (HLH) 管理服务器进行设置 |Microsoft Docs
description: 从规划到后期部署中了解 Azure Stack 中心耐用的成功现场部署的预期内容。
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 0d788e1a41ed512a1d39c7a3e3680d193fdaecf2
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939385"
---
# <a name="azure-stack-hub-ruggedized-deployment-overview"></a>Azure Stack 中心耐用部署概述

本部署指南介绍了安装和配置 Azure Stack Hub 耐用的步骤。 

本指南的目标包括：

- 提供部署前清单，以便在安装组件之前验证是否已满足所有先决条件。
- 介绍 Azure Stack 中心耐用的关键组件。
- 说明如何安装和配置关键组件。
- 验证客户部署。

本部署指南适用于负责在客户现场部署 Azure Stack 中心耐用的 Microsoft 现场专业团队。

若要充分理解本指南的内容，需要具备虚拟化、服务器、操作系统、网络和存储解决方案方面的技术经验。 部署工程师必须了解 Microsoft Windows Server 2019 with Hyper-V、Azure Stack Hub、Azure 和 Microsoft PowerShell。

本指南重点介绍 Microsoft Azure Stack 集线器的核心组件的部署，以及 Azure Stack 中心耐用解决方案的具体信息。 本指南未介绍 Azure Stack Hub 的操作过程，并且未涵盖 Azure Stack Hub 提供的所有功能。 有关详细信息，请参阅 [Azure Stack 中心运营商指南](https://docs.microsoft.com/azure-stack/operator/)。

## <a name="introduction"></a>介绍

Azure Stack 集线器耐用是 Microsoft Azure Stack 中心的耐用和现场可部署产品。 核心组件（如服务器和交换机）包含在称为 pod 的传输事例中。

Pod 是一种4U 机架容器，其尺寸比常规4U 机架小。 有一个管理 pod 和两个缩放单位 (SU) pod。 此管理组件包含硬件生命周期主机 (HLH) ，2 25 GbE 顶层 (ToR) 交换机，基板管理控制器 (BMC) 交换机。

每个 SU pod 都包含两个 Azure Stack Hub 耐用 R640 SU 服务器。 一台 R640 服务器占据盒中的2U 货架空间。 在部署过程中，SU pod 中的服务器连接到管理 pod 中的 BMC 和 ToR 交换机。

## <a name="terminology"></a>术语

下表列出了本指南中使用的一些术语。

|术语   | 定义 |
|-------|------------|
|硬件生命周期主机 (HLH)| HLH 是用于 Azure Stack Hub 基础结构的初始部署启动以及日常硬件管理、支持和备份的物理服务器。 HLH 运行具有桌面体验和 Hyper-V 角色的 Windows Server 2019。 服务器用于承载硬件管理工具、交换机管理工具、Azure Stack Hub 合作伙伴工具包和部署虚拟机。 |
|部署虚拟机 (DVM)|  DVM 是在部署 Azure Stack Hub 软件期间在 HLH 上创建的虚拟机。 DVM 运行 Azure Stack Hub 软件业务流程引擎（称为企业云引擎 (ECE)），可以通过网络在所有 Azure Stack Hub 缩放单元服务器上安装和配置 Azure Stack Hub 构造基础结构软件。|
|Azure Stack Hub 合作伙伴工具包|   一个软件工具集合，用来捕获 Azure Stack Hub 的特定于客户的输入参数并启动其安装和配置。 它包括部署工作表，该工作表是一个图形用户界面 (GUI) 工具，用于捕获和存储 Azure Stack Hub 安装的可配置参数。 它还包括网络配置生成器工具，该工具使用部署工作表输入为解决方案中的所有物理网络设备生成网络配置文件。|
|OEM 扩展包  |一个包含固件、设备驱动程序和硬件管理工具的包，它采用专用格式，在初始部署和更新过程中由 Azure Stack Hub 使用。|
|集成 Dell 远程访问控制器 (iDRAC) |  使用生命周期控制器的 iDRAC 是每个 Azure Stack 集线器耐用 R640 服务器中嵌入的基板管理控制器。 iDRAC 提供带外管理功能，以帮助部署、更新、监视和维护 Azure Stack 中心服务器。|
|缩放单元 |Azure Stack Hub 的一个核心组件，为 Azure Stack Hub 构造基础结构和工作负荷提供计算和存储资源。 由四个 Azure Stack 集线器耐用 R640 服务器组成，这些服务器 (也称为节点) ，可以动态地扩展到最多16个节点。|
|Pod    |在 Azure Stack Hub 耐用的上下文中，pod 是一种物理耐用的容器，其设计目的是包含机架安装支架和电击 absorbers，以保护 Azure Stack 集线器耐用硬件免受环境物理压力。 包括前端和后传输案例，其中包括可以安装和密封以传输硬件的情况。 最小配置中的完整解决方案包含三个 pod。|


## <a name="deployment-overflow"></a>部署溢出

在高级别中，Azure Stack 集线器耐用部署过程包含以下步骤。

1. 规划阶段：
   1. 规划数据中心电源和冷却。
   1. 规划 Azure Stack Hub 的逻辑网络配置。
   1. 规划数据中心网络集成。
   1. 规划标识和安全集成。
   1. 规划 PKI 证书。
1. 准备阶段：
   1. 取消装箱和收集清单。
   1. 连接电源并为解决方案接通电源。
   1. 验证物理硬件运行状况。
1. 执行阶段：
   1. 配置硬件生命周期主机。
   1. 配置网络交换机。
   1. 数据中心网络集成。
   1. 配置物理硬件设置。
   1. 部署 Azure Stack Hub 构造基础结构。
   1. 数据中心标识集成。
   1. 安装附加产品以实现扩展功能。
1. 验证阶段：
   1. 部署后运行状况验证。
   1. 将 Azure Stack Hub 注册到 Microsoft。
   1. Azure Stack Hub 操作员移交。
   
本指南将进一步详细介绍上述每个主题。
