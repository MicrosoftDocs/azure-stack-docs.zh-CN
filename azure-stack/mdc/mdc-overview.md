---
title: 模块化数据中心概述 |
description: Azure 模块化数据中心是一种可移植、快速部署的数据中心，适用于临时和固定命令发布中的大规模对付操作。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 11/06/2020
ms.reviewer: prchint
ms.lastreviewed: 11/06/2020
ms.openlocfilehash: eabafd31d25a59cd43daa1a0779afed0a80f8354
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924310"
---
# <a name="modular-datacenter-overview"></a>模块化数据中心概述

Azure 模块式 Datacenter (MDC) 基于 Azure Stack 集线器。 MDC 是可移植、快速部署的数据中心，适用于临时和固定命令发布中的大规模对付操作。

Azure Stack 中心是一种水平和垂直缩放的解决方案，可提供多租户的本机混合云功能，可用于基础结构即服务 (IaaS) 和平台即服务 (PaaS) 服务到边缘的环境。 Azure Stack 集线器支持各种模块化方案，用于临时和固定的命令发布和 expeditionary 强制。

Azure Stack 中心是一种集成的硬件和软件设备，可基于通过节点缩放单位进行缩放而从多种容量获得。 Azure Stack 集线器适用于扩展，其中包括常规用途的图形处理单元 (GPU) 启用的配置和可扩充的外部存储。

## <a name="use-mdc"></a>使用 MDC

Azure Stack 实行四项核心原则，这些原则与 Azure 的用于模块化边缘方案的功能相符，并且扩展了这些功能。

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>使用通用的 DevOps 模型（包括与 Azure 的 API 对称性）开发和交付应用

Azure 与 Azure Stack 之间的一致性意味着解决方案开发一次，部署为支持各种用例，并使用一组常用工具来保护和保持安全。 工具的示例包括用于密钥管理的 Azure Key Vault，以及用于资源监视和管理的 Azure Monitor。 Azure Stack 可与本地数据、应用以及适用于 DevOps 和安全操作（例如密钥管理）的工具交互。

### <a name="deliver-azure-services-on-premises"></a>在本地提供 Azure 服务

Azure Stack 在 disadvantaged (争用、拥塞或拒绝) 通信环境和可靠的通信环境。 Azure Stack 不依赖于 Azure 的连接来执行任务应用和启用本地操作。

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>使用集成的硬件和软件传递体验

模块化边缘需要一系列功能，这些功能可提供基线计算和存储服务，以及用于机器学习、AI 和分析的高级功能。 从模块边缘连接到安全云的功能，以及在 austere 环境中独立于其进行操作的能力，这一点对于提供对所需数据的访问至关重要。

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>通过混合云安全操作使数据中心保持安全且可用

Azure Stack 的云本机设计消除了传统虚拟化环境中的运营复杂性。 管理员可以选择何时通过安装过程以及本地内置于系统中的 Azure Stack 管理结构来修补和协调整个操作。

可以通过基于角色的访问控制为用户提供适当的访问权限，通过基于角色的访问控制访问 Microsoft 中的远程协助管理或 Microsoft 托管的产品/服务 Azure Stack， (RBAC) ，通过管理门户、特权管理终结点或命令行接口 (CLI) 执行操作。 此功能可让 Microsoft 执行所有修补和其他管理和监视活动。 Azure Stack 可以通过安全管理门户或 CLI 命令（可应用安全更新和 IaaS 和 PaaS 功能更新），通过本地或远程网络维持 Azure 的商业奇偶校验。

## <a name="benefits-of-using-mdc"></a>使用 MDC 的优点

在条件很差的环境中，MDC 支持与 Azure 一致的环境：

 - 具有 Azure 云服务的静态、模块化、快速可部署的数据中心，可为模块化操作中心的大型分析应用提供支持。
 - Azure 的模块化边缘产品/服务通过一种用于支持 IaaS 基元（例如虚拟机、存储和虚拟网络）的单一方法，维护云与边缘之间的一致性。
 - Azure Active Directory 和 RBAC 支持。
 - 通用管理接口。
 - API 对称和支持 Microsoft、第三方和开源 DevOps 工具。
 - 通过 Azure Log Analytics 和 Azure 安全中心进行管理和监视。
 - 通过启用混合云，将云计算的灵活性引入本地环境和边缘。<br>方法：
     - 重复使用代码并在 Azure 与本地环境中一致地运行云原生应用。
     - 使用 Azure 服务的可选连接来运行传统的虚拟化工作负荷。
     - 将数据传输到云，或将数据保留在主权数据中心，以保持符合性。
     - 在智能边缘运行硬件加速的机器学习、容器化或虚拟化工作负荷。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 容量计划](../operator/azure-stack-capacity-planning-overview.md)
