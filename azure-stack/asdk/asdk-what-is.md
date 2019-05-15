---
title: Azure Stack 开发工具包 (ASDK) 简介 | Microsoft Docs
description: 介绍 ASDK 的新增和评估 Microsoft Azure Stack 的常见用例。
services: azure-stack
documentationcenter: ''
author: mattbriggs
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
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 08f5d51eb4ded47d1e52ec53d4c75903d13c8815
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617683"
---
# <a name="what-is-the-azure-stack-development-kit"></a>什么是 Azure Stack 开发工具包？
[Microsoft Azure Stack 集成系统](../operator/azure-stack-overview.md)范围从 4 到 16 节点的大小，由硬件合作伙伴和 Microsoft 共同提供支持。 使用 Azure Stack集成系统为自己的生产工作负荷实现新方案。 如果你是 Azure Stack 操作员，想要管理集成系统基础结构并提供服务，请参阅[操作员文档](/azure-stack/operator)。

Azure Stack 开发工具包 (ASDK) 用于对 Azure Stack 进行单节点部署，可以**免费**下载和使用。 所有 ASDK 组件都安装在单主机服务器计算机上运行的虚拟机中，该计算机必须满足或超过[最低硬件要求](asdk-deploy-considerations.md#hardware)。 ASDK 旨在提供一个特定的环境，方便你在其中评估 Azure Stack 并开发现代应用程序，所使用的 API 和工具与非生产环境中的 Azure 一致。 

> [!IMPORTANT]
> 不应在生产环境中使用 ASDK 或为其提供支持。

由于所有 ASDK 组件都部署到单个开发工具包主机，因此可用的物理资源有限。 进行 ASDK 部署时，Azure Stack 基础结构 VM 和租户 VM 共存于同一服务器计算机。 此配置不适用于规模或性能评估。

ASDK 旨在为以下人员提供 Azure 一致性混合云体验：
- **管理员**（Azure Stack 操作员）。 ASDK 是适用于评估和了解可用 Azure Stack 服务的资源。
- **开发人员**。 ASDK 可以用来在本地（开发/测试环境）开发混合应用程序或现代应用程序。 这样就可以在进行 Azure Stack 生产部署的同时或之前重复地获得开发体验。 

观看此短视频，了解有关 ASDK 的详细信息：

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK 和多节点 Azure Stack 的差异
单节点 ASDK 部署在一些应该注意的重要方面不同于多节点 Azure Stack 部署。

|描述|ASDK|多节点 Azure Stack|
|-----|-----|-----|
|**缩放**|所有组件都安装在一个单节点服务器计算机上。|大小范围为 4-16 节点。|
|**复原能力**|单节点配置不提供高可用性|支持[高可用性](../operator/azure-stack-overview.md#providing-high-availability)功能。|
|**网络**|ASDK 主机路由所有 ASDK 网络流量。 无其他切换要求。|需要在多节点部署中使用更复杂的[网络路由基础架构](../operator/azure-stack-network.md#network-infrastructure)，包括架顶 (TOR)、基板管理控制器 (BMC) 和边界（数据中心网络）交换机。|
|**修补和更新过程**|若要改用新版 ASDK，必须在开发工具包主机上重新部署 ASDK。|[修补和更新](../operator/azure-stack-updates.md)过程用于更新已安装的 Azure Stack 版本。|
|**支持**|MSDN Azure Stack 论坛。 Microsoft 客户服务和支持 (CSS) 支持不适用于非生产环境。|[MSDN Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack)和完整的 CSS 支持。|
| | |

## <a name="learn-about-available-services"></a>了解可用的服务
作为 Azure Stack 操作员，你需要了解哪些服务可以提供给用户。 Azure Stack 支持部分 Azure 服务，而随着时间的推移，受支持服务的列表会不断变化。

### <a name="foundational-services"></a>基本服务
默认情况下，ASDK 在部署时包括以下“基本服务”：
- 计算
- 存储
- 网络
- Key Vault

有了这些基本服务，在向用户提供基础结构即服务 (IaaS) 时就可以尽量减少配置。

### <a name="additional-services"></a>其他服务
目前支持下述额外的平台即服务 (PaaS) 服务：
- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库

> [!NOTE]
> 这些服务在提供给用户之前，需要进行额外的配置，并且不是在安装 ASDK 时默认提供的。

## <a name="service-roadmap"></a>服务路线图
Azure Stack 会持续增加对其他 Azure 服务的支持。 若要了解 Azure Stack 随后会推出哪项功能，请参阅 [Azure Stack 路线图](https://azure.microsoft.com/roadmap/?tag=azure-stack)。 


## <a name="next-steps"></a>后续步骤
若要开始评估 Azure Stack，需要先[下载最新 ASDK](asdk-download.md) 并准备 ASDK 主机。 准备开发工具包主机后，可以安装 ASDK 并登录管理员和用户门户以开始使用 Azure Stack。