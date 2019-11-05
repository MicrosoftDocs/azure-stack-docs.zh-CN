---
title: 什么是 Azure Stack 集线器开发工具包（ASDK）？ | Microsoft 文档
description: 了解 Azure Stack 集线器开发工具包以及如何使用它来评估 Azure Stack 中心。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 2669c8e9a4d684fbbd9340c6dbae43191f0261dc
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73594909"
---
# <a name="what-is-the-azure-stack-hub-development-kit-asdk"></a>什么是 Azure Stack 集线器开发工具包（ASDK）？
[Microsoft Azure Stack 集线器集成系统](../operator/azure-stack-overview.md)的大小范围是4-16 个节点，由硬件合作伙伴和 Microsoft 共同支持。 使用 Azure Stack 集线器集成系统为生产工作负荷启用新方案。 如果你是管理集成系统基础结构并提供服务的 Azure Stack 中心操作员，请参阅我们的[操作员文档](/azure-stack/operator)。

ASDK 是 Azure Stack 集线器的单节点部署，可以**免费**下载和使用。 所有 ASDK 组件都安装在单个主计算机上运行的虚拟机 (VM) 中，该计算机必须满足或超过[最低硬件要求](asdk-deploy-considerations.md#hardware)。 ASDK 旨在提供一个环境，在该环境中，你可以在*非生产*环境中使用与 Azure 一致的 api 和工具来评估 Azure Stack 中心和开发新式应用。 

> [!IMPORTANT]
> 不应在生产环境中使用 ASDK 或为其提供支持。

由于所有 ASDK 组件都部署到单个主计算机，因此可用的物理资源有限。 在 ASDK 部署中，Azure Stack 中心基础结构 Vm 和租户 Vm 均共存于同一台服务器计算机上。 此配置不应用于规模或性能评估。

ASDK 旨在为以下人员提供 Azure 一致性混合云体验：
- **管理员**（Azure Stack 中心操作员）： ASDK 是一个有用的资源，可用于评估和了解可用 Azure Stack 中心服务。
- **开发人员**：可以使用 ASDK 在本地（开发/测试环境）开发混合或新式应用。 这种灵活性在 Azure Stack 中心生产部署之前或一起提供了可重复性的开发体验。

观看此短视频，了解有关 ASDK 的详细信息：

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-hub-differences"></a>ASDK 和多节点 Azure Stack 集线器差异
单节点 ASDK 部署与多节点 Azure Stack 集线器部署的不同之处在于：

|说明|ASDK|多节点 Azure Stack 集线器|
|-----|-----|-----|
|**缩放**|所有组件都安装在一个单节点服务器计算机上。|大小范围为 4-16 节点。|
|**复原能力**|单节点配置不提供高可用性|支持[高可用性](../operator/azure-stack-overview.md#providing-high-availability)功能。|
|**联网**|ASDK 主机路由所有 ASDK 网络流量。 无其他切换要求。|需要在多节点部署中使用更复杂的[网络路由基础架构](../operator/azure-stack-network.md#network-infrastructure)，包括架顶 (TOR)、基板管理控制器 (BMC) 和边界（数据中心网络）交换机。|
|**修补和更新过程**|若要改用新版 ASDK，必须在 ASDK 主计算机上重新部署 ASDK。|用于更新安装的 Azure Stack 集线器版本的[修补程序和更新](../operator/azure-stack-updates.md)过程。|
|**支持**|MSDN Azure Stack 论坛。 Microsoft 客户服务和支持 (CSS) 支持不适用于非生产环境。|[MSDN Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack)和完整的 CSS 支持。|
| | |

## <a name="learn-about-available-services"></a>了解可用的服务
作为 Azure Stack 中心操作员，需要知道哪些服务可供用户使用。 Azure Stack 中心支持 Azure 服务的一部分，并且随着时间的推移，受支持服务的列表将继续演化。

### <a name="foundational-services"></a>基本服务
默认情况下，在部署 ASDK 时，Azure Stack 中心包含以下 "基础服务"：
- 计算
- 存储
- 网络
- 密钥保管库

有了这些基本服务，在向用户提供基础结构即服务 (IaaS) 时就可以尽量减少配置。

### <a name="additional-services"></a>其他服务
目前支持下述额外的平台即服务 (PaaS) 服务：
- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库

> [!NOTE]
> 这些服务在提供给用户之前，需要进行额外的配置，并且不是在安装 ASDK 时默认提供的。

## <a name="service-roadmap"></a>服务路线图
Azure Stack 中心将继续添加对其他 Azure 服务的支持。 若要了解 Azure Stack 中心的下一步，请参阅[Azure Stack 路线图](https://azure.microsoft.com/roadmap/?tag=azure-stack)。 


## <a name="next-steps"></a>后续步骤
若要开始评估 Azure Stack 中心，需要先[下载最新的 ASDK](asdk-download.md)并准备 ASDK 主机。 然后，可以安装 ASDK 并登录到管理员和用户门户，开始使用 Azure Stack 中心。