---
title: 模块化数据中心概述 |
description: 模块化数据中心是可移植、快速部署的数据中心，适用于临时和固定命令发布中的大规模对付操作。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 01/13/2020
ms.reviewer: prchint
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: d2803f1968be7a4a3e64f65f3d86a2dbb41aaf1c
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182862"
---
# <a name="modular-data-center-overview"></a>模块化数据中心概述 

模块化数据中心 (MDC) 基于 Azure Stack 集线器。 MDC 是可移植、快速部署的数据中心，适用于临时和固定命令发布中的大规模对付操作。

Azure Stack 中心是一种水平和垂直缩放的解决方案，可为 IaaS 和 PaaS 服务的多租户本机混合云功能提供到边缘的环境，并支持各种模块化方案，用于临时和固定的命令发布和 expeditionary 的强制。 Azure Stack 中心是一种集成的硬件和软件设备，可基于按节点缩放单位进行缩放，以多种容量提供，并可用于扩展，其中包括常规用途图形处理单元 (GPU) 启用的配置和可扩充的外部存储。

## <a name="how-you-can-use-the-mdc"></a>如何使用 MDC

Azure Stack 提供了四种与一致的核心原则，并为模块化边缘方案扩展了 Azure 的功能。 

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>使用通用的 DevOps 模型开发和交付应用，包括使用 Azure 的 API 对称

Azure 与 Azure Stack 的一致性是指开发一次用于 war 战斗机的解决方案，并将其部署为支持各种用例，并使用一组常用工具（如用于密钥管理的 Azure Key Vault 和用于资源监视和管理的 Azure Monitor）来保护和持续。 Azure Stack 可与本地数据、应用和工具 DevOps 和安全操作（例如密钥管理）互操作。

### <a name="deliver-azure-services-on-premises"></a>在本地提供 Azure 服务

Azure Stack 在 disadvantaged (争用、拥塞或拒绝) 通信环境以及稳健的通信环境中运行，并且不依赖于 Azure 的连接来执行任务应用和启用本地操作。 

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>使用集成的硬件和软件传递体验

模块化边缘需要一系列功能，这些功能不仅提供基准计算和存储服务，而且还提供机器学习、AI 和分析的高级功能。 在 austere 环境中，从模块边缘连接到安全云的功能非常重要，因为它可以提供对所需数据的 war 喷气式访问。

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>通过混合云安全操作使数据中心保持安全和可用

Azure Stack 的云本机设计通过允许管理员选择何时通过安装过程和内置于系统中的 Azure Stack 管理结构来修补和协调整个操作，从而消除了传统虚拟化环境的运营复杂性。

可以通过为用户提供相应的访问权限，通过 RBAC 向用户提供相应的访问权限管理终结点或命令行界面来执行操作，从而允许 Microsoft 在 Azure Stack 设备的字段中进行远程辅助管理。 这使得所有修补和其他管理和监视活动都能由 Microsoft 执行。 Azure Stack 可以通过安全管理门户或 Command-Line 接口 (CLI) 命令进行现场升级和更新，这两个命令应用安全更新和 IaaS 和 PaaS 功能更新，以便在本地或远程网络中根据需要维护与 Azure 的商业奇偶校验。 

## <a name="benefits-of-using-the-mdc"></a>使用 MDC 的优点

MDC 支持在 disadvantaged 通信环境中使用 Azure 的一致环境：
 - 利用 Azure 云服务的静态、模块化、快速部署数据中心，可在模块化操作中心 (TOC) 中为大型分析应用提供支持。
 - 通过一种支持 IaaS 基元（例如虚拟机、存储和虚拟网络）的单一方法，Azure 的模块化边缘产品/服务在云与边缘之间保持一致。
 - Azure Active Directory 和基于角色的访问控制支持
 - 通用管理接口
 - API 对称与 Microsoft、第三方和开源 DevOps 工具的支持
 - 通过 Azure Log Analytics 和 Azure 安全中心进行管理和监视
 - 通过启用混合云，将云计算的灵活性引入本地环境和边缘。<br>方法：
     - 重复使用代码并在 Azure 与本地环境中一致地运行云原生应用。
     - 使用 Azure 服务的可选连接来运行传统的虚拟化工作负荷。
     - 将数据传输到云，或将数据保留在主权数据中心，以保持符合性。
     - 运行硬件加速的机器学习、容器化或虚拟化工作负荷，所有这些操作都可以在智能边缘进行。

## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器容量规划](../operator/azure-stack-capacity-planning-overview.md)
